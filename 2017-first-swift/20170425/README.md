# [2017/04/25] 2회차 스터디
## 과제 : SAIO 설치 확인
* 설치 과정에서 erasurecode library 관련 문제 발견
  * liberasurecode-dev 패키지의 버전이 맞지 않아 생기는 문제로 예상.
* 패치를 보낼 수 있을까?
### 설치 과정 확인
* root 계정으로 설치는 비 권장.
* rsync / xfsprogs / python-xattr를 중심으로 파악
#### loopback device(가짜 디스크) 생성
* xfs filesystem : 대용량이나 파일이 많을때 적합한 파일시스템.
  * ext4 보다 성능이 우수하다고 함.
* python-xattr :file에 대한 attribute를 더 붙이고 싶을 때(xfs의 attribute 영역을 사용).
  * Swift metadata : User metadata -> 사용자가 임의로 해당 Object에 tag를 붙일 때 / System metadata -> System이 Object에 tag를 붙일 때.
    * metadata는 filesystem에 저장
#### Getting the code
* requirements : python library의 의존성 패키지 설치
* python setup.py install vs python setup.py install develop : /usr/local/lib/python2.7/dist-packages 에 설치하기 vs 개발을 위해 현재 이 디렉터리에 설치
#### Configuring each code
* /etc/swift/swift.conf : ring을 만들 때 사용하는 정보
#### Setting up scripts for running Swift
* startmain : Swift실행(필수요소)
* startrest : 나머지 daemon들 실행
### Swift를 설치할 수 있는 다양한 방법

## PyCharm으로 디버깅 환경 구성
### proxy-server
#### startmain script
* swift-init을 참조
* command = args[-1] : 명령어의(Python에서 List) 맨 마지막만 가져오겠다. (Ex) startmain stop -> stop / Python List 참조.)
#### Debug
* /etc/swift : swift가 참조하는 script files의 경로
  * swift-init proxy stop : swift-proxy-server 서비스만 중지 -> 이후 PyCharm을 통해 구동하며 line by line으로 trace
  ![swift-init_proxy_stop](https://github.com/openstack-kr/openstack-study/tree/master/2017-first-swift/20170425/images/swift-init_proxy_stop.png)
  * /swift/bin/swift-proxy
* Run -> Run swift-proxy-server -> Edit Configuration
  * Debug Configurations 창에서
    * Script parameters : /etc/swift/proxy-server.conf
    * Working directory : /home/jang/swift
    * Single instance only 체크
    ![Debug Configuration](https://github.com/openstack-kr/openstack-study/tree/master/2017-first-swift/20170425/images/Debug_Configurations.png)
  * File -> Settings -> Python Debugger 창에서
    * Gevent Compatible 체크
    * PyQT Compatible 체크
    ![Settings_Python_Debugger](https://github.com/openstack-kr/openstack-study/tree/master/2017-first-swift/20170425/images/Settings_Python_Debugger.png)
  * Debug 시작
  ![swift_bin_swift-proxy-server](https://github.com/openstack-kr/openstack-study/tree/master/2017-first-swift/20170425/images/swift_bin_swift-proxy-server.png)
#### Remote Debugging 설정
* Virtualbox에서 호스트 전용 네트워크 생성 및 DHCP 서버 설정
  ![virtualbox_dhcp](https://github.com/openstack-kr/openstack-study/tree/master/2017-first-swift/20170425/images/virtualbox_dhcp.png)
* 해당 VM에서 호스트 전용 어댑터 추가
  ![virtualbox_network](https://github.com/openstack-kr/openstack-study/tree/master/2017-first-swift/20170425/images/virtualbox_network.png)
#### Middleware 분석하기
* wsgi : web service gateway interface -> 내장 웹서버
* tempauth 분석해보기
  ![tempauth.py](https://github.com/openstack-kr/openstack-study/tree/master/2017-first-swift/20170425/images/tempauth.py.png)
  * proxy-server를 디버깅 시작하고 breaking point를 찍었을때의 예시
  ![server.py_break_point](https://github.com/openstack-kr/openstack-study/tree/master/2017-first-swift/20170425/images/server.py_break_point.png)
  ![server.py_req_contents](https://github.com/openstack-kr/openstack-study/tree/master/2017-first-swift/20170425/images/server.py_req_contents.png)
  * def __call__(self, env, start_response): 시작 부분
    * env : 이전에 request를 통해 넘어온 data
    * start_response
  * return self.app(env, start_response)
* Token 발급 테스트 : `curl -v -H 'X-Storage-User: test:tester' -H 'X-Storage-Pass: testing' http://127.0.0.1:8080/auth/v1.0`
![Token_generation_test_001](https://github.com/openstack-kr/openstack-study/tree/master/2017-first-swift/20170425/images/Token_generation_test_001.png)
  * Token 인증 확인
    ![Token_generation_test_002](https://github.com/openstack-kr/openstack-study/tree/master/2017-first-swift/20170425/images/Token_generation_test_002.png)
  * 해당 Token이 유효하지 않은 경우
    ![Token_generation_test_fault](https://github.com/openstack-kr/openstack-study/tree/master/2017-first-swift/20170425/images/Token_generation_test_fault.png)

## 과제
* - middleware -
  * tempauth - 장태희
  * proxy-logging - 강성진

* - 기본 개념 -
  * Ring - 양유석
  * Swift 의 API 설명 - 박광희
  * Swift 에서 지원하는 인증방식 (keystone, swauth, tempauth 등)
    * - 김신수
    * - 한기홍