---
layout: post
title:  "시보 알리미 제작해보자"
date:   2018-09-26 02:45:04 +0700
categories: MeuWorks
---
## 주의사항!
본 문서는 <b>Python3</b>를 좋아한 사람이 다녀간 문서입니다.<br>
본 문서는 <b>Ubuntu</b>를 너무 사랑한 나머지 <b>Ubuntu 18.04 LTS</b>기준으로 작성되었습니다.<br>
본 문서는 시보파일을 제공하지 않습니다.(알아서 구하셔야합니다.)<br>
본 문서에 필요한 모듈들은 다음과 같습니다.
* subprocess
* time
* datetime
* mpg123
<br><sub>mpg123모듈은 <b>sudo apt install mpg123</b> 이렇게 설치해주세오!</sub>

## 결과물(최종 스크립트 파일)
# <a href="https://bitbucket.org/hong9802/timesignal-notifier/src/master/RTC.py">Python3</a>
```py
import subprocess
import time
from datetime import datetime

def play_mp3(TimeSignalpath):
    subprocess.Popen(['mpg123', '-q', TimeSignalpath]).wait()

while(True):
    TimeSignalpath = str(datetime.now().hour) + ".mp3"
#시보파일 경로, 파일이름을 맞추어야함. 본 문서에서는 시간.mp3로 되어있었음.
# 예) 0.mp3, 1.mp3, 23.mp3 이런 순
    if(datetime.now().minute == 0 and datetime.now().second == 0):
        play_mp3(TimeSignalpath)
    else:
        time.sleep(1) # 쉬엄쉬엄 GAZA...!
        pass
```
