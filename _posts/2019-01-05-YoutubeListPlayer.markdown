---
layout: post
title:  "유투브 재생목록을 가져와서 play 시키자!"
date:   2019-01-05 06:15:10 +0700
categories: MeuWorks
---
## 주의사항!
본 문서는 <b>Python 3</b>성애자가 다녀간 문서입니다.<br>
본 문서는 <b>Ubuntu</b>를 너무 사랑한 나머지 <b>Ubuntu 18.04.1 LTS</b>기준으로 작성되었습니다.<br>
본 문서는 <a href = "https://www.youtube.com/watch?v=P33Qa2eML-s">youtube Link</a>를 참조해주세요!<br>
본 문서에서 필요한 모듈들은 다음과 같습니다.
* os
* subprocess
* bs4
* requests
* re
* <a href="https://python-pytube.readthedocs.io/en/latest/">pytube</a>
* <a href="https://www.ffmpeg.org/">ffmpeg</a>
* <a href="https://www.mpg123.de/">mpg123</a>

## 간단한 설명!
listParshing이라는 함수는 재생목록 내에 각각 영상들의 주소를 가져옵니다.<br>
download함수는 pytube를 통해 영상을 다운받은 후 ffmpeg를 이용하여 파일 포맷을 mp3로 바꿉니다.
나머지 코드들은 영상을 재생하기 위한 코드들입니다.
## 결과물
# <a href="https://bitbucket.org/hong9802/youtube_listplayer/src/master/main.py">Python3</a>
```py
import pytube
import subprocess
from bs4 import BeautifulSoup
import time
import os
import requests
import re

playlist = [] #재생목록 저장용!
lists = [] #유투브 재생목록 내에 각각의 영상 링크들 저장용
url = input("Insert your youtube playlist url : ")
path = input("Insert your locale : ")
path += '/'

def listParshing(url): #각 영상의 링크들을 가져오기 위한 함수
    r = requests.get(url)
    soup = BeautifulSoup(r.text, "html.parser")
    countlist = soup.find_all("a", class_=" spf-link playlist-video clearfix yt-uix-sessionlink spf-link ") #영상 링크들 가져오기...
    for i in range(0, len(countlist), 1):
        strchange = str(countlist[i])
        cleanr = strchange[168:187]
        lists.append("http://www.youtube.com" + "/" + cleanr) #링크 추가!

def download(): #영상들을 받기 위한 함수
    for i in range(0, len(lists), 1):
        try:
            yt = pytube.YouTube(lists[i])
            playlist.append(yt.title) #순서를 맞추기 위한 코드
            if(os.path.exists(path + yt.title + ".mp3")): #이미 받아진 파일이면 pass
                continue
            yt.streams.filter(only_audio=True, subtype="mp4").first().download() #.mp4타입에 오디오만 있는 버전 다운
            subprocess.call(["ffmpeg", "-i", os.path.join(path + yt.title + ".mp4"),
            os.path.join(path + yt.title + ".mp3")]) #ffmpeg를 통하여, mp4 포맷을 mp3로 convert
            os.remove(path + yt.title + ".mp4") #기존 파일 제거

        except pytube.exceptions.RegexMatchError as e: # pytube 오류 핸들링...
            print(e)
            pass

if(__name__ == "__main__"):
    listParshing(url)
    download()
    playmode = len(playlist)
    nowplaying = 0
    while(1):
        if(nowplaying == playmode):
            nowplaying = 0
        print(playlist[nowplaying])
        subprocess.Popen(['mpg123', '-q', playlist[nowplaying] + ".mp3"]).wait()
        nowplaying+=1
```
간단하죠?
