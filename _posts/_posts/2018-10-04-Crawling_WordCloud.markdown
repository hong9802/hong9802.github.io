---
layout: post
title:  "한성공지를 크롤링 후 단어 빈도 수 시각화하기"
date:   2018-10-04 02:45:10 +0700
categories: MeuWorks
---

## 주의사항!
본 문서는 <b>Python 3</b>성애자가 다녀간 문서입니다.<br>
본 문서는 <b>Ubuntu</b>를 너무 사랑한 나머지 <b>Ubuntu 18.04.1 LTS</b>기준으로 작성되었습니다.<br>
본 문서에서 크롤링한 한성대학교 공지는 2018.10.03까지 입니다.(관련 data.txt는 <a href="https://bitbucket.org/hong9802/crawling_wordcloud/src/master/data.txt">bitbucket</a>에 있습니다.)<br>
본 문서에서 필요한 모듈들은 다음과 같습니다.<br>
* re
* BeautifulSoup
* requets
* konlpy
* collections
* pytagcloud
* time
<br>
<sub>time모듈은 크롤링할 때 필수...</sub>
<br>

## 간단한 설명!
listparshing이라는 함수가 한성대학교 한성공지 전체를 가져오는 역할 및 타 함수를 실행 후 결과를 data.txt에 기록합니다.<br>
contentparshing이라는 함수는 한성대학교의 세부적인 공지를 가져온 문자열을 가공 후 return 합니다.<br>
urlmaker이라는 함수는 한성대학교 한성공지의 세부적인 링크에 amp;단어들이 하나라도 있을 시 제대로 접속할 수 없는 상황이 나오기 때문에, amp;단어들을 제거해주는 역할을 합니다.<br>
imagemake이라는 함수는 <b>KoNLPy</b>와 <b>Collections</b>의 <b>Counter</b>모듈을 이용해서 형태소별로 나누고, 그것을 Counter를 통해 얼마나 자주 나왔는가 Counting합니다.<br>


## 결과물
# Counter한 결과!
<img src="https://bitbucket.org/hong9802/crawling_wordcloud/raw/77b8b9314d38b6bfd4afdeddf60c03a3bfdd491f/Counter.png"><br>
# WordCloud!
<img src="https://bitbucket.org/hong9802/crawling_wordcloud/raw/3ad355e3b70b209de4709d31f91fec8bb7dc5aa3/wordcloud.jpg"><br>
# <a href="https://bitbucket.org/hong9802/crawling_wordcloud/src/master/main.py">Python3</a>
```py
import re
from bs4 import BeautifulSoup
import requests
from konlpy.tag import Twitter
from collections import Counter
import time
import pytagcloud

j = 1
def listparshing(url):
    r = requests.get(url)
    soup = BeautifulSoup(r.text, "html.parser")
    hansunglist = soup.find("table", class_="bbs-list")
    sublist = hansunglist.find_all("td", class_="subject")
    i = 0
    try:
        while True:
            f = open("data.txt", "a")
            mainurl = sublist[i].find("a")
            mainurl = str(mainurl)
            front = mainurl.find("http")
            back = mainurl.find(">")
            mainurl = mainurl[front:back-1]
            new_url = urlmaker(mainurl)
            print(new_url)
            content = contentparshing(new_url)
            i+=1
            f.write(content)
            f.close()
            time.sleep(300)
    except IndexError as e:
        print(str(j) + " page end...") 
        if(i == 0):
            return 0
        else:
            pass

def contentparshing(url):
    r = requests.get(url)
    soup = BeautifulSoup(r.text, "html.parser")
    hansunglist = soup.find("table", class_="bbs-view")
    subject = hansunglist.find("th")
    subject = str(subject)
    cleaner = re.compile('<.*?>')
    cleantext = re.sub(cleaner, '', subject)
    print(cleantext)
    return cleantext

def urlmaker(string):
    while True:
        if(string.find("amp;") == -1):
            return string
        else:
            a = string.find("amp;")
            string1 = string[:a]
            string2 = string[a + 4:]
            string = string1 + string2

def imagemake():
    f = open("data.txt", "r")
    text = f.read()
    nlp = Twitter()
    nouns = nlp.nouns(text)
    words = Counter(nouns)
    tags = words.most_common(80) #80개만!!! print(tags)를 하면, 아름다운 Counting이...
    taglist = pytagcloud.make_tags(tags, maxsize=100)
    pytagcloud.create_tag_image(taglist, 'wordcloud.jpg', size=(640 , 480), fontname='Korean', rectangular=True)
    f.close()

while True:
    url = "http://hansung.ac.kr/web/www/cmty_01_01?p_p_id=EXT_BBS&p_p_lifecycle=0&p_p_state=normal&p_p_mode=view&p_p_col_id=column-1&p_p_col_pos=1&p_p_col_count=3&_EXT_BBS_struts_action=%2Fext%2Fbbs%2Fview&_EXT_BBS_sCategory=&_EXT_BBS_sKeyType=&_EXT_BBS_sKeyword=&_EXT_BBS_curPage=" + str(j)
    check = listparshing(url)
    if(check == 0):
        break
    else:
        j+=1
imagemake()
```
