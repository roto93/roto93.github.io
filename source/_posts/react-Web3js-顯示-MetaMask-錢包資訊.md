---
title: react+Web3js  顯示 MetaMask 錢包資訊
date: 2022-10-13 22:44:25
tags:
---

<font size="2" color="#999">

## <font color="#f4a261">前言</font>

因緣際會踏入區塊鏈這個圈子
也創建了自己的 MetaMask 錢包
雖然還沒有錢可以放進去 QQ
但身為工程師就要試著自己在網站中連接錢包並顯示出來！


## <font color="#f4a261">Web3.js</font>

[Web3.js](https://web3js.readthedocs.io/en/v1.2.11/index.html) 是一個用來和以太坊區塊鏈互動的 library
讓你可以跟本地的區塊鏈，或是用 HTTP, IPC, WebSocket 去跟遠端的區塊鏈互動
安裝方式很簡單

```bash
npm install web3
```

他提供很多 api，讓你可以做到連接錢包，查詢餘額，或是跟智能合約互動等等的功能


## <font color="#f4a261">一：取得 Provider</font>

首先要 import web3

```javascript
import Web3 from 'web3'
```

並且取得 provider

```javascript
const web3 = new Web3(Web3.givenProvider)
```

所謂的 provider 我認為可以把它當作「對區塊鏈發出請求的實體」
他就像律師一樣，幫我跟以太坊交談
(不知道為什麼會突然拿律師當例子，可能最近看太多禹英禑ㄌ)
例如當你要查詢餘額，就對 provider 發出請求，他會在跟乙太坊要資料後做出回應。
網路上查到的一個解釋如下：

> Web3 provider allows your application to communicate with an Ethereum or Blockchain Node. 
> Providers take JSON-RPC requests and return the response. 
> This is normally done by submitting the request to an HTTP or IPC socket based server.

另外，如果我們是在測試階段，就不一定要真的連上以太坊網路
那我們就可以設定一個 fallback URL
以 Ｇanache 為例 ([Ganache](https://trufflesuite.com/ganache/) 是一個讓你能夠搭建虛擬區塊鏈的工具)
他的測試 port 是 `http://localhost:8545`
於是上面的code 可以改寫成這樣
```javascript
const web3 = new Web3(Web3.givenProvider || 'http://localhost:8545')
```

## <font color="#f4a261">二：連接錢包</font>

稍微看了幾個 Web3 網站，發現通常都會有一個按鈕叫做「connect wallet」
按下去後 MetaMask 小視窗就會跳出來，按下確認後網頁就跟錢包做好連接了
我們現在就是要來做這件事情
需要用到 `web3.eth.requestAccounts` API
不過我這次暫時先寫在`useEffect`裡面好了

```javascript
const [account, setAccount] = useState('');

useEffect(() => {
  // run once at mount
  const loadAccount = async () => {
    const accounts = await web3.eth.requestAccounts() // 回傳的是 array
    setAccount(accounts[0])
  }
  loadAccount()
}, [])
```

如此一來就取得錢包帳號了


## <font color="#f4a261">三：顯示餘額</font>

連上錢包就能顯示餘額
要用到的是`web3.eth.getBalance`這個 API，並傳入剛剛的帳號
另外我們也可以利用`web3.eth.net.getNetworkType`來檢視目前連接的區塊鏈網路

```javascript
const [network, setNetwork] = useState('');
const [balance, setBalance] = useState(0);

useEffect(() => {
  const loadBalance = async () => {
    const network = await web3.eth.net.getNetworkType()
    const balance = await web3.eth.getBalance(account) // given in Wei
    setNetwork(network)
    setBalance(balance)
  }
  loadBalance()
}, [account])
```

記得填入 dependency array,
每當`account`更新，我們都要重新呼叫`loadBalance()`

最後，我們將步驟二跟三合併在同一個`useEffect`裡面
由於如果還沒有取得`account`就無法呼叫`getBalance()`
所以還需要一個判斷

```javasacript
if (!account) {
  loadAccount()
} else loadBalance()
```

## <font color="#f4a261">結論</font>

至此就完成了極簡易的 dapp
之後再繼續探究更多功能！

完整 code 如下

```javascript
import { useEffect, useState } from 'react'
import './css/App.css'
import Web3 from 'web3'

function App() {
  const [account, setAccount] = useState('');
  const [network, setNetwork] = useState('');
  const [balance, setBalance] = useState(0);

  useEffect(() => {
    const GANACHE_URL = 'http://localhost:8545'
    const web3 = new Web3(Web3.givenProvider || GANACHE_URL)

    const loadAccount = async () => {
      const accounts = await web3.eth.requestAccounts()
      setAccount(accounts[0])
    }

    const loadBalance = async () => {
      const network = await web3.eth.net.getNetworkType()
      const balance = await web3.eth.getBalance(account) // given in Wei
      setNetwork(network)
      setBalance(balance)
    }

    if (!account) {
      loadAccount()
    } else loadBalance()
  }, [account])

  return (
    <div className="App">
      <header className='header'>

        <h2>My First Web3 </h2>

        <hr style={{ marginBottom: '2rem' }} />

        <p className="account">
          <span>Account: </span>{account}
        </p>

        <p className="account">
          <span>Network: </span>{network || 'loading...'}
        </p>

        <p className="account">
          <span>Balance: </span>{balance ? `${balance / 1000000000} GWei` : 'loading...'}
        </p>

      </header>
    </div>
  )
}

export default App

```