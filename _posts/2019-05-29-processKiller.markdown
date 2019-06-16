---
layout: post
title:  "프로세스 죽이는 프로그램을 만들어보자!!"
date:   2019-05-29 23:15:10 +0900
categories: MeuWorks
---

## 주의사항!
본 문서는 <b>Python 3</b>성애자가 다녀간 문서입니다.<br>
본 문서는 <b>Window</b>를 너무 사랑한 나머지 <b>Windows 10</b>기준으로 작성되었습니다.<br>
본 문서는 <b>Skype</b>를 싫어해서 만든 프로그램입니다.<br>
본 문서에서 필요한 모듈들은 다음과 같습니다.<br>
* os
* sys
* signal
* sqlite3
* time
<br>

## 간단한 설명!
본 문서는 os 모듈을 통해 process를 죽이는 프로그램입니다.<br>
본 문서에서 process_kill함수는 프로세스를 죽이기 위해 사용됩니다.<br>
본 문서에서 add_kill_program함수는 database에 죽일 프로그램 이름을 추가합니다.<br>


## 결과물
# <a href="https://bitbucket.org/hong9802/process_killer/src/master/main.py">Python3</a>
```py
import os
import sys
import signal
import sqlite3
import time

def process_kill(kill_program_name):
    tasklist = os.popen("tasklist").read()
    tasklist = tasklist.split()
    try:
        start_number = tasklist.index(kill_program_name)
        os.kill(int(tasklist[start_number + 1]), signal.SIGTERM)
    except ValueError as e:
        return
        #print("Process have been terminated...")

def add_kill_program(program_name):
    i = 0
    conn = sqlite3.connect("program_name.db")
    curs = conn.cursor()
    insert_data = "insert into program_name(name) values (?)"
    try: #program_name table이 있다면~
        curs.execute("SELECT * FROM program_name")
        all_program_name = curs.fetchall()
        for i in all_program_name:
            if(program_name in i):
                print("already in ban table")
                i = -1
                break
        if(i != -1):
            curs.execute(insert_data, [program_name])
            conn.commit()
            conn.close()
    except sqlite3.OperationalError as e: #program_name table이 없다면
        curs.execute("create table program_name(name)")
        curs.execute(insert_data, [program_name])
        conn.close()

if __name__ == "__main__":
    print("Process 관리 프로그램")
    mode = 4
    while(mode):
        print("mode (1) kill_process_name add (2) process kill start! (0) exit")
        mode = int(input())
        if(mode == 1):
            print("죽일 프로그램 이름을 입력하세요.(.exe까지 포함)")
            name = input()
            add_kill_program(name)
            mode = 4
        elif(mode == 2):
            print("끄기 위해서는 Crtl + C를 누르세요 ^^")
            conn = sqlite3.connect("program_name.db")
            curs = conn.cursor()
            curs.execute("SELECT * FROM program_name")
            all_program_name = curs.fetchall()
            conn.close()
            while True:
                for i in all_program_name:
                    process_kill(i[0])
                time.sleep(1)
```
