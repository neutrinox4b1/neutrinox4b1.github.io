---
layout: post
title: de-compressed
description: DAM CTF 2023
tags:
categories: misc
giscus_comments: false
date: 2023-04-08
---
## Background
---
As an elite cyber security expert, you’ve been tasked with uncovering the secrets hidden within a message intercepted from a notorious spy.

We suspect there may be more to this message than meets the eye. Can you use your skills in steganography to uncover whatever else might be hiding?

The fate of national security is in your hands.

<img width="102" alt="image" src="https://user-images.githubusercontent.com/101745344/232465818-cbfae6d7-0845-4ac1-954c-c0bdfdad96d9.png">

message라는 zip 형식의 파일이 주어진다.

여기서 플래그를 찾아내는 문제이다.
<br><br><br>

## Solution
---
zip 파일을 분석해보면 secret.txt라는게 들어있는게 보인다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="https://user-images.githubusercontent.com/101745344/232466385-73e9e7a3-c577-428c-ae3a-f82f4655191b.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

cyberchef를 통해서 해당 zip 파일을 upload 하면, extract file을 사용해서 secrte.txt가 있는 zip 파일을 얻어낼 수 있다.

<img width="100%" alt="image" src="https://user-images.githubusercontent.com/101745344/232466816-4840a608-023a-4b2e-aa55-bba4acbb3049.png">

secret.txt는 다음과 같다. 평범해보이지만 여기서 스테가노그래피가 적용되었음이 틀림없다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="https://user-images.githubusercontent.com/101745344/232467172-61ae1a97-295c-4efa-9e58-7b5dd5b6dd84.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>


실제로 secret.txt에는 이상한 문자들이 가득 차 있었다. 여기까지 알아내었으나 이게 도대체 어떤 방식으로 flag가 숨겨져있는 지 알지 못했다.

write up을 보니 [unicode steganography](https://330k.github.io/misc_tools/unicode_steganography.html) 이었다. 여기에 입력을 하고 해독을 진행했다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="https://user-images.githubusercontent.com/101745344/232467885-8e43c754-50d8-449b-b793-5175f19c1c24.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

짜잔.

> dam{t1m3_t0_kick_b4ck_4nd_r3l4x}

unicode steganography에 대해서 처음 알게 되었다.