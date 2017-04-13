# [2017/04/11] 1회차 스터디
## 준비물 : Pycharm
* Line by Line 분석
* Professional 버전은 Remote Debugging이 가능
* Community Edition은 VM내에 설치하여 직접 분석. VM에 Memory를 많이 할당하여야 함.

## 시작일 및 종료일
* 시작일 : 2017년 4월 11일
* 종료일 : 원하는 목표를 이룰 때 까지
* 모임 일시 : 격주 화요일 오후 8시
* 모임 장소 : 강남역 부근 토즈(3곳 중 1곳 랜덤)

## 스터디 목표
* Openstack Swift 코드 구조와 내부 동작 원리 파악
  * Devstack을 이용한 개발(디버깅) 환경 구축(with Pycharm)
  * Openstack Swift의 디버깅 방법
* IRC / Gerrit을 통해 Upstream 개발 현황 파악
  * https://review.openstack.org/#/q/status:open+openstack/swift
  * Bug를 파악하고 Patch까지(가능하면)

## 스터디 진행 방법
* 마일스톤 1(어느정도 진행 된 수준)
  * 2주 단위 스프린트
  * 1 스프린트동안 기능 혹은 모듈을 선정하여 코드 분석 및 문서화(돌아가면서 발표)
  * 스터디 당일 분석한 코드를 코드 리뷰하듯이 진행(자신이 짠 것 처럼 설명)
  * Slack을 통한 상시 정보 공유
  * Swift의 가장 기본적인 기능 코드 분석
    * Account / Container / Object에 대한 CRUD
    * Middleware(인증, ACL처리 등)
  * 여기까지가 Swift가 대략적으로 어떻게 동작하는지에 대해 익힐 수 있는 단계
    * 에러 로그에 대한 의미를 파악 가능 예상

* 마일스톤 2
  * Swift 고급 기능 분석
    * Ring Build
    * Replicator(복제. 총 3개를 Copy하는데 Data가 유실되었을 경우 어디에서 복원되는지), Auditor(정합성)등 다양한 데몬
    * Erasure Coding(용량은 줄이면서 데이터가 유실되었을 때 복원할 수 있는 기법)
    * Swift 3(AWS S3의 API를 지원)
      * AWS S3의 Object Lifecycle 기능 구현(Rule을 지정하기)
  * 인증 시스템
  * Upstream 버그

## Swift
* https://printf.kr/archives/17
* Object Storage vs Block Storage
  * Block Storage : HDD / SSD(Block Device) 처럼 블록 단위로 쪼개서 저장하는 구조.
    * VM을 Start하고 Disk를 Attach 할때 많이 사용.
  * Object Storage : Data를 하나의 Object로 바라 봄. File을 식별할 수 있는 여러가지의 metadata를 부여하고, Disk에 저장하는 방식.
* Eventual Consistency System : 언젠가는 정합성이 맞아진다는 논리.
* account : 계정
* container : S3기준 bucket
* object : data
* 포함관계 : obejct < container < account
### 구성요소
* account-server vs conainter server
  * account의 정보를 저장하는게 주 목적. 어느 폴더를 가지고 있느냐(리스트). 그 계정에 대한 스토리지 사용량.
  * container : 계정의 hash 값을 container의 object에 또 다시 할당.
* proxy-server : REST API를 사용할 수 있도록 해주는 server.
* 흔히 A/C/O Server 단위로 많이 구성하고 계산한다.
* Ring : "Data가 어느 서버로 갈지 몰라도 돼요"의 진실. 해당 Object가 어느 서버로 가서 저장이 될지를 결정.
### 개발 언어
* Python
### Swift 개요
* https://docs.openstack.org/developer/swift/
* Source : https://github.com/openstack/swift | http://git.openstack.org/cgit/?q=swift

## Devstack 설치
* stack.sh / unstack.sh / clean.sh
  * 참고 : https://github.com/openstack-kr/openstack-study/tree/master/2016-spring-a
  * stack.sh : Devstack 가동
  * unstack.sh : 설치했던 packages들을 다시 내림
  * clean.sh : 설치햇던 것들을 모두 삭제
* samples/local.conf
  * HOST_IP : 10.0.2.15
  * SWIFT_REPLICAS=3
  * 맨 아래에 disable_all_services
  * enable_service key mysql s-proxy s-object s-container s-object s-account
* Devstack 확인 : https://printf.kr/archives/28

## Swift Code 분석
* bin : 각종 Daemon 실행 코드
* doc : Webpage의 문서 내용
* etc : 각종 Daemon configuration sample(어떤 의미인지 다 볼것)
* examples : python wsgi 모듈만 사용하면 성능 저하가 발생하기 때문에 apache와 같이 띄우기 위한 내용이 포함.
* install guide : webpage document
* release notes
* swift : core codes
  * common : 모든 code에서 공통으로 호출되는 code들
    * ring
  * account
  * container
  * obj
    * 세 폴더의 구조가 비슷.
    * server / backend는 다 볼것임. 나머지는 필요에 따라.
  * proxy : 전체 다 봄.
* test : test를 위한 test code

## Swift 구축
* SAIO - Swift All In One https://docs.openstack.org./developer/swift/development_saio.html

## 과제
* 다음 시간까지 Clean한 VM을 만들고 apt-get update && upgrade -> SAIO 구축
* Openstack Swift에 대해서 학습하기(Overview는 꼭)
  * https://docs.openstack.org/developer/swift
