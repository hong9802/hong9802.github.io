---
layout: post
title:  "DCINSIDE에서 특정 게시판 사진을 긁어오자!"
date:   2019-04-12 03:35:10 +0900
categories: MeuWorks
---

## 주의사항!
본 문서는 <b>Python 3</b>성애자가 다녀간 문서입니다.<br>
본 문서는 <b>Ubuntu</b>를 너무 사랑한 나머지 <b>Ubuntu 18.04.1 LTS</b>기준으로 작성되었습니다.<br>
본 문서는 <a href="https://www.dcinside.com/">DCINSIDE</a> 사이트를 크롤링 했습니다.<br>
본 문서는 아직 완벽하지 않습니다! 특정 게시판에서는 오류가 날 수 있습니다.<br>
본 문서를 작성한 필자는 깡갤(칸코레 갤러리)에서 가끔 출몰합니다.
본 문서에서 필요한 모듈들은 다음과 같습니다.<br>
* re
* BeautifulSoup
* requets
* urllib
<br>

## 간단한 설명!
본 문서에 url은 dcinside에 있는 게시글 링크입니다.<br>
<b>예) http://m.dcinside.com/board/kancolle/8335989 </b><br>
img_link(soup, name_list)함수는 게시글 주 내용에 있는 이미지 링크를 가져와서 저장합니다.<br>
name(soup, name_list)함수는 파일 이름을 긁어오기 위해 사용됩니다.<br>
if(__name__ == "__main__")부터는 사용자한테 url입력을 받아서 request 때려주고~ 각 함수 호출하는 일을 합니다.<br>


## 결과물
# 스크린샷!
<img src="https://bitbucket.org/hong9802/dcinside-img-crawler/raw/a1cedab67c2692ee730bcb7092bd2ab349084792/screenshot.png"><br>
# <a href="https://bitbucket.org/hong9802/dcinside-img-crawler/src/master/dcimg.py">Python3</a>
```py
from bs4 import BeautifulSoup
import re
import requests
import urllib

def img_link(soup, name_list, url):
    opener = urllib.request.build_opener()
    opener.addheaders = [("Referer", url)]
    urllib.request.install_opener(opener)
    div_class = soup.find("div", class_="writing_view_box")
    img_class = div_class.find_all("img")
    for i in range(0, len(img_class), 1):
        string_img_url = str(img_class[i])
        tmp_start_urlPoint = string_img_url.find("src=")
        tmp_end_urlPoint = string_img_url.find("style=")
        string_img_url = string_img_url[tmp_start_urlPoint + 5:tmp_end_urlPoint-2]
        string_img_url = string_img_url.replace("amp;", "")
        urllib.request.urlretrieve(string_img_url, "./dcinside/" + name_list[i])

def name(soup, name_list):
    ul_class = soup.find("ul", class_="appending_file")
    a_tags = ul_class.find_all("a")
    for i in range(0, len(a_tags), 1):
        string_name = str(a_tags[i])
        tmp_start_namePoint = string_name.find(">")
        tmp_end_namePoint = string_name.find("</a>")
        string_name = string_name[tmp_start_namePoint + 1 : tmp_end_namePoint]
        name_list.append(string_name)
    name_list.sort()

def debuging_request(r):
    f = open("debuger.txt", mode="wt", encoding="utf-8")
    f.write(r)
    f.close()

if __name__ == "__main__":
    headers = {'Content-Type': 'application/json; charset=utf-8',
    "User-Agent" : "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Ubuntu Chromium/74.0.3729.169 Chrome/74.0.3729.169 Safari/537.36"}
    print("dcinside IMG 크롤러입니다. dc에서 파싱할 게시글 URL을 입력해주세요")
    url = input()
    name_list = []
    r = requests.get(url, headers = headers)
    debuging_request(r.text)
    soup = BeautifulSoup(r.text, 'html.parser')
    name(soup, name_list)
    img_link(soup, name_list, url)
```
