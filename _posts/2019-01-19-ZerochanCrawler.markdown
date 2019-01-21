---
layout: post
title:  "특정 사이트에서 캐릭터 사진을 긁어오자!"
date:   2019-01-19 15:22:10 +0700
categories: MeuWorks
---

## 주의사항!
본 문서는 <b>Python 3</b>성애자가 다녀간 문서입니다.<br>
본 문서는 <b>Ubuntu</b>를 너무 사랑한 나머지 <b>Ubuntu 18.04.1 LTS</b>기준으로 작성되었습니다.<br>
본 문서는 <a href="https://www.zerochan.net/">Zerochan</a> 사이트를 크롤링 했습니다.<br>
본 문서는 기존에 짯던 소스에 multiprocessing을 첨가한 케이스라서 소스코드가 매우 답이 없습니다!(갈아엎어야 하는데 귀찮...)<br>
본 문서를 작성한 필자는 <a href="https://namu.wiki/w/%EC%8B%9C%EA%B5%AC%EB%A0%88(%ED%95%A8%EB%8C%80%20%EC%BB%AC%EB%A0%89%EC%85%98)">시구레</a> 캐릭터를 가져오기 위해 삽질했습니다.<br>
본 문서에서 필요한 모듈들은 다음과 같습니다.<br>
* re
* BeautifulSoup
* requets
* urllib
* multiprocessing
<br>
<sub>매우 빠른 크롤링을 위해! multiprocessing을 사용...</sub>
<br>

## 간단한 설명!
본 문서에 userurl은 Zerochan내에 특정 태그를 입력한 사이트 url을 넣으시면 됩니다.<br>
<b>예) 시구레 : https://www.zerochan.net/Shigure+%28Kantai+Collection%29</b><br>
checkingimg(imgstr)함수는 각 페이지에 이미지의 확장자들을 체크합니다.<br>
pageChecker()함수는 특정 태그를 입력한 사이트의 이미지가 총 몇페이지 있나 체크합니다.<br>
worker(page)함수는 이미지를 긁어옵니다!<br>
if(__name__ == "__main__")부터는 즐거운 멀티프로세싱을 하기 위한 코드입니다.<br>
<sub><i>파이썬은 GIL때문에 쓰레드를 늘리면 그 만큼 더 느려집니다!!!</i></sub><br>


## 결과물
# <a href="https://bitbucket.org/hong9802/zerochancrawler/src/master/ZerochanCrawler.py">Python3</a>
```py
from bs4 import BeautifulSoup
import re
import requests
import urllib
from multiprocessing import Pool

count = [] #페이지 저장용 리스트
i = 1
userurl = "https://www.zerochan.net/Shigure+%28Kantai+Collection%29"
#시구레 사진만 크롤링하기 위해 시구레 이미지 주소 넣음
#타 캐릭터도 저런 형식으로 이미지 주소 넣으면 됨.

def checkingimg(imgstr): #각 이미지 확장자 함수
    if(imgstr.find(".jpg") != -1) :
        return ".jpg"
    elif(imgstr.find(".png") != -1) :
        return ".png"
    elif(imgstr.find(".jpeg") != -1) :
        return ".jpeg"
    elif(imgstr.find(".gif") != -1) :
        return ".gif"

def pageChecker(): #페이지 수 체크용 함수
    while True :
        global i
        print(i)
        url = userurl +'?p='+ str(i)
        r = requests.get(url)
        soup = BeautifulSoup(r.text, "html.parser")
        mr2 = soup.find("ul", id = 'thumbs2')
        if(mr2 == None) :
            print('break')
            break
        else :
            count.append(i)
            i+=1
    print(len(count))

def worker(page): #크롤링 전용 함수
    global i
    url = userurl + '?p='+ str(page)
    r = requests.get(url)
    soup = BeautifulSoup(r.text, "html.parser")
    try :
        for z in range(0, 90, 1) :
            mr = soup.find("ul", id = 'thumbs2')
            mr2 = mr.find_all("a")
            link1 = str(mr2[z])
            print(link1[9:17])
            if(link1[9:17].startswith('https://')) :
                pass
            elif(link1[9:17].startswith('/registe')) :
                pass
            else :
                url2 = 'https://www.zerochan.net' + link1[9:17]
                r2 = requests.get(url2)
                soup2 = BeautifulSoup(r2.text, "html.parser")
                mr = soup2.find("div", id = "large")
                if(mr == None) :
                    pass
                mr2 = mr.find("img")
                i+=1
                link2 = str(mr2)
                first = link2.find('src') + 5
                link3 = link2[first:]
                a = link3.split()
                stra = str(a[0])
                final = stra.replace("\"", "")
                extension = checkingimg(final)
                urllib.request.urlretrieve(final, 'shigure/' + str(page) + "00" + str(i) + extension) #이미지 저장!
    except IndexError as e :
        pass

if(__name__ == "__main__"):
    pageChecker() #페이지를 체크하고~
    i = 0
    thread = Pool(8) #프로세스를 8개 만들고~
    thread.map(worker, range(1, len(count) + 1, 1)) #일해라 핫산!!!
```
