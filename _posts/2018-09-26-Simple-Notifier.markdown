---
layout: post
title:  "간단한 알리미 제작 with Python"
date:   2018-09-26 02:45:03 +0700
categories: MeuWorks
---
## 주의사항!
이 강의는 <b>Python</b>에 대해 살짝 알아야합니다.<br>
이 강의 기준은 <b>Ubuntu 18.04 LTS</b>와 <b>Python 3</b> 기준으로 작성되었습니다.<br>
이 강의는 간단한 스크립트 하나를 작성하는 것 뿐입니다! 만약 이를 통해 자동적으로 돌아가게 하고 싶은 분은 리눅스 구조(예: Ubuntu Service 등록)과 <b>bash shell</b>을 추가적으로 공부하셔야 할 것입니다.<br>
필요한 Python Module들 정리
* bs4(BeautifulSoup) 선택사항
* re
* requests 선택사항
* subprocess 필수사항
<sub>위 모듈들은 pip 혹은 easy_install 등을 사용하여 설치해주세요!</sub>

## 스크래이핑!(선택사항)
우선, 스크래이핑할 Site를 잘 분석하셔야 합니다. 예를 들어 제가 이번에 파싱해볼 <a href="http://hansung.ac.kr/web/www/cmty_01_01">한성대학교 한성공지</a> 사이트를 개발자 도구를 이용해 확인해보면, table tag와 class는 bbs-list 안에 우리가 원하는 내용이 있는 것을 볼 수 있습니다.
<br>
이런 부분들을 가져오기 위해 JSON방법 등을 활용할 수 있지만, 여기서는 bs4(BeautifulSoup)과 requests를 사용하겠습니다.
<br>
```py
r = requests.get("http://hansung.ac.kr/web/www/cmty_01_01")
soup = BeautifulSoup(r.text, "html.parser")
mr = soup.find("table", class_="bbs-list")
# 그 뒤는 알아서...
```
여기서 requests는 HTTP GET 요청을 보내는 모듈이고, 이를 통해 응답을 받아볼 수 있습니다.<br>
bs4(BeautifulSoup)은 간단하게 소개하자면, HTML태그들을 사용하기 편하게 Python의 객체 형태로 바꾸는 모듈입니다.<br>
이런 형식으로, 스크래이핑 후 re모듈(문자열관련 모듈)로 원하는 부분을 잘 추출하시면 됩니다.

## 알리미
여기서는 subprocess 모듈을 만지게 됩니다.<br>
subprocess모듈은 간단하게 소개를 하자면, 외부 프로그램을 실행해주고, 결과값을 반환 받을 수 있는 모듈입니다. 이 강의는 Ubuntu 18.04 LTS기반으로 되어있기 때문에, subprocess에 <a href="https://wiki.archlinux.org/index.php/Desktop_notifications">notify-send</a>라는 프로그램을 사용하여, 알리미가 오도록 해볼 것입니다. 
<br>
코드는 다음과 같습니다.<br>
```py
subprocess.Popen(['notify-send'], "새로운 한성공지가 나왔습니다!")
```
이를 위에 스크래이핑한 부분과 접목을 시킨다고 생각해봅시다. 한성대학교 한성공지를 처음 실행 시 외부에 저장해두고, 스크래이핑해서 새로운 공지가 나왔을 경우 한성공지를 저장해 둔 파일을 수정 및 subprocess를 통한 알림을 주면, 간단한 알리미가 완성됩니다.
<br>

총 소스는 다음과 같습니다. <a href="https://bitbucket.org/hong9802/hansungnotice">bitbucket 링크</a>
<br>
