# Ring의 구성 및 활용(koain 김영우님 자료 활용)
## 클러스터
* cluster에서 region을 생성한다.(여러개가 될 수 있음)
* 여러개의 region
  * region 간은 독립된 환경이어야 함. (전기, 네트워크 등)
  * region 안에 여러개의 zone
  * 여러개의 rack을 묶어 region을 만들 수도 있고, 하나의 데이터센터를 region으로 할 수도 있다.
  * 각 region은 독립된 전원과 네트워크 등을 사용해야 한다.
  * zone은 하나의 rack이라고 보면 된다.
  * 주노 버전에서는 region 상관안했었음.
    * zone은 하나의 랙이라고 봐도 무방
    * 한 열이 zone이 될수도, 랙 여러개가 zone이 될수도 있음
    * zone안에 여러개의 node
      * node는 서버라고 봐도 무방
      * node안에 여러개의 하드디스크
      * node는 각 서버 장비라고 보면 된다.
      * hash 알고리즘은 md5를 기본으로 사용.

## Storage Policies
* 노드별, 존별 정책 부여 가능, 컨테이너에 정책을 부여 (복제 몇개할지, Erasure code 적용여부), container에 storage policy 추가가 가능하다.
* 주노부터 들어감
* 따라서 각 존 내부에서 다양한 policy를 적용할 수 있다.

## 분산 시스템에서 이용되는 알고리즘
* consistence hash ring 알고리즘을 가장 많이 사용한다.
* 해시 링을 사용. 해시값이 0000... ~ ffff... 라면 이를 원으로 생각해서 이를 서버수를 분할
* mod 연산을 이용해서 어느 서버인지 정함
* 서버 하나에 너무 몰릴수가 있으니 딱 맞춰서 나누는게 아니라 마구마구 쪼개서 할당함.
* drive의 유실을 방지하기 위해 같은 drive를 세분화 하여 저장하고, 서버 하나가 장애가 나면 문제가 생길 수 있으니 modified consistent hasing ring이라는걸 만듬
  * Partition : 하나의 원을 여러개로 쪼개 놓은것.
  * Partition Power : 서버의 개수, 2의 partition power 승으로 원을 쪼갬
  * Replica Count : 다른 곳에도 복제할 개수
* 데이터의 분산
  * weight : 어느 하드디스크를 좀더 파티션을 많이 배치할 것인가. (용량이 큰 디스크라고 보면 됨)

* Rebalancing the Rings
  * account, container, object 별로 링을 만듬

* Inside the Rings
  * ring은 account, container, object 3개를 생성하게 된다.
  * lookup table에 각종 정보가 저장된다. partition 개수 X replicas 횟수 = table field의 갯수 -> ring 1개
  * 링을 만들떄, 리존,존,아이피,포트,디스크를 지정함. (ring-builder)
  * 장치 목록을 얻을 수 있음
  * Device lookup table을 얻을 수 있음, 장치의 리존, 존, 가중치, 아이피, 포트등이 적힌 테이블
  * rebalancing을 하면 다른 테이브을 만듬
    * partition 수만큼의 열, 복제수만큼의 행
	* 이 파티션의 복제본이 어느 장치에 저장되는지 적혀있음
	* 핸드 오버 행이라고 2개를 더 넣는데(백업 디스크), 여분의 디스크를 두개 정도 넣어둠(레이드 구성할 때, 스페어 하나 더 넣는 것처럼)
	* account get 명령어를 때리면, account에서 md5 해시값얻고 mod 연산을 해서 몇번 파티션인지 찾음, 그다음 테이블에서 리블리케이션 어딨는지 확인하고, 이를 가지고 디바이스룩업테이블로 가서 아이피랑 포트를 가져옴

* 네트워크 구성할 때
  * 서비스 망 : proxy, as, cs, os 를 연결하는 망 10g
  * replication 망 : as, cs, os 를 연결하는 망 대부분 1g 또는 10g, rebalancing, replication이 일어남.

## 스위프트 코드
  * proxy/server.py __call__
    * wsgi가 부르는 함수, 한번 인증된 키에 대해서는 memcached 가 가지고 있어 키스톤으로 갈 필요가 없음

  * ring.py get_nodes, get_part
    * md5 호출함
	* partition 정보 리턴
	* 노드도 찾음
	* _replica2part2dev_id 이게 테이블

  * base.py make_requests
    * green thread? 스레드를 만드는게 아니라 코루틴으로 흉내냄 (파이프라인식으로)
	* 세개의 request에 대해서 실행
	* pile.spawn 에서 요청 보내고 받음
	* pile에 response가 담김
	* quorum 이 맞는지 검사. (정합성 검사)
	* replication 노드 수 전부 저장확인 안함 quorum만 만족하면 break 뒤에 노드 검사안함

  * _make_request
    * node에 대해서 순회 노드에게 요청 보내는거임 (replica 갖고있는 node)

  * generate_request_handlers 함수
    * account server에게 보내기 위한 헤더를 만듬

  * best_response
    * 여기서 다시 quorum 확인함.
	* 마구마구 뭔짓을 한 다음에 response 헤더와 바디를 만듬

  * account/server.py __call__
    * http://prox_svr/v1/account/cont/obj (프록시서버로 올때)
	* http://ac_svr:p/v1/sdb1/70/account/obj (어카운트 서버로 올때)

* proxy server -> proxy/containers/server.py 가 핵심
* account server -> proxy/containers/accout.py
* base.py의 make_request 메소드에서 for node in nodes가 어느 서버에 갈지 request를 만든다.
* quorum 함수들에 의해 node를 가지고 온 다음 best response를 선출해서 response를 보낸다.

## PUT
  * 어카운트 생성

  *  _get_account_broker
    * hash : ake9cjasdjewljelfd
    * 파일 경로 : /srv/node/sdb1/accounts/70/lfd/ake9cjasdjewljelfd.db

  * broker.initialize
    * tmp 파일을 만들고 여기다가 테이블 만들고 다 쿼리 때리고 커넥션 끊고, 다시 티엠피 파일의 이름을 .db 이름으로 바꿈

  * 106 line (server.py) if continer :
    * container 서버에 요청이 온경우, 다 처리를 한 다음에 account 서버에 알려줌
    * .lock 이라는 파일을 디렉토리에 만들어서 그 디렉토리에 대한 lock을 검
    * 펜딩 파일에다가 게속 밀어 넣다가, 일정 용량 초과하면 그때 commit_puts(db query)
    * pickle이라는걸 써서 씀.

## GET
* backend.py list_containers_iter
* _commit_puts_stale_ok()
  * lock을 잡고 펜딩파일 다 커밋

## DELETE
* broker.delete_db, _delete_db
  * 테이블에서 status를 DELETED를 바꿈.
  * 나중에 auditor라는 애가 지움. 서버들은 마킹만하고 넘어감. 다른 일들은 데몬들이 함.

# Account와 Container가 code상에서 어떻게 처리 되는지
* swift 내의 account, container, obj 로 나누어져 있다.
* account와 container는 코드 구조가 비슷하다. database에 접근
  * account/server.py
    * PUT -> proxy 서버에서 request를 받을 때에는 http://prox_svr/v1/account/container/obj
    * PUT에서 database로 request를 보낼 때에는 http://account_server/v1/sdb1/70/account/obj 로 바뀌어 보내게 된다.
    * account 생성 == db file 생성
* db.py -> initialize == sqlite에 table을 만드는 것 까지만 진행, 실제 저장은 put_record
* PUT 요청 -> db 파일을 하나 생성하고 원하는 이름은 rename으로 db 파일을 생성한다. PUT 명령이 오면 실제 sqlite에 바로 쓰는것이 아니라 pending 파일에 기록하고, pending 파일이 일정 capacity를 넘어서면 .lock 파일로 모든 요청을 잠시 막고 db에 기록한다.
* GET 요청 -> 모든 요청을 잠시 막고 pending에 있는 것을 db에 merge시킨 뒤 db를 읽어온다.
* DELETE -> table field에 DELETED라고 flag 표시만 하고 실제 지우지는 않는다. 나중에 auditor daemon이 돌면서 실제 파일을 지운다.

## 다음 시간
  * Replicator
    * acs, conts, objs 각각 모두가 replicator가 돔.
  * db_replicator.py , db에 대해서는 rsync,
  * obj/server.py REPLICATE()
  * auditor.py
  * diskfile.py
  * hash 값 중복 처리 : ringbuilder.py