# [2017/05/30] 4회차 스터디
## 과제 점검 : SLO / DLO 분석 공유
* 강성진님 : SLO 코드 따라가는 중
  * response 쪽은 모여있고 GET / HEAD로 나뉘어진다.
  * Swift에 연결이 되지 않았을 때는 fake 값을 넘겨준다.
  * DELETE는 bulk라는 middleware로 넘긴다.
  * PUT : error가 발생한 경우 모든 프로세스를 진행한 뒤(continue) 마지막에 한번에 던진다.
    * seg_dict[] : dictionary를 다 뜯은 뒤 다시 재조합 하는 부분이 있다. 일부 불필요한 부분도 있을 것으로 예상 됨.
* 김영우님 : API 사용
  * 먼저 파일들을 올림(Q1. 여러 파일들을 하나의 objct로 관리한다? 용량이 큰 파일을 분할 업로드?) -> 일단 먼저 client측에서 쪼개야 함.
  * SLO는 commnad시 multipart-manifest query를 추가해야 한다.
  * data_for_storage : parse 받은 데이터를 저장 할 준비
  * MD5 함수는 왜 call 하는지 모르겠음.
  * `def filter_factory` : pipeline을 모았다가 실행하는 곳으로 추정 됨.
  * `multipart-manifest == get`이어야 slo 처리를 해준다.
* 김호진님 : DLO 구현
  * /etc/swift/proxy-server -> middleware pipeline에서 dlo는 거의 뒤에 위치. <br />
    앞에 middleware에서 dlo로 테스트용으로 가짜 request를 보내면 debugger 입장에서는 진짜로 착각. <br />
    이때 path를 보면 진짜 request인지 가짜 request인지 알 수 있음. <br />
    여기서는 method = 'HEAD'면 테스트(가짜), 'PUT'이면 진짜 request.

## Custom Middleware 개발하기
* https://docs.openstack.org/developer/swift/development_middleware.html
  * Creating Your Own Middleware
  * `def filter_factory`
  * closer 기법을 통한 작성
    * class MyMiddleware:
    * def filter_factory(global_conf, **local_conf): <br />
        conf.update <br />
        def my_filter(app): <br />
          return MyMiddleware(app, conf) <br />
        return my_filter
  * pipeline에서 동작시 wsgi.py module이 어느 클래스를 로드 할지 결정한다.(paste.filter_factory 부분) <br/>
    wsgi가 각 middleware의 클래스들을 초기화 시켜준다.
  * 각 middleware별 특정 변수들은 local_conf에 저장
  * app parameter는 다음에 호출 할 middleware를 가리킴.
  * 문서에 나온것과 달리 middleware는 따로 repository를 만들어서 관리. 이후 설치할 수 있는 설치형으로 만듬.
  * setup.py에 pbr 설정을 한 뒤 setup.cfg에 swift를 설치하듯이 설치.

## DLO 분석
* https://docs.openstack.org/developer/swift/overview_large_objects.html
* 
```
# First, upload the segments
curl -X PUT -H 'X-Auth-Token: <token>'         http://<storage_url>/container/myobject/00000001 --data-binary '1'
curl -X PUT -H 'X-Auth-Token: <token>'         http://<storage_url>/container/myobject/00000002 --data-binary '2'
curl -X PUT -H 'X-Auth-Token: <token>'         http://<storage_url>/container/myobject/00000003 --data-binary '3'

# Next, create the manifest file
curl -X PUT -H 'X-Auth-Token: <token>'         -H 'X-Object-Manifest: container/myobject/'         http://<storage_url>/container/myobject --data-binary ''

# And now we can download the segments as a single object
curl -H 'X-Auth-Token: <token>'         http://<storage_url>/container/myobject
```
* D(Dynamic)LO : 파일을 무한정 올릴 수 있다. multipart 종료 시점을 알 수 없음.
* S(Static)LO : multipart 업로드 종료 이후 더이상 part 업로드 불가.
  * _call_ 부터 시작
* RateLimitedIterator
* line 317 ~ 337 이 핵심!
* DLO line 228 ~ 부터 분석하면 어떻게 동작하는지 알 수 있다.
* req = Request() 객체를 어떻게 잘 활용하는지, Debugging point, 분기를 눈에 익히는것이 목적

## 과제
* 모두 : account - GET 요청
  * proxy-server <- line by line debugging
  * account-server(/swift/account/server.py) -> `AccountController : GET()` : 눈으로 분석 해볼것