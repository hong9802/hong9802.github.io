---
layout: post
title:  "LED를 이용한 3색 신호등"
date:   2018-09-27 03:45:10 +0700
categories: Hansung Univ
---
## 소개
본 문서는 아두이노와 LED 3개를 이용해서 만든 3색 신호등입니다.(신호등이라고 봐야하나?)<br>
본 문서의 결과물은 아래 bitbucket소스와 영상이 있습니다.<br>
<a href="https://www.youtube.com/watch?v=1vaP2G66OsI">YouTube 링크</a><br>

## 결과물
# <a href="https://bitbucket.org/hong9802/opensourcehw/src/master/led3traffic/led3traffic.ino">Arduino</a>
```C
int green = 12; //LED핀 지정
int yellow = 8;
int red = 7;
int i = 0;
int ledtime[4] = {1000, 500, 250, 125}; //delay시간!
void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  pinMode(green, OUTPUT);
  pinMode(yellow, OUTPUT);
  pinMode(red, OUTPUT);
}
void loop() {
  // put your main code here, to run repeatedly:
  for(i = 0; i < 4; i++)
  {
    if(i == 4) 
    {
      i = 0;
      break;
    }
    digitalWrite(green, HIGH);
    delay(ledtime[i]);
    digitalWrite(green, LOW);
    digitalWrite(yellow, HIGH);
    delay(ledtime[i]);
    digitalWrite(yellow, LOW);
    digitalWrite(red, HIGH);
    delay(ledtime[i]);
    digitalWrite(red, LOW);
    Serial.println(i);
  }
}

```