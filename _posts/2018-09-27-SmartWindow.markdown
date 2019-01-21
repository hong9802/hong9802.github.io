---
layout: post
title:  "SmartWindow Prototype"
date:   2018-09-27 03:46:14 +0700
categories: Hansung Univ
---
## 소개
본 문서는 아두이노와 블루투스 + 미세먼지 센서 등을 이용해서 만든 스마트 창문입니다.<br>
본 문서의 결과물은 아래 bitbucket소스와 영상이 있습니다.<br>
<a href="https://www.youtube.com/watch?v=m74aqhykTCo">YouTube 링크</a><br>

## 결과물
# <a href="https://bitbucket.org/hong9802/opensourcehw/src/master/SmartWindow/SmartWindow.ino">Arduino</a>
```C
#include <SoftwareSerial.h>
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <dht11.h>
#define DHT11PIN 12
dht11 DHT11;  
int state = 2; //1 Open 2 Closed
int dustpin = 8;
unsigned long duration;
unsigned long starttime;
unsigned long sampletime_ms = 5000;
unsigned long lowpulseoccupancy = 0;
float ratio = 0;
float concentration = 0;
float pcsPerCF = 0;
float ugm3 = 0;
int DCmotorA = 4;
int DCmotorB = 5;
int bluetoothTx = 2;
int bluetoothRx = 3;
int bluetoothmode = 0; //블루투스 상태 체크
int ledA = 9; //블루투스 Mode ON
int ledB = 10; //블루투스 Mode가 X
SoftwareSerial bluetooth(bluetoothTx, bluetoothRx);
LiquidCrystal_I2C lcd(0x27, 16, 2);
void Opening()
{
  Serial.println("Opening...");
  delay(2000);
  digitalWrite(DCmotorA, LOW);
  digitalWrite(DCmotorB, HIGH);
  analogWrite(6, 255);
  delay(5000);
  digitalWrite(DCmotorA, LOW);
  digitalWrite(DCmotorB, LOW);
  state = 1;
}
void Closeing()
{
  Serial.println("Closeing...");
  delay(2000);
  digitalWrite(DCmotorA, HIGH);
  digitalWrite(DCmotorB, LOW);
  analogWrite(6, 255);
  delay(5000);
  digitalWrite(DCmotorA, LOW);
  digitalWrite(DCmotorB, LOW);
  state = 2;
}
void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  starttime = millis();
  delay(100);
  bluetooth.begin(9600);
  delay(100);
  pinMode(dustpin, INPUT);
  pinMode(DCmotorA, OUTPUT);
  pinMode(DCmotorB, OUTPUT);
  pinMode(ledA, OUTPUT);
  pinMode(ledB, OUTPUT);
  lcd.init();
  lcd.backlight();
}

void loop() {
  // put your main code here, to run repeatedly:
  Serial.println(state);
  lcd.clear();
  duration = pulseIn(dustpin, LOW);
  lowpulseoccupancy += duration;
  if((millis() - starttime) > sampletime_ms)
  {
    ratio = lowpulseoccupancy / (sampletime_ms *10.0);
    concentration = 1.1 * pow(ratio, 3) - 3.8 * pow(ratio, 2) + 520 * ratio + 0.62;
    pcsPerCF = concentration *100;
    ugm3 = pcsPerCF/13000;
    lowpulseoccupancy = 0;
    starttime = millis();
  }
  int chk = DHT11.read(DHT11PIN);
  lcd.setCursor(0, 0);
  lcd.println("Humidity: ");
  lcd.setCursor(10, 0); lcd.print(DHT11.humidity);
  lcd.setCursor(0, 1);
  lcd.print("Temp:");
  lcd.setCursor(5, 1); lcd.print(DHT11.temperature);
  lcd.setCursor(8, 1);
  lcd.print("Dust:");
  lcd.setCursor(13, 1); lcd.print(ugm3);
  delay(100);
  char cmd;
  Serial.println(bluetoothmode);
  if(bluetooth.available() || bluetoothmode == 1) // 블루투스 모드일 시
  {
    digitalWrite(ledA, HIGH);
    digitalWrite(ledB, LOW);
    Serial.println("input Command ON");
    cmd = (char)bluetooth.read();
    if(state == 1) //창문이 열려 있을 시
    {
      if(cmd == '2') //창문 닫기 명령일 시
        Closeing();
    }
    if(state == 2)//창문이 닫혀 있을 시
    {
      if(cmd == '1')//창문 열기 명령일 시
        Opening();
    }
    if(cmd == '3') //블루투스 모드 고정
      bluetoothmode = 1;
    if(cmd == '4') //블루투스 모드 OFF, Auto모드 On
      bluetoothmode = 0;
  }
  else //블루투스 Mode가 아닐 시(Auto모드)
  {
    digitalWrite(ledA, LOW);
    digitalWrite(ledB, HIGH);
    Serial.println("No bluetooth");
    if(state == 1)//창문이 열려 있을 시
    {
      if((ugm3 > 119 || DHT11.humidity > 80 || DHT11.temperature > 26) == 1) //온도, 습도, 미세먼지 농도가 저 조건일 시
      {
        Closeing();
      }
    }
    if(state == 2)//창문이 닫혀 있을 시
    {
      if((ugm3 < 119 && DHT11.humidity < 80 && DHT11.temperature < 27) == 1)
      {
        Serial.println("Automatic Opening...");
        Opening();
      }
     }
   }
  delay(100);
}
```