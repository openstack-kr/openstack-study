# 2016년 upstream : 오픈스택 컨트리뷰션 스터디
## [사전 준비]
* http://docs.openstack.org/ko_KR/upstream-training/ 미리 읽어보기

## [2016/11/22] 1회차 스터디
### Openstack 한글 문서
* http://docs.openstack.org/ko_KR/
* etherpad 공유문서 : https://etherpad.openstack.org/p/upstream-training-korea-2016-fall-study
### Git
* Git log
* Git status
* Git remote -v -> remote URL을 추가하여 clone 받는 곳과 push 하는 repository를 다르게 설정 할 수도 있다.
* Git commit 관련 규칙 : https://medium.com/@preslavrachev/what-s-with-the-50-72-rule-8a906f61f09c#.h309t4bqy
### 과제
1. 리눅스 노드1(가상머신 or 물리머신 or mac) 
2. www.openstack.org -> Foundation member로 가입(Community Memeber는 안됨)
3. www.launchpad.net -> Ubuntu One 계정 가입
4. review.openstack.org ID -> launchpad 계정으로 로그인한 후, username 생성 필요 + ICLA 동의 필요
5. your SSH 공개키: 커밋 등에 사용하는 컴퓨터를 인증하기 위한 수단
6. Zanata ID: 번역 컨트리뷰션을 위해 필요함 (translate.openstack.org)
7. GitHub ID: 없어도 되지만, 컨트리뷰션을 GitHub에 보이도록 하고 싶다면 gerrit username 및 이메일을 동일하게 사용할 것

## [2016/12/13] 2회차 스터디
### Zanata 가입 / Contribute 방법
* github / gerrit / zanata 등의 ID 및 Mail 주소가 같으면 서로 연동 가능.
* "Languages" -> Korean 에서 "Request to Join Team" 선택
* 한국어 번역 프로젝트 목록 : https://translate.openstack.org/language/view/ko-KR#
  * 75% 이상 번역된 문서는 나의 translation이 자동 반영된다.
  * 75% 이하는 release 주기에 맞추어 나중에 반영된다.
* Openstack 메일링 리스트 보기 : http://lists.openstack.org/cgi-bin/mailman/listinfo

### Openstack 거버넌스 / IRC 이용
* 기술위원회 : http://governance.openstack.org/
* IRC 접속하기 : http://docs.openstack.org/upstream-training/irc.html
  * 의사소통 관련 문서 : http://docs.openstack.org/ko_KR/upstream-training/howitsmade-communication.html#1
  * IRC 미팅 일정 페이지 : http://eavesdrop.openstack.org/
  * 이전 IRC 미팅 기록 열람하기 : http://eavesdrop.openstack.org/meetings/
    * 프로젝트 참가를 위해서는 meeting 회의록 및 agenda를 통해서 상황을 알아야 함.
  * 채널 내에서 미팅 시작하기 : #startmeeting upstream-korea(미팅 / 채널명)
    * 채널 내의 모든 사람들이 회의에 참가하게 되며, 호출을 통해 참석자들에게 알림을 보낸다.
    * 미팅이 시작되면 http://eavesdrop.openstack.org/meetings/ 에 디렉터리가 생성되며, 삭제되지 않으므로 미팅을 시작하기 전에 함부로 생성되지 않도록 주의하여야 한다.

### Git review와 Gerrit 이용하기
* git-review 설치(sudo apt install git-review)
* Upstream training github 주소 : http://docs.openstack.org/ko_KR/upstream-training/git.html
* git clone http://docs.openstack.org/ko_KR/upstream-training/git.html
* git config --global gitreview.username <username> (빼먹을 수 있으니 주의)
* git review -s (git review를 local repository에 설치한다.)

<pre><code>
Trying again with ssh://janghe11@review.openstack.org:29418/openstack-dev/sandbox.git
Creating a git remote called "gerrit" that maps to:
ssh://janghe11@review.openstack.org:29418/openstack-dev/sandbox.git
</code></pre>
와 같이 콘솔창에 출력되면 성공적으로 git review가 세팅되었다.

* Dummy file 추가하기
<code>cat > ko24.txt</code>
<code>git add ko24.txt</code>
<code>git commit -m "Test commit"</code>
<code>git review</code>
  * git review를 하고 나면 https://review.openstack.org/#/c/410196/ 와 같은 gerrit URL이 생성된다.

    * Permission Key Denied가 뜬 경우
     1. username을 입력하지 않았다.(git config --global gitreview.username <username>)
     2. Openstack Foundation Member가 아니다. (Community Member는 안됨.)
     3. Gerrit에서 ICLA Agree를 하지 않았다.
     4. Gerrit에서 Contact Information을 넣지 않았다.(review.openstack.org -> Contact information last updated on 이 있어야 함.)
     5. ssh key(id_rsa 말고) 를 다른 key file을 사용하였다.
     6. git config에 gerrit.review가 중복된 경우.

  * branch를 따서 작업하던 도중 다른 사람의 commit이 올라오면 rebase를 통해서 master를 갱신해준 다음 push를 해주어야 한다. (그렇지 않으면 Gerrit에서 Conflict가 발생한다.)
  * commit 여러개를 하고 싶으면 amend로 무조건 하거나 cherry pick으로 댕겨서 작업 후 올릴 것. 1 work에 1 commit
  * review에서 문제가 발생하여 patch set 2, 3을 해야하는 경우, 해당 커밋을 지우지 말고 그 상태에서 수정하여 push 한다.

### 과제
1. 연습을 위한 sandbox 읽어보고 오기.
2. 자신의 git review URL Slack에 공유.
3. 다른 사람 5명의 URL에 접속하여 점수를  -2 -1 0 1 2를 준다.
4. 다른 사람 URL에 접속하여 comment를 작성해 본다.
5. 혹시 자신에게 아무런 점수/comment가 주말까지도 없는 경우 임의로 스터디원 중 1명을 reviewer 초대
6. 자신의 패치가 마음에 들지 않으면 abandon을 하고 새로 올린다.
7. Exercise : 자신의 patchset 2 or 3 올리기.
