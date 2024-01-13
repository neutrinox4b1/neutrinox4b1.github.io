# 임베디드 디바이스 펌웨어의 웹 인터페이스 취약점 식별을 위한 에뮬레이션 기반 퍼징 기법

<!--more-->

{{< admonition type=info open=true >}}
제목: 임베디드 디바이스 펌웨어의 웹 인터페이스 취약점 식별을 위한 에뮬레이션 기반 퍼징 기법

저자명: 허정민, 김지민, 지청민, 홍만표

발행일자: 2019.12.

논문 종류: 국내학술저널

컨퍼런스명/저널명: 한국정보보호학회 논문지
{{< /admonition >}}

## 연구 배경 및 필요성

유무선 공유기같은 네트워크 장비는 외부 트래픽을 받아서 처리해야 하기 때문에 안전하지 않은 펌웨어를 사용하면 웹 인터페이스(httpd, lighttpd)의 보안 위협에 노출될 수 있다.

이러한 공격을 예방하기 위해서 웹 인터페이스의 취약점을 찾아내고 결함을 제거해야하는데<br>수천 개에 달하는 임베디드 디바이스 펌웨어를 식별하는 것은 하드웨어에 의존적이므로 물리적으로 불가능에 가깝다.

## 선행 연구의 한계점

최근 연구 
- [Zaddach, Jonas, et al. "AVATAR: A Framework to Support Dynamic Security Analysis of Embedded Systems' Firmwares." NDSS. Vol. 14. 2014.](https://www.researchgate.net/profile/Jonas-Zaddach/publication/269197057_Avatar_A_Framework_to_Support_Dynamic_Security_Analysis_of_Embedded_Systems'_Firmwares/links/5e0b2725299bf10bc3852355/Avatar-A-Framework-to-Support-Dynamic-Security-Analysis-of-Embedded-Systems-Firmwares.pdf)
- [Chen, Jiongyi, et al. "IoTFuzzer: Discovering Memory Corruptions in IoT Through App-based Fuzzing." NDSS. 2018.](https://staff.ie.cuhk.edu.hk/~khzhang/my-papers/2018-ndss-iot.pdf)
- [Chen, Daming D., et al. "Towards automated dynamic analysis for linux-based embedded firmware." NDSS. Vol. 1. 2016.](https://www.ndss-symposium.org/wp-content/uploads/2017/09/towards-automated-dynamic-analysis-linux-based-embedded-firmware.pdf)

에서는 하드웨어 및 소프트웨어 에뮬레이션 기법을 사용한다. 특히 Chen et al의 도구, Firmadyne은 전체 시스템 에뮬레이션 기능을 사용하여 펌웨어 만으로도 실제 장비가 동작하는 것처럼 하였으며, 실행된 프로그램을 대상으로 동적 분석을 수행하도록 구현된 프레임워크이다.

그러나, Firmadyne의 동적분석 방법은 도구에서 정의된 취약점에 대해서만 점검이 가능하여 찾을 수 있는 취약점의 범위가 한정되어 있다.

본 논문에서는 임베디드 디바이스 펌웨어의 웹 인터페이스 취약점 식별을 위한 방법으로 Firmadyne의 전체 시스템 에뮬레이션 기능과 스마트 퍼징을 수행하기 위해 오픈소스로 공개된 Boofuzz의 네트워크 프로토콜 기능을 통합하여
<br>
에뮬레이션 기반 퍼징에 초점을 맞추어 구현된 Fabfuzz를 제안한다.

## 관련 연구

### Firmadyne 프레임워크

Firmadyne은 리눅스 기반 펌웨어를 대상으로 한 최초의 자동화된 동적 분석 프레임워크이다.

QEMU 전체 시스템 에뮬레이터를 사용하여 추출된 루트파일 시스템을 사전 컴파일된 커널을 사용하여 아키텍처별 커널 부팅을 실행한다. <br>
이후 프레임워크 내에 정의된 동적 분석 방법을 사용하여 세 가지 방법으로 취약점 점검을 수행.


1. 엑세스 가능한 웹 페이지를 탐색한다.
2. Snmpwalk를 사용하여 인증되지 않은 SNMP 프로토콜 정보를 덤프한다.
3. 메타스플로잇 프레임워크로 알려진 악용사례를 확인하여 이미지를 검사한다.

### 에뮬레이션 기반 퍼징

Firm-AFL은 임베디드 디바이스의 취약점 식별을 위한 도구로 Firmadyne과 AFL을 사용하여 에뮬레이션 이후 퍼징한다.

기존 Firmadyne 에뮬레이션은 전체 시스템 에뮬레이션으로 수행되어 AFL의 퍼징 속도가 느리다.

그러나, 이후 퍼징프로세스 절차를 개선한 Frimadyne을 통해 빨라짐. => 펌웨어 취약점을 찾는 방법으로 에뮬레이션 기반 퍼징이 효과적이다.


### 네트워크 프로토콜 퍼징

dumb fuzzer, smart fuzzer

dumb fuzzer은 그냥 대상 정보가 없는 경우 가능한 프로토콜에 대해서 임의 입력값을 생성하여 전송.
<br>
smart fuzzer은 대상의 프로토콜 정보를 알고있는 경우에 사용.

대표적으로 Boofuzz, Peach, SPKIE가 있다.

Boofuzz는 python 스크립트를 통해 프로토콜 형식 파일을 구현한다.

이전 연구
- [Chen, Jiongyi, et al. "IoTFuzzer: Discovering Memory Corruptions in IoT Through App-based Fuzzing." NDSS. 2018.](https://staff.ie.cuhk.edu.hk/~khzhang/my-papers/2018-ndss-iot.pdf)
- [Gorbunov, Serge, and Arnold Rosenbloom. "Autofuzz: Automated network protocol fuzzing framework." Ijcsns 10.8 (2010): 239.](http://people.csail.mit.edu/sergeyg/publications/autofuzz.pdf)
- [Han, Xing, Qiaoyan Wen, and Zhao Zhang. "A mutation-based fuzz testing approach for network protocol vulnerability detection." Proceedings of 2012 2nd International conference on computer science and network technology. IEEE, 2012.](https://ieeexplore.ieee.org/abstract/document/6526099/?casa_token=hJar2ey4AngAAAAA:0mMSPV9vafRIfqMGNl7YJqftmvTJ44M19UAn4Q_IG8pPJXT8GXUXYFC2SUZa9KQJO9j3cgGXIw)
- [Hammersland, Rune, and Einar Snekkenes. "Fuzz testing of web applications." Retrieved 7.16 (2008): 2010.](http://rune.hammersland.net/tekst/fuzzing_article.pdf)

에서 네트워크 프로토콜 퍼징 수행 시 실제 통신 케이스를 기반으로 테스트케이스를 생성하는 것이 효율적이다.

## 문제 해결 방안

![image](https://github.com/neutrinox4b1/neutrinox4b1.github.io/assets/101745344/5c1ab524-e27b-4151-a0a4-e5adcd6ffa34)

fabfuzz controller에서 모든 작업을 수행한다.

Firmadyne을 이용하여 전체 시스템을 에뮬레이션 한다.

네트워크 프로토콜 퍼징 시 테스트케이스 생성을 위해 tshark와 selenium을 이용한다.<br>
tshark는 와이어샤크의 커맨드라인 버전으로 pcap파일을 수집한다.

selenium은 웹 드라이버를 통해 대상 웹 서비스에 요청을 보내는 등 웹 페이지를 동작시켜서 tshark와 함께 패킷을 수집한다.

Make Protocol Format File에서는 pyshark도구를 사용하여 pcap파일의 구문분석을 수행하며 중복된 구조의 패킷을 제거한다.

이후 Boofuzz에서 실행하 수 있는 프로토콜 형식 파일로 변환되어 퍼징, 모니터링을 수행하는 방식을 제안하였다.

## 실험 및 평가

Firm-AFL에서 퍼징으로 크래시가 발생된 3개의 펌웨어와, Firmadyne에서 동적 분석으로 식별된 28개의 펌웨어를 대상으로 퍼징테스트 및 평가를 수행한다.

![image](https://github.com/neutrinox4b1/neutrinox4b1.github.io/assets/101745344/d556688b-8005-4828-b5a6-6e4eed032c7c)

다음과 같은 표를 사용하여 시간을 측정한 성능비교를 제시한다.

또한, 알려진 취약점에 대한 퍼징 성능 테스트를 수행하여 기존 도구인 Firmadyne에서 식별되지 않았던 취약점을 제안된 도구로 새로 발견했다.

## 시사점 및 제언

제조사의 펌웨어를 에뮬레이트 하여 분석, 기존에 연구되었던 효과적인 방법을 적용하고 검증한 연구임에 의의가 있어보인다.

실험 결과를 볼 때, Firmadyne의 방법과 함께 사용할 시 보안 인증과 관련된 취약점 뿐만 아니라 더 많은 취약점을 점검하여 향상된 식별 성능을 보일 것으로 기대된다고 밝힘.

## 한계점 

세션 값을 고려해서 패킷을 보낼 때 세션값을 취득하고 패킷에 반영하여 퍼징을 수행하는 것이 필요함.

같은 헤더값을 가진 파일에 대해서 처리하는 작업이 필요하다.

퍼징을 통해 확인할 수 없는 문제도 있어 firmadyne과 상호 보완적인 취약점 점검 절차가 필요하다.
