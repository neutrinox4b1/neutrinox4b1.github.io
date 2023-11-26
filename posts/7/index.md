# IncognitoCTF 2023

<!--more-->

# About
- Starts: 24 11 2023, 20:00 KST   
- Ends: 25 11 2023, 20:00 KST    
- On-line
- Format: Jeopardy

# Overview
no image
# Writeups
## Find the Location(crypto)
### Background

![image](https://github.com/neutrinox4b1/neutrinox4b1.github.io/assets/101745344/b6cc1fc5-26a8-424a-88ea-95e7755d1309)


ENC file
```
NDA3NzU5ODQwNzc1OTM0MDc3NTg4MzkzOTgzODQwNzc2MTIzOTM5ODQwNDA3NzU3NjM5Mzk4NTY0MDc3NjY0NDA3NzYxMTQwNzc2ODYzOTM5ODM4MzkzOTgxOTM5Mzk4MTIzOTM5ODcxMzkzOTg1NjM5Mzk4NDczOTM5ODE4NDA3NzU5NjQwNzc1OTM0MDc3NTkyMzkzOTg0MjQwNzc2ODg0MDc3NjgzMzkzOTg4MTM5Mzk4NzI0MDc3NTg0MzkzOTkzNDM5Mzk4OTM0MDc3NjQ3NDA3NzY1MDM5Mzk5NDIzOTM5ODQxNDA3NzY5MTM5Mzk3NzczOTM5ODUwNDA3NzY4MjM5Mzk5NDY0MDc3NjQ2MzkzOTg1ODQwNzc2NTYzOTM5OTM4MzkzOTc5MzQwNzc2NzM0MDc3NjY0NDA3NzY3MzQwNzc2NTQ0MDc3NjY5MzkzOTg2NzM5Mzk5NDY0MDc3NjY4MzkzOTc4MjM5Mzk3ODEzOTM5Nzc4NDA3NzY3ODM5Mzk5NTgzOTM5ODUxMzkzOTg2NjQwNzc2NjY0MDc3NjYzNDA3NzY0NjM5Mzk4OTA0MDc3NTc2MzkzOTkwODM5Mzk5MTEzOTM5ODQ0
```

encrypt.py
```python
import base64 as b64 
def encode (O0000OO00OO000O00 ):
    O0OOOOOO0O0O00O0O =list (O0000OO00OO000O00 .encode ())
    for OOO0000OOOO0O0O0O in range (len (O0OOOOOO0O0O00O0O )):
        O0OOOOOO0O0O00O0O [OOO0000OOOO0O0O0O ]=~(O0OOOOOO0O0O00O0O [OOO0000OOOO0O0O0O ]^~(OOO0000OOOO0O0O0O -0x123456 ))
        if O0OOOOOO0O0O00O0O [OOO0000OOOO0O0O0O ]%2 ==0 :
            O0OOOOOO0O0O00O0O [OOO0000OOOO0O0O0O ]=O0OOOOOO0O0O00O0O [OOO0000OOOO0O0O0O ]+0x22222 
        O0OOOOOO0O0O00O0O [OOO0000OOOO0O0O0O ]=O0OOOOOO0O0O00O0O [OOO0000OOOO0O0O0O ]^~(OOO0000OOOO0O0O0O +0x2C0C02 )
    OO00OO000OO0O0000 ="".join ([str (O00O000OOO00OOOOO )for O00O000OOO00OOOOO in O0OOOOOO0O0O00O0O ])
    OO00OO000OO0O0000 =OO00OO000OO0O0000 .encode ()
    OO00OO000OO0O0000 =b64 .b64encode (OO00OO000OO0O0000 ).decode ()
    print (f"Hashed code = {OO00OO000OO0O0000}")
    if OO00OO000OO0O0000 ==FLAG_HASH :
        return True 
    return False 
with open ("ENC.txt","r")as f :
    FLAG_HASH =f .read ()
while True :
    pw =input ("Flag is? : ")
    if encode (pw ):
        break 
    print ("Try Again!")
    print ("\n________________\n")
print (f"Correct! Submit {pw}")
```

encode 함수를 빼고? 일단. enc.txt를 열어서 read하고<br>무한반복으로 flag를 질문하여 pw를 입력받는다. 입력받은 문자열 pw를 encode한 값이 true이면 break하여 프로그램이 종료된다.

결과적으로 encode 함수에 어떤 값을 넣어야 true를 반환하는지가 관건이다.

gpt한테 해석을 맡긴다.

해석을 맡기는 와중에 생각으로 변수명을 난해하게 한 것 같다.

그냥 변수명만 치환해서 생각해보자.

```python
import base64

def encode(input_string):
    # 문자열을 바이트 배열로 변환
    byte_array = list(input_string.encode())

    # 각 바이트에 대해 복잡한 변환 수행
    for index in range(len(byte_array)):
        byte = byte_array[index]
        # 비트 반전과 XOR 연산 수행
        byte = ~(byte ^ ~(index - 0x123456))

        # 바이트가 짝수이면 특정 값 추가
        if byte % 2 == 0:
            byte += 0x22222

        # 다시 비트 반전과 XOR 연산 수행
        byte = byte ^ ~(index + 0x2C0C02)
        byte_array[index] = byte

    # 변환된 바이트 배열을 문자열로 변환 후 Base64 인코딩
    transformed_string = ''.join([str(byte) for byte in byte_array])
    encoded_string = base64.b64encode(transformed_string.encode()).decode()

    # 결과 출력
    print(f"Hashed code = {encoded_string}")

    # 결과 비교 및 반환
    return encoded_string == FLAG_HASH

# 파일에서 FLAG_HASH 읽기
with open("ENC.txt", "r") as file:
    FLAG_HASH = file.read()

# 사용자 입력 처리
while True:
    input_value = input("Flag is? : ")
    if encode(input_value):
        break
    print("Try Again!\n________________\n")

print(f"Correct! Submit {input_value}")

```

gpt가 해준거

### Solution

왜 계속 역연산이 안되나 싶었는데, flag를 받을 때 숫자로 해석했어야 했다. 이는 조금만 encrypt.py를 돌려보면 알 수 있는 부분이었는데..


exploit code


```python
import base64

with open('ENC.txt', 'r') as f:
    flag_hash = f.read()

flag_hash_string = base64.b64decode(flag_hash.encode()).decode()

flag_byte = [int(flag_hash_string[i : i+7]) for i in range(0, len(flag_hash_string), 7)]

print(flag_byte)


for index in range(len(flag_byte)):
    byte = flag_byte[index]
    byte = byte ^ ~(index + 0x2c0c02)

    if byte % 2 == 0:
        byte -= 0x22222

    byte = ~byte
    byte = (byte) ^ (~(index - 0x123456))

    flag_byte[index] = byte

flag = ''.join([chr(byte) for byte in flag_byte])
print(flag)
```


> INCO{yOu'l1_NEver_KNOw_Tha7_thE_pl4ce_Is_G4ngnArn_s747IoN_Exi7_12}

## Find my name! (1) (reversing)
### Background

![image](https://github.com/neutrinox4b1/neutrinox4b1.github.io/assets/101745344/27ff2341-a17c-4fb4-8e45-c9ba5cebcdb8)

![image](https://github.com/neutrinox4b1/neutrinox4b1.github.io/assets/101745344/cb87b8b1-4773-4c4b-b230-76650942462a)

static analysis<br>
amd64 PEfile

![image](https://github.com/neutrinox4b1/neutrinox4b1.github.io/assets/101745344/d9491b66-94de-4379-a9e3-569121db41f6)

dynamic analysis<br> Guess the number라는 문자열 찾기


![image](https://github.com/neutrinox4b1/neutrinox4b1.github.io/assets/101745344/0ff0d0af-bf1c-4e58-9069-a186f4b97b16)


![image](https://github.com/neutrinox4b1/neutrinox4b1.github.io/assets/101745344/5ac71b75-74f0-4eb9-b74e-19060e528dd8)

입력한 v13이 v12와 같아야 함.
랜덤이 하나의 플래그가 되는건 말이 안되는디;; Seed가 정해져 있어서 그런가?

![image](https://github.com/neutrinox4b1/neutrinox4b1.github.io/assets/101745344/75ead9a4-ec9d-4b8a-b8d9-b2fbf4a3bcff)

그냥 동적분석하면 되지 않나 싶었는데..

### Solution
> INCO{7716b2901c6ccf1b8ea936dbcf7cb14bd32b7e9c}

모르겠따.. 맞는지 틀린지라도 좀 알려줬으면 좋을듯.
동적분석으로 바로 풀리면 누구나 다 맞췄겠지 싶다.

## Random VS Random (crypto)
### Background

```python
#!/usr/bin/python3
import random
import os
from Crypto.Util.number import bytes_to_long
import signal

def timeout(a1, a2):
    print("BYE ~")
    exit(-1)

class User:
    def __init__(self, name, secret):
        self.key = self.getRandom()
        self.rng = random.Random(self.key) 
        self.name = name
        self.secret = bytes_to_long(secret)

    @staticmethod
    def getRandom(generator = None):
        if generator == None:
            return random.getrandbits(32) 
        else:
            return generator.getrandbits(32)
        
    @staticmethod
    def sum(a, b):
        res = 0
        for _ in range(32):
            res += (a & 1) * (b & 1)
            a >>= 1
            b >>= 1
        return res
    
    def reset(self):
        print(f'[{self.name}] Recent Key : {self.key}')
        self.key = self.getRandom()
        print(f'[{self.name}] Key Updated !! ')
        self.rng = random.Random(self.key)        

    def start(self):
        val = self.getRandom(self.rng)
        res = self.sum(val, self.secret)
        return res

if __name__ ==  "__main__":
    signal.signal(signal.SIGALRM, timeout)
    signal.alarm(20)
    random.seed(os.urandom(32))
    Alice = User("Alice", os.urandom(8))
    Bob = User("Bob", os.urandom(8))
    Point = 0
    while(1):
        try:
            menu = int(input("Menu > "))
        except:
            print("Please input the int value")
            continue
        if menu == -1:
            print("BYE ~")
            exit(-1)
        if menu == 0:
            Alice.reset()
        elif menu == 1:
            Bob.reset()
        elif menu == 2:
            # fight
            res = (Alice.start() - Bob.start()) % 3
            if res == 1:
                print("Oh, Alice WIN !!")
            elif res == 2:
                print("Oh, Bob WIN :(")
            elif res == 0:
                print("TIE")

        elif menu == 3:
            res = (Alice.start() - Bob.start()) % 3
            if res == 1:
                Point += 1
                print(f'Current Point : {Point}')
            else:
                print("Nah...")
                exit(-1)
        if Point == 100:
            with open('/home/ctf/flag.txt','rb') as f:
                print(f.read())
        
```

chall.py이고

User는 name과 secret을 받는다. rng는 random nubmer generator인거 같은데, random모듈을 사용한다. secret에서는 byte_to_long으로 정수 형태로 넣는다.

getRandom 메소드는 generator가 none면 random모듈의 getrandbits로 32bit를 받고. 아니면 generator로 32bit를 받는다.

key는 random을 사용하므로 random을 key로 하여 난수 생성기를 사용한다.

sum은 두 숫자의 각 비트를 곱한 결과의 합을 계산한다. 즉 and를 한 결과의 1의 갯수이다.

reset은 사용자 키를 재설정하고 rng도 재설정된다.

start는 rng를 사용하여 값을 생성하고 이를 secre과 sum의 인자로 하여 그 결과를 반환한다.

point가 100이 되면 플래그를 획득한다. 이는 20초 이내로 이루어져야 한다.

메뉴가 0이면 alice키를 재설정, 1이면 bob키를 재설정한다.

2면 alice의 start값에서 bob의 start값을 빼고 3으로 나눈 나머지를 취한다.<br>
나머지가 1이면 alice가 이기고 2면 bob가 이기고 0이면 비긴다. 

메뉴가 3이면 아까처럼 Alic start에서 bob start를 빼는데 이때 res%3가 1이면 point를 증가시키고 아니면 종료한다.<br>
포인트를 증가시킬 수단은 이것 뿐이다.

즉, 100번 연속으로 Alice가 이겨야 한다.

<br>

그러기 위해서는 start값을 예측 == sum을  예측 == secret과 getRandom값을 알아야 함(secret은 예측 불가) == rng 예측 == key 예측 => 초기 getrandbits를 알면 key를 아는 것 => random.seed는 한 프로그램 내에서 고정.

key나 rng를 어떻게 예측하는가?

reset에서 출력되는 값은 Recent Key이다.<br>어? key값이 getRandom에서 항상 random.getrandbits로 고정되어있다. rng에서 다시 생성되는 줄 알았는데 이러면 예측이 쉬워진다.

그럼 Recent Key로 key를 예측하고. 이를 통해 예측된 rng에서 예측된 val의 정확한 값을 알아낼 수 있음.<br>문제는 res의 예측인데. secret은 고정이므로, 초반 val 데이터를 가지고 항상 같은 결과를 내는 val의 1 갯수를 알 수 있을 것임 => 이는 menu 2번으로 확인 가능함.

남은 시간에서는 해당 조건에 맞을 때마다 menu 3을 사용하여 point를 100을 채우는 것임.


![image](https://github.com/neutrinox4b1/neutrinox4b1.github.io/assets/101745344/cab72285-837d-47d6-9b42-030983bc8db2)


val예측까지 성공했으나, secret에서 막힘..

alice가 이기는 패턴만 모아서 할 수는 없나..?


```python
from pwn import *
from  randcrack import RandCrack
from tqdm import tqdm
import random

def menu(cmd):
    p.sendlineafter(b'> ', str(cmd).encode())
    if cmd == 0:
        p.recvuntil(b'Recent Key : ')
        pdrc_alice.submit(int(p.recvline()))
        p.recvline()

    elif cmd == 1:
        p.recvuntil(b'Recent Key : ')
        pdrc_bob.submit(int(p.recvline()))
        p.recvline()

    
# p = remote('13.125.251.104', 12010)
p = process('./chall.py')

pdrc_alice = RandCrack()
pdrc_bob = RandCrack()

for i in tqdm(range(624)):
    menu(0)
for i in tqdm(range(624)):
    menu(1)

alice_key = pdrc_alice.predict_getrandbits(32)
bob_key = pdrc_bob.predict_getrandbits(32)
# print('alice_key:',alice_key)
# print('bob_key:', bob_key)
pattern = []

alice_rng = random.Random(alice_key)
bob_rng = random.Random(bob_key)

for i in tqdm(range(10000)):
    alice_val = alice_rng.getrandbits(32)
    bob_val = bob_rng.getrandbits(32)
    # print('alice_val:', alice_val)
    # print('bob_val:', bob_val)
    menu(2)
    if b'Alice' in p.recvline():
        pattern.append((alice_val, bob_val))
while True:
    alice_val = alice_rng.getrandbits(32)
    alice_val = bob_rng.getrandbits(32)
    if (alice_val, bob_val) in pattern:
        menu(3)
        print(p.recvline())
        # if b'100' in p.recvline():
        #     break
    else:
        menu(2)
        print(p.recvline())
    

p.interactive()
```

현재까지 작성한 코드..

여기까지가 끝인가보오.

