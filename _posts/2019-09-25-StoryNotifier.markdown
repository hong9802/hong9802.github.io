---
layout: post
title:  "StoryNotifier Project"
date:   2019-09-25 01:18:10 +0900
categories: MeuWorks
---

## 주의사항!
본 문서는 <b>Python 3</b>성애자가 다녀간 문서입니다.  
본 문서는 <b>Ubuntu 18.04</b>를 너무 사랑한 나머지 <b>Ubuntu 18.04 </b>기준으로 작성되었습니다.  
본 프로그램을 사용함으로써 발생하는 문제는 개발자가 책임을 지지 않습니다.  
본 문서는 [Reflection](http://chihaya.kr/snoty/)를 참고하여 우분투에서 구동하게끔 만들었습니다.  
본 프로그램의 [소스](https://github.com/hong9802/StoryNotifier)는 [github](https://github.com/hong9802/StoryNotifier)에 올라와 있읍니다.  
<sub>본 문서의 macOS전용은 [CenoX](https://cenox.co/storynotifier/index.html)님이 진행중이십니다.</sub>  
본 프로그램을 위해 필요한 모듈들은 다음과 같습니다.  
* notify-send
* chrome


## 간단한 설명!
[카카오스토리](https://story.kakao.com)의 알림을 Ubuntu에서 받아볼 수 있는 앱 입니다.  
notify-send라는 앱을 통해서 Ubuntu 알림바에 알림을 해줍니다.  
![noti_image1](https://bitbucket.org/hong9802/image/raw/9eea528dfd6084215f2923b8815256e79e326abf/StoryNotifier/story_noti1.png)    
  
![noti_image2](https://bitbucket.org/hong9802/image/raw/9eea528dfd6084215f2923b8815256e79e326abf/StoryNotifier/story_noti2.png)  
알림은 위의 이미지처럼 나옵니다.  

## 사용방법
```bash
$ git clone https://github.com/hong9802/StoryNotifier
```
그 후 [chromedriver](https://chromedriver.chromium.org/downloads)를 받아야합니다. [chromedriver](https://chromedriver.chromium.org/downloads)는 본인이 사용하고 있으시는 크롬 버전에 맞춰서 하시길 바랍니다!  
버전 체크하는 명령어는 다음과 같습니다.  
```bash
$ google-chrome --version
```
chromedriver를 clone한 프로젝트에 옮겨주신 후, 다음과 같이 run을 시키면 됩니다.  
```bash
$ python3 run.py
```
