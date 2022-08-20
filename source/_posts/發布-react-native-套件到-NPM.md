---
title: 發布 react native 套件到 NPM
date: 2021-07-19 01:13:47
tags:
---

<font size="2" color="#aaa">

## <font color="#f4a261">前言</font>

最近把日期選擇器開發到一個段落了
差不多要發布了吧...

等等! 我的開發紀錄才寫了兩篇欸 
後面還有很多內容沒寫
而且其實唯一寫的那兩篇規劃的東西大部分都改了...

好啦我之後補!!!
現在誰也不能阻止我發布套件!!!

## <font color="#f4a261">NPM 是什麼?</font>

NPM, Node Package Manager
是 NodeJS 專屬的 套件管理工具

就像 Python 有 pip，Ruby 有 gem 一樣
裡面放著來自世界各地工程師提供的套件
讓你可以不用從頭開始打造，就能使用許多功能

`npm` command line tool 會在你安裝 NodeJS 時一起被安裝

今天這篇便是要來介紹如何在 npm 上發布自己的套件
那我當然就以 react native 為例囉!

## <font color="#f4a261">發布步驟</font>

#### 1. 建立專案資料夾

```
mkdir react-native-neat-date-picker
cd react-native-neat-date-picker
```

#### 2. 連結 github

到 github 上建立一個新的 repository 叫 react-native-neat-date-picker
並將它連結到你的專案資料夾

```
echo "# test" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/roto93/react-native-neat-date-picker.git
git push -u origin main
```

#### 3. 建立專案

執行 `npm init`
接下來他會問你一系列問題
以下是我的輸入值

```
package name: (react-native-date-picker) react-native-neat-date-picker
version: (1.0.0)
description: An easy-to-use date picker for React Native
entry point: (index.js)
test command:
git repository: (https://github.com/roto93/react-native-date-picker.git)
keywords: react-native, date, datePicker, date-picker, date picker, react native
author: roto93
license: (ISC) MIT
```

<font size="2">
	
- **package name:** 套件的名稱。
- **version:** 版本，通常從 1.0.0 開始。
之後每次你 publish 套件到 npm 時都必須更新版本，如果沒更新 npm 不會讓你發布。
- **description:** 套件描述。可以在這裡描述套件的主要功能。
- **entry point:** 套件的程式進入點。
一般來說不會更動，維持index.js就好。
雖然自訂進入點也是可以，但會不利於其他人 contribute
- **test command:** 執行測試的指令，我目前沒用到所以沒深入研究。
- **git repository:** 原始碼來源。
之所以會在 npm init 之前就先建立 github repo 就是為了讓這裡能自動設定。
我個人是認為 github 要打從一開始就設定好比較不會有問題
- **keywords:** 關鍵字。
意思是其他人可以藉由查詢什麼關鍵字來找到你的套件，越多越相關越好。
- **author:** 作者。
通常就放自己的 github 帳號，或名字
- **license:** 認證。
open source 的認證百百種，通常最簡單的就選 ISC 或 MIT。
他們的意思都差不多是：「嘿 自己拿去用啦 你可以自由修改 但不要哪天反過來說是我抄你的ㄛ」
</font>

#### 4. 建立套件進入點

剛剛有提到 index.js 是套件的進入點，意思是你必須讓你的套件要提供的功能從 index.js 中 export 出去。
你可以選擇直接把套件主程式寫在 index.js 裡
或者是你也可以寫在其他檔案中，import 進 index.js 裡，再 export 
我的作法是後者，因為一開始開發時我就直接把主元件寫在 components 資料夾裡了，
如果現在又改變結構的話還要處理一些 import 的路徑問題
但我懶

```js
// index.js
import DatePicker from './src/components/NeatDatePicker'
export default DatePicker
```

#### 5. 設定 Babel

Babel 簡單來說是一個語法轉換器。
以網頁為例，就是讓瀏覽器可以使用 JS 的新語法

很幸運地，有現成的 preset 可以使用

```
npm install metro-react-native-babel-preset --save-dev
```

接著新增一個 `.babelrc` 檔案
裡面放:

```
{
  "presets": ["module:metro-react-native-babel-preset"]
}
```

這部分就 ok 了

#### 6. 建立 .gitignore 和 .npmignore

這兩份檔案會影響到當你上傳套件或 push 到 github 時，要忽略那些檔案
這邊提供簡單的 template
可以再視情況做增減

`.gitignore`
```
# Logs
*.log
npm-debug.log

# Runtime data
tmp
build
dist

# Dependency directory
node_modules
```

`.npmignore`
```
# Logs
*.log
npm-debug.log

# Dependency directory
node_modules

# Runtime data
tmp

# Examples (If applicable to your project)
examples
```

#### 7. 本地測試

在發布之前，比較好的做法是先進行本地安裝測試看看
如果先發布，後來卻發現有嚴重問題的話就會修改得很匆忙
因為很可能已經有人下載來用了
然後他們就會覺得這套件怎麼這麼爛XD
與其如此不如在發布前先確保沒有什麼大問題

雖然這麼說
但我其實這步驟沒有成功
但我還是把流程放上來辣

首先在你即將發布套件的專案中執行
`npm link`
這行命令會告訴 npm
這個資料夾的專案是一個可安裝的套件喔

接著你進到本地的任何一個 app 專案中
執行 `npm link react-native-neat-date-picker`
便會進行本地安裝

如果以上步驟遇到問題
也可以選擇以路徑安裝
如: `npm install {PATH_TO_PACKAGE}`

這個做法的先天缺陷是一旦你對套件本身有做任何修改
你必須重新安裝一次，才能更新

不過很可惜上述兩個方法我都沒成功，
link 沒報錯，install 也沒 error
但 import 時就告訴我找不到 module
試了網路上很多方法都不行，所以我乾脆先跳過
直接發布試試看
結果可以 run ! 到底是怎樣 !!!

#### 8. 發布

終於進到發布的環節了
首先我們登入 npm (你要先去[註冊](https://www.npmjs.com))

```
npm login
```

接著

```
npm publish
```

完成! 現在任何人都可以上去下載你提供的套件了
國家...不
我是說全世界都會感謝你的:)))


##  <font color="#f4a261">結語</font>

上傳我的第一個 npm 套件實在是很興奮
可是也隱約有股壓力，因為不知道大家會不會覺得東西很爛
而且因為我發布的時候連 README 都很不完整，
也就是說前期下載的人可能完全不知道怎麼用
所以發布後到寫文的現在經過的這兩天
我便盡力把 README 補充到至少知道用法的地步
最後
希望世界上有人會喜歡我的套件
哈哈哈哈哈

</font>
