---
layout: post
title:  "MP3 시간 짜르기 프로그램!"
date:   2019-04-25 16:55:10 +0900
categories: MeuWorks
---

## 주의사항!
본 문서는 <b>Python 3</b>성애자가 다녀간 문서입니다.<br>
본 문서는 <b>Ubuntu</b>를 너무 사랑한 나머지 <b>Ubuntu 18.04.1 LTS</b>기준으로 작성되었습니다.<br>
본 문서는 mp3파일 시간을 자르는 프로그램입니다.<br>
본 문서에서 필요한 모듈들은 다음과 같습니다.<br>
* tkinter
* ffmpeg
* subprocess
* os
<br>

## 간단한 설명!
본 문서는 ffmpeg를 통해 mp3 파일 시간을 자르는 프로그램입니다.<br>
본 문서에서 fileselect함수는 mp3파일을 열기 위해 사용됩니다.<br>
본 문서에서 startButton함수는 mp3파일 자르는 것을 시작하고 진행이 성공적으로 끝나면 우측 맨 아래있는 버튼이 finish, 실패하면 failed로 바뀝니다.<br>
맨 위 MP3 FILE버튼을 누르면 fileselect함수가 실행됩니다.<br>
MP3 FILE아래 있는 entry는 저장할 경로 + 저장할 파일 이름입니다.<br>
좌측 00:00:00이 적힌 entry는 파일 시작 시간 우측 00:04:20이 적힌 entry는 파일 끝 시간을 알리는 시간입니다.<br>
Start!버튼을 누르면 startButton함수가 작동됩니다.<br>
Not_Started버튼은 결과값을 표시합니다.(startButton함수의 결과)<br>
나머지는 GUI를 꾸미기 위한 코드들입니다.<br>
<sub><b>처음으로 tkinter쓰는 것이라 많이 어렵더군요...</b></sub>


## 결과물
# 스크린샷!
<img src="https://bitbucket.org/hong9802/mp3_time_cut/raw/09859daca3957736453983e2c51fd704e01c45f6/screenshot.png"><br>
# <a href="https://bitbucket.org/hong9802/mp3_time_cut/src/master/main.py">Python3</a>
```py
from tkinter import *
from tkinter import filedialog
import subprocess
import os

def fileselect():
    filename = filedialog.askopenfilename(initialdir = os.getenv("HOME"), title = "Select File",
    filetypes = (("mp3 files", "*.mp3"), ("all files", "*.*")))
    file_Select["text"] = filename

def startButton():
    origin_File_URL = file_Select.cget('text')
    save_Path_URL = str(savePath.get())
    start_Time = str(startTime.get())
    end_Time = str(endTime.get())
    try:
        subprocess.check_call(['ffmpeg', '-i', origin_File_URL, '-ss', start_Time, '-to',
        end_Time, '-y', save_Path_URL])
        resultBtn["text"] = "finish"
    except subprocess.CalledProcessError as e:
        resultBtn["text"] = "failed"

window = Tk()

window.title("MP3 FILE TIME CUT Program")
window.geometry("400x100")
window.resizable(False, False)
file_Select = Button(window, text = "MP3 FILE", command = fileselect, height = 1, width = 100)
file_Select.pack(side = TOP)
savePath = Entry(window, width = 100)
savePath.insert(0, os.getenv("HOME") + "/example.mp3")
savePath.pack(side = TOP)
startTime = Entry(window, width = 20)
startTime.insert(0, "00:00:00")
startTime.pack(side = LEFT)
endTime = Entry(window, width = 20)
endTime.insert(0, "00:04:20")
endTime.pack(side = LEFT)
startBtn = Button(window, text = "Start!", command = startButton)
startBtn.pack()
resultBtn = Button(window, text = "Not_Started")
resultBtn.pack()

window.mainloop()
```
