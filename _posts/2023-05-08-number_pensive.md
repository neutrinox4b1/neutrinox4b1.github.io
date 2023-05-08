---
layout: post
title: "Numbers : pensive :"
description: TAMU CTF 2023 
tags:
categories: crypto
giscus_comments: false
date: 2023-04-29
---

> Author: `nhwn`
> 
> 
> It wouldn't be a real CTF without some contrived RSA challenge, right?
> 

## [0x00] 분석
---

<img width="100%" alt="Untitled (4)" src="https://user-images.githubusercontent.com/101745344/236756226-02d01da2-a279-4954-b22c-614b581b8117.png">

서버에 연결하면 다음과 같이 n이 주어지고 e가 주어진다.

우리가 e를 입력하면 그에 해당하는 d를 준다.

65537을 입력하면 break시킨다.

```python
from Crypto.Util.number import getPrime
from Crypto.Random.random import getrandbits, randint
from pathlib import Path
from math import gcd

flag = Path("flag.txt").read_text()

e = 65537

while True:
    p = getPrime(1024)
    q = getPrime(1024)
    n = p * q
    phi = (p - 1) * (q - 1)
    if gcd(e, phi) == 1:
        break

print(f"n = {n}")
print(f"e = {e}")

while True:
    chosen_e = int(input("Give me an `e`, and I'll give you a `d`: "))
    if chosen_e == e:
        print("Nice try!")
        break
    try:
        print(pow(chosen_e, -1, phi))
    except:
        print("That's not invertible :pensive:")
        continue
    m = getrandbits(1024)
    c = pow(m, e, n)
    print("If you can decrypt this, I'll give you a flag!")
    print(c)
    ans = int(input("Your answer: "))
    if ans == m:
        print(flag)
        break
    else:
        print("Numbers, am I right :pensive:")
```

뭔가 우리한테 e를 입력하게 해주고 d를 주는게 이상하다.

<br>
<br>
<br>

## [0x01] 풀이
---

저번에 비슷한 문제를 풀어보기도? 한거 같아서

e에 -1을 넣어봤다.

그러면 $(-1)^{-1} \equiv (\phi - 1)^{-1} \bmod \phi$ 이다. 그럼 여기에 +1을 하면 $\phi$를 구할 수 있다.

그럼 비밀키를 계산할 수 있고 복호화 가능하다.

```python
from pwn import *

p = remote("tamuctf.com", 443, ssl=True, sni="numbers-pensive")

p.recvuntil(b'n = ')
n = int(p.recvline())
print('n:', n)
p.recvuntil(b'd`: ')
p.sendline(b'-1')
phi = int(p.recvline())
phi += 1

print('phi:', phi)
e = 65537
d = pow(e, -1, phi)

p.recvuntil(b'flag!\n')
c = int(p.recvline())

ans = pow(c, d, n)
p.recv()
p.sendline(str(ans).encode())

p.interactive()
```