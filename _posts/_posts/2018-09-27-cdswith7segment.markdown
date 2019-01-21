---
layout: post
title:  "CDS(조도센서)를 이용한 밝기 표시(7Segment 활용)"
date:   2018-09-27 03:45:12 +0700
categories: Hansung Univ
---
## 소개
본 문서는 아두이노와 CDS + 7Segment를 이용해서 만든 밝기 표시기입니다.<br>
본 문서의 결과물은 아래 bitbucket소스와 영상이 있습니다.<br>
<a href="https://www.youtube.com/watch?v=wvD3aPnQdUk">YouTube 링크</a><br>

## 결과물
# <a href="https://bitbucket.org/hong9802/opensourcehw/src/master/cdswith7segment/cdswith7segment.ino">Arduino</a>
```C
//주의! 7Segment마다 display_number가 다르게 될 수도 있음!
const int _a=2;
const int _b=3;
const int _c=4;
const int _d=5;
const int _e=9;
const int _f=8;
const int _g=7;
const int _dot=6;

void display_number(int n)
{
  switch(n)
  {
  case 0:
    digitalWrite(_e,LOW);
    digitalWrite(_b,LOW);
    digitalWrite(_c,LOW);
    digitalWrite(_d,LOW);
    digitalWrite(_g,LOW);
    digitalWrite(_f,LOW);
    digitalWrite(_a,HIGH);
    digitalWrite(_dot,HIGH);
    break;
  case 1:
    digitalWrite(_e,HIGH);
    digitalWrite(_b,HIGH);
    digitalWrite(_c,HIGH);
    digitalWrite(_d,HIGH);
    digitalWrite(_g,LOW);
    digitalWrite(_f,LOW);
    digitalWrite(_a,HIGH);
    digitalWrite(_dot,HIGH);
    break;
  case 2:
    digitalWrite(_e,LOW);
    digitalWrite(_b,HIGH);
    digitalWrite(_c,LOW);
    digitalWrite(_d,LOW);
    digitalWrite(_g,HIGH);
    digitalWrite(_f,LOW);
    digitalWrite(_a,LOW);
    digitalWrite(_dot,HIGH);
    break;
  case 3:
    digitalWrite(_e,LOW);
    digitalWrite(_b,HIGH);
    digitalWrite(_c,HIGH);
    digitalWrite(_d,LOW);
    digitalWrite(_g,LOW);
    digitalWrite(_f,LOW);
    digitalWrite(_a,LOW);
    digitalWrite(_dot,HIGH);
    break;
  case 4:
    digitalWrite(_e,HIGH);
    digitalWrite(_b,LOW);
    digitalWrite(_c,HIGH);
    digitalWrite(_d,HIGH);
    digitalWrite(_g,LOW);
    digitalWrite(_f,LOW);
    digitalWrite(_a,LOW);
    digitalWrite(_dot,HIGH);
    break;
  case 5:
    digitalWrite(_e,LOW);
    digitalWrite(_b,LOW);
    digitalWrite(_c,HIGH);
    digitalWrite(_d,LOW);
    digitalWrite(_g,LOW);
    digitalWrite(_f,HIGH);
    digitalWrite(_a,LOW);
    digitalWrite(_dot,HIGH);
    break;
  case 6:
    digitalWrite(_e,LOW);
    digitalWrite(_b,LOW);
    digitalWrite(_c,LOW);
    digitalWrite(_d,LOW);
    digitalWrite(_g,LOW);
    digitalWrite(_f,HIGH);
    digitalWrite(_a,LOW);
    digitalWrite(_dot,HIGH);
    break;
  case 7:
    digitalWrite(_e,LOW);
    digitalWrite(_b,LOW);
    digitalWrite(_c,HIGH);
    digitalWrite(_d,HIGH);
    digitalWrite(_g,LOW);
    digitalWrite(_f,LOW);
    digitalWrite(_a,HIGH);
    digitalWrite(_dot,HIGH);
    break;
  case 8:
    digitalWrite(_e,LOW);
    digitalWrite(_b,LOW);
    digitalWrite(_c,LOW);
    digitalWrite(_d,LOW);
    digitalWrite(_g,LOW);
    digitalWrite(_f,LOW);
    digitalWrite(_a,LOW);
    digitalWrite(_dot,HIGH);
    break;
  case 9:
    digitalWrite(_e,LOW);
    digitalWrite(_b,LOW);
    digitalWrite(_c,HIGH);
    digitalWrite(_d,LOW);
    digitalWrite(_g,LOW);
    digitalWrite(_f,LOW);
    digitalWrite(_a,LOW);
    digitalWrite(_dot,HIGH);
    break;
  case 10:
    digitalWrite(_e,HIGH);
    digitalWrite(_b,HIGH);
    digitalWrite(_c,HIGH);
    digitalWrite(_d,HIGH);
    digitalWrite(_g,HIGH);
    digitalWrite(_f,HIGH);
    digitalWrite(_a,HIGH);
    digitalWrite(_dot,LOW);
    break;
  }
}
void setup()
{
  pinMode(_a,OUTPUT);
  pinMode(_b,OUTPUT);
  pinMode(_c,OUTPUT);
  pinMode(_d,OUTPUT);
  pinMode(_e,OUTPUT);
  pinMode(_f,OUTPUT);
  pinMode(_g,OUTPUT);
  pinMode(_dot,OUTPUT);
  Serial.begin(9600);
};
void loop()
{
  int cds, i;
  cds = analogRead(0)/4;
  Serial.println(cds);
  if(cds < 26)
    i = 0;
  else if(cds > 25 && cds < 51)
    i = 1;
  else if(cds > 50 && cds < 76)
    i = 2;
  else if(cds > 75 && cds < 101)
    i = 3;
  else if(cds > 100 && cds < 126)
    i = 4;
  else if(cds > 125 && cds < 151)
    i = 5;
  else if(cds > 150 && cds < 176)
    i = 6;
  else if(cds > 175 && cds < 201)
    i = 7;
  else if(cds > 200 && cds < 226)
    i = 8;
  else if(cds > 225)
    i = 9;
  display_number(i);
  delay(1000);
}
```