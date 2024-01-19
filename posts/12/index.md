# TPM을 활용한 임베디드 시스템 환경의 보안 부팅 구현

<!--more-->

{{< admonition type=info open=true >}}
제목: TPM을 활용한 임베디드 시스템 환경의 보안 부팅 구현
저자명: 김진우, 이상길, 고재용, 이철훈
발행일자: 2019.10.

논문 종류: 국내학술저널
컨퍼런스명/저널명: 한국정보보호학호 논문지
{{< /admonition >}}

## 서론

### 연구의 배경 / 필요성
휴대용 기기의 보급이 증가하고 IoT 기기 또한 널리 이용됨에 따라 시장이 빠른 추세로 발전.

IC-Insights에 따르면 전세계적으로 MCU가 2018년 가을을 기준으로 누적 300억개 이상 출하.
실례도 있음. KT olleh 커스텀 펌웨어 업로드[[ref]](https://blog.naver.com/p53onco/221190314195).

대다수의 임베디드 공격은 OS와 펌웨어가 공격의 주 대상임.

최근에 이러한 보안 해이를 방지하기 위해 보안 대책과 기준이 연구되고 있다. 그 기준 중에는 보안 부팅과 같은 펌웨어 단계에서의 시스템 무결성 검증 기술이 임베디드 시스템의 기저에 존재해야 한다고 제시되었음. (Jae-yong Ko, “Technologies Analysis based on IoT Security Requirements and Secure Operating System)


### 선행 연구의 한계점
상용화된 보안 부팅은 자사 제품군에 최적화된 보안을 위해 개발됨.

임베디드 환경에서의 보안 부팅 개발은 제조사별로 다른 H/W 환경으로 인해 서로 다른 보안 부팅 모델과 규격, 제조사에 대한 보안 의존성이 생기게 됨.

임베디드 환경에서의 규격화되지 않은 보안 부팅방식은 소규모 임베디드 환경에서 개발이 중단, 지연되는 원인이 될 수 있다.

보안 업체와의 기술 협약의 경우 기술에 대한 인세가 부담이 될 수 있다.

즉, 임베디드와 같이 다양한 H/W환경에서 보안 부팅 방식이 규격화되지 않는 것이 문제.

### 연구의 목적
TPM을 통한 시스템 무결성 보호와 이를 바탕으로 기초적인 보안 부팅 구조를 제안, 구현한다.

## 관련 연구
보안 부팅은 신뢰 체인(CoT, Chain of Trust)로 일걸어지는 무결성확장 방식을 사용함. 신뢰체인은 검증된 RoT(Root of Trust)로부터 연쇄됨.

제조사는 일반적으로 보안 부팅을 구현을 위해 BootROM 코드를 비롯해 SHA, RSA 인증서와 같은 알고리즘, 별도의 보안 회로로 RoT를 제공함.

1. UEFI 보안 부팅:
일반 PC 사용자를 위한 부팅환경으로, 무결성 검증을 위해 RSA 디지털 서명을 사용한다. 보안부팅 외적인 무결성 문제를 해결하기 위해 windows의 bitlocker같은 디스크 암호화 기능같은 추가적인 시스템 무결성 보안을 제공.

2. Samsung Knox:
스마트폰 환경을 위한 보안 솔루션. ARM에서 설계한 Cortex의 TrustZone 기능으로 Secure World 내부에 존재하는 보호된 메모리 영역에 무결성 정보를 저장하고, 인증된 자사의 Knox 보안부팅만 무결성 정보에 접근 가능하도록 설계됨. 보안 부팅으로 이어진 CoT를 통해 TEE(Trust Execution Environmnet)기능을 제공함.

3. FPGA 환경에서의 보안 부팅:
FPGA(Field Programmable Gate Array) Block RAM 같은 기능으로 무결성을 보호.
(F. Devic, L. Torres, “Securing Boot of an Embedded Linux on FPGA,” )
Block RAM에 무결성 정보를 저장한다. 보호된 RoT를 부트 과정에서 측정된 커널의 해시와 비교하여 무결성을 검증. 업데이트마다 해시 갱신 문제를 해결하기 위해 RSA 공개키로 인증서를 활용한 디지털 서명방식도 가능.

## 연구

### 사전 지식

![image](https://github.com/neutrinox4b1/neutrinox4b1.github.io/assets/101745344/53504be6-f9f8-4f2a-a0c1-3f5124cfb608)
기존에는 임베디드에서 BootROM -> FSBL -> SSBL(U-BOOT) -> Kernel 방식으로 동작하며 빠른 부팅을 위해 커널 검증과 부트로더에 대한 검증이 이루어지지 않음.


![image](https://github.com/neutrinox4b1/neutrinox4b1.github.io/assets/101745344/23bba881-7570-4c98-b6f7-5f2645516169)
TPM(Trusted Platform Module)에서 탑재된 보안 기능의 일부를 사용한다. PCR(Platform Configuration Register)을 사용하며, 무결성 정보를 암호화하기 위해 Seal Data 기능을 수행. 암호화된 무결성 정보는 TPM의 NVRAM에 저장하여 보안 부팅에 활용하도록 한다.

NVRAM은 내부 데이터영역 할당시 RW 권한설정 가능. 해당 영역 패스워드설정 가능.


### 문제 해결

![image](https://github.com/neutrinox4b1/neutrinox4b1.github.io/assets/101745344/39f6610d-efcc-415e-b0bb-df1ddd14b706)

TPM의 보안 해시와 접근제어, 비대칭 암호화를 배경으로 보안성을 제공.

SSBL 단계 직후에 보안 부팅이 이루어짐. TPM에 저장된 부트로더 무결성 측정(FSBL에서 이루어지고 BootROM의 CRC와 부트로더 영역 코드를 해싱하여 얻는다)값을 RoT로 사용 BootROM, FSBL, SSBL의 해싱값은 PCR 영역에 저장.

최초 부팅 시 커널 이미지 sha-1과 AES-128하여 해시값이랑 암호화 키를 NVRAM에 저장.

PCR 값이 다르면 복호화 수행 X -> 부트로더가 변조됨.
커널 이미지를 복호화, 복호화한 커널 이미지랑 해시값을 비교. -> 실패 시 커널 변조

## 실험

### 연구 방법
사소한 H/W 설정과 같은 호환성을 고려하여 제품 설정.
FSBL과 같은 부트시 매우 낮은 레벨에서의 코딩이 가능한 칩 Xilinx사의 Zynq-7000… Zedboard

TPM의 접근 주소가 가상화되어 맵핑되므로 직접 접근이 아닌 커널에서 제공하는 접근 방식을 사용.

오버헤드를 측정함. U-Boot와 BOOT.bin에 대해서 파일 사이즈를 측정하였고, Execution Time을 소숫점 3자리까지 ms 단위로 측정하였음.

![image](https://github.com/neutrinox4b1/neutrinox4b1.github.io/assets/101745344/81307128-6e9d-48dc-a632-3e5c3e3b27b9)

### 연구 결과
알고리즘 코드 추가로 용량이 10%가량 증가.
수행시간은 27%가 증가하였다. sha 연산과 커널 복호화에는 많은 시간이 소요되지 않았으나, TPM의 Unseal data 함수에서 사용자에 대한 인증, 무작위 난수 생성 등 프로세서와 TPM간에 잦은 통신과 통신간 요구되는 응답대기시간으로 인해 증가하였다고 함.

연구에서는 TPM과의 통신에서 SPI프로토콜로 프로세서 성능에 비례하는 프로콜을 사용하였으나,
I2C프로토콜을 사용하면 프로세서 성능과 무관하게 일정한 통신속도를 제공한다고 함.

## 결론 및 논의

### 시사점 및 제언
커널과 부트로더에 대한 무결성을 보호하도록 설계함.

논문을 확장하여 구현에 사용된 TPM 1.2버전을 2.0버전으로 변경하거나 ARM Trustzone의 보안 격리 메모리 영역과의 융합으로 다양한 보안 알고리즘을 활용하여 안정성을 높일 수 있을 것이라 시사함.

나아가 사용자의 어플리케이션과 주변 기기에 대한 무결성 검증으로까지 다양한 방면에서 활용될 수 있을 것이라 함.

### 한계점
TPM의 응답대기시간으로 인한 부팅시간의 지연,
성능 향상에 대한 논의가 필요해보임.

\+ 난수 생성이랑 Unseal이랑 무슨 관련이 있는거지?
