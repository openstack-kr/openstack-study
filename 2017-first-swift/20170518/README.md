# [2017/05/18] 3회차 스터디
## Swift API
* Rest API로 제공
* https://developer.openstack.org/api-ref/object-storage/
* Swift에는 폴더의 개념이 없다
  * /v1/AUTH_ASDF/container1/ - PUT
  * S3
  * /v1/AUTH_ASDF/container1/ folder - DELETE
  * folder/fol/a.txt <- 논리적으로 지정된 파일 명
  * 저장된 timestamp <- 실제로 저장되는 파일 명
* 단일 파일 업로드 용량이 정해져 있음

## proxy-logging
* https://docs.openstack.org/developer/swift/middleware.html#module-swift.common.middleware.proxy_logging

## tempauth 분석
* 목표 : 명령어의 각 parameter는 무엇이고, URL은 어떻게 제공되는가?

### 접근 : code에 원하는 단어가 있는지 없는지를 찾아보는것이 시작
* 의심되는 부분은 breaking point를 찍고 해당 사항을 note taking 적어 놓을것
* breaking point : ```key = req.headers.get('x-storage-pass')```
* handler = self.handle_get_token
* handle_request
* 최상위 : ```def __call__(self, env, start_response):```

*         if env.get('PATH_INFO', '').startswith(self.auth_prefix):
  * IDE의 python console에서 `env.get('PATH_INFO')`
  *             if 'x-storage-token' in req.headers and \
  *         account_user = account + ':' + user -> 유저 인증 시작 부분(user와 group이 정상적인지 이전에 확인)
  *         if account_user not in self.users: -> 뭔진 모르겠지만 이미 채워져있다(if 들어가지 않음)
  *         if self.users[account_user]['key'] != key -> 키 검사
  *         account_id = self.users[account_user]['url'].rsplit('/', 1)[-1] -> 계정 id 가져오기
  *         if not token: -> token이 생성되는 과정을 볼 수 있다
  *         resp = Response(request=req, headers={ -> token이 모두 발급 된 것을 확인
* X-Auth Token을 언제 가져오는가?
  *         token = env.get('HTTP_X_AUTH_TOKEN', env.get('HTTP_X_STORAGE_TOKEN'))
* 유효성 검사
  * breaking point : groups = self.get_groups(env, token)
  *         memcache_token_key = '%s/token/%s' % (self.reseller_prefix, token)
  *             memcache_token_key = '%s/token/%s' % (self.reseller_prefix, token)
* token의 문자열을 검사 하는 것이 아니라, memcache에서 무엇인가를 가져와서 비교하는구나 라고 알게 됨.

### self.users 는 언제 생성 되는가?
* self.users = {}
  * init에서 넣어준 다는 것을 알게 됨
  * if conf_key.startswith('user_') or conf_key.startswith('user64_'): -> user_ / user64_를 shell에서 검색
  * 데이터가 저장되는 곳을 확인 (vim /etc/swift/proxy-server.conf)

### 나머지 코드는?
* 나머지 함수는 관심사 밖은 굳이 볼 필요 없음.(지금은)

### 결론
* 원하는 대상 정하기
* request에 보낸 내용을 가지고 보기
* request를 보내는 것이 없으면 log를 가지고 (log가 찍혔다면 code가 지나간 자리이므로) 해당 부분을 검색하여 string을 검색

### test2:tester2는 만든 적인 없는데 요청을 확인하기
* proxy/controllers/account.py
  * resp = self.GETorHEAD_base( -> 계정, 디바이스, 포트 등의 정보
  * 실제 account가 만들어 지는 과정 : base .py

## 다음 분석
* SLO와 DLO 분석(API 호출 미리 많이 연습하고 코드 분석 시작할 것 e.g. Chrome Postman)
  * common/middleware/dlo.py
  * common/middleware/slo.py
* SLO : 김영우, 장태희, 강성진
* DLO : 김호진, 김현지
* 특정 부분(코드 라인)을 해도 되고, Flow Chart를 그려오면 좋음.