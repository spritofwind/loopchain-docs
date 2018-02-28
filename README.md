# Introduction

## Why Private blockchain
비트코인이나 이더리움과 같이 공개된 블록체인 기술은 실제 엔터프라이즈 서비스에 적용하기 부족한 낮은 성능 및 데이터 공개 이슈가 있습니다.  이러한 문제를 해결하기 위해서 R3 Corda와 같은 프라이빗 블록체인이 개발되어지고 있습니다. 

프라이빗 블록체인의 특징

* 금융, 공공 등 규제 기반 거래에 적합한 거래방식 지원
* 허가된 사용자들의 합의과정을 통해 거래가 확정되므로 거래 확정의 주체가 명확하다.
* 규제 및 컴플라이언스 요소들을 만족시킬 수 있도록 커스터마이징 가능하게 설계
* 거래의 성격에 따라 합의 알고리즘, 블록생성 주기 등을 최적화하여서 빠른 속도가 필요한 거래에도 활용 가능

## What is loopchain?
![loopchain] (https://www.dropbox.com/s/r2qd652own9m4k6/new_theloop_ci.png?dl=1)

![Modular_Architecture] (https://www.dropbox.com/s/3xp8lvuo21cem57/loopchain_architecture.png?dl=1)

* Admin Layer는 주로 블록체인 네트워크 관리를 위해 노드의 장애 상황을 감독하고 SCORE(Smart Contract on Reliable Environment)의 버전 관리, 각 노드의 권한을 감독합니다. 
* Engine Layer는 블록 체인 노드의 주 역활인 분산합의, 원장 저장, 스마트 계약 실행을 담당합니다. 특히 분산합의를 위한 엔진 모듈인 Blockchain 과 실제 블록체인에 올라가는 서비스와 실행환경인 SCORE 가 분리되어 있습니다. 
* Interface Layer는 다른 비지니스 어플리케이션이 블록체인 네트워크에 접속할수 있는 환경을 만들어 줍니다. 

### loopchain 주요 특징



![loopchain 주요 특징] (https://www.dropbox.com/s/0ppa9vju109rb5g/loopchain_features.png?dl=1)


#### SCORE (**S**mart **C**ontract **o**n **R**eliable **E**nvironment)
SCORE는 loopchain에서 지원하는 Smart Contract을 지칭하는 것으로 별도의 VM(Virtual Machine) 없이 노드 운영환경에서 직접적으로 실행되는 고성능 Smart Contract 지원 기능입니다. 

* SCORE는 쉽게 작성할 수 있어서 높은 개발 생산성을 가진 Smart Contract입니다.
* SCORE는 블록체인 프로세스와 별도의 프로세스로 동작하면서 다양한 업무를 개발할 수 있도록 지원합니다. 
* SCORE store를 통한 등록, 배포 및 버전 관리를 제공합니다. 


![SCORE] (https://www.dropbox.com/s/nxi60f9kae8xf5e/SCORE_structure.png?dl=1)

#### LFT algorithm 
 LFT(loopchain Fault Tolerance) algorithm은 BFT(Byzantine Fault Tolerance) 계열의 알고리즘으로 분기가 없는 빠른 합의를  지원합니다. BFT 계열 합의 알고리즘은 머신의 개수나, 지분을 통하여 투표를 하여 합의하는 방식으로 에너지 낭비가 없고 즉각적인 합의가 가능하다는 장점이 있습니다. 

* plug-in 형태로 합의 알고리즘이 구현되어 있기 때문에 필요에 따라 PBFT(Pratical Byzantine Fault Tolerance)와 같은 다른 합의 알고리즘을 사용 가능 
* 기존 PBFT를 사용하는 합의 알고리즘에서 발생하는 통신 오버헤드를 Piggybacking(네트워크에서 메시지를 통합하여 통신 오버헤드를 감소시키는 방법)을 이용하여 감소
* Spinning(리더를 매번 교체하는 기법) 기법을 이용하여 일정한 갯수의 블록 생성 시 마다 리더를 교체하여 비잔틴 리더에 발생할수 있는 서비스 장애 요소(특정 노드의 트랜젝선을 거부하는 문제, 리더가 매번 시간초과 시간에 맞춰 블록을 생성하려는 시도에 대한 피해)를 최소화 
* 기존 알고리즘들이 가지고 있는 지나치게 복잡한 리더 선정 알고리즘을 단순화

![LFT] (https://www.dropbox.com/s/wzm9duhmujr0rey/LFT_Normal_Process.png?dl=1)


[LFT 알고리즘의 합의 과정]

*  네트워크가 시작되면 검증노드(검증을 통해 합의에 참혀하는 노드)들은 사전에 결정되어 있는 리더 노드에게 처리를 원하는 트랜젝션을 전송
*  리더 노드는 수집한 트랜잭션을 이용하여 블록을 생성하고 자신의 서명과 함께 모든 검증 노드에게 전송
*  각 검증 노드들은 블록을 받으면 다음의 순서로 블록을 검증합니다. **1) 현재의 리더가 블록을 생성했는지 확인. 2) 블록 높이와 이전 블록 해시가 올바른지 확인. 3) 블록의 데이터가 올바른지 확인.** 검증 노드는 검증과정이 옳다면 Vote 데이터를 생성하여 네트워크의 모든 노드들에게 전파합니다.

Vote 데이터를 전체 노드에게 전파하는 것은 매우 중요한데 이는 리더 노드가 비잔틴일 경우 정족 수 이상의 노드들에게만 블록을 전파하여 특정 노드들을 네트워크로부터 분리하도록 시도할 수 있기 때문입니다. 이러한 문제를 방지하기 위해 모든 Peer에게 Vote 데이터를 전파하며 이는 기존 Raft 알고리즘과의 다른 점입니다. 이 과정에서 블록을 못받은 노드는 블록이 생성되었는지에 대한 정보를 알 수 있고 다른 노드에게 블록을 요청할수 있습니다. 

#### Multi Channel

Multi Channel은 하나의 독립적인 블록체인 네트워크 안에서 업무별로 채널이라는 가상의 네트워크를 구성하여 채널 별로 거래 요청, 합의 및 Smart Contract를 수행할 수 있는 기능입니다. 

하나의 노드에서 여러 업무별 당사자들만 연결된 다양한 업무별 채널을 형성하기 때문에 채널별로 무결성 보장 및 합의가 이루어집니다. 따라서 거래 데이터가 실제 거래 당사자들만 보유하게 되어 다양한 규제에 대응할 수 있습니다. 
![Multi Channel] (https://www.dropbox.com/s/w175xyo3tm6x3mu/MultiChannel.png?dl=1)


#### Tiered Channel 

블록체인 네트워크에 참여시 인증과 함께 거래별로 PKI 기반 인증을 통해 거래 내역 검증 및 보안이 이뤄집니다. 인증된 기관만 참여시키며 각 참가자에게 차등적 권한을 부여함으로서 다양한 엔터프라이즈 업무 환경에 적합한 시스템 구현이 가능합니다. 

거래에 참여하지 않지만 필요에 따라 거래 내역을 감사할 수 있는 기능을 특정 노드에 부여를 하여 감사만을 위한 노드 생성이 가능하므로 금융 시스템이 요구하는 Compliance 기능을 제공합니다.  

* 다른 권한을 가진 인증서 배포. 블록 체인 참여자는 정보 확인 및 관리에 대해서 각각 다른 권한을 가집니다. 
* 검증 노드, 트랜젝션 생성 노드 등의 특정 노드 생성 가능


#### Modular Architecture

모듈 방식 아키텍처를 채택하여 참여 노드 인증 및 합의 알고리즘, Smart Contract 모듈 등을 필요한 경우에 추가 및 커스터마이징이 가능합니다.
![Modular_Architecture] (https://www.dropbox.com/s/3xp8lvuo21cem57/loopchain_architecture.png?dl=1)




## loopchain의 기본 설명

### 기본 구조도 

![Peer Network] (https://www.dropbox.com/s/bdzjajr5ucc119s/PeerNetwork.png?dl=1)

_구조도는 검토가 필요함. Leader Peer는 순차적으로 돌아가기 때문에 변경이 가능하다는 것을 표시해줘야 하나?_

### RadioStation

* Peer들의 인증을 담당하고 Peer들의 목록을 관리한다. 
* 모든 Peer의 인증서를 보관한다. 
* Group ID 생성. 여러 Peer들이 속할 Group ID를 생성해 준다. 

#### RadioStation과 Peer의 접속 
* RadioStation과 Peer는 시작할 때에 자신의 인증서/개인키 경로를 입력한다. 
* Peer 인증서 발급은 RadioStation에서 진행하고 OfflLine으로 전달 (Peer 인증서/개인키, Root 인증서)및 설치한다. 
* Peer에서 RadioStation으로 접속할 때에 보안 시퀸스를 따른다. 

### Peer
* 블록 생성, 블럭관리, 트랜젝션 생성, 조회, 원장 조회등의 기능을 처리한다. 
* Peer가 생성될 때에 RadioStation과 연결한다. 시작할 때에 RadioStation의 접속정보(IP:Port)를 가지고 연결한다. 
* **가장 먼저 RadioStation에 연결되는 Peer가 Leader Peer가 된다.**
* Peer는 RadioStation 인증서(Root)를 보관한다. 

#### Leader Peer 
* 일정 시간마다 Transaction 들을 모아 Block을 만들고 보낸 다음 검증을 Peer 들에게 받아서 공표한다(검증 주기는 설정 가능하다.)
* 다른 Peer를 Subscription(구독)한 다음에 Transaction / Block data를 동기화한다.
* Leader Peer의 변경은 등록된 Peer의 순서대로 Leade 권한을 준다(Round Robin). 1000개의 Block을 만들고 다음 Peer에게 Leader권한을 넘긴다.(주의: 이 block 생성 갯수 기준은 성능에 따라서 변경 가능하다.)
* Leader peer 변경시 검증을 보내지 못한 Block이 있는 경우에 처리하지 못한 Block이 있다면, Block안의 Transaction을 모두 다음 Leader peer에게 보낸다. 

#### Leader Peer의 업무처리 순서
* Leader Peer가 Boradcast message 를 보낸다.
* 다른 Peer들은 Leader Peer의 Broadcast Message를  Subscribe(구독)한다)
* Leader Peer를 Subscribe 한 모든 Peer는 Leader의 Broadcast Message를 받게 된다. 
* Leader Peer를 제외한 모든 Peer는 Transaction을 Leader에게 전송한다. 
* Leader Peer는 일정시간마다 Transaction을 모아 Block을 만들고 보낸 다음 검증을 Peer들에게 받아서 공표한다.(검증 주기는 설정이 가능하다)




# Tutorial
## Installation

# SCORE
## SCORE 작성 1
## SCORE 작성 2

# FAQ

# Reference
## loopchain API-RESTful - peer
## loopchain API-RESTful - radiostation