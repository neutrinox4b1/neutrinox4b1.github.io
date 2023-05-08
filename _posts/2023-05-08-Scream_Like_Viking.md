---
layout: post
title: "Scream Like Viking"
description: PwnMe 2023 - 8bits
tags:
categories: crypto
giscus_comments: false
date: 2023-05-06
---

## Background

---

> *Author: Ectario#7001*
> 
> 
> Our protagonist John is in a room, he hears some kind of noise, like something resonating. But he doesn't understand it... Perhaps he could play with his own echoes to guess what the meaning of this famous resonance could be...
> 

### server.py

```python
#!/usr/bin/env python3

from Crypto.Util.number import getPrime, bytes_to_long, long_to_bytes
from Crypto.Util.Padding import pad

e = 17
p = getPrime(512)
q = getPrime(512)
N = p * q

def encrypt(m):
    assert 0 <= m < N    
    c = pow(bytes_to_long(pad(long_to_bytes(m), 50)), e, N)
    return int(c)

def encrypt_flag():
    with open("flag.txt", "rb") as f:
        flag = f.read()
    c = pow(bytes_to_long(pad(flag, 50)), e, N)
    return c

def main():
    try:
        while True:
            print("Enter your option (Encrypt or Flag) > ", end='')
            cmd = (input().strip())
            if cmd == "Encrypt":
                print("Enter your integer to encrypt > ", end='')
                m = int(input())
                c = encrypt(m)
                print(str(c) + '\n')
            elif cmd == "Flag":
                c = encrypt_flag()
                print("Flag cipher for you: " + str(c) + '\n')
                return
    except Exception as e:
        print("An error occured:\n", e)

if __name__ == "__main__":
    main()
```

RSA에 대한 문제이다.

main에서 Encrypt나 Flag를 입력받고 Encrypt일 경우에는 encrypt할 숫자를 입력한다.

Flag일 경우엔 flag를 바이너리모드로 읽어서 암호화하여 출력한다.

원하는 평문은 마음대로 입력 가능하지만, 0부터 N사이여야 한다.

모든 암호화 과정에는 평문을 50 byte 단위로 padding한다.

<br>
<br>
<br>

## Solution

---

Flag를 입력하면 프로그램이 종료되니 그 전에 비밀키 d를 파악해야 한다.

일단 50 패딩으로 인해 낮은 지수 공격은 어려울듯

풀이를 보니 hastad Attack을 이용했다. 왜 계속 한 연결 내에서만 문제를 풀려고 했는지 싶다.

낮은 지수 e에 대해서 Hastad Attack이 가능하다.

같은 평문인 flag.txt에 대해서 다른 N끼리 작은 공개키 17을 가지고 암호화된다.

연결을 반복하면서 합동식을 여러 개 수집하고 이에 대해서 hastad 공격을 수행한다.

아니 근데 N을 모르지 않나?

N을 계산하기 위해서 Encrypt가 존재한다. 임의의 평문들에 대해서 같은 모듈리 N으로 암호화되기 때문에 gcd를 이용한 N 계산이 가능하다.

평문 m에 대해서 $$m^e \equiv c \bmod N$$이다. 우리는 c와 e, 그리고 m을 알고 있다.

$$m^e - c \equiv \bmod N$$ 이고 $$N \mid (m^e - c)$$ 이다. $$m_1, m_2, m_3$$에 대해 $$c_1, c_2, c_3$$를 가지고 있으므로

$$\gcd(m_1^e-c_1, m_2^e-c_2, m_3^e-c_3)$$ 계산 시 N을 가질 가능성이 있다. 얘네들끼리 서로소라면 더 잘 구할 수 있을듯..?

```python
from pwn import *
from Crypto.Util.number import *
from Crypto.Util.Padding import pad, unpad
from math import gcd
from sympy.ntheory.modular import *
from gmpy2 import *

def get_n():
	msg = [long_to_bytes(getPrime(150)), long_to_bytes(getPrime(150)), long_to_bytes(getPrime(150))]
	server_msg = [pad(i, 50) for i in msg]
	ans = []
	for i in range(len(msg)):
		p.sendlineafter(b'> ', b'Encrypt')
		p.sendlineafter(b'> ', str(bytes_to_long(msg[i])).encode())
		ans.append(int(p.recvline().strip().decode()))
	multiples = [bytes_to_long(server_msg[i]) ** 17 - ans[i] for i in range(len(server_msg))]
	N = gcd(*multiples)
	return N

def get_flag():
	p.sendlineafter(b'> ', b'Flag')
	p.recvuntil(b': ')
	flag = int(p.recvline().strip().decode())
	p.close()
	return flag

def delete_no_coprime(argn, argc):
	retn = []
	retc = []
	for i in range(len(argn)):
		keep = True
		for j in range(i+1, len(argn)):
			if gcd(argn[i], argn[j]) != 1:
				keep = False
				break
		if keep:
			retn.append(argn[i])
			retc.append(argc[i])
	return retn, retc

n = []
c = []
e = 17

for _ in range(7):
	p = remote('51.68.95.78', 32773)
	n.append(get_n())
	c.append(get_flag())

n, c = delete_no_coprime(n, c)
res = crt(n, c)

with local_context() as lcx:
	lcx.precision = 12000
	flag = unpad(long_to_bytes(int(root(res[0], e))),50)
	print(flag.decode())
	open('flag.txt', 'wb').write(flag)
```

원래 coprime 검사를 넣지 않았는데 풀이에서 있길래 넣었다. 이렇게 하면 정확도가 더 올라간다.

CRT는 모든 n이 서로소여야 하기 때문이다.

n과 flag를 암호화 한 값들을 구하고 hastad공격을 수행한다.

CTF가 끝나서 맞는지 확인할 수는 없지만 Flag 형식을 보아 맞는 것으로 생각된다.

 

<img width="100%" alt="Untitled (4)" src="https://user-images.githubusercontent.com/101745344/236817966-e12871a1-51ce-4e83-b5e8-2f22f3150432.png">

PWNME{3e851a6cc5525581446cad5694185b99}