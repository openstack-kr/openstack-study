# 2017년 상반기 Openstack Swift 분석 스터디
----

## 스터디 개요
* 개요
  * 시작일 : 2017년 4월 11일 오후 8시 강남토즈타워점
  * 종료일 : 2017년 10월 
  * 스터디 인원 : 10명
  * 모임 일시 : 격주 화요일 오후 8시
  * 장소 : 강남역 부근 토즈
* 목표
  * Openstack Swift 의 코드 구조와 내부 동작 원리를 파악한다.
  * Devstack 을 이용하여 Openstack 의 개발환경을 구성하는 방법과 디버깅 방법에 대해 학습한다.
  * IRC/gerrit 을 통해 Upstream 개발 현황을 파악한다.
    * 스터디를 진행하다가, 버그 혹은 개선 할 점을 찾으면 contribution을 해본다

* 진행 방법
  * 2주 단위로 진행되며, 2주를 1 sprint로 계산하여 진행한다.
  * 1 스프린트동안 기능 혹은 모듈을 선정하여 코드 분석 및 문서화(예: UML)한다.
  * Slack(혹은 IRC)으로 정보 공유

## 스터디 내용
내용은 스터디를 진행하면서 수정될 수 있습니다.

### 알아볼 내용
* Proxy/Account/Container/Object Server의 내부 동작 원리.
  * 예: Object 는 어떤 과정을 통해 디스크에 저장될까?
* Ring 빌드 과정 및 알고리즘.
* Eventually Consistent 를 위해 실행하는 여러 데몬의 동작 원리. (ex: replicator)
* 다양한 Middleware 를 분석을 통한, Custom Middleware 의 개발 방법.
* Upstream 의 개발 현황

### 1일차
* Openstack Swift 소개 및 프로젝트 이해
* Devstack으로 Swift 설치하기
* Openstack Swift 의 구성요소 이해
* 기본API 톺아보기

### 2일차
* PyCharm 을 이용한 개발 환경 구축
* Upstream 개발 진행상황 파악
* Proxy, Account, Container, Object 서버 설정파일 분석

### 3일차
* Account PUT/GET/DELETE 분석
* Container PUT/GET/DELETE 분석

### 4일차
* Ring Build 분석 (1)
* Object PUT/GET/DELETE 

### 5일차
* Ring Build 분석 (2)
* Middleware 살펴보기

### 6일차
* Middleware 코드 분석(1)

### 7일차
* 미정

### 8일차
* 미정

### 9일차
* 미정

### 10일차
* 미정
