# SCORE(Smart Contract On Reliable Environment)

## Loopchain SCORE?
* Loopchain의 Smart Contract입니다.
* 현재는 별도의 VM(Virtual Machine)이 없이 노드 운영환경에서 직접 실행됩니다.
  - Python Code로 직접 작성할 수 있어 높은 생산성을 가지고 있습니다.
* 블록체인 프로세스와 별도의 프로세스로 동작하면서 다양한 업무를 개발할 수 있도록 지원합니다.
* 각 피어에서 독립적으로 실행되며, Block 이 확정되는 시점에서 실행됩니다.
* Block별로 실행하며, 블록체인으로 구성하는 비지니스 로직을 구현한다.
* Python 언어로 개발되며, Loopchain의 dependency 를 따릅니다.

## SCORE 구조

![SCORE 구조](https://www.dropbox.com/s/8cu9gq182jgp92l/SCORE_Structure_2.png?dl=1)

_SCORE container를 Git Project로 관리하는 이유에 대해서 간결한 설명 추가 필요._
_SCORE container를 엔터프라이즈 별(고객별)로 수정해야 할 것으로 생각되는데 SCORE 심화 튜토리얼로 추가 필요하다고 생각됨_

## SCORE 실행 순서
1. channel_manage_data.json에 따라서 특정 채널에서 불러올 SCORE를 지정합니다.
  ```
  {
    "channel1":
      {
        "score_package": "{your_github_id}/contract_sample"
      }
  }
  ```
2. DEFAULT_SCORE_HOST로 SCORE를 가져올 Git service URL을 지정합니다.
  ```
  $ docker run -d --name peer0 \
    -v $(pwd)/conf:/conf \
    -v $(pwd)/storage0:/.storage \
    -v $(pwd)/score:/score \
    -v "${SSH_KEY_FOLDER}:/root/.ssh/id_rsa \
    -e "DEFAULT_SCORE_HOST=github.com" \
    --link radio_station:radio_station \
    --log-driver fluentd --log-opt fluentd-address=localhost:24224 \
    -p 7100:7100 -p 9000:9000  \
    loopchain/looppeer:${TAG} \
    python3 peer.py -o /conf/peer_conf.json  -r radio_station:7102
  ```






# Local computer에서 SCORE개발 환경 만들기



## SCORE 저장소 생성

1. Github에서 SCORE Sample 프로젝트(https://github.com/theloopkr/contract_sample) 를 fork 하여서 SCORE 개발 환경을 위한 테스트용 SCORE 저장소를 생성합니다. ![github](https://www.dropbox.com/s/5pny2b9m76vjrsj/github_contract-sample.png?dl=1)
2. SCORE 저장소와 SSH통신을 위해 SSH 키쌍을 생성합니다.

  ```
  $ ssh-keygen
  Generating public/private rsa key pair.
  Enter file in which to save the key (/Users/donghanlee/.ssh/id_rsa): id_tutorial
  Enter passphrase (empty for no passphrase):
  Enter same passphrase again:
  Your identification has been saved in id_tutorial.
  Your public key has been saved in id_tutorial.pub.
  The key fingerprint is:
  SHA256:lqoOoxWXIh44lAvq1RBuwjaBcuJ1ufkqFXZxbIE/va8 donghanlee@Donghanui-MacBook-Pro.local
  The key's randomart image is:
  +---[RSA 2048]----+
  |.. .  . o..      |
  |= =..o o +       |
  |+X.+. o = .      |
  |*.= o* . + .     |
  |=oo.+.+ S . .    |
  |ooo+ . +   .     |
  | o+ . o     .    |
  | o + o       .   |
  |.  .+      E.    |
  +----[SHA256]-----+

  $ cat id_tutorial.pub
  ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDm4bGpb3VIvHc6DCIWh8PUui5BlSc9+MkgCLUonaiJnhIBjc8LPqQzo2OyrMfSW/1pgIGipU0O0cGr9aFixCetVmiSWgojWVkWl/jadTTNOcbwY+Fk9IZSeLyIkHpEw7TKkfpqR6o888zxNR7Lr+D+dCjO+803t++Lc8eluAGhbcd1pjcWqx3l+gpw7Rj7pI9+Y+kXvBmywL3rJTK6/DCvtIDfa5HO2Ltqwhj+CrTERScXJC6Xz2l2JZe9fjPlJziZ03gpLVNJXbwAGIVEptbsgafh8TQjQX7uY4K87sfQPiagtgM0siCQZ45lbarTropzBVBpP4Uf4Po2ev3mkNtZ donghanlee@Donghanui-MacBook-Pro.local
  $

  ```
3. Github SCORE 저장소에 SSH public key 내용을 (예:id_tutorial.pub)를 등록합니다.
