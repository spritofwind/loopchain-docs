# Tutorial
loopchain을 실제 설치하고 운영하는데 있어서 가장 중요한 설치 과정을 연습해보는 과정에 대해서 설명합니다. 

## 설치 준비

### Dockcer 설치하기 
Docker CE(Community Edition)X86_64, Docker EE(Enterprise edition) X86_64를 운용할 수 있는 최신 환경이면 됩니다.

* Docker CE: 무료 사용버전
* Docker EE: 상용 버전, 무료 Hosted Trial 사용 가능. 각종 OS들에 대한 지원 추가제공.
* 모든 상황에서 방법이 없으면 [Docker를 Binary로부터 설치할 수 있는 방법](https://docs.docker.com/install/linux/docker-ce/binaries/)이 있습니다.  


#### linux에서 Docker 설치하기 

|Platform|Docker CE X86_64|Docker EE X86_64|Note |
|----|----|----|----|
|CentOS|O|O|CentOS 7이상. [Installation Guide](https://docs.docker.com/install/linux/centos/) |
|Devian|O||Stretch (stable) / Raspbian Stretch Jessie 8.0 (LTS) / Raspbian Jessie Wheezy 7.7 (LTS). [Installation Guide](https://docs.docker.com/install/linux/docker-ce/debian/) |



















Donghanui-MacBook-Pro:~ donghanlee$ cat delete.sh 
#!/usr/bin/env bash

docker rm -f $(docker ps -a -q --filter name=loop-logger --filter name=radio_station --filter name=peer0)
Donghanui-MacBook-Pro:~ donghanlee$ 



Donghanui-MacBook-Pro:~ donghanlee$ cat start.sh 
#!/usr/bin/env bash

##############################################
#           환경변수등록
##############################################
export TAG=latest
export CONF=$(pwd)/conf
export LOGS=$(pwd)/logs
export FLUENTD=$(pwd)/fluentd
export STORAGE_RS=$(pwd)/storageRS
export STORAGE_PEER_0=$(pwd)/storage0

##############################################
#       로그 및 데이터 디렉토리 생성
##############################################
if [ ! -d ${LOGS} ]
    then    mkdir -p ${LOGS}
fi

if [ ! -d ${STORAGE_RS} ]
    then    mkdir -p ${STORAGE_RS}
fi

if [ ! -d ${STORAGE_PEER_0} ]
    then    mkdir -p ${STORAGE_PEER_0}
fi

##############################################
#           로그서버실행
##############################################
docker run -d \
--name loop-logger \
--publish 24224:24224/tcp \
--volume ${FLUENTD}:/fluentd \
--volume ${LOGS}:/logs \
loopchain/loopchain-fluentd:${TAG}

##############################################
# Radio Station 실행
##############################################
docker run -d --name radio_station \
-v ${CONF}:/conf \
-v ${STORAGE_RS}/storageRS:/.storage \
-p 7102:7102 \
-p 9002:9002 \
--log-driver fluentd --log-opt fluentd-address=localhost:24224 \
loopchain/looprs:${TAG} \
python3 radiostation.py -o /conf/rs_conf.json

##############################################
#           Peer0 실행
##############################################
docker run -d --name peer0 \
-v ${CONF}:/conf \
-v ${STORAGE_PEER_0}/storage0:/.storage \
--link radio_station:radio_station \
--log-driver fluentd --log-opt fluentd-address=localhost:24224 \
-p 7100:7100 -p 9000:9000  \
loopchain/looppeer:${TAG} \
python3 peer.py -o /conf/peer_conf.json  -r radio_station:7102

Donghanui-MacBook-Pro:~ donghanlee$ 