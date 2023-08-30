# RITSEC CTF 2023 - 110th

<!--more-->

# About
- Starts: 토요일, 01 4 2023, 01:00 KST   
- Ends: 월요일, 03 4 2023, 01:00 KST    
- On-line
- Format: Jeopardy
- Future weight: 25.44    
- Rating weight: 30.62    

<br>

# Overview
{{<image width="75%" alt="image" src="https://user-images.githubusercontent.com/101745344/232290343-9c5f04bd-1488-472c-b281-ad38e6916d28.png">}}
{{<image width="75%" alt="image" src="https://user-images.githubusercontent.com/101745344/232303426-d503e5cf-974d-4967-9875-aec13ea19820.png">}}

<br>

# Writeups
## Either or Nither nor (crypto)
### Background

```python
#! /usr/bin/env python

flag = "XXXXXXXXXXXXXXXXXXXXX"
enc_flag = [91,241,101,166,85,192,87,188,110,164,99,152,98,252,34,152,117,164,99,162,107]

key = [0, 0, 0, 0]
KEY_LEN = 4

# Encrypt the flag
for idx, c in enumerate(flag):
    enc_flag = ord(c) ^ key[idx % len(key)]
```

The python code in the enc_flag part is wrong..

Anyway

In the problem, because the FLAG format is MetaCTF{}, we know the original

using the self-inverse property of XOR.

### Solution
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
Meta is outputted with xor value and is put in the key to attempt decoding.

> MetaCTF{x0r_th3_c0re}

<br>

## A Fine Cipher (crypto)
### Background
A ciphertext is given.
```
JSNRZHIVJUCVIVFCVYBMVBDRZCXRIVBINCORBCSFHCBINOCRMHBD
```

### Solution
[https://www.dcode.fr/cipher-identifier](https://www.dcode.fr/cipher-identifier)

I thought there might be a hint in the title, so I thought of Affine Cipher. (recently unwrapped from Incognito CTF)

> RS{IFYOUAREINTERESTEDCHECKOUTMORECRYTPOCTFSATCRYPTOHACK}

<br>

## Weird (steg)
### Background
This file was supposed to contain a secret message but it looks like just a blank page. Something weird is going on here.

<br>

### Solution
It was just the beginning, so I watched it thinking it would be released if I turned stegsolve.

It was on blue plane zero.

> RS{Th4t5_w4cky_m4n}

<br>

## Turtle (steg)
### Background

On a frame-by-frame basis, you can see a dot on the back of the turtle like a Morse code.
<br>

### Solution

At first, I thought it was Morse code,

There was just a flag in the 40th frame.
> RS{G00D_3Y3_&H4PPY_TUR713}

<br>

<br>

## Chandi bot 1, 2, 4, 5 (misc)
### 1. Solution

![ritsec2023_4](https://user-images.githubusercontent.com/101745344/231845298-29aeaf6d-04ee-4b37-a226-766de025d880.png)


> RS{QUANTUM_RESISTANT_ENCRYPTION}

<br>

### 2. Solution
lost image

> RS{HMMM_WHAT_ARE_YOU_LOOKING_AT}

<br>

### 4. Background
{{<image width="418" alt="Untitled" src="https://user-images.githubusercontent.com/101745344/231843579-630a035a-a50a-4190-9a67-7642e4552b7b.png">}} 

If you say /balance, it outputs the current point.

/rps is to play rock paper scissors with Chandi, but I always lose.

When playing rock-paper-scissors, points are hung, and points are input as integers.

<br>

### 4. Solution
---

It seems that the point should not be zero and should be less than or equal to your point.

If you specify a point as a negative number and lose it, you will earn the point.

{{<image width="472" alt="Untitled (1)" src="https://user-images.githubusercontent.com/101745344/231843564-a70b4d63-62c8-468e-9ed3-6f098318850f.png">}}

{{<image width="388" alt="Untitled (2)" src="https://user-images.githubusercontent.com/101745344/231843571-797f1e5a-f230-4252-8522-f9b02fda3576.png">}}

{{<image width="567" alt="Untitled (3)" src="https://user-images.githubusercontent.com/101745344/231843575-f0bb5c16-457d-4f0e-a776-8f85d44fde03.png">}}

> RS{TWO_NEGATIVES_DO_MAKE_A_POSITIVE}

<br>

### 5. Solution
---

{{<image width="495" alt="Untitled (4)" src="https://user-images.githubusercontent.com/101745344/231844470-cdbbbebe-cc80-4e50-bbd4-6a95885acbcc.png">}}

brute force..

> RS{TRIVIAL_TRIVIA_TRIUMPHS}

<br>

Other than this, I think it would be good to check the [writeup](https://gitlab.ritsec.cloud/competitions/ctf-2023-public). Steg et al..  
