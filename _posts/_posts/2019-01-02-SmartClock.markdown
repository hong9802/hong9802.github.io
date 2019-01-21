---
layout: post
title:  "Smart 탁상시계"
date:   2019-01-02 07:46:14 +0700
categories: Hansung Univ
---
## 소개
본 문서는 아두이노와 WeMos보드, 블루투스 + 온습도 센서 등을 이용해서 만든 스마트 탁상시계입니다.<br>
본 문서의 결과물은 아래 bitbucket소스와 영상이 있습니다.<br>
<a href="https://www.youtube.com/watch?v=yjzkyfAVpsc">YouTube 링크</a><br>
## 들어간 기능
* HW 및 SW 인터럽트
   - 시간 측정 기능
* IoT디바이스와 스마트폰 앱간 블루투스 양방향 통신 기능
   - 온습도 측정 기능
   - 시간 측정 기능
* IoT플랫폼에 Acting 기능
   - 시간 측정 기능
   - 트윗 기능
* IoT 디바이스에서 센싱한 데이터를 IoT플랫폼에 업로드
   - 온습도 측정 기능
   - 시간 측정 기능

<b> 기본적으로 시계이다보니, 시간 알림 기능을 가지고 있음</b><br>
아두이노에서 모든 처리를 하고, WeMos에서는 Data만 올리도록 설계되어 있음.
## 결과물
# <a href="https://bitbucket.org/hong9802/osmcu_project/src/master/Arduino/OSMCU.ino">Arduino</a>
```C
#define DS3231_I2C_ADDRESS 104
#include <dht11.h>
#define DHT11PIN 9
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <SoftwareSerial.h>
#include <MsTimer2.h>
#define bTx 8
#define bRx 7
#define btnPin 2
#define Buzzer 6
LiquidCrystal_I2C lcd(0x27, 16, 2);
SoftwareSerial btSerial(bRx, bTx);
String s1 = "";
String host = "http://api.thingspeak.com";
String url = "/update?api_key=";
//String key = "Input your Thingspeak_Channel_API_Key";
String key = "Input your Thingspeak_Channel_API_Key";
String field1 = "&field1=";
String field2 = "&field2=";
String field3 = "&field3=";
dht11 DHT11;
String sender = "";//ThingSpeak에 전달용 문자열
int delaytime = 0; //ThingSPeak Delay변수
int counter = 0; //스톱워치용 시간 변수
boolean interrupt = false; //버튼입력을 구분하는 변수
volatile boolean mode = false; //HW인터럽트 변수
volatile boolean countmode = false; //스톱워치 확인용 변수
boolean once = false; //시간 저장 체크용 변수
byte seconds, minutes, hours, day, date, month, year;
byte savehours, savemin; //시간 저장용 변수
void btnHandler() //HW인터럽트 함수
{
  mode= !mode;
}
//get3231Date code by http://deneb21.tistory.com/327
void get3231Date()
{
  Wire.beginTransmission(DS3231_I2C_ADDRESS);
  Wire.write(0x00);
  Wire.endTransmission();
  Wire.requestFrom(DS3231_I2C_ADDRESS, 7);
  if (Wire.available()) {
    seconds = Wire.read(); // get seconds
    minutes = Wire.read(); // get minutes
    hours   = Wire.read();   // get hours
    day     = Wire.read();
    date    = Wire.read();
    month   = Wire.read(); //temp month
    year    = Wire.read();

    seconds = (((seconds & B11110000) >> 4) * 10 + (seconds & B00001111)); // convert BCD to decimal
    minutes = (((minutes & B11110000) >> 4) * 10 + (minutes & B00001111)); // convert BCD to decimal
    hours   = (((hours & B00110000) >> 4) * 10 + (hours & B00001111)); // convert BCD to decimal (assume 24 hour mode)
    day     = (day & B00000111); // 1-7
    date    = (((date & B00110000) >> 4) * 10 + (date & B00001111)); // 1-31
    month   = (((month & B00010000) >> 4) * 10 + (month & B00001111)); //msb7 is century overflow
    year    = (((year & B11110000) >> 4) * 10 + (year & B00001111));
  }
  else
  {
    //oh noes, no data!
  }
}
void stopwatch() //SW인터럽트 함수
{
  if(counter > 0)
    counter--;
  delaytime++;
}
void setup()
{
  Wire.begin();
  Serial.begin(115200);
  btSerial.begin(9600);
  lcd.init();
  lcd.backlight();
  attachInterrupt(0, btnHandler, FALLING);
  pinMode(btnPin, INPUT);
  pinMode(Buzzer, OUTPUT);
  MsTimer2::set(1000, stopwatch);//SW인터럽트 설정
  MsTimer2::start();//시작!
}
void loop()
{
  get3231Date();
  lcd.clear();
  if(mode == false)//초기는 무조건 false 누르면 true
  {
    if(interrupt == true)
    {
      if(savehours > hours)
        hours += 24;
      if(savemin > minutes)
      {
        hours -= 1; minutes += 60;
      }
      byte caltime = (hours - savehours)*60 + (minutes-savemin);
      String sender = host + url + key + field3 + caltime;
      Serial.print(sender);//분단위로 체크 후 전송
      interrupt = false;
      once = false;
    }
  }
  else
  {
    if(interrupt == true)
    {
      lcd.setCursor(14, 0);
      lcd.print("OK");
    }
    else
    {
      //savemin = minutes;sender
      //savehours = hours;
      lcd.setCursor(14, 0);
      lcd.print("OK");
      interrupt = true;
    }
  }
  if(interrupt == true && once == false) //시간 저장용
  {
    once = true;
    savemin = minutes;
    savehours = hours;
  }
  if (counter == 0 && countmode == false) //스톱워치 설정용
  {
    while (btSerial.available())
    {
      char myChar = (char)btSerial.read();
      s1 += myChar;;
      delay(5);
    }
    if (!s1.equals(""))
    {
      counter = s1.toInt();
      countmode = true;
      s1 = "";
    }
  }
  else if (counter == 0 && countmode == true)
  {
    btSerial.println(9999); countmode = false;
    digitalWrite(Buzzer, HIGH);
    lcd.setCursor(0, 0);
    lcd.print("StopWatch END");
    delay(2000);
    digitalWrite(Buzzer, LOW);
  }
  int chk = DHT11.read(DHT11PIN);
  //btSerial.print("습도 :");
  String timer = "20" + (String)year + "-" + hours + ":" + minutes + ":" + seconds;
  float tem, h;
  tem = (float)DHT11.temperature;
  h = (float)DHT11.humidity;
  btSerial.print(tem, 2);
  btSerial.print("\n");
  btSerial.print("\n");
  btSerial.print(h, 2);
  lcd.setCursor(0, 0);
  lcd.print(timer);
  lcd.setCursor(0, 1);
  String output = (String)tem + "^C " + (String)h + "%";
  lcd.print(output);
  if (delaytime == 15)
  {
    sender = host + url + key + field1 + tem + field2 + h;
    Serial.print(sender);
    delaytime = 0;
  }
  delay(1000);
}
```
# <a href="https://bitbucket.org/hong9802/osmcu_project/src/master/WeMos/sketch_nov05a.ino">WeMos</a>
```C
#include <ESP8266WiFi.h>
#include <ESP8266HTTPClient.h>
char *ssid = "Your_SSID";
char *password = "Your_Password";
String url = "";
void setup() {
  // put your setup code here, to run once:
  Serial.begin(115200);
  WiFi.mode(WIFI_STA);
  WiFi.disconnect();
  Serial.print("Connecting to \'");
  Serial.print(ssid);
  Serial.println("\'");
  WiFi.begin(ssid, password);
  while(WiFi.status() != WL_CONNECTED)
  {
    delay(500);
    Serial.print(".");
  }
  Serial.println();
  Serial.println("WiFi connected...");

  Serial.println();
  Serial.println("* AP information");
  Serial.print("SSID : "); Serial.println(WiFi.SSID());
  Serial.print("RSSI : "); Serial.println(WiFi.RSSI());
  Serial.print("GateWay IP : "); Serial.println(WiFi.gatewayIP());

  Serial.println();
  Serial.println("* station information");
  Serial.print("IP Adress : "); Serial.println(WiFi.localIP());
  Serial.print("MAC Adress : "); Serial.println(WiFi.macAddress());
}
void loop() {

  HTTPClient httpClient;
  while(Serial.available())
  {
    char myChar = (char)Serial.read();
    url += myChar;
    delay(5);
  }
  Serial.println(url);
  httpClient.begin(url);
  int httpCode = httpClient.GET();
  if(httpCode > 0)
    Serial.println("Success");
  else
  {
    Serial.println("Failed");
  }
  if(!url.equals(""))
  {
    url = "";
  }
  delay(15000);
}
```
