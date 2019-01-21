---
layout: post
title:  "Joystick 값을 LCD(i2c)를 이용하여 표시하기"
date:   2018-09-27 03:45:13 +0700
categories: Hansung Univ
---
## 소개
본 문서는 아두이노와 Joystick + LCD를 이용했습니다.<br>
본 문서의 결과물은 아래 bitbucket소스와 영상이 있습니다.<br>
<a href="https://www.youtube.com/watch?v=_NqtkG_T2Og">YouTube 링크</a><br>

## 결과물
# <a href="https://bitbucket.org/hong9802/opensourcehw/src/master/Joystickwithlcd/Joystickwithlcd.ino">Arduino</a>
```C
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
int JoyX = 0;
int JoyY = 1;
int value1 = 0;
int value2 = 0;
LiquidCrystal_I2C lcd(0x27,16,2);
void setup()
{
  lcd.init();
  lcd.backlight();
}
void loop()
{
  lcd.clear();
  value1 = analogRead(JoyX);
  value2 = analogRead(JoyY);
  lcd.setCursor(0, 0);
  lcd.print("Joy X : ");
  lcd.setCursor(8, 0);
  lcd.print(value1);
  lcd.setCursor(0, 1);
  lcd.print("Joy Y : ");
  lcd.setCursor(8, 1);
  lcd.print(value2);
  delay(500);
}
```