---
title: 控制 Expo app 的讀取畫面
date: 2021-05-23 09:54:13
tags:
---

<font size="2" color="#aaa">


<font color='gold'>

#### (2022.08.22 更新) 從 Expo SDK 45 之後 expo-app-loading 已經 deprecated，只能使用 expo-splash-screen

</font>

## <font color="#BC2C1A">介紹</font>
---

Expo 提供了兩個 API
讓你能夠修改 app 的讀取畫面
分別是 [AppLoading](https://docs.expo.io/versions/latest/sdk/app-loading/) 跟 [SplashScreen](https://docs.expo.io/versions/v41.0.0/sdk/splash-screen/)

AppLoading 是一個元件，當整個app渲染的只有AppLoading而沒有別的元件時
它就會讓 splash screen 持續顯示
所以我們就可以在 app 剛打開正在進行 API call 時先渲染 AppLoading 
等資料都得到了再恢復顯示主畫面

另一個 SplashScreen 就不是元件了
但他提供兩個函數 

1. `SplashScreen.preventAutoHideAsyce()`:防止 splash screen 在程式讀取完後關閉
2. `SplashScreen.hideAsyce()`: 觸發時，關閉 spalsh screen 

簡單來說他讓我們能自由控制 splash screen 消失的時機
除了可以達到跟單獨使用 AppLoading 一樣的效果外
更厲害的是我們也能藉此讓App初始化完成後
由顯示靜態 splash screen 切換成動畫(看你要放 gif 或寫 animation 都可以)

## <font color="#BC2C1A">使用方式</font>
---

首先下載並引入

```
expo install expo-app-loading
expo install expo-splash-screen
```

```javascript
import AppLoading from 'expo-app-loading';
import * as SplashScreen from 'expo-splash-screen';
```

再來，若你只想單獨使用 AppLoading 可以這樣做:

```javascript
const [isLoading, setIsLoading] = useState(true)

const fetchData = async () => {
    try{
        await fetch('...')
    } catch(e){
        console.log(e)
    }finally{
        setIsLoading(false)
    }
}

useEffect(()=>{
    fetchData()
},[])

if (isLoading) {
    return <AppLoading/>
} else return <App/>
```

其實這可以應付大部分的狀況
例如在進入app前先讀取字型等等的
然而使用 SplashScreen 的話靈活度更高

```javascript
 const [isLoading, setIsLoading] = useState(true)

const fetchData = async () => {
    try{
    await SplashScreen.preventAutoHideAsync() //等等!先別關!
        await fetch('...') // 做一些事
    } catch(e){
        console.log(e)
    }finally{
        SplashScreen.preventAutoHideAsync() //好可以關了
    }
}

useEffect(()=>{
    fetchData()
},[])

if (isLoading) {
    return null //不渲染東西 但因為 splash screen 還蓋在畫面上，所以不會沒畫面
} else return <App/>
```

<font color='gold'>

#### (2022.08.22 更新) 從 Expo SDK 45 之後 expo-app-loading 已經 deprecated，只能使用 expo-splash-screen

</font>