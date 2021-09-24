---
title: Firebase Google登入
date: 2021-04-01 09:32:47
tags:
---
***
繼之前介紹 Firebase 的 Email 登入和匿名登入後

這篇要示範的是 Google 登入

主要可以分成下列幾個環節：
- #### 開啟 google API 
- #### 使用 Expo 的 Google Auth SDK
- #### 使用 Firebase 的登入流程

個人覺得有點小複雜，原因正如我之前說的
Firebase 和 google 之間多了 Expo 的交接
需要一些 firebase 官網沒有提供的環節 

像是 user 物件的內容就不一樣
我需要兩邊 doc 來回查才知道到底是怎樣

這真的要記錄下來
不然我大概一下就忘了

***

## <font color="#BC2C1A">一、開啟 google API    
#### [參考 link](https://docs.expo.io/versions/v40.0.0/sdk/google/#using-it-inside-of-the-expo-app)
#### 1. 首先到 google 的 [credentials page](https://console.developers.google.com/apis/credentials)

#### 2. 建立專案
<img src="firebase google Auth_1.png" style="box-shadow:3px 3px 8px darkgray">
&nbsp;

<img src="firebase google Auth_2.png" style="box-shadow:3px 3px 8px darkgray">

這邊無法選擇內部，因此放心選外部
後續還有一些步驟但不成問題

#### 3. 回到憑證頁面 -> 建立憑證 -> 選 OAuth 用戶端 ID

<img src="firebase google Auth_3.png" style="box-shadow:3px 3px 8px darkgray">

#### 4. 依照手機系統選擇 Android / iOS

<img src="firebase google Auth_4.png" style="box-shadow:3px 3px 8px darkgray">

#### 5. 設定套件名稱＆憑證指紋
- 套件名稱填 `host.exp.exponent`
- 在 terminal 中 run `openssl rand -base64 32 | openssl sha1 -c` 並將 output 填入 憑證指紋

***
## <font color="#BC2C1A">二、使用 Expo 的 Google Auth SDK    

#### [參考 link](https://docs.expo.io/versions/v40.0.0/sdk/google/#using-it-inside-of-the-expo-app)

`expo install expo-google-app-auth`



```javascript
import * as Google from 'expo-google-app-auth';

async function signInWithGoogleAsync() {
  try {
    const result = await Google.logInAsync({
      androidClientId: YOUR_CLIENT_ID_HERE, //在這裡貼上你的用戶端編號, 記得加引號
      // iosClientId: YOUR_CLIENT_ID_HERE,
      scopes: ['profile', 'email'],
    });

    if (result.type === 'success') {
      return result.accessToken;
    } else {
      return { cancelled: true };
    }
  } catch (e) {
    return { error: true };
  }
}
```

用戶端編號可以從 Google API 的憑證頁面中找到

***

## <font color="#BC2C1A">三、使用 Firebase 的登入流程 

#### [參考 link](https://firebase.google.com/docs/auth/web/google-signin)

firebase 提供的 popup/redirect 兩種方法
只能用在 web 上，在手機上會報錯：

<font color="red">`Error: This operation is not supported in the environment this application is running on. "location.protocol" must be http, https or chrome-extension and web storage must be enabled.`

所以我們在行動端只能用[進階做法](https://firebase.google.com/docs/auth/web/google-signin#expandable-2)

## 1. 到 firebase console 開啟 Google sign in 的功能
## appId 和 appKey 可以在 Google API 頁面找到


## 2. 定義 `onSignIn()`
```javascript
function onSignIn(googleUser) {
        var unsubscribe = firebase.auth().onAuthStateChanged((firebaseUser) => {
            unsubscribe();   
							// 似乎是listener的常用語法，藉由呼叫函式本身，讓後面的程式只運行一次就好
            if (!isUserEqual(googleUser, firebaseUser)) {
                var credential = firebase.auth.GoogleAuthProvider.credential(
                    googleUser.idToken,
                    googleUser.accessToken
                );

                firebase.auth().signInWithCredential(credential)
                    .catch((error) => { alert('onSignInCredential'+error) });
            } else {
                console.log('User already signed-in Firebase.');
            }
        });
    }
```

- 為什麼 `unsubscribe` 裡面還有 `unsubscribe`? [link](https://stackoverflow.com/questions/47043188/firebase-onauthstatechanged-unsubscribe-recursion)

- `GoogleAuthProvider.credential()`有兩個input，`idToken`和`accessToken`，官網給的寫法是`googleUser.getAuthResponse().id_token`，但因為我們用的是 Expo `Google.logInAsync`回傳的`googleUser`，他不含任何函式，自然就沒有`getAuthResponse()`可以用。 [link](https://stackoverflow.com/questions/60711364/react-native-googleuser-getbasicprofile-is-not-a-function)

## 3. 定義 isUserEqual()
```javascript
function isUserEqual(googleUser, firebaseUser) {
        if (firebaseUser) {
            var providerData = firebaseUser.providerData;
            for (var i = 0; i < providerData.length; i++) {
                if (providerData[i].providerId === firebase.auth.GoogleAuthProvider.PROVIDER_ID &&
                    providerData[i].uid === googleUser.user.id) {   
                    // We don't need to reauth the Firebase connection.
                    return true;
                }
            }
        }
        return false;
    }
```
- 這邊的 `googleUser.user.id` 也是上述的原因

## 4. 把`onSignIn()`放進 `signInWithGoogleAsync()` 裡並傳入 user 物件
```javascript
async function signInWithGoogleAsync() {
        try {
            const result = await Google.logInAsync({
                androidClientId: '994735805040-7q2nlrcnma0mssgvs7fimrmdp6dvuaqk.apps.googleusercontent.com', //在這裡貼上你的用戶端編號
                // iosClientId: YOUR_CLIENT_ID_HERE,
                scopes: ['profile', 'email'],
            });

            if (result.type === 'success') {
                onSignIn(result)               // <---- HERE
                return result.accessToken;
            } else { return { cancelled: true } }
        } catch (e) { return { error: true } }
    }
```

完成！ 可以到[這裡](https://github.com/roto93/react-native-firebase-works)參考我的範例
