---
layout: post
title: "Wrong-Implementation (1)" 
description: 
tags:
categories: crypto
giscus_comments: false
date: 2023-06-10
---

## Background
---

```python
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad
from Crypto.Util.number import *
import time, random, sys

FLAG = "cce2023{cce_schedule_20230610_0900_2000!!}".encode()
Message = "Hello, Alice!. My flag is here.".encode()


def encrypt(P, K):
    aesCipher = AES.new(key=K, mode=AES.MODE_ECB)

    N = FLAG[:len(FLAG) // 2]
    #print('N:', N)
    Counter = hex(0x00)[2:].zfill(16).encode()
    #print('Counter:', Counter) #8byte
    keyStream = aesCipher.encrypt(pad(N + Counter, 16))
    #print('ks:',bytes_to_long(keyStream))

    return int.from_bytes(keyStream, byteorder='big') ^ int.from_bytes(P, byteorder='big')


if __name__ == '__main__':
    random.seed(time.time())

    # I looking forward to the secret Key, which is very very safety
    while True:
        K = f"{int(random.random() * 10000000)}".encode()
        K += "ABCDEFABC".encode() ## key = 7byte number + 9 byte ABCDEFABC

        if len(K) == 16:
            break
    
    try:
        print(f"My Message! : {hex(encrypt(P=Message, K=K))[2:]}")
        print(f"My FLAG! : {hex(encrypt(P=FLAG[len(FLAG) // 2:], K=K))[2:]}")
    finally:
        sys.exit()
```

중간 중간 print하는 부분은 문제를 풀기 위해서 추가한 부분으로 원래는 해당 부분들이 없었다.

<br>
<br>
<br>

## Solution
---

문제에서는 AES에서 CTR mode를 잘못 구현하였다. 그리하여 단순히 XOR 하는 암호가 되었고,

평문 "Hello, Alice!. My flag is here."을 알고 있는 상태이다.

이 상황에서 평문 메시지를 encrypt한 값을 얻으므로 keystream을 알아낼 수 있고 이를 통해 flag의 절반 하위 부분 복구가 가능하다.

상위 부분은 encrypt 함수에서 N으로 사용된다.따라서 Key를 구해야 한다.

keystream은 key를 이용하여 AES ECB를 거쳐 생성된 값이고 N은 그게 아니라 key로 암호화되기 전 값이기 때문이다.
다행히도 key는 $$10^7$$ 의 경우의 수를 가진다.

따라서 이는 브루트포싱 가능하다.

```python
from pwn import *
from Crypto.Util.number import *
from Crypto.Cipher import AES
from Crypto.Util.Padding import unpad

from tqdm import tqdm

p = remote('20.214.200.9', 18080)

p.recvuntil(b'Message! : ')
msg = int(p.recvline().strip().decode())

p.recvuntil(b'FLAG! : ')
flag = int(p.recvline().strip().decode())

Message = "Hello, Alice!. My flag is here.".encode()

ks = msg ^ bytes_to_long(Message)
flag = long_to_bytes(flag ^ ks)

ks = long_to_bytes(ks)

for i in tqdm(range(1000000, 9999999)):
	key = str(i).encode() + b'ABCDEFABC'
	cipher = AES.new(key, AES.MODE_ECB)
	fhag = cipher.decrypt(ks)
	if b'cce2023' in fhag:
		print(unpad(fhag, 16)[: -16] + flag)
		break
```

디스코드 후기를 보고 tqdm을 사용해보았다.
이런 모듈이 있는지 처음 알았다.

```python
from pwn import *
from Crypto.Util.number import *
from Crypto.Cipher import AES
from Crypto.Util.Padding import unpad

from multiprocessing import Process
from tqdm import tqdm

def f(pn):
	p = remote('20.214.200.9', 18080)

	p.recvuntil(b'Message! : ')
	msg = int(p.recvline().strip().decode())

	p.recvuntil(b'FLAG! : ')
	flag = int(p.recvline().strip().decode())

	Message = "Hello, Alice!. My flag is here.".encode()

	ks = msg ^ bytes_to_long(Message)
	flag = long_to_bytes(flag ^ ks)

	ks = long_to_bytes(ks)

	st = 10000000 // 10 * (pn+1)

	for i in tqdm(range(st, st+1000000)):
		key = str(i).encode() + b'ABCDEFABC'
		cipher = AES.new(key, AES.MODE_ECB)
		fhag = cipher.decrypt(ks)
		if b'cce2023' in fhag:
			print(unpad(fhag, 16)[: -16] + flag)

if __name__ == '__main__':
	procs = []
	for i in range(9):
		p = Process(target=f, args=(i, ))
		p.start()
		procs.append(p)

	for p in procs:
		p.join()
```

다른 방법으로 멀티 프로세싱을 사용하였다.
이러면 속도가 매우 빨라진다.

`cce2023{a80107b0ac3bc2980625bbcfa2f2958da}`