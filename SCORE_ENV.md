## Local computer에서 SCORE 환경 만들기 Tutorial

### SCORE 저장소 생성

1. Github에서 SCORE Sample 프로젝트(https://github.com/theloopkr/contract_sample) 를 fork 하여서 SCORE 개발 환경을 위한 테스트용 SCORE 저장소를 생성합니다. ![github](/images/github_contract-sample.png)
2. SCORE 저장소와 SSH통신을 위해 SSH 키쌍을 생성합니다. `ssh-keygen`명령어를 사용하셔서 `id_tutorial`이라는 이름으로 생성합니다. 아래 화면을 참고하시고 Github의 자신의 이메일주소로 SSH키를 생성하셔야 합니다. 상세한 내용은 다음의 링크를 참고해주세요. 만약 Sierra 10.12.2 혹은 그 이후의 MacOS를 사용하시는 분들은 꼭 아래 내용을 참고하셔서 추가적으로 진행하셔야 하는 내용이 있으니 꼭 확인하여 주세요.
  https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/

  ```
  $ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
  Generating public/private rsa key pair.
  Enter file in which to save the key (/Users/donghanlee/.ssh/id_rsa): /Users/donghanlee/.ssh/id_tutorial
  Enter passphrase (empty for no passphrase):
  Enter same passphrase again:
  Your identification has been saved in /Users/donghanlee/.ssh/id_tutorial.
  Your public key has been saved in /Users/donghanlee/.ssh/id_tutorial.pub.
  The key fingerprint is:
  The key fingerprint is:
  SHA256:oBGzxiaAM0etn1jMyyAFRq4aQPWa64WPhTpRKpUmU/0 your_email@example.com
  The key's randomart image is:
  +---[RSA 4096]----+
  |=*o+o            |
  |B.+.++           |
  |.B.=*o.          |
  |* *=*oE.         |
  |o*oO.o  S        |
  |o+. O            |
  |o .+ o           |
  | .o =            |
  | ..o .           |
  +----[SHA256]-----+
  $ ls -la ~/.ssh/ | grep id_tutorial
  -rw-------   1 donghanlee  staff  1679  3  7 09:45 id_tutorial
  -rw-r--r--   1 donghanlee  staff   420  3  7 09:45 id_tutorial.pub
  $
  ```

3. Github SCORE 저장소에 SSH public key 내용을 (예:id_tutorial.pub)를 등록합니다.
  ```
  $ cat .ssh/id_tutorial.pub
  ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCgVxiE+wlEbxdWXzaV5v9Q/LdMG0E0UBxX8j9ImSus4bSSn4ylKsjxKUONMT2eZuzYjz4uCnmLl4OUkEU4RV8lE6F8VOZyxjQebaZJuk2S+6Tk03Oy/zs7nHsbW8BUEoqNB4UTpmhMkqi/iQGLf6UvfhwwEiWIZB3CYQgbghMkm1z/xKYdgMMNO/MxxBnOlkJzhc9BgcpWAnDn79f9feufGZ8Iafdrb38ire0X9kGB6K6lFbHGNk/FUa2FQgnQ1ELC41QS+A2ooLRW6vZm4JKlU8gX5CWWbaKAALgoxzZO09PulDkRRl0FCc//x353QkZmPKf6wl1u42r21veQH4dKL2d1rWkXVP0FLH5kW/rl/YURo4Oxw6p6WQwGWGjcf9+uxPec0S+gtJ9TktMfLGJpwav8Rl8VLw3IYdHKAVtL3NSN5wYn2BGhExxQ760TlAXc1W0prlxfzeh4KglMCXKEZUI5qoOvWWDvE/pRGOOtDY4u5+n5bJebipXqr9V/xDtrsfcz54Zs9qFliUDhhMSTVGdQsVZIlCZS54khL0urrD0c7R7YovL/gugAbIHaHRGG4CQJ7krVhVP3iymwCW7WDpVBGAJ3mSr43ONQqa2OsGrvoo5817yzjlcyvYBx/NNPhBQygz1MJg9tHqQsRsCcsQHUkJAQfMHQx8RZSAHn3Q== your_email@example.com
  $
  ```
  ![ssh public key](/images/github_add_sshkey.png)



### 환경설정
[`Local computer에서 RadioStation과 2개의 Peer로 Blockchain network 구성하기`](Tutorial_1R2P.md) 내용을 기반으로 SCORE 환경설정을 하겠습니다. 해당 파일과 디렉토리를 복사하여서 다른 디렉토리로 만들고 아래 내용대로 추가 하거나 수정합니다.

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
  1. 환경변수 추가 등록 `export SSH_KEY_FOLDER=/Users/{user_id}/.ssh/id_tutorial`
  2. SSH key 경로설정 `-v "${SSH_KEY_FOLDER}:/root/.ssh/id_tutorial"`
  2. SCORE 저장소 도메인 설정 `-e "DEFAULT_SCORE_HOST=github.com"`

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
  **추가되고 변경된  `start.sh`파일 내용**

    ```
    ##############################################
    #           환경변수등록
    ##############################################
    export SSH_KEY_FOLDER=/Users/{user_id}/.ssh/id_tutorial

    ##############################################
    #           Peer0 실행
    ##############################################
    docker run -d --name peer0 \
    -v $(pwd)/conf:/conf \
    -v $(pwd)/storage0:/.storage \
    -v ${SSH_KEY_FOLDER}:/root/.ssh/id_rsa \
    -e "DEFAULT_SCORE_HOST=github.com" \
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
    -v ${SSH_KEY_FOLDER}:/root/.ssh/id_rsa \
    -e "DEFAULT_SCORE_HOST=github.com" \
    --link radio_station:radio_station \
    --log-driver fluentd --log-opt fluentd-address=localhost:24224 \
    -p 7200:7200 -p 9100:9100 \
    loopchain/looppeer:${TAG} \
    python3 peer.py -o /conf/peer_conf1.json -p 7200 -r radio_station:7102
    ```



### 환경 설정 확인

1. loopchain 도커 컨테이너를 모두 실행: `start.sh`
  ```
  $ ./start.sh
  7bbbac55f38b99b5206e2d84dec027b1d08e6cd7099bba2ca89f9c6f23d9841a
  3811b3c6193d79a4dff3535d57b7d1c635023e7dc971eeba5564138971673f9d
  395e37133d60fd7852b31f4fa8a05c9e1882f8126f529221e72e5efb9609c65d
  6457ba6460052cfcd9ec0cc71135bb68d6ed1bf77fdb3add1f33ae12ed325d4f
  $
  ```
2. peer 목록 조회
  ```
  $ curl http://localhost:9002/api/v1/peer/list | python -m json.tool
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
  100  1573  100  1573    0     0  24681      0 --:--:-- --:--:-- --:--:-- 24968
  {
      "data": {
          "connected_peer_count": 2,
          "connected_peer_list": [
              {
                  "cert": "MFYwEAYHKoZIzj0CAQYFK4EEAAoDQgAE+HQPBowjyJnyinsYjiztl5i6hQ1JiWdpRmyFR1T283M4liQia7weerQQ4Qw6jDVwd+RkwHeenvR0xxovUFCTQg==",
                  "group_id": "d0060308-22b2-11e8-b58b-0242ac110004",
                  "order": 1,
                  "peer_id": "d0060308-22b2-11e8-b58b-0242ac110004",
                  "peer_type": 1,
                  "status": 1,
                  "status_update_time": "2018-03-08 09:27:06.400219",
                  "target": "172.17.0.4:7100"
              },
              {
                  "cert": "MFYwEAYHKoZIzj0CAQYFK4EEAAoDQgAE+HQPBowjyJnyinsYjiztl5i6hQ1JiWdpRmyFR1T283M4liQia7weerQQ4Qw6jDVwd+RkwHeenvR0xxovUFCTQg==",
                  "group_id": "d0581aee-22b2-11e8-bab0-0242ac110005",
                  "order": 2,
                  "peer_id": "d0581aee-22b2-11e8-bab0-0242ac110005",
                  "peer_type": 0,
                  "status": 1,
                  "status_update_time": "2018-03-08 09:27:07.162296",
                  "target": "172.17.0.5:7200"
              }
          ],
          "registered_peer_count": 2,
          "registered_peer_list": [
              {
                  "cert": "MFYwEAYHKoZIzj0CAQYFK4EEAAoDQgAE+HQPBowjyJnyinsYjiztl5i6hQ1JiWdpRmyFR1T283M4liQia7weerQQ4Qw6jDVwd+RkwHeenvR0xxovUFCTQg==",
                  "group_id": "d0060308-22b2-11e8-b58b-0242ac110004",
                  "order": 1,
                  "peer_id": "d0060308-22b2-11e8-b58b-0242ac110004",
                  "peer_type": 1,
                  "status": 1,
                  "status_update_time": "2018-03-08 09:27:06.400219",
                  "target": "172.17.0.4:7100"
              },
              {
                  "cert": "MFYwEAYHKoZIzj0CAQYFK4EEAAoDQgAE+HQPBowjyJnyinsYjiztl5i6hQ1JiWdpRmyFR1T283M4liQia7weerQQ4Qw6jDVwd+RkwHeenvR0xxovUFCTQg==",
                  "group_id": "d0581aee-22b2-11e8-bab0-0242ac110005",
                  "order": 2,
                  "peer_id": "d0581aee-22b2-11e8-bab0-0242ac110005",
                  "peer_type": 0,
                  "status": 1,
                  "status_update_time": "2018-03-08 09:27:07.162296",
                  "target": "172.17.0.5:7200"
              }
          ]
      },
      "response_code": 0
    }
  $
  ```
3. peer 상태 조회
  ```
  $ curl http://localhost:9000/api/v1/status/peer | python -m json.tool
    % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                   Dload  Upload   Total   Spent    Left  Speed
  100   264  100   264    0     0  17697      0 --:--:-- --:--:-- --:--:-- 18857
  {
      "audience_count": "0",
      "block_height": 0,
      "consensus": "siever",
      "leader_complaint": 1,
      "made_block_count": 0,
      "peer_id": "d0060308-22b2-11e8-b58b-0242ac110004",
      "peer_target": "172.17.0.4:7100",
      "peer_type": "1",
      "status": "Service is online: 1",
      "total_tx": 0
  }
  $
  ```
4. SCORE 버전 조회
  ```
  $ curl http://localhost:9000/api/v1/status/score | python -m json.tool
    % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                   Dload  Upload   Total   Spent    Left  Speed
  100  1047  100  1047    0     0  35738      0 --:--:-- --:--:-- --:--:-- 36103
  {
      "all_version": [
          "f58b8b3e955984a09674a1f74c493001678d706c",
          "b39064b358b84798f20f024fca066a113ec88b18",
          "99923ce139350cf8f37ef9f72fddf3f327da4d7a",
          "e25e2fba404bbc42b010c552d280063c704a0917",
          "909b1ee00a00f12f744f3d669232c6f4549e945f",
          "51f258059bcc4f1fa46ba3df8762b953e27fcdee",
          "359b1f79b8bf2064ce0605d4b081da43a845beda",
          "3d7195e1e98e38bdddab93fd03ee0c7aa0a20765",
          "669b6db3a6c085b3de96d7bd13bc19efc26162ae",
          "5136f28e83e3aaf6fabb0c0556b505ca5b95a44c",
          "a74476425197c2b2b009a180f24f52efec932da8",
          "95c0dd33b826c9b529a9f8b6b349e1b002bb9835",
          "71afe3ca44fa46acced9b12c80ad1951fe83e4bd",
          "f01986ae06e402a97e48bfddb31d5aeebe1dc07b",
          "99ece33bb62b8b1c61182d074351b5062311d2f5",
          "eabe94b94545faac1c8951fb31ef62a9f549cc5f",
          "f5aab582d9f390f5378daf08f54d08c071f15d0c",
          "f79c480fc7af6d02c79e1fe3191bbc471962166f",
          "e38140e76766f2e51f30858a0ee3c82a90b9c258",
          "af7c49743fecd315d4e4491751fbdae9b92dead7",
          "bcc0d0f05d1a219cd4ed47955a86b0e16d1b2778"
      ],
      "id": "spritofwind/contract_sample",
      "status": 0,
      "version": "f58b8b3e955984a09674a1f74c493001678d706c"
  }
  $
  ```
5. SCORE Transaction 생성
  ```
  $ curl -H "Content-Type: application/json" -X POST -d '{"jsonrpc":"2.0","method":"propose","params":{"proposer":"RealEstateAgent" , "counterparties": ["leaseholder","jinho"], "content": "Theloop APT 101-3001, lease for 3 months from 3th April,2018", "quorum": "3"}}'  http://localhost:9000/api/v1/transactions | python -m json.tool
    % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                   Dload  Upload   Total   Spent    Left  Speed
  100   329  100   119  100   210   6361  11226 --:--:-- --:--:-- --:--:-- 11666
  {
      "more_info": "",
      "response_code": "0",
      "tx_hash": "7bc856e972da62a6cba3deff71e74e848174fc1e28feaae66f58ff2447875f0a"
  }
  $
  ```
6. SCORE Transaction 조회 - `tx_hash` 사용     (7bc856e972da62a6cba3deff71e74e848174fc1e28feaae66f58ff2447875f0a)
  ```
  $ curl http://localhost:9000/api/v1/transactions/result?hash=7bc856e972da62a6cba3deff71e74e848174fc1e28feaae66f58ff2447875f0a | python -m json.tool
    % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                   Dload  Upload   Total   Spent    Left  Speed
  100    66  100    66    0     0   4023      0 --:--:-- --:--:-- --:--:--  4125
  {
      "response": {
          "code": 0,
          "jsonrpc": "2.0"
      },
      "response_code": "0"
  }
  $
  ```

7. SCORE Transaction 실행 결과 조회 (Query)
  ```
  $ curl -H "Content-Type: application/json" -X POST -d '{"jsonrpc": "2.0","channel":"channel1","method":"get_user_contracts","id":"11233","params":{"user_id":"jinho"}}' http://localhost:9000/api/v1/query | python -m json.tool
    % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                   Dload  Upload   Total   Spent    Left  Speed
  100   445  100   334  100   111  20982   6973 --:--:-- --:--:-- --:--:-- 22266
  {
      "response": {
          "code": 0,
          "id": "11233",
          "jsonrpc": "2.0",
          "response": {
              "user_contracts": [
                  {
                      "approvers": [
                          "RealEstateAgent"
                      ],
                      "content": "Theloop APT 101-3001, lease for 3 months from 3th April,2018",
                      "contract_id": 1,
                      "counterparties": [
                          "leaseholder",
                          "jinho"
                      ],
                      "proposer": "RealEstateAgent",
                      "quorum": "3"
                  }
              ]
          }
      },
      "response_code": "0"
  }
  $
  ```
8. peer 상태 조회(block_height, made_block_count,total_tx 변화 확인)
  ```
  $ curl http://localhost:9000/api/v1/status/peer | python -m json.tool
    % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                   Dload  Upload   Total   Spent    Left  Speed
  100   264  100   264    0     0  17697      0 --:--:-- --:--:-- --:--:-- 18857
  {
      "audience_count": "0",
      "block_height": 1,
      "consensus": "siever",
      "leader_complaint": 1,
      "made_block_count": 1,
      "peer_id": "d0060308-22b2-11e8-b58b-0242ac110004",
      "peer_target": "172.17.0.4:7100",
      "peer_type": "1",
      "status": "Service is online: 1",
      "total_tx": 1
  }
  $
  ```
