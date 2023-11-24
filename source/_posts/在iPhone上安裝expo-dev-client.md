---
title: 在 iPhone 上安裝 expo dev client
date: 2023-11-24 17:14:30
tags:
---

<font size="2" color="#999">

## <font color="#f4a261">前言</font>

最近要在 expo 專案加上相機功能，由於會調用到 native api，必須重新安裝 dev client
又因為相機功能不能在 emulator 測試，所以必須想辦法裝在 iphone 上
簡單紀錄一下做法

## <font color="#f4a261">步驟</font>

1. **下載 xcode 並更新到最新版本**

2. **開啟 iPhone 的開發者模式(需要重啟手機)**
   設定 > 隱私權與安全性 > (滑到最下面)開發者模式

3. **把 iPhone 用 USB 連接上電腦**
   除了插線，也要記得解鎖手機按允許連接

4. **照著 [這些步驟](https://github.com/expo/fyi/blob/main/setup-xcode-signing.md) 設定簽證，過程中會要求登入 apple 帳號**

5. **打包 app 並安裝到手機上**
   `npx expo run:ios -d`
   -d 代表要選擇裝置。然後 expo 會做出一個 ios 資料夾，安裝所需的東西

6. **選擇實體裝置(透過 USB 連上的 iPhone)，便會開始 build 並安裝到手機上**

7. **前兩步驟如果遇到問題，可以改用 Xcode 開啟專案直接 build**
   build 之前如果還沒有 ios 資料夾，可以用 `npx expo prebuild -p ios` 生成一個

8. **安裝過程中會需要輸入兩次密碼**

9. **完成**

## <font color="#f4a261">小發現</font>

意外得知其實 emulation 跟 simulation 不一樣
中文雖然都叫模擬
但 emulation 的意思是在一個系統下運行另一個系統，達到模擬
simulation 則是建立理論模型後去預測結果，這也是模擬

所以像前述的 iPhone 模擬器就是在電腦系統下去運行 iOS 系統，是 **emulator**
