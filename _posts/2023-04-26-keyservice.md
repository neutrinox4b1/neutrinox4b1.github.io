---
layout: post
title: keyservice
description: BSidesSF CTF 2023
tags:
categories: crypto
giscus_comments: false
date: 2023-04-22
---

## Background
---
> WienerCorp has a new keyservice that can generate specially optimized keys! All empolyees are expected to connect to the service and get a new key by COB Friday.
>
> nc [keyservice-96f72922.challenges.bsidessf.net](http://keyservice-96f72922.challenges.bsidessf.net/) 1031

<img width="100%" alt="Untitled (7)" src="https://user-images.githubusercontent.com/101745344/236091431-fdf6167d-a2a7-4920-8f0c-b1df70686b25.png">

<img width="100%" alt="Untitled (8)" src="https://user-images.githubusercontent.com/101745344/236091446-22d847e1-e7ae-4900-a1d8-b2cf165c7512.png">



> help types
> 
> 
> The keyservice has the ability to generate a "standard" key or a
> signing-optimized key.  If you are unsure which to pick, go with a
> standard key.
> 
> Standard keys are optimized for fast encryption and serve most users
> well. Signing-optimized keys are much slower for encryption but enable
> substantially faster signing.  Signing-optimized keys are still secure
> by ensuring your private exponent has 128 bits of entropy. The only
> drawback in that your resulting public exponent is much larger.
> 

listkeys

```plaintext
User  0. (Michael, Wiener)
Public modulus: 23688712016268069111957434004706792845435035134674317452941249047394388384339340162303677821524105079444156178402473729333292677634064574356352459213998507795947080043734519874960255696085079398956977662003566771135478670568954128746139542483672079261449854702431638345016117956154265558192465040609108240826200608326280057289262571839392713499567857386131701105291901630759932811453630053115111615168755998780337872709649064936703148839513147543106870694861880274622449224084646147285320440553817283657444086246729935317218167694390763475285940639359466202483878847327555490486804276371433398306839838788086263318989
Public exponent: 3865363620121948671835054378324102522041599582880477323765025997361013454635674378451127282582241194920119864895285625836542696455666170840678063690565104517339899367326128991616289651301409583513097518518355770101773568574774581490687703099425413899625998244463532711668250046230332751817055181424699901610714700152050001283276027181125456539460880944775054151332255736310896532881824031442395478210779709078179055058304830480140813706982963841220086313208832034019770661263713923706357303383444013251482711558202699801492808596246472330590588504570327062292884439468167837672629968578977422091184520103195463780465
```

이름이 Michael, Wiener인것을 보아 Wiener’s attack을 이용하는 거 같다.

public modulus는 n을 의미하고 public exponent는 공개키 e를 말하는거 같은데

<img width="100%" alt="Untitled (9)" src="https://user-images.githubusercontent.com/101745344/236092140-f7cbed95-5e79-4118-8303-f9dacc00d21d.png">

listmsgs를 보면 user0에 msg가 존재한다.

이 평문을 읽어들이면 flag가 있을 것만 같다.

<br>
<br>
<br>

## Solution
---
<img width="100%" alt="Untitled (10)" src="https://user-images.githubusercontent.com/101745344/236092229-2e6ed99a-3e66-4529-9d3e-cfc1331d4caf.png">


0번에 대한 readmsg를 하기 위해서 서명을 해야한다. 

wiener’s Attack은 e값이 매우 큰 경우 사용된다. 이 문제에서는 e와 n값이 대략 256bit 정도 되는 것 같다.

e가 클 경우 d가 작을 확률이 높고 이를 이용해서 공격이 가능하다. 구체적으로는 $$e > n^{3/2}$$ 이면 사용 불가능하고, $$d < {1/3}n^{1/4}$$ 일때 쉽게 d를 얻을 수 있다.

[https://github.com/pablocelayes/rsa-wiener-attack](https://github.com/pablocelayes/rsa-wiener-attack)


이미 wiener's Attack을 수행하는 코드가 있다.

위 모듈을 사용하는 익스 코드를 작성한다.

```python
from RSAwienerHacker import *

n = int(input('input n: ').strip())
e = int(input('input e: ').strip())

d = hack_RSA(e, n)

if d is None:
	print('failed')
else:
	print(d)
	h = int(input('input h for sign: ').strip())
	print('signed h:', pow(h, d, n))
```

d가 failed 되는 경우도 있는데 여러번 하다보면 된다.

근데 failed 되는 경우는 어떤 경우인지 모르겠다..? [블로그]()에 공부하여 게시하도록 하였다.


<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="https://user-images.githubusercontent.com/101745344/236094224-756765af-bdc1-4156-8c81-fd3d1f979f34.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="https://user-images.githubusercontent.com/101745344/236094361-c636d927-d53e-44dd-b3fe-9da5676f89b1.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

<img width="100%" alt="Untitled (3)" src="https://user-images.githubusercontent.com/101745344/236094502-715443d7-bb3c-4e2c-a736-19a4ea1dd875.png">