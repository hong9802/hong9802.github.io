---
layout: post
title:  "파일 확장자 일괄변경기 만들어보자!"
date:   2018-09-26 02:45:10 +0700
categories: MeuWorks
---
## 주의사항!
본 문서는 <b>Python 3</b>성애자가 다녀간 문서입니다.<br>
본 문서는 <b>Ubuntu</b>를 너무 사랑한 나머지 <b>Ubuntu 18.04.1 LTS</b>기준으로 작성되었습니다.<br>
본 문서는 <a href = "http://devanix.tistory.com/304">파이썬 – os 모듈</a>을 참조하였습니다.<br>
본 문서에서 필요한 모듈들은 다음과 같습니다.
* os

## 간단한 설명!
이번에는 <b>os</b>모듈에서 <b>os.listdir</b>하고 <b>os.rename</b>만을 활용합니다.<br>
os.listdir("경로")는 경로 내에 있는 파일들을 리스트 형태로 반환합니다.<br>
os.rename("이전", "이후") 이전 파일을 이후 파일로 이름을 변경하거나 이동합니다.<br>
<sub>위 내용들은 <a href = "http://devanix.tistory.com/304">파이썬 – os 모듈</a>을 참조하였습니다!</sub>

## 결과물
# <a href="https://bitbucket.org/hong9802/extensionconvert/src/master/extensionConvert.py">Python3</a>
```py
import os

print("바꿀 확장자 입력 : ")
change = input() #바꿀 확장자 저장 변수
print("폴더 경로 입력")
path = input() #경로 저장 변수
target = os.listdir(path)
for i in range(len(target)):
    extension = target[i].find(".") #확장자는 .txt, .jpg이런 형식이므로 .을 찾음 여기서는 .이 있는 인덱스 번호 출력
    convert = target[i][:extension + 1] + change #파일 이름 저장!
    os.rename(path + '/'+ target[i], path + '/' + convert) #이름 바꾸기!!!
```
간단하죠?