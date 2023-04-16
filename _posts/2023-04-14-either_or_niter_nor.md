---
layout: post
title: Either or Niter nor
description: RITSEC CTF 2023
tags:
categories: crypto
giscus_comments: false
date: 2023-04-01
---

##  Background
---

```python
! /usr/bin/env python

flag = "XXXXXXXXXXXXXXXXXXXXX"
enc_flag = [91,241,101,166,85,192,87,188,110,164,99,152,98,252,34,152,117,164,99,162,107]

key = [0, 0, 0, 0]
KEY_LEN = 4

# Encrypt the flag
for idx, c in enumerate(flag):
    enc_flag = ord(c) ^ key[idx % len(key)]
```

enc_flag 부분이 python code가 잘못되어있는데..

암튼

문제에서 FLAG 형식이 MetaCTF{}라고 하여서 원문을 알고 있기 때문에

XOR의 self-inverse 속성을 이용한다.
<br>
<br>
<br>
## Solution
---

```python
#! /usr/bin/env python

flag = "XXXXXXXXXXXXXXXXXXXXX"
enc_flag = [91,241,101,166,85,192,87,188,110,164,99,152,98,252,34,152,117,164,99,162,107]

key = [22, 148, 17, 199]
KEY_LEN = 4

print(enc_flag)
# Encrypt the flag
#for idx, c in enumerate(flag):
#    enc_flag = ord(c) ^ key[idx % len(key)]
res = ''
for idx, c in enumerate(enc_flag):
	res += chr(c ^ key[idx % KEY_LEN])

print(res)

#print(enc_flag[0] ^ ord('M'))
#print(enc_flag[1] ^ ord('e'))
#print(enc_flag[2] ^ ord('t'))
#print(enc_flag[3] ^ ord('a'))
```

Meta를 xor한 값을 출력하고 키에 넣어서 복호화를 시도한다.

> MetaCTF{x0r_th3_c0re}