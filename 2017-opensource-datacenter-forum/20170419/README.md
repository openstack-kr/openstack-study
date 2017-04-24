# 제2회 스터디

* 진행 일시 : 2017년 4월 19일 오후 7시 30분 ~ 10시
* 장소 : 강남 토즈2호점

## 스터디 요약
Bonding
- 정의
- 기본은 두 NIC를 묶는것
- LACP

병렬(parallel) vs 병행(concurrent)

병렬 : 여러개의 코어에서 프로세스가 동시에 실행
병행 : 사람이 느끼기에 동시에 처리되지만, 1 코어에서 스케쥴링에 의해 동시에 실행되는 것 처럼 보임

프로세스의 실행가능 상태 : 프로세스가 대기큐에 들어가있는 상태

stack size 는 어디서 정할까?
- OS가 Stack size 를 결정한다.

Text 영역 : 함수
DATA : 초기화 되어있는 전역변수
BSS : 초기화 되어있지 않은 전역변수
-- 여기까지는 컴파일 이후 고정되는 사이즈
Context Switching 의 Context 가 Text, Data, BSS 이다.

PCB가 stack의 내용

폴링 / 인터럽트
폴링 : 지속적인 확인
인터럽트 : 작업이 완료되면 알려줘 (대부분이 인터럽트 방식으로 동작)

------------

인프라
개발자가 개발에 집중할 수 있는 환경을 제공
서비스가 거기에 잘 맞추어서 운영될 수 있는 플랫폼
문제/장애가 발생하는 원인을 스스로 찾아서 검증/대응 -> 무중단

형상관리 자동화 툴 어떤게 좋나?
- 본인이 쓰기 편한 툴
- Ansible 이 최근에 두각을 나타냄
  - 진입장벽이 낮다.

스크립트를 작성할 필요는 없다.
Ansible, Fabric, Chef, salt 등 원하는 툴로 만든다.
핵심은 꾸준하게 관리해야한다.
  - 개발자가 한번 작성하고 버린 코드는 재앙
  - SE가 한번 작성하고 방치한 스크립트는 핵폭탄

Python, Perl 을 왜 사용하지 않았나?
- 서버 운영에서 엄청 다양한 환경이 있다. 외부모듈을 import 하면 환경에 따라서 다양한 오류를 만날 수 있다.
- 하지만 bash shell 은 어디에서나 실행이 가능하다

**가상화기술**
- 비용 절감이 목적이 되면 안됨
- 리소스 관리 효율성 고려
- 자동화에 초점을 맞추어 궁극적으로는 클라우드 형태를 지향

**인프라**
성능 vs 편의성
예) LoadBalancer
DSR VS Inline(Proxy/NAT)
Facebook 은 software LB로 모두 전환
HA Proxy 는 DSR 미 지원

Software LB는 Inline 방식으로 대부분 구현
왜? 자동화적인 측면에서 서버 설정이 필요 없는 방식이 효율적
DSR은 서버 셋팅이 필요하다. Inline 은 서버 설정이 필요 없다

자동화 측면에서 효율이 좋다면, 어느 정도 성능 감소는 감내할 수 있다.

Open Source Technology Landscape
![ostl](http://3.bp.blogspot.com/--ukQ-XHL_VI/UzIHKEVgtdI/AAAAAAAAAFs/qugm3VD4f4g/s1600/opensourcelandscape_2.png)
