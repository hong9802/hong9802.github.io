---
layout: post
title:  "수위측정 경보계"
date:   2018-09-27 03:45:11 +0700
categories: Hansung Univ
---
## 소개
본 문서는 아두이노와 수위측정센서를 이용해서 만든 경보계입니다.<br>
본 문서의 결과물은 아래 bitbucket소스와 영상이 있습니다.<br>
<a href="https://www.youtube.com/watch?v=d9mfk2lDWHk">YouTube 링크</a><br>

## 결과물
# <a href="https://bitbucket.org/hong9802/opensourcehw/src/master/WaterLevel/WaterLevel.ino">Arduino</a>
```C
int buzzer = 8;
int led = 9;
int waterSensor = A0;
int waterLevel = 0;
void setup() {
  // put your setup code here, to run once:
  pinMode(buzzer, OUTPUT);
  pinMode(led, OUTPUT);
  Serial.begin(9600);
}

void loop() {
  // put your main code here, to run repeatedly:
  waterLevel = analogRead(waterSensor)/4;
  Serial.println(waterLevel);
  if(waterLevel > 150)
  {
    digitalWrite(buzzer, HIGH);
    digitalWrite(led, HIGH);
  }
  else if(waterLevel > 130 && waterLevel < 150)
  {
    digitalWrite(buzzer, HIGH);
    digitalWrite(led, HIGH);
    delay(100);
    digitalWrite(buzzer, LOW);
    digitalWrite(led, LOW);
    delay(100);
  }
  else if(waterLevel > 70 && waterLevel < 130)
  {
    digitalWrite(buzzer, HIGH);
    digitalWrite(led, HIGH);
    delay(500);
    digitalWrite(buzzer, LOW);
    digitalWrite(led, LOW);
    delay(500);
  }
  else if(waterLevel > 30 && waterLevel < 70)
  {
    digitalWrite(buzzer, HIGH);
    digitalWrite(led, HIGH);
    delay(1000);
    digitalWrite(buzzer, LOW);
    digitalWrite(led, LOW);
    delay(1000);
  }
  delay(500);
}
```