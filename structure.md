


## loopchain의 기본 구조

### 기본 구조도

![Peer Network](images/PeerNetwork.png)



### RadioStation

* Peer들의 인증을 담당하고 Peer들의 목록을 관리한다.


#### RadioStation과 Peer의 접속
* RadioStation과 Peer는 시작할 때에 자신의 인증서/개인키 경로를 입력한다.
* Peer 인증서 발급은 RadioStation에서 진행하고 Offline으로 전달 (Peer 인증서/개인키, Root 인증서)및 설치한다.

### Peer
* 블록 생성, 블럭관리, 트랜젝션 생성, 조회, 원장 조회등의 기능을 처리한다.
* Peer가 생성될 때에 RadioStation과 연결한다. 시작할 때에 RadioStation의 접속정보(IP:Port)를 가지고 연결한다.
* **가장 먼저 RadioStation에 연결되는 Peer가 Leader Peer가 된다.**
* 주의 : 최소 4개 이상의 Peer가 필요하다.

#### Leader Peer
* 일정 시간마다 Transaction 들을 모아 Block을 만들고 보낸 다음 검증을 Peer 들에게 받아서 공표한다(검증 주기는 설정 가능하다.)
* 다른 Peer를 Subscription(구독)한 다음에 Transaction / Block data를 동기화한다.
* Leader Peer의 변경은 등록된 Peer의 순서대로 Leader 권한을 준다(Round Robin). (주의: block 생성 개수 기준은 성능에 따라서 변경 가능하다.)
