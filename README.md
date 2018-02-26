# Introduction

## Why Private blockchain
비트코인이나 이더리움과 같이 공개된 블록체인 기술은 실제 엔터프라이즈 서비스에 적용하기 부족한 낮은 성능 및 데이터 공개 이슈가 있습니다.  이러한 문제를 해결하기 위해서 R3 Corda와 같은 프라이빗 블록체인이 개발되어지고 있습니다. 

프라이빗 블록체인의 특징

* 금융, 공공 등 규제 기반 거래에 적합한 거래방식 지원
* 허가된 사용자들의 합의과정을 통해 거래가 확정되므로 거래 확정의 주체가 명확하다.
* 규제 및 컴플라이언스 요소들을 만족시킬 수 있도록 커스터마이징 가능하게 설계
* 거래의 성격에 따라 합의 알고리즘, 블록생성 주기 등을 최적화하여서 빠른 속도가 필요한 거래에도 활용 가능

## What is loopchain?
![loopchain] (https://www.dropbox.com/s/wuuqcz8tdxdm2l4/new_theloop_ci.png?dl=1)



### loopchain 주요 특징

![loopchain 주요 특징] (https://www.dropbox.com/s/wq8qxav3ivvmtm3/loopchain_features.png?dl=1)


#### SCORE (**S**mart **C**ontract **o**n **R**eliable **E**nvironment)
SCORE는 loopchain에서 지원하는 Smart Contract을 지칭하는 것으로 별도의 VM(Virtual Machine) 없이 노드 운영환경에서 직접적으로 실행되는 고성능 Smart Contract 지원 기능입니다. 

* SCORE는 쉽게 작성할 수 있어서 높은 개발 생산성을 가진 Smart Contract입니다.
* SCORE는 블록체인 프로세스와 별도의 프로세스로 동작하면서 다양한 업무를 개발할 수 있도록 지원합니다. 
* SCORE store를 통한 등록, 배포 및 버전 관리를 제공합니다. 


![SCORE] (https://www.dropbox.com/s/8mo8pzrc3k9t1un/SCORE_structure.png?dl=1)

#### LFT(loopchain Fault Tolerance) algorithm 
 LFT algorithm은 BFT(Byzantine Fault Tolerance) 계열의 알고리즘으로 분기가 없는 빠른 합의를  지원합니다. BFT 계열 합의 알고리즘은 머신의 개수나, 지분을 통하여 투표를 하여 합의하는 방식으로 에너지 낭비가 없고 즉각적인 합의가 가능하다는 장점이 있습니다. 

* plug-in 형태로 합의 알고리즘이 구현되어 있기 때문에 필요에 따라 PBFT(Pratical Byzantine Fault Tolerance)와 같은 다른 합의 알고리즘을 사용 가능 
* 기존 PBFT를 사용하는 합의 알고리즘에서 발생하는 통신 오버헤드를 Piggybacking(네트워크에서 메시지를 통합하여 통신 오버헤드를 감소시키는 방법)을 이용하여 감소
* Spinning(리더를 매번 교체하는 기법) 기법을 이용하여 매 블록 생성 시 마다 리더를 교체하여 비잔틴 리더에 발생할수 있는 서비스 장애 요소(특정 노드의 트랜젝선을 거부하는 문제, 리더가 매번 시간초과 시간에 맞춰 블록을 생성하려는 시도에 대한 피해)를 최소화 
* 기존 알고리즘들이 가지고 있는 지나치게 복잡한 리더 선정 알고리즘을 단순화

LFT 알고리즘의 합의 과정


![LFT] (https://www.dropbox.com/s/npcs7e3p5sfmdb3/LFT_Normal_Process.png?dl=1)

*  네트워크가 시작되면 검증노드(검증을 통해 합의에 참혀하는 노드)들은 사전에 결정되어 있는 리더 노드에게 처리를 원하는 트랜젝션을 전송
*  리더 노드는 수집한 트랜잭션을 이용하여 블록을 생성하고 자신의 서명과 함께 모든 검증 노드에게 전송
*  각 검증 노드들은 블록을 받으면 다음의 순서로 블록을 검증합니다. **1) 현재의 리더가 블록을 생성했는지 확인. 2) 블록 높이와 이전 블록 해시가 올바른지 확인. 3) 블록의 데이터가 올바른지 확인.** 검증 노드는 검증과정이 옳다면 Vote 데이터를 생성하여 네트워크의 모든 노드들에게 전파합니다.

Vote 데이터를 전체 노드에게 전파하는 것은 매우 중요한데 이는 리더 노드가 비잔틴일 경우 정족 수 이상의 노드들에게만 블록을 전파하여 특정 노드들을 네트워크로부터 분리하도록 시도할 수 있기 때문입니다. 이러한 문제를 방지하기 위해 모든 Peer에게 Vote 데이터를 전파하며 이는 기존 Raft 알고리즘과의 다른 점입니다. 이 과정에서 블록을 못받은 노드는 블록이 생성되었는지에 대한 정보를 알 수 있고 다른 노드에게 블록을 요청할수 있습니다. 

#### Multi Channel

Multi Channel은 하나의 독립적인 블록체인 네트워크 안에서 업무별로 채널이라는 가상의 네트워크를 구성하여 채널 별로 거래 요청, 합의 및 Smart Contract를 수행할 수 있는 기능입니다. 

하나의 노드에서 여러 업무별 당사자들만 연결된 다양한 업무별 채널을 형성하기 때문에 채널별로 무결성 보장 및 합의가 이루어진다. 따라서 거래 데이터가 실제 거래 당사자들만 보유하게 되어 다양한 규제에 대응할 수 있습니다. 

#### Tiered Channel 

블록체인 네트워크에 참여시 인증과 함께 거래별로 PKI 기반 인증을 통해 거래 내역 검증 및 보안이 이뤄집니다. 또한, 거래에 참여하지 않지만 필요에 따라 거래 내역을 감사할 수 있는 기능을 특정 노드

## loopchain의 기본 설명

## Roadmap of Loopchain

## Community
## Contributors

# Tutorial
## Installation

# SCORE
## SCORE 작성 1
## SCORE 작성 2

# FAQ

# Reference
## loopchain API-RESTful - peer
## loopchain API-RESTful - radiostation