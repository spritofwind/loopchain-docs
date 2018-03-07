## Local computer에서 SCORE개발 환경 만들기 Tutorial

### SCORE 저장소 생성

1. Github에서 SCORE Sample 프로젝트(https://github.com/theloopkr/contract_sample) 를 fork 하여서 SCORE 개발 환경을 위한 테스트용 SCORE 저장소를 생성합니다. ![github](/images/github_contract-sample.png)
2. SCORE 저장소와 SSH통신을 위해 SSH 키쌍을 생성합니다. `ssh-keygen`명령어를 사용하셔서 `id_tutorial`이라는 이름으로 생성합니다.
```
  $ ssh-keygen
  Generating public/private rsa key pair.
  Enter file in which to save the key (/Users/donghanlee/.ssh/id_rsa): /Users/donghanlee/.ssh/id_tutorial
  Enter passphrase (empty for no passphrase):
  Enter same passphrase again:
  Your identification has been saved in /Users/donghanlee/.ssh/id_tutorial.
  Your public key has been saved in /Users/donghanlee/.ssh/id_tutorial.pub.
  The key fingerprint is:
  SHA256:K7FLYMr1+p0184oVjUak6KAS9rFULXQPf0lwVzVB76k donghanlee@Donghanui-MacBook-Pro.local
  The key's randomart image is:
  +---[RSA 2048]----+
  |    .o.o .oo .o=+|
  |    ...o+oo o   o|
  |.. o. o .o.o    .|
  |..o.oo   ..o   ..|
  |. .o+ o S + .  ..|
  | o + o o o .  .  |
  |  o   = . =  E   |
  |     o + = +     |
  |    ..o + ...    |
  +----[SHA256]-----+
  $ ls -la ~/.ssh/ | grep id_tutorial
  -rw-------   1 donghanlee  staff  1679  3  7 09:45 id_tutorial
  -rw-r--r--   1 donghanlee  staff   420  3  7 09:45 id_tutorial.pub
  $
```

3. Github SCORE 저장소에 SSH public key 내용을 (예:id_tutorial.pub)를 등록합니다.
  ```
  $ cat .ssh/id_tutorial.pub
  ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCbMFh3PpVqjoHzV8KTLwGayUhX4GzcIBDGNuvbhNUJzU9o/1Ua2htKnFrQAN7DhWhTlLtVozBDl8o8vQck83KvSIdFnG7bruF5ch/k8vSMn8jBYrNuz3s3566NT63D73lf4sLZ/LP5Lz4EQGZnGlgYpSrykJW61u+BxNDxTeT39rKR97C9/bhwMK18pjLbw9M+Q7G54kAe1ak3anpqUxVNTWWGkJ4hWTH7eDJeIwyScZqAw+6NhiBc2bU1dl1gbworaQO528oJAx+W32+jsbe/AtxceqYp5vkN2+WFuMRsNKVuwxSGk0dhwOwJXt5o8aJ9/knT46LE8Y3lKeBvAf6V donghanlee@Donghanui-MacBook-Pro.local
  $
  ```
  ![ssh public key](/images/github_add_sshkey.png)



### 환경설정
[`Local computer에서 RadioStation과 2개의 Peer로 Blockchain network 구성하기`](Tutorial_1R2P.md) 내용을 기반으로 SCORE 환경설정을 하겠습니다.

1. RadioStation 설정 - `channel_manage_data.json`

  SCORE 저장소 경로 설정 "score_package":"{your_github_id}/contract_sample"
  **기존 `channel_manage_data.json` 파일 내용**
  ```
  {
  	"channel1": {
  		"score_package": "loopchain/default"
  	}
  }
  ```
  **변경 `channel_manage_data.json`파일 내용**
  ```
  {
    "channel1":
      {
        "score_package": "{your_github_id}/contract_sample"
      }
  }
  ```
2.  시작 스크립트인 start.sh 스크립트의 peer 부분을 수정
  1. SSH key 경로설정 `-v "${SSH_KEY_FOLDER}:/root/.ssh/id_tutorial"`
  2. SCORE 저장소 도메인 설정 `-e "DEFAULT_SCORE_HOST=github.com"

  **기존 `start.sh` 파일의 일부 내용**

  ```
  ##############################################
  #           Peer0 실행
  ##############################################
  docker run -d --name peer0 \
  -v $(pwd)/conf:/conf \
  -v $(pwd)/storage0:/.storage \
  --link radio_station:radio_station \
  --log-driver fluentd --log-opt fluentd-address=localhost:24224 \
  -p 7100:7100 -p 9000:9000 \
  loopchain/looppeer:${TAG} \
  python3 peer.py -o /conf/peer_conf0.json -p 7100 -r radio_station:7102

  ##############################################
  #           Peer1 실행
  ##############################################
  docker run -d --name peer1 \
  -v $(pwd)/conf:/conf \
  -v $(pwd)/storage1:/.storage \
  --link radio_station:radio_station \
  --log-driver fluentd --log-opt fluentd-address=localhost:24224 \
  -p 7200:7200 -p 9100:9100 \
  loopchain/looppeer:${TAG} \
  python3 peer.py -o /conf/peer_conf1.json -p 7200 -r radio_station:7102
  ```
  **변경 `start.sh`파일 내용**

    ```
    ##############################################
    #           Peer0 실행
    ##############################################
    docker run -d --name peer0 \
    -v $(pwd)/conf:/conf \
    -v $(pwd)/storage0:/.storage \
    --link radio_station:radio_station \
    --log-driver fluentd --log-opt fluentd-address=localhost:24224 \
    -p 7100:7100 -p 9000:9000 \
    loopchain/looppeer:${TAG} \
    python3 peer.py -o /conf/peer_conf0.json -p 7100 -r radio_station:7102

    ##############################################
    #           Peer1 실행
    ##############################################
    docker run -d --name peer1 \
    -v $(pwd)/conf:/conf \
    -v $(pwd)/storage1:/.storage \
    --link radio_station:radio_station \
    --log-driver fluentd --log-opt fluentd-address=localhost:24224 \
    -p 7200:7200 -p 9100:9100 \
    loopchain/looppeer:${TAG} \
    python3 peer.py -o /conf/peer_conf1.json -p 7200 -r radio_station:7102
    ```
