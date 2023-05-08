---
layout: post
title: Gib me Initials of Victor
description: PwnMe 2023 - 8bits
tags:
categories: crypto
giscus_comments: false
date: 2023-05-06
---

## Background

---

> Imagine making an encryption system.
That's what someone did...
> 
> 
> Find a way to recover the flag.
> 

### server.py

```python
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad
import os

FLAG = "PWNME{redacted}"
KEY = os.urandom(16)

def encrypt_flag():
    iv = os.urandom(16)
    cipher = AES.new(KEY, AES.MODE_CBC, iv)
    encrypted = cipher.encrypt(pad(FLAG.encode(), 16))
    signature = [hex(a ^ b)[2:].zfill(2) for a, b in zip(iv, KEY[::-1])]
    signature = "".join(signature)
    ciphertext = iv.hex()[4:] + encrypted.hex() + signature
    return {"ciphertext": ciphertext}
```

### output.txt

```
{"ciphertext":"ca92b5919084c02658a2e3d57ee3a37b8ab32e581d2615359cc7858e966090d328df2be8b5ad889561d9abce6f961fe03a95a2051e882db2373b33b5a43a1c13ff62af7f835df3cc1ccc64571e74f8b84587c727d56c4d37983e7b80ce3c"}
```

server.py에서  AES를 사용한다.

iv, key 모두 랜덤이고, encrypted에서는 flag를 AES로 암호화한다.

signature는 key를 역순으로 한 것과 iv를 원소마다 xor하여 만든다.

iv의 하위 14byte와, 암호문, 시그니처를 더해서 ciphertext를 만든다.

따라서 key의 14byte까지는 xor하여 되돌릴 수 있다.

<br>
<br>
<br>

## Solution

---

앞 2byte는 bruteforce한다

```python
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad
from Crypto.Util.number import long_to_bytes, bytes_to_long

ciphertext = bytes.fromhex( 'a37b8ab32e581d2615359cc7858e966090d328df2be8b5ad889561d9abce6f961fe03a95a2051e882db2373b33b5a43a1c13ff62af7f835df3cc1ccc64571e74')
iv14 = bytes.fromhex('ca92b5919084c02658a2e3d57ee3')
sign = bytes.fromhex('f8b84587c727d56c4d37983e7b80ce3c')

for i in range(0x0100, 0xffff + 1):
    iv = long_to_bytes(i) + iv14
    # print(iv)
    key = b''
    for a, b in zip(iv, sign):
        key += long_to_bytes(a ^ b)
    key = key[::-1]
    
    cipher = AES.new(key, AES.MODE_CBC, iv)
    flag = cipher.decrypt(ciphertext)
    try:
        unpad(flag, 16)
        flag = flag.decode()
        if 'PWNME' in flag:
            print(flag)
    except:
        print('', end='')
```

<img width="100%" alt="Untitled (8)" src="https://user-images.githubusercontent.com/101745344/236761012-dd734dc2-3dbf-434f-baed-56c2d3df7a64.png">

PWNME{1t_1s_d4ng3r0us_wh3n_t0_m4ny_1nf0_4r3_g1v3n}