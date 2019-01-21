---
layout: post
title:  "적외선 리모콘 제어(with LCD & Buzzer)"
date:   2018-09-27 03:45:14 +0700
categories: Hansung Univ
---
## 소개
본 문서는 아두이노와 적외선 리모콘 + LCD + Buzzer를 이용했습니다.<br>
본 문서의 결과물은 아래 bitbucket소스와 영상이 있습니다.<br>
<a href="https://www.youtube.com/watch?v=TiXfGVc6lWE">YouTube 링크</a><br>

## 결과물
# <a href="https://bitbucket.org/hong9802/opensourcehw/src/master/IRwithLCDandBuzzer/IRwithLCDandBuzzer.ino">Arduino</a>
```C
#include <IRremote.h>
int RECV_PIN = 11;
IRrecv irrecv(RECV_PIN);
decode_results results;
int delaytime[5] = {100, 500, 1000, 1500, 2000};
int delays = 100;
void setup()
{
  Serial.begin(9600);
  pinMode(8, OUTPUT);
  pinMode(9, OUTPUT);
  irrecv.enableIRIn();
}
void loop() {
  if (irrecv.decode(&results)) 
  {
    Serial.println(results.value, HEX);
    if(results.value == 0xFF6897)
    {
      delays = delaytime[0];
    }
    else if(results.value == 0xFF30CF)
    {
      delays = delaytime[1];
    }
    else if(results.value == 0xFF18E7)
    {
      delays = delaytime[2];
    }
    else if(results.value == 0xFF7A85)
    {
      delays = delaytime[3];
    }
    else if(results.value == 0xFF10EF)
    {
      delays = delaytime[4];
    }
    irrecv.resume(); //Receive the next value
    }
    digitalWrite(8, HIGH);
    digitalWrite(9, HIGH);
    delay(delays);
    digitalWrite(8, LOW);
    digitalWrite(9, LOW);
    delay(delays);
}
```