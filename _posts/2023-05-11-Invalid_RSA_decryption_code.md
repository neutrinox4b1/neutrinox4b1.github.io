---
layout: post
title: Invalid_RSA_decryption_code
description: HackTheon Sejong 2023
tags:
categories: crypto
giscus_comments: false
date: 2023-05-11
---

> When d is the private key, RSA decryption using CRT is done as follows:
> 
> 1. mp = c^dp mod p (dp = d mod p-1)
> 
> 2. mq = c^dq mod q (dq = d mod q-1)
> 
> 3. qinvp = q^(-1) mod p
> 
> 4. s = (mp-mq) mod p
> 
> 5. m = mq+s*qinvp*q
> 
> Unfortunately, I implemented the decryption code incorrectly. In Step 2, mq had to be coded as pow(c, self.dq, self.q), but I implemented this part as mq = pow(c, self.dq, self.p). Can you find the 1024-bit private key d?
> 
> NOTICE:
> 
> 1. Flag format should be HACKTHEON{...}.
> 
> 2. To capture the right flag in this challenge, put private key d in hexadecimal representation and lower case of alphabet like HACKTHEON{c4ca4238a0b923820dcc509a6f75849b}.
> 
> 3. Remove "0x" notation also.
> 
> Format:*HACKTHEON{Flag}*
> 

```python
from Crypto.Util.number import getPrime, bytes_to_long, inverse
from math import gcd

class RSA:
    def __init__(self):
        self.p = getPrime(512)
        self.q = getPrime(512)
        self.n = self.p * self.q
        self.phin = (self.p - 1) * (self.q - 1)
        self.e = 65537
        assert gcd(self.e, self.phin) == 1
        self.d = inverse(self.e, self.phin)
        self.dp = self.d % (self.p-1)
        self.dq = self.d % (self.q-1)
        self.qinvp = inverse(self.q, self.p)

    def print_public_key(self):
        with open("modulus.txt", "w") as f:
            f.write(f"{self.n}")
        with open("exponent.txt", "w") as f:
            f.write(f"{self.e}")

    def decrypt(self, c):
        mp = pow(c, self.dp, self.p)
        mq = pow(c, self.dq, self.p)    # mq must be pow(c, self.dq, self.q)
        s = (mp-mq)%self.p
        return mq+s*self.qinvp*self.q

rsa = RSA()
c = 401578470156104860340584014730570245702458205483572045
m = rsa.decrypt(c)

rsa.print_public_key()
with open("message.txt", "w") as f:
    f.write(f"{m}")
```

주어진 값은 n, c, e 값이고 우리가 구해야 하는 값은 비밀 키 d이다.

저번에 풀었던 비슷한 문제로 시그니처에 대한 fault를 가지고 q를 복호화하는 문제가 있다.

[https://neutrinox4b1.github.io/blog/2023/rsalab_2022_second/](https://neutrinox4b1.github.io/blog/2023/rsalab_2022_second/) 에서 chal8이다.


[https://crypto.stackexchange.com/questions/63710/fault-attack-on-rsa-crt](https://crypto.stackexchange.com/questions/63710/fault-attack-on-rsa-crt)

관련 공격으로 fault attack on RSA CRT 인데

이 공격에서 잘 만들어진 m 값에서 잘못된 m을 뺀 값과 n을 gcd하면 p를 구할 수 있다.

이 점에서 착안하여 우리는 암호문을 가지고 비슷한 작업을 수행할 수 있다.

```python
from Crypto.Util.number import *
from math import gcd

with open('./modulus.txt', 'r') as f:
    n = int(f.read())

with open('./message.txt', 'r') as f:
    m = int(f.read())
e = 65537
c = 401578470156104860340584014730570245702458205483572045

# print('n:', n)
# print('m:', m)
# print('c:', c)
# print('e:', e)

p = gcd(pow(m, e, n) - c, n)
q = n // p
phi = (p-1) * (q-1)
d = pow(e, -1, phi)
print(hex(d)[2:])
```


<img width="100%" alt="Untitled (5)" src="https://github.com/neutrinox4b1/neutrinox4b1.github.io/assets/101745344/ef22f5f8-24a5-46d1-8066-0c37f9d36e48">

flag : `HACKTHEON{53ee69a7d70eeca57d92aa2d766aaed75547bef3b4311875ce9f60e14f55a7792cfd0b75b4e000881f94f8ad9418e8970f4e9819ee1d4b0d40642ec482ca43a710910384b443da2a82124e608863c240c5b48de9e3b274ddf233a011cb786fef7e1fdd959c159401dfb91cd827514fc824c894015d123cff54eda9dee72388e5}`

[https://medium.com/asecuritysite-when-bob-met-alice/beware-of-rsa-fault-attacks-they-may-comprise-your-trust-infrastructure-cf61c57f5c28](https://medium.com/asecuritysite-when-bob-met-alice/beware-of-rsa-fault-attacks-they-may-comprise-your-trust-infrastructure-cf61c57f5c28)