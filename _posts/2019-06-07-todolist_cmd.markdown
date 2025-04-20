---
layout: post
title:  "간단한 todolist앱을 만들어보자!"
date:   2019-06-07 16:00:10 +0900
categories: MeuWorks
---

## 주의사항!
본 문서는 <b>Python 3</b>를 좋아하는 사람이 다녀간 문서입니다.  
본 문서는 <b>Ubuntu 18.04</b>를 너무 사랑한 나머지 <b>Ubuntu 18.04 </b>기준으로 작성되었습니다.  
본 문서는 터미널로 일정 추가하고 지우는 앱이 있었으면 좋겠다는 생각에 시작했읍니다.  
본 문서에서 필요한 모듈들은 다음과 같습니다.  
* sqlite3
* sys
* subprocess
* datetime
  

## 간단한 설명!
본 문서는 database에 날짜와 일정을 등록합니다.  
본 문서에서 make_string함수는 일정을 하나의 문자열로 만들기 위해 사용됩니다.  
나머지는 database에서 날짜를 check 후에 subprocess를 통해 알려줍니다.  
<sub>본 문서에서 알려주는 기능은 init.d를 요구합니다.</sub>  

## 사용방법
```bash
$ python3 todo.py -a your_date your_work
```
ex) python3 todo.py -a 2019-06-15 할머니집 가기!  
ex) python3 todo.py -d 2019-06-15  

# 옵션들!
* -a : 일정을 database에 추가하기
* -s : 시작!(init.d 등록이 필요!)
* -d : 일정 지우기!
  

## 결과물
# <a href="https://bitbucket.org/hong9802/todo_list_cmd/src/master/todo.py">Python3</a>
```py
import sqlite3
import subprocess
import sys
from datetime import datetime

#python3 todo.py -a 2019-06-15 doing my workblablabla

def make_string(): #make a work sentence...
    s = ""
    for i in range(3, len(sys.argv), 1):
        s += sys.argv[i] + " "
    return s

if (__name__ == "__main__"):
    conn = sqlite3.connect("ToDo_List.db")
    curs = conn.cursor()
    try:
        curs.execute("SELECT * FROM todolist")
    except sqlite3.OperationalError as e:
        curs.execute("create table todolist(date, work)")
    if(sys.argv[1] == "-a"): #append your date & work
        insert_data = "insert into todolist(date, work) values (?, ?)"
        curs.execute(insert_data, [sys.argv[2], make_string()])
        print("Done~")
    elif(sys.argv[1] == "-s"): #using init.d with looping coodinate
        times = curs.fetchall()
        for i in range(0, len(times), 1):
            if(times[i][0] == datetime.now().strftime("%Y-%m-%d")):
                subprocess.Popen(['notify-send', times[i][1]])
                break
    elif(sys.argv[1] == "-d"): #delete your date & work...
        times = curs.fetchall()
        del_data = "DELETE FROM todolist WHERE date = '" + sys.argv[2] + "'"
        for i in range(0, len(times), 1):
            if(times[i][0] == sys.argv[2]):
                curs.execute(del_data)
                print("Done~~")
                break
    conn.commit()
    conn.close()
```
