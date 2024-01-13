# FPGA 기반 임베디드 시스템의 사이버 보안 위협 동향 분석

<!--more-->

{{< admonition type=info open=true >}}
제목: FPGA 기반 임베디드 시스템의 사이버 보안 위협 동향 분석

저자명: 정세연, 조민기, 황은비, 권태경

발행일자: 2020.08.

논문 종류: 국내학술저널

컨퍼런스명/저널명: Journal of the KNST
{{< /admonition >}}

## 연구 배경 및 필요성

FPGA(Field programmable gate array)는 하드웨어 제조사, 서드파티 IP 제공자, 위탁업체 등에 의해 하드웨어 설계가 이루어지므로 HT(Hardware Troijan)을 삽입할 가능성이 존재함.

따라서 본 논문에서는 FPGA를 대상으로 이루어질 수 있는 보안 위협과 FPGA 기반 임베디드 시스템의 위협 동향을 분석하였다.

## 기존 FPGA 기반 임베디드 시스템 생태계

기존 FPGA 생태계는
1. 개발/생산 단계
2. 설치 단계
3. 운용 관리 단계

로 나뉘며, 펌웨어 파일을 비트스트림이라고 지칭한다.

이는 개발자는 HDL(Hardware Description Language)로 회로를 설계, 합성(synthesis), 과정, 그리고 배치 및 배선 등을 수행하여 비트스트림 파일로 변환한다.

![image](https://github.com/neutrinox4b1/neutrinox4b1.github.io/assets/101745344/102bd4ee-2e83-4b03-8b7a-51343686bf9f)

### DPR(Dynamic partial reconfiguration)

FPGA에서는 펌웨어 일부만 수정 가능하다는 장점이 있다.

![image](https://github.com/neutrinox4b1/neutrinox4b1.github.io/assets/101745344/82a77ddf-f48c-4b18-9f2d-c4040729c203)

이를 위해서 위와 같은 구조를 가진다.

여러 VP(virtual portion)으로 나뉘는데 각각의 VP는 다른 VP의 운영에 영향을 주지 않고 독립적으로 작동한다.

따라서 런타임에 다른 기능으로 재구성이 가능한데, 이를 DPR이라고 한다.

## FPGA 기반 임베디드 시스템의 위협

논문에서는 FPGA 라이프사이클의 일반적인 예를 고려하여 나타낸 FPGA 위협 모델을 제시한다.

![image](https://github.com/neutrinox4b1/neutrinox4b1.github.io/assets/101745344/397c9883-8b63-4361-93c6-db15c8a9d305)


### 개발/생산 단계에서의 사이버 보안 위협

![image](https://github.com/neutrinox4b1/neutrinox4b1.github.io/assets/101745344/06f4fee4-ad59-4d34-b781-aea32c321091)

1. 신뢰되지 않은 설계 도구
2. 신뢰되지 않은 개발자
3. 신뢰되지 않은 3PIP 제공자:<br>
    개발상의 편의를 위해 사용하는 서드파티에서 HT를 탑재
4. 신뢰되지 않은 SoC 통합자


### FPGA 설치 단계에서의 사이버 보안 위협

![image](https://github.com/neutrinox4b1/neutrinox4b1.github.io/assets/101745344/1cb2240d-59ef-4f3f-84c7-99481401542a)

부채널 공격으로 인한 사이버 보안 위협을 제시한다.

FPGA에서는 전원이 들어올 때 비트스트림에 저장되어있는 프로그래밍된 회로 설정을 불러와서 회로를 구성한다.

내부 비휘발성 메모리가 없으면 외부에서 비트스트림을 불러오는데, 이때 비트스트림 정보 유출을 막기 위해 암호화를 사용.

회로 정보를 분석하고 HT 삽입을 위해 부채널 공격으로 복호화 시도 가능.


- 2011년 [Moaradi 등의 연구](https://dl.acm.org/doi/abs/10.1145/2046707.2046722)에서 전력분석방법으로 비밀키 추출에 활용가능한 것을 보여주었다.

암호화키를 추출한 후 비트스트림을 복호화하여 HT를 삽입 가능할 것이다.

### FPGA 운용/관리단계에서의 사이버 보안 위협

![image](https://github.com/neutrinox4b1/neutrinox4b1.github.io/assets/101745344/561acda8-b4a6-46d9-9929-3afd4b9441f6)

![image](https://github.com/neutrinox4b1/neutrinox4b1.github.io/assets/101745344/6be7ba60-4780-4177-a101-a05fc7e0c4d8)

원격 DRP기능으로 인한 사이버 위협이 발생할 것이다.

2017년 [Johnson 등의 연구](https://www.sciencedirect.com/science/article/pii/S0141933116303970?casa_token=_wvKVyt69v4AAAAA:l9nf7E5i8sNBSdgyEtgYSADKdZ-lqYsosBMmKnLpmwSILHnNd4besB_kqrc_BrA0RurrSRu0hjc)에서는 비트스트림 수정 및 재구성 기능으로 HT 삽입이 가능하다는 것을 제안함.

이외에도 사전 DPR기능을 사용하여 트리거 가능하도록 HT를 탑재한 경우, 원격으로 HT를 동작시켜 피해를 유발할 가능이 존재한다. => 트리거 시 오버헤드를 줄이고 실용적이며, HT를 사전에 탐지하기 어렵게 한다.

## 결론

FPGA는 현재 금융, 방위, 항공 우주 등 많은 곳어세 사용되고 있으며, 보편화되어가는 만큼 보안에 대해서도 관심을 가져야 한다.

설명한 보안 위협 외에도 다양한 보안 위협이 있을 것이며 이러한 보안 위협을 막기 위해서<br>
사용중인 기능에 대한 분석을 통해, 필요한 기능만 사용하고 HT탐지를 위한 활발한 연구가 진행되어야 한다.
