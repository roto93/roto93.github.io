---
title: Expo 內購設定
date: 2021-12-02 23:38:23
tags:
---

<font size="2" color="#aaa">

## <font color="#f4a261">前言</font>

有點久沒發文了，來更新一下近況
現在到了2021年的最後一個月，開始有點壓力了
記得年初的時候訂了個目標，說今年一定要上架APP
現在只剩不到30天，好緊張啊

今天就要來分享一下有關內購式產品的設定

## <font color="#f4a261">什麼是內購</font>

應用程式內購買項目(In App Purchase, IAP)
是跟買斷相對的概念
意思是應用程式本身也許是免費的，但使用者可能會在APP內課金
沒錯! 就是課金!
無論是單次購買體力，或是訂閱制，pro 等等都屬於 IAP

react native 是可以支援 IAP 的
但在 Expo 上可能就有點麻煩
在 Expo SDK v40 以前，只有 eject 一條路可以走
不想 eject 就沒門

但在 Expo SDK v41 之後，出現了新東西: development client
雖然我還不知道這東西到底是做什麼的以及好在哪
但貌似可以讓各位"Expoer"在不用 eject 的情況下也能使用 native 相關功能
感覺很棒，這樣我就可以應用在去除廣告上了

## <font color="#f4a261">前置作業</font>

首先我們要完成幾件事情，但因為不是本篇的重點，我會直接放說明文件連結

1. 安裝 [EAS](https://docs.expo.dev/build/setup/)(Expo Application Services)
2. 將專案至少[升級](https://docs.expo.dev/workflow/upgrading-expo-sdk-walkthrough/)到 Expo SDK v41.0 以上
3. 擁有 [Google play](https://play.google.com/intl/zh-TW/console/about/) 或 [App Store](https://developer.apple.com/programs/) 的開發人員帳號

由於我會先在 Google play 上架，目前就先以 Android 版來示範
iOS 不知道在貴什麼啦爛透ㄌ

## <font color="#f4a261">安裝 react-native-purchase</font>

以正常流程來說，如果我們要提供內購產品
必須修改 native 程式碼，為 APP 新增一個 `com.android.vending.BILLING` 的權限
這也是為什麼我們原本需要 eject 
但現在

> Recent versions of React Native will automatically link the SDK, so all that's needed is to install the library.

我們只要在專案中安裝 react-native-purchase，直接完成這個部分
就不用再修改原生程式碼了
```
npm install react-native-purchase
```

## <font color="#f4a261">Build 出一個 dev-client</font>

首先 `npm install expo-dev-client`
接著跟著[這些步驟](https://docs.expo.dev/development/build/) configure EAS (如果你還沒做的話)
最後 `eas build -p android --profile development`

相關細節可參考[官網](https://docs.expo.dev/development/getting-started/)


## <font color="#f4a261">註冊 RevenueCat 帳號</font>
其實在前面安裝的 react-native-purchase 除了是為了加上 billing 權限以外
還有一個理由是因為我們要用 RevenueCat
RevenueCat 是一個幫助你完成 IAP 的服務
除了可以統一管理各平台的內購項目外