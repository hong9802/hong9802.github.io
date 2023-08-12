---
layout: post
title:  "안드로이드 빌드 환경 구축하기!"
date:   2023-08-12 00:23:15 +0900
categories: android
---
  
## 짧은 이야기거리
갑자기 동생이 휴대폰을 바꾸게 되어 S10 5G를 얻게 되었다. 이떻게 사용할지 고민하다가 예전에 롬 빌드를 했던 추억이 떠올라 구축하게 되었다.  
S10 5G가 안드로이드 버전 12(One UI 4)에서 지원이 끝났기 때문에, 필자는 Android 13버전으로 빌드해서 올려보기로 결정했다. 여기서 필자는 순정 AOSP가 아닌 Lineage OS를 빌드해보려고 한다.
  

## 환경을 구축해보자!
먼저, 우분투 18.04를 설치해야 한다. 우분투는  [다음 사이트](https://releases.ubuntu.com/18.04/)에서 설치할 수 있다.  
설치가 완료되면, repository 업데이트부터 확인한 후 필요한 패키지들을 설치한다. 다음은 해당 명령어이다.  
```Shell
$ sudo apt-get update
$ sudo apt-get upgrade
$ sudo apt-get install git-core gnupg flex bison build-essential zip curl zlib1g-dev libc6-dev-i386 libncurses5 lib32ncurses5-dev x11proto-core-dev libx11-dev lib32z1-dev libgl1-mesa-dev libxml2-utils xsltproc unzip fontconfig
```
  
다음은 파이썬 버전 설정이다. AOSP에서도 써져있듯, 파이썬 2버전 지원이 종료됨에 따라 파이썬 3버전 사용을 권장하고 있다. Ubuntu18.04에서는 python3버전이 설치가 되어 있지만, 터미널에서 python을 입력하면 인식을 못한다. 여기서 가장 좋은 방법은 /usr/bin에 python3를 가리키는 python 심볼릭 링크를 만드는 것이다. 그러나 필자는 당시 실수로 sudo apt-get python을 입력하여 python2버전을 설치해버려서 다른 방식으로 진행했다.  
필자와 같은 경우에는 python 버전 변경이 필요했으며, 이와 관련된 내용은  [다음 블로그](http://www.dreamy.pe.kr/zbxe/CodeClip/3769501)에서 참조했다. Alternatives를 이용하면 python 기본 버전을 3버전으로 바꿀 수 있다. 중요한 점은 python 버전에 맞게 해야한다.  
다음 명령어로 파이썬 실행 파일들을 볼 수 있다.
```Shell
$ ls /usr/bin | grep python
```  
필자의 경우 python 2.7과 python 3.6버전이 설치되어 있었으므로, 다음 명령어를 입력했다.
```Shell
$ sudo update-alternatives --install /usr/bin/python python /usr/bin/python2.7 1
$ sudo update-alternatives --install /usr/bin/python python /usr/bin/python3.6 2
```  
그 다음 python의 기본 버전(python을 몇버전으로 링크할 지) 결정하는 명령어이다.
```Shell
$ sudo update-alternatives --config python
```
입력 후에 내가 바꿀 버전에 맞는 번호를 입력 후 엔터를 누르면 된다. 여기서는 우선순위(Priority) 번호 1번이 python 2.7버전, 2번이 python 3.6버전으로 2번을 누르고 엔터를 누르면 된다.
  
다음은 선택사항이지만, 빌드 속도를 올리기 위해 ccache라는 패키지도 받을 수 있따. ccache는 처음 빌드시 관련 정보와 결과물을 저장하고, 차기 빌드부터는 정보를 비교하여 같은 파일의 빌드면 사전에 빌드했던 결과물을 활용하는 것이다. ccache에 대해 자세히 알고 싶다면 [다음 블로그](https://www.whatwant.com/entry/Ubuntu%EC%97%90%EC%84%9C-ccache-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)를 참고하자. 필자도 이 내용을 참고한 것이다.  
ccache 설치 및 설정 방법은 다음과 같다.
```Shell
$ sudo apt-get install ccache
$ mkdir ~/ccache
$ mkdir /mnt/ccache
$ sudo mount --bind ~/ccache /mt/ccache
$ export CCACHE_DIR=~/mnt/ccache
$ export USE_CCACHE=1
$ export CCACHE_EXEC=/usr/bin/ccache
$ ccache -M 50G
```
  
여기까지 하면 빌드환경은 구축된 것이다.
  
## 소스 코드를 다운로드하기
  
소스코드를 다운로드 하려면, 우선 repo를 받아야한다.
repo는 sudo apt-get install repo라는 명령어를 입력하여 설치해도 되지만, lineageOS소스코드를 받을 때에는 repo 버전이 문제였는지, 오류가 나는 문제가 있었다.
필자는 다음과 같은 명령어를 입력하여서 repo를 설치했다.  
```Shell
$ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
$ chmod a+x ~/bin/repo
```

repo 설치가 끝났다면, 이제 git config를 입력하자!
```Shell
$ git config --global user.email "이메일주소"
$ git config --global user.name "이름"
```  
그리고 나서 소스코드를 받을 폴더를 만들고, 그 경로에 들어가서 받을 준비를  한다.
```Shell
$ mkdir android
$ cd android
```  
그 다음 어떤 버전의 소스코드를 받을 지 지정하고, 받으면 된다.
```Shell
$ repo init -u https://github.com/LineageOS/android.git -b lineage-20.0 --git-lfs
$ repo sync -j16
```



