---
layout: post
title:  "안드로이드 롬을 빌드해보자!"
date:   2023-08-14 00:02:39 +0900
categories: android
---
  
## 빌드하기
이전 포스트에서 소스 코드를 받았으니, 이제 빌드를 한번 해보려 한다.  
먼저 빌드를 하기 위한 준비로는 소스 코드를 받은 디렉토리로 들어가서 빌드 준비를 한다. 필자의 경우 소스 코드를 android 폴더에 받았으므로, 다음과 같이 입력했다.
```Shell
$ cd android
$ source build/envsetup.sh
```
  
위 명령어를 실행하면 빌드 준비가 끝난 것이다.  
이제부터 디바이스에 맞는 device 코드와 kernel코드를 받아야 한다. 필자가 사용하는 S10 5G의 경우 'beyondx'라는 모델명을 가지고 있으니, 다음과 같이 명령어를 입력하면 된다.
```Shell
$ breakfast beyondx
```  
하지만, 필자의 경우 vendor가 안받아졌는지… vendor폴더의 누락으로 오류가 발생했다 ㅠㅜ…  
이럴 때는 휴대폰을 컴퓨터와 USB디버깅 모드로 연결한 후에 디바이스 소스 코드(device/제조사명/기기명) 내에 있는 extract-files.sh를 실행하면 된다고 한다. 그러나 S10 5G에서 필요한 파일을 완벽하게 추출하지 못해서 오류가 생겼다. 그래서 필자가 선택한 방법은 이거였다.
  
github에 있는 [beyondx vendor파일](https://github.com/PixelExtended-Devices/vendor_samsung_beyondx)을 받아서 추출되지 않은 부분들을 vendor폴더(vendor/제조사명/기기명)에 넣었다. 그리고 다시 'breakfast beyondx'하니 완벽하게 작동되는 것을 볼 수 있었다.  
'breakfast'로 어떤 것을 빌드할 지 지정했다면 이제 남은 것은 빌드이다.  
빌드는 'brunch'라는 명령어를 통해 하며, 'brunch' 뒤에 기기명을 붙여주면 된다.
```Shell
$ brunch beyondx
```  
'brunch'를 입력하는 시점부터 빌드가 시작되기에 엄청나게 오랜 시간이 걸린다.
이 과정에서 'prebuilt/arm64/webview.apk'가 없다는 것을 보고 LineageOS github에 있는 것을 받아서 정상적으로 빌드가 성공되었다.
  
여담이지만, 이 방식으로 했을때 webview가 정상적으로 동작하지 않아서 gapps와 관련된 것들이 제대로 동작하지 않았다. 필자는 apkmirror에서 최신 webview버전을 받아서 넣으니 잘 동작하는 것을 봐서는 webview.apk를 잘못넣었던 것 같아보인다 ㅠㅜ…
  
암튼 이렇게 빌드가 끝나면 out/target/product/기기명 폴더 안에 파일에 lineage_기기명_ota-eng.사용자명.zip과 recovery.img가 있을 것이다.
recovery.img는 fastboot으로 flash시켜준 뒤 리커버리 모드로 진입하여 lineage_기기명_ota-eng.사용자명.zip을 install 시켜주면 내가 빌드한 롬을 올려볼 수 있다.  
참고로, twrp로 설치를 해봤던 결과 /mnt/system이 마운트 안된다고 빠꾸먹은 전례가 있었다 ㅠㅜ… 얌전하게 lineage 리커버리로 설치하면 gapps도 그렇고, 빌드한 롬도 그렇고 설치가 잘 되니까 lineage 리커버리를 사용하자.

