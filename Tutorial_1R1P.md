# Local computer에서 RadioStation과 1개의 Peer로 Blockchain network 구성하기





## 목적
자신의 컴퓨터상에서 Docker를 이용하여 RadioStation과 Peer 1개로 구성된 네트워크 환경을 구성하고 정상적으로 동작하는지 테스트를 합니다. 다음의 그림과 같은 구조로 네트워크가 구성이 됩니다.

![RadioStation & 1 peer](https://www.dropbox.com/s/72mzupkeq5snre1/1Radio1Peer.png?dl=1)

## 디렉토리 구성
이 문서의 내용을 따라하게 되면 다음과 같은 디렉토리 구성이 만들어지게 됩니다.

![RadioStation & 1 peer](https://www.dropbox.com/s/jcycqo9wif7rdtc/1Radio1Peer_Directory.png?dl=1)

## 설정 파일 생성
###  디렉토리 생성
먼저 로그서버의 설정 파일 폴더를 생성하고 로그를 따로 저장할 폴더를 생성한다. 그리고 RadioStation과  Peer의 설정파일을 따로 보관할 폴더를 만든다.
```
$ mkdir -p fluentd/etc.   	# 로그서버의 설정 파일 폴더를 생성.
$ mkdir logs.			# 로그를 따로 저장할 폴더를 생성.
$ mkdir conf			# RadioStation과  Peer의 설정파일을 따로 보관할 폴더를 생성.
```

### 설정 파일 생성 및 이동

#### log 서버의 설정
log 서버의 설정 파일을 작성하고 설정 파일 위치로 이동합니다. 이것은 모든 log 들을 파일로 남기는 설정입니다.

 1. ```fluent.conf```을 아래와 같이 만듭니다.

 ```
 <source>
 	@type forward
 	@id input1
 	port 24224
 	bind 0.0.0.0
 </source>

 <match **> # Add your log tag to show in <>.
 	@type copy
 	<store> # Add your log tag to show in <>.
 		@type file # Leave log file in path.
 		path /logs/data.*.log
 		symlink_path /logs/data.log time_slice_format %Y%m%d
 		time_slice_wait 10m
 		time_format %Y%m%dT%H%M%S%z
 		compress gzip
 		utc
 	</store>
 </match>
 ```

 2. 작성된 `fluent.conf`를 `fluentd/etc` 디렉토리로 이동합니다.
```
$ mv fluent.conf ./fluentd/etc
```

#### ```channel_manage_data.json``` 작성
이 설정 파일은 multichannel을 사용할 때에 설정하는 파일입니다. 해당 파일은 RadioStation과 Peer 모두에게 필요합니다.

 1. ```channel_manage_data.json``` 파일을 아래와 같이 작성합니다.
```
{
	"channel1": {
		"score_package": "loopchain/default"
	}
}
```

 2. 



---

**`channel_manage_data.json` 작성**
이 설정 파일은 multichannel을 사용할 때에 설정하는 파일입니다. 해당 파일은 RadioStation과 Peer 모두에게 필요합니다.

`channel\manage\data.json` 파일을 작성합니다.
```
$ touch channel\manage\data.json.
$ printf '{"channel1": {"score\package": "loopchain/default"} } \n' > channel\manage\data.json
```
설정 파일의 내용은 'channel1'이라는 channel에 loopchain/default라는 SCORE를 이용하는 것입니다. loopchain/default는 기본적으로 각 peer들이 가지고 있는 SCORE파일입니다.

channel\manage\data.json 파일을 conf 디렉토리로 이동합니다.
```
$ mv channel\manage\data.json ./conf
```

---

**`rs_conf.json` 작성**
이 설정 파일은 RadioStation의 설정들을 담고 있는 파일입니다.
```
$ touch rs_conf.json
$ printf '{"CHANNEL_MANAGE_DATA_PATH": "/conf/channel_manage_data.json", "LOOPCHAIN_DEFAULT_CHANNEL": "channel1","ENABLE_CHANNEL_AUTH": false}\n' > rs\conf.json
```




----

* `peer_conf.json` 작성

-----

## Log server 설정하기
가장 먼저 로그 서버를 구성하고




```
git status
git add
git commit
```

____


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
