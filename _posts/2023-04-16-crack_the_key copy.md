---
layout: post
title: 
description: DAM CTF 2023
tags:
categories: crypto
giscus_comments: false
date: 2023-04-08
---

##  Background
---
> I have used an RSA key to encrypt the flag. Can you find a way to reconstruct the private key using the public key? (Check out factordb.com to help with brute forcing)

```python
#!/usr/bin/env python3
from cryptography.hazmat.primitives.asymmetric import rsa, padding
from cryptography.hazmat.primitives import serialization
from cryptography.hazmat.backends import default_backend
from base64 import b64encode, b64decode
from flag import FLAG

# public key file name
PUB_FILE = 'pub.pem'
FLAG_FILE = 'flag.enc'


# convert key to pem format
def get_pem(key:rsa.RSAPrivateKey|rsa.RSAPublicKey):
    # check the type of key, and create pem is respective format
    if isinstance(key, rsa.RSAPublicKey):
        pem = key.public_bytes(encoding=serialization.Encoding.PEM, format=serialization.PublicFormat.SubjectPublicKeyInfo)
    else:
        pem = key.private_bytes(encoding=serialization.Encoding.PEM, format=serialization.PrivateFormat.PKCS8, encryption_algorithm=serialization.NoEncryption())

    # return the key in pem format
    return pem


# loads in a public key that is saved in PEM format
def load_public_key():
    with open(PUB_FILE, 'rb') as pubf:
        pubkey = serialization.load_pem_public_key(pubf.read(), backend=default_backend())
    return pubkey


# encrypts a plaintext with the provided public key and returns the ciphertext encoded in base 64
def encrypt(pubkey:rsa.RSAPublicKey, ptxt:str) -> str:
    # encrypt the flag using the public key
    ctxt = pubkey.encrypt(ptxt.encode(), padding.PKCS1v15())

    # return the encrypted flag in base 64
    return b64encode(ctxt).decode()


# decrypts a ciphertext (encoded to base 64) with the provided private key and returns the decrypted string
def decrypt(privkey:rsa.RSAPrivateKey, ctxt:bytes) -> str:
    # decode the ciphertext from base 64 and encrypt
    ptxt = privkey.decrypt(b64decode(ctxt), padding.PKCS1v15())

    # return the decrypted string
    return ptxt.decode()


if __name__ == '__main__':
    # Load in the key from PEM file
    pub_key = load_public_key()
    print(pub_key)

    # save public key in PEM format to be printed out
    pub_key_pem = get_pem(pub_key).decode()

    # encrypt the flag using the public key
    enc_flag = encrypt(pub_key, FLAG)

    # write encrypted flag to file
    with open('flag.enc', 'w') as f:
        f.write(enc_flag)
```
문제 파일은 다음과 같다.
<br>
<br>
<br>

## Solution
---
pub.pem을 열어보니 키의 길이가 그렇게 길지가 않았다. 문제 내용을 통해서 충분히 소인수분해 가능하리라 생각된다. pem파일을 어떻게 다루는지 몰랐지만 super_securt_rsa.py 파일을 통해서 사용법을 익히고

cryptography.hazmat.primitives 모듈에 있는 함수들을 사용하여 직렬화 된 키를 역직렬화하고 n과 e의 값을 알아낸다

factordb에서 n을 소인수분해하여 p와 q를 알아내고 phi값을 구한다.

phi값을 통해 비밀키 d를 계산하고, flag.enc 파일을 비밀키로 복호화한다.

```python
from cryptography.hazmat.primitives import serialization
from cryptography.hazmat.primitives.asymmetric import rsa, padding
from cryptography.hazmat.backends import default_backend
from base64 import b64decode, b64encode
from sympy.ntheory.factor_ import totient
from Crypto.Util.number import *

PUB_FILE = 'pub.pem'
FLAG_FILE = 'flag.enc'

with open(PUB_FILE, 'rb') as pubf:
    pub_key = serialization.load_pem_public_key(pubf.read(), backend=default_backend())
n = pub_key.public_numbers().n
e = pub_key.public_numbers().e

p = 106824314365456746562761668584927045312727977773444260463553547734415788806571
q = 109380489566403719014973591337211389488057388775161611283670009403393352513149

phi = (p-1) * (q-1)
d = pow(e, -1, phi)
with open(FLAG_FILE, 'r') as ff:
    enc_flag = ff.read()
flag = pow(bytes_to_long(b64decode(enc_flag)), d, n)
print(long_to_bytes(flag))
```
<img width="100%" alt="Untitled" src="https://user-images.githubusercontent.com/101745344/231911605-dc628115-11bc-4db2-a06e-4f0bd6a57c9d.png">