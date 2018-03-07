## Local computer에서 SCORE개발 환경 만들기 Tutorial

### SCORE 저장소 생성

1. Github에서 SCORE Sample 프로젝트(https://github.com/theloopkr/contract_sample) 를 fork 하여서 SCORE 개발 환경을 위한 테스트용 SCORE 저장소를 생성합니다. ![github](https://www.dropbox.com/s/5pny2b9m76vjrsj/github_contract-sample.png?dl=1)
2. SCORE 저장소와 SSH통신을 위해 SSH 키쌍을 생성합니다. `ssh-keygen`명령어를 사용하셔서 `id_tutorial`이라는 이름으로 생성합니다. 다음 내용을 참고로 하여서 생성하여 보세요.
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
