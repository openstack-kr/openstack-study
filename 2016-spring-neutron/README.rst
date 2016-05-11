2016년 상반기 B반: Neutron 스터디
+++++++++++++++++++++++++++++++++

This repository contains study data that was used to learn
OpenStack Neutron in Korea user group as study activities.

스터디 개요 (Overview)
======================

 * 중급 스터디 주제 : OpenStack Neutron Networking Deep Dive

 * 진행 방식 :

   * 아래 각 Chapter별로 1주차 분량으로 진행 + 추가 ( 스터디 멤버와 협의후 선정 )

     * 매주 전체 멤버는 해당 Chapter의 내용을 완료한후 Offline 스터디에 참여하여야 함.
     * 각 멤버는 매주 해당 내용에 대해서 1명씩 발표
     * 사전에 아래 해당 도서를 각자 사전 구매하여야 함.

   * PACKT Learning OpenStack Networking(Neutron)

     * (한글제목: 클라우드 네트워크 인프라 구축을 위한 Neutron 오픈스택 네트워킹)

   * 내용
     
     * Chapter 1. Preparing the Network for OpenStack
     * Chapter 2. Installing OpenStack
     * Chapter 3. Installing Neutron
     * Chapter 4. Building a Virtual Switching Infrastructure
     * Chapter 5. Creating Networks with Neutron
     * Chapter 6. Creating Routers with Neutron
     * Chapter 7. Load Balancing Traffic in Neutron
     * Chapter 8. Protecting Instances on the Network
     * ...

 * 진행 기간 : 총 10주차

Day 1: Apr 7, 2016
==================

 * 내용

   * 스터디 안내 (최영락)
   * Chapter 1. Preparing the Network for OpenStack (박형석)

 * 참고 자료

   * OpenStack 설치 가이드 (Liberty) 한글 [draft]

     * SUSE 계열: http://docs.openstack.org/liberty/ko_KR/install-guide-obs/
     * Redhat 계열: http://docs.openstack.org/liberty/ko_KR/install-guide-rdo/
     * Ubuntu 계열: http://docs.openstack.org/liberty/ko_KR/install-guide-ubuntu/

 * 다음 주까지 준비 사항

   * 책 1장-3장까지 살펴보고
   * Kilo 기준으로, All-in-one + Compute node 추가 설치합니다.

 * 발표 관련

   * 각 발표는 최대 30분을 넘기지 않는 것으로 합시다.
   * 발표 일정 변경 등이 있을 경우 미리 이야기 부탁드립니다.
   * 주제가 너무 무겁지 않아도 되니 (예: 책 ~~를 살펴보았다. 설치했는데 실패했다.
     명령어 A는 알겠는데 B는 모르겠다) 편하게 준비 부탁드립니다.
   
Day 2: Apr 14, 2016
===================

 * 내용

   * Chapter 2. Installing OpenStack
   * Chapter 3. Installing Neutron

 * 자료

   * 권영부: (업데이트 예정) 
   * 박준상: (업데이트 예정)

 * 참고 자료

   * nicholas, `Installation Guide for OpenStack Kilo MultiNode + Docker <https://www.evernote.com/shard/s15/sh/a96599f5-6b07-4db7-9396-2658261fa411/5c209a6dcf7459fbc57dea9e3ec1ed72>`_

Day 3: Apr 21, 2016
===================

 * 내용

   * Chapter 4. Building a Virtual Switching Infrastructure

 * 자료

   * 성호찬: `발표 자료 <materials/20160509_sunh.docx>`_
   * 최광훈: (업데이트 예정)

 * 참고 자료

   * codetree, `rdo_multi_node_setup_study.txt <materials/rdo_multi_node_setup_study.txt>`_
   * ianychoi, `Chapter 4 부분 설명 (책 내용 참고) <materials/20160421_ianychoi.pdf>`_

Day 4: Apr 28, 2016
===================

 * 내용

   * Chapter 5. Creating Networks with Neutron

 * 자료

   * 장지태: `발표 자료 <materials/20160428_jtjang.pptm>`_

 * 참고 자료

   * wikitree, `VLAN <https://wikibootup.gitbooks.io/network/content/vlan.html>`_
   * heavenkong, `[Resolved] Mitaka Linux Bridge Agent Error Linux Bridge Agent out of sync with plugin !
     <http://heavenkong.blogspot.kr/2016/04/resolved-mitaka-linux-bridge-agent.html>`_

Day 5: May 12, 2016
===================

 * 내용

   * Chapter 6. Creating Routers with Neutron

 * 자료

   * (업데이트 예정)

Day 6: May 19, 2016
===================

Day 7: May 26, 2016
===================

Day 8: Jun 2, 2016
==================

Day 9: Jun 9, 2016
==================

Day 10: Jun 16, 2016
====================

Contributing
============

Our community welcomes all people interested in open source cloud
computing, and encourages you to join the `OpenStack Foundation
<http://www.openstack.org/join>`_.

The best way to get involved with the community is to talk with others
online or at a meet up and offer contributions through our processes,
the `OpenStack wiki <http://wiki.openstack.org>`_, blogs, or on IRC at
``#openstack`` on ``irc.freenode.net``.

We welcome all types of contributions, from blueprint designs to
documentation to testing to deployment scripts.

If you would like to contribute to the documents, please see the
`Documentation HowTo <https://wiki.openstack.org/wiki/Documentation/HowTo>`_.


Bugs
====

Bugs should be filed on Launchpad, not GitHub:

   https://bugs.launchpad.net/openstack


Installing
==========
Refer to http://docs.openstack.org to see where these documents are published
and to learn more about the OpenStack project.
