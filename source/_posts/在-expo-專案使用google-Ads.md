---
title: 在 expo 專案使用 google Ads
date: 2022-09-03 16:55:09
tags:
---

<font size="2" color="#aaa">

## <font color="#f4a261">前言</font>

當完兵後把 JapanGo 從 Expo SDK 42 升級到 46
(因為 42 已經不能用了)
跨度這麼大的升級必然會導致一些套件不能用
例如 `expo-app-loading` 和 `expo-ads-admob`
前者只要改成使用 expo-splash-screen 就可以了
但處理後者花了我不少時間

根據 [Expo SDK 45 Doc](https://docs.expo.dev/versions/v45.0.0/sdk/admob/)
`expo-ads-admob`在 SDK 46 之後就不能用了
建議改用[react-native-google-mobile-ads](https://github.com/invertase/react-native-google-mobile-ads)

## <font color="#f4a261">react-native-google-mobile-ads 基本用法</font>

首先安裝

```bash
npm install react-native-google-mobile-ads
```

到 [google admob](https://admob.google.com/home/?utm_source=sem&utm_medium=text&utm_campaign=2022-admob-apac-apac&utm_content=rsa&gclid=CjwKCAjw9suYBhBIEiwA7iMhNKdrqxpgsuQbieggu-V-cqbNMAPcmpgiaNW3vxAaClmCULfKPOS7gBoC6pMQAvD_BwE&gclsrc=aw.ds) 取得 Android 和 iOS 的 app id (如果還沒有的話)

接著一樣在 google admob 中取得 ad id
或者也可以從 [這裡](https://developers.google.com/admob/android/test-ads) 取得測試id

下一步，編輯`app.json`
這邊需要注意插入以下程式碼的位置，
react-native-google-mobile-ads 的相關設定必須在 expo 之外

```json
{
  "expo": {
    ...
  },
  "react-native-google-mobile-ads": {
    "android_app_id": "ca-app-pub-xxxxxxxxxxxxxxxx~xxxxxxxxxx",
    "ios_app_id": "ca-app-pub-xxxxxxxxxxxxxxxx~xxxxxxxxxx"
  }
}
```

再來打開你要放廣告的元件
這邊我也 test id 為例

```javascript
import { BannerAd, BannerAdSize, TestIds } from 'react-native-google-mobile-ads';

...

<BannerAd
  size={BannerAdSize.BANNER}
  unitId={TestIds.BANNER}
/>
```

最後一步，由於這個元件需要動用原生 API
代表我們不能夠用 Expo Go 來預覽開發了
而是要改用 expo dev client
這篇我就不深入探討這是什麼
可以先當作我們要客製一個可以動用原生元件的 Expo Go 來用

結果這邊就出錯了

## <font color="#f4a261">無法 build...</font>

執行 `eas build --profile development` 後

<img src='error.jpg' />

重點應該在第 27 行(重新排版了一下)

```
/home/expo/workingdir/build/android/app/src/debug/AndroidManifest.xml:17:85-105 Error:
Attribute meta-data#com.google.android.gms.ads.DELAY_APP_MEASUREMENT_INIT@value value=(true) 
from AndroidMenifest.xml:17:85-105 is also present at [:react-native-google-mobile-ads] 
AndroidManifest.xml:19:13-34 value=(false).

Suggestion: add 'tools:replace="android:value"' to <meta-data> element 
at AndroidManifest.xml:17:5-107 to override.
```

雖然不太清楚意思
但大概可以猜到問題出在`Android.Menifest.xml`裡的
`DELAY_APP_MEASUREMENT_INIT`這個值
前者是 true 後者是 false

於是我爬了一些資料:

[how do I use react-native-google-mobile-ads with expo and expo go](https://stackoverflow.com/questions/72337988/how-do-i-use-react-native-google-mobile-ads-with-expo-and-expo-go)

[[Medium] migrate expo-ads-admob to react-native-google-mobile-ads](https://medium.com/react-native-expo/migrate-expo-ads-admob-to-react-native-google-mobile-ads-3747b3a3b75e)

[[github issue] migrate expo-ads-admob to react-native-google-mobile-ads](https://github.com/invertase/react-native-google-mobile-ads/issues/150)

[Add installation guide for Expo users](https://github.com/invertase/react-native-google-mobile-ads/issues/155)

關鍵是最後一篇
[這個留言](https://github.com/invertase/react-native-google-mobile-ads/issues/155#issuecomment-1142400646)提到一個可能的解法是進到 android 資料夾更改 AndroidMenifest.xml 
但那需要 eject，先盡可能避免這個做法
不過我[往下看](https://github.com/invertase/react-native-google-mobile-ads/issues/155#issuecomment-1161686309)發現作者有提供在`app.json`設定menifest的方法:

```json
"react-native-google-mobile-ads": { 
     "android_app_id": "ca-app-pub-3940256099942544~3347511713", 
     "ios_app_id": "ca-app-pub-3940256099942544~1458002511", 
     "delay_app_measurement_init": false, 
     "user_tracking_usage_description": "This identifier will be used to deliver personalized ads to you." 
   } 
```

接著我在進一步去翻套件的檔案
在`european-user-consent.mdx`裡看到這段說明

```
### Delaying app measurement

By default, the Google Mobile Ads SDK initializes app measurement and begins sending user-level event data to Google immediately when the app starts.
If your app will be used by users within the EEA, it is important you prevent app measurement until your first ad has been requested (after consent).

Within your projects `app.json` file, set the `delay_app_measurement_init` to `true` to delay app measurement:

``json
{
  "react-native-google-mobile-ads": {
    "android_app_id": "ca-app-pub-xxxxxxxx~xxxxxxxx",
    "ios_app_id": "ca-app-pub-xxxxxxxx~xxxxxxxx",
    "delay_app_measurement_init": true
  }
}
``
```

這時我就想
既然 error message 說套件的 DELAY_APP_MEASUREMENT_INIT 和 Expo 裡的不合
那就設為 true 吧
然後就成功了!!!!!!!!! 可喜可賀

## <font color="#f4a261">結論</font>

其實還有很多環節不懂
例如這個 delay app measurement init 是什麼意思
或者說任何跟原生 code 有關的東西我都不知道是什麼意思...
總覺得還有好長一段路要走

但這次的 debug 經驗還不錯
有從 error message 判斷出一些可能的方向
然後看了很多討論串
也實際去翻套件的程式碼
雖然不是完全懂了才解決問題
但也不至於誤打誤撞 不清楚自己做了什麼

總之 APP 的廣告功能又回來啦！

