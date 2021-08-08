---
title: Firebase Facebook 登入
date: 2021-04-01 13:31:04
tags:
---
***

這篇文章會告訴你如何在 react native 利用 firebase 進行 Facebook 登入

主要可以分成三個步驟：
1. 設定 facebook API 
2. 連結 Expo 和 facebook
3. 連結 Firebase

***

## <font color="#BC2C1A"> 一、設定 facebook API

#### 1. 安裝 Expo 的 expo-facebook
```
expo install expo-facebook
```

#### 2. 到 [Facebook for Developers](https://developers.facebook.com/?no_redirect=1) 註冊並建立應用程式

<img src="Firebase_Facebook_Auth1.png" style="box-shadow:3px 3px 8px darkgray">

我們要的是facebook登入，所以選「打造互聯體驗」

<img src="Firebase_Facebook_Auth2.png" style="box-shadow:3px 3px 8px darkgray">

輸入名稱和email

<img src="Firebase_Facebook_Auth3.png" style="box-shadow:3px 3px 8px darkgray">

#### 3. 設立 → 基本資料 → 新增平台

<img src="Firebase_Facebook_Auth5.png" style="box-shadow:3px 3px 8px darkgray">

#### 4. 選擇 iOS 和 Android

<img src="Firebase_Facebook_Auth6.png" style="box-shadow:3px 3px 8px darkgray">

#### 5. 
#### 在 ios 套件組合編號輸入 `host.exp.Exponent`
#### 在 android 金鑰輸入 `rRW++LUjmZZ+58EbN5DVhGAnkX4=`
#### → 儲存變更

<img src="Firebase_Facebook_Auth7.png" style="box-shadow:3px 3px 8px darkgray; height: 500px" >


糟糕為了放一些圖結果文章變得好長XD 

趕快進到下個步驟

***

## <font color="#BC2C1A"> 二、連結 Expo 和 facebook

#### 1. 到 App 中定義 `onlogIn()`

```javascript
async function onLogIn() {
        try {
            await Facebook.initializeAsync({
                appId: YOUR_APP_ID,      // 換成你的AppId
            });

            //{type,token,expirationDate,permissions,declinedPermissions,}
            const result = await Facebook.logInWithReadPermissionsAsync({
                permissions: ['public_profile', 'email'],   // 還有其他 permission
            });
            if (result.type === 'success') {
                // 利用 FB 提供的圖形API fetch 用戶名
                const response = await fetch(`https://graph.facebook.com/me?access_token=${result.token}`);
		alert( await response.json().name)
		//setToken(result.token) 
		//在這把token存起來，有時會在onLogIn外面遇到

                console.log('Logged in!');
            } else {
                console.log('type = cancel')
            }
        } catch ({ message }) {
            alert(`Facebook Login Error: ${message}`);
        }
    }
```
Firebase提供的方法有些部分不適用Expo
所以這步驟與官網相比有更改一些變數
像是把`Facebook.logInWithReadPersissionsAsync()`回傳的值設成`result`

完成後可以先 run 看看
在執行 `onLogIn()` 時
會跳出 popup 問你要登入哪個 fb 帳戶
如果手機本身已經有登入 fb 帳號的話
就會直接跳出是否提供權限的視窗，按同意
但我們還沒有定義在 firebase 註冊的方法，所以還沒辦法登入

***

## <font color="#BC2C1A"> 三、連結 Firebase

#### 1. 到 firebase console 開啟 facebook sign in 的功能
 appId 和 appKey 可以在 facebook API 頁面找到

#### 2. 定義 `checkLoginState()`
```javascript
function checkLoginState(response) {
        let unsubscribe = firebase.auth().onAuthStateChanged((firebaseUser) => {
            unsubscribe();
            if (!isUserEqual(response, firebaseUser)) {
                let credential = firebase.auth.FacebookAuthProvider.credential(
                    response.token);
                firebase.auth().signInWithCredential(credential)
                    .catch((error) => { });
            } else {
                console.log('User is already signed-in Firebase with the correct user.')
            }
        });
    }
```
Firebase 提供的方法有些部分不適用 Expo
所以這步驟有更改一些變數
像是刪除`response.authResponse`的段落
以及將`response.authResponse.accessToken`修改成`response.token`

#### 3. `定義 isUserEqual()`
```javascript
function isUserEqual(facebookAuthResponse, firebaseUser) {
        if (firebaseUser) {
            var providerData = firebaseUser.providerData;
            for (var i = 0; i < providerData.length; i++) {
                if (providerData[i].providerId === firebase.auth.FacebookAuthProvider.PROVIDER_ID &&
                    providerData[i].uid === facebookAuthResponse.userID) {
                    // We don't need to re-auth the Firebase connection.
                    return true;
                }
            }
        }
        return false;
    }
```

最後在`onLogIn()`裡面呼叫`checkLoginState()`
並傳入 result 便完成登入

***

## <font color="#BC2C1A"> 小插曲

最後用 facebook 登入時遇到一個問題
如果這個用戶之前有用其他登入方式 (如email) 註冊過的話
又正好他也用同個 email 來註冊 facebook
這樣當他用 facebook 註冊 app 時就會報錯

這時有幾個解決方案：

#### 1. 要求用該方式登入
用 `fetchSignInMethodsForEmail()` 找到當初用這個 email 登入的方法，然後跳出視窗請用戶改用此方法登入。

#### 2. 讓他創建新帳號 獨立運作
到 **Firebase console** -> **Sign-in Method** -> **進階**
允許用相同 email 建立不同帳戶

<img src="Same_Email_1.png" style="box-shadow:3px 3px 8px darkgray;" >

讓用不同管道登入的相同帳號獨立運作
但這有時不太好，因為登入最初的用意就是針對每個真實用戶
如果一個用戶能有多個帳號，有點違背初衷

#### 3. 讓他創建新帳號 再合併

與上個步驟相同，但在用戶登入後請他合併帳號
可用 [linkWithCredential()](https://firebase.google.com/docs/reference/js/firebase.User#linkwithcredential) 

***

Facebook 還有提供 [圖形API](https://developers.facebook.com/docs/graph-api/)
聽說功能強大
之後有機會學一學 再整理上來






