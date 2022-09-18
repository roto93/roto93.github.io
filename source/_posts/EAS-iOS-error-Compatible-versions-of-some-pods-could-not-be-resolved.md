---
title: 'EAS iOS error: Compatible versions of some pods could not be resolved'
date: 2022-09-15 12:22:39
tags:
---

<font size="2" color="#aaa">

## <font color="#f4a261">前言</font>

最近總算花了 999 鎂 (但卻是 3400 台幣!?)註冊了開發者帳戶
總算可以打包 expo dev client 到 iPhone 上了

所以前幾天註冊了我自己的 iPhoneX 到 JapanGo 上
並下載 dev client 測試，沒什麼問題
那我就想，應該把 Josie 的 iPhone SE 也拿來測試一下
畢竟那是最小的 iPhone
那因為我要多註冊一個裝置，所以必須重新打包
這時 EAS 就報錯了

## <font color="#f4a261">題外話：等好久...</font>

不知道是不是因為凌晨是國外的 APP 打包旺季
EAS build 居然需要排隊 270 分鐘!!!
阿不就幸好我可以去睡覺 (其實也早該睡了)
不然也等太久了吧
算了反正我是 EAS 免費仔
不付費就是要排隊XD

## <font color="#f4a261">回來正題：EAS 報錯</font>

總之我睡了一覺起來就看到紅色的 error
錯誤訊息如下: 

```
Error: Compatible versions of some pods could not be resolved.
You are seeing this error because either:
  - Versions in the Podfile.lock cached by EAS do not match required values in Podspecs of some of the libraries. To fix that add the "cache.key" field (it can be set to any value) in the build profile in eas.json to invalidate the cache.
  - Some of the pods used in your project depend on different versions of the same pod. See logs for more information.
```

看起來是 cache 出問題，訊息中也提到說要忽略 cache
因此方法很單純，只要再重新`eas build`
並記得加上 `--clear-cache` 的 flag 
把 cache 清掉就好了

[參考資料](https://stackoverflow.com/questions/73318976/expo-eas-build-ios-fails-in-pod-installation-step-sdk45-46)

## <font color="#f4a261">結論</font>

如果需要重新打包 APP 的話，建議都加上 `--clear-cache` flag
尤其是當你有更改打包的設定時