# Tutorial
loopchain을 실제 설치하고 운영하는데 있어서 가장 중요한 설치 과정을 연습해보는 과정에 대해서 설명합니다.
이 문서는 로컬 컴퓨터 상에서 loopchain 을 어떻게 설치 하는지에 대해서 설명합니다.

## 필요 환경
* 추천 OS : Linux(CentOS 7 이상, Ubuntu), MacOS 10.12 이상, Windows 10
* Python : v3.6 이상
* Vituralenv: v15.1.0 이상
* Docker: 17.x이상

## 설치

### Python 설치
이 문서에서는 MacOS를 기준으로 먼저 설명을 하도록 하겠습니다.

일반적으로 MacOS에 기본으로 설치되어 있는 Python은 2.7.x입니다.따라서 Python 3.6이상의 버전으로 설치를 해야만 합니다.
버전 확인 방법은 다음과 같습니다.
```
$ python -V
Python 2.7.10
$
```
맥에서 가장 쉽게 Python 3.6이상의 버전을 설치하는 방법은 Homebrew를 이용하는 방법입니다. Homebrew를 설치하는 것은 매우 간단합니다. 터미널에서 다음의 명령어를 복사하여서 붙여넣고 실행하시면 됩니다.

```
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
설치 중에 비밀번호를 입력하는 부분이 있는데, 컴퓨터 비밀번호를 입력해주면 됩니다.



### ghksrudtjf

1. 먼저 Github의 loopchain 프로젝트(https://github.com/theloopkr/loopchain.git)를 clone 합니다.
2. 프로젝트 폴더로 이동한 다음 사용자 환경을 구축합니다.
```
$ virtualenv -p python3 .  # Create a virtual environment
$ source bin/activate    # Enter the virtual environment
$ pip3 install -r requirements.txt  # Install necessary packages in the virtual environment
$ ./generate_code.sh #  gRPC generates codes necessary for communication
```
혹은, 다음의 방법으로 더 쉽게 설정할 수도 있습니다.
```
$ ./setup.sh
$ source bin/activate
$ ./setup.sh
$ ./generate_code.sh
```






## 설치 준비

### Docker 설치하기


####  Linux에서 Docker 설치하기
Docker CE(Community Edition)X86-64, Docker EE(Enterprise edition) X86-64를 운용할 수 있는 최신 환경이면 됩니다.

* Docker CE: 무료 사용버전
* Docker EE: 상용 버전, 무료 Hosted Trial 사용 가능. 각종 OS들에 대한 지원 추가제공.
* 모든 상황에서 방법이 없으면 [Docker를 Binary로부터 설치할 수 있는 방법](https://docs.docker.com/install/linux/docker-ce/binaries/)이 있습니다.

|Platform|Docker CE X86_64|Docker EE X86_64|Note |
|----|----|----|----|
|CentOS|O|O| CentOS 7이상. [Installation Guide](https://docs.docker.com/install/linux/centos/) |
|Devian|O||Stretch (stable) / Raspbian Stretch Jessie 8.0 (LTS) / Raspbian Jessie Wheezy 7.7 (LTS). [Installation Guide](https://docs.docker.com/install/linux/docker-ce/debian/) |
|Fedora|O||Fedora 24, 25 [Installation Guide](https://docs.docker.com/install/linux/docker-ce/fedora/) |
|Windows Server 2016||O| [Installation Guide](https://docs.docker.com/install/windows/docker-ee/) |
|Oracle Linux||O| 7.3 이상 [Installation Guide](https://docs.docker.com/install/linux/docker-ee/oracle/) |
|Red Hat Enterprise Linux||O|64bit version Redhat Enterprise linux 7 on an X86 or S390x [Installation Guide](https://docs.docker.com/install/linux/docker-ee/rhel/) |
|SUSE Linux Enterprise server||O|SUSE 12.x version (OpenSUSE지원 안함) [Installation Guide](https://docs.docker.com/install/linux/docker-ee/suse/) |
|Ubuntu|O|O| EE : Xenial 16.04 (LTS) / Trusty 14.04 (LTS) , CE : Zesty 17.04 / Xenial 16.04 (LTS) / Trusty 14.04 (LTS) [Installation Guide](https://docs.docker.com/install/linux/ubuntu/)|

자세한 정보는 Docker 홈페이지의 <https://docs.docker.com/install/> 페이지를 참조하여서 설치하시면 됩니다.

TIP:
```
Docker 사용자 설정

Add the docker group if it doesn't already exist:
$ sudo groupadd docker

Add the connected user "$USER" to the docker group. Change the user name to match your preferred user if you do not want to use your current user:
$ sudo gpasswd -a $USER docker

Either do a "newgrp docker" or log out/in to activate the changes to groups.
```

#### Windows / Mac에서 Docker 설치하기

Docker 홈페이지의 <https://docs.docker.com/install/> 페이지를 참조하여서 설치하시면 됩니다.


#### Docker 동작 확인 하기

 "docker version" 명령어로 Docker 가 정상 설치되었는지 확인합니다.  다음의 화면을 참고하셔서 확인하십시오.
```
$ docker version
Client:
 Version:	17.12.0-ce
 API version:	1.35
 Go version:	go1.9.2
 Git commit:	c97c6d6
 Built:	Wed Dec 27 20:03:51 2017
 OS/Arch:	darwin/amd64

Server:
 Engine:
  Version:	17.12.0-ce
  API version:	1.35 (minimum version 1.12)
  Go version:	go1.9.2
  Git commit:	c97c6d6
  Built:	Wed Dec 27 20:12:29 2017
  OS/Arch:	linux/amd64
  Experimental:	true
$
```

### loopchain Docker Image

##### loopchain Docker Image 종류

loopchain의 Docker image는 다음의 3종류가 있습니다.

* looprs: RadioStation docker image
* looppeer: Peer docker image
* loopchain-fluentd: log를 저장하기 위해서 수정한 fluentd image

#### Docker image 받기

아래 화면과 같이 ```docker pull``` 명령을 이용하여서 Docker hub로부터 loopchain docker image들을 다운 받아옵니다.

```
$ docker pull loopchain/looprs
$ docker pull loopchain/looppeer
$ docker pull loopchain/loopchain-fluentd
```
