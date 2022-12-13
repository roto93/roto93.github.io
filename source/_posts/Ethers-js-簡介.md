---
title: Ethers.js 簡介
date: 2022-12-13 11:56:47
tags:
---

<font size="2" color="#999">

## <font color="#f4a261">前言</font>

之前在面試期間稍微碰了一下 web3.js
當時只有照做的感覺，對區塊鏈的運作還很不熟
但至少對怎麼跟小狐狸互動有點概念

後來有幸進到區塊鏈公司，第一件事主管就請我先研究 ethers.js 和 web3modal
我發現 web3 和 ethers 是兩個很類似的套件
都是在處理錢包、智能合約互動之類的事情

整體來說
web3 背後有比較多人在維護，網路上的教學也比較多
ethers 則輕量很多，支援用 ENS name 來呼叫合約，state(provider) 和 key(wallet) 分開管理
另外 ethers 有一個好用的框架：wagmi，真的是方便很多，以後有機會來介紹
現在就筆記一下 Ethers.js 的常用功能吧

延伸閱讀
1. [Announcing ethers.js — a web3 alternative](https://medium.com/l4-media/announcing-ethers-js-a-web3-alternative-6f134fdd06f3)
2. [Web3.js vs Ethers.js 實際應用上的區別](https://github.com/adrianmcli/web3-vs-ethers)


## <font color="#f4a261">Ethers.js</font>

### Install
`npm install ethers`

### Terms

| 名詞      | 說明                                             |
| -        | -                                                |
| Provider | 與區塊鏈對接，負責提供「唯讀」資訊給網頁                |
| Signer   | 權限比 Provider 更高，除了讀取還可以寫入區塊鏈         |
| Contract | 智能合約的實例化物件，可以用類似 ORM 的方式跟合約互動    |


### Wallet Connection

這邊示範如何連接瀏覽器的 MetaMask 擴充套件
以及如何利用 web3Modal 來連接錢包

```js
import ethers from 'ethers'
import Web3Modal from 'web3modal'

// Connect with MetaMask
// window.ethereum 是 MetaMask plug-in inject 到網頁的物件
const provider = new ethers.providers.Web3Provider(window.ethereum)

// Connect with web3Modal
const web3Modal = new Web3Modal({
	cacheProvider: true,
	providerOptions: {} // 這邊可以設定能支援什麼錢包，會在跳出 modal 的時候
})
const instance = await web3Modal.connect()
const provider = ethers.providers.Web3Provider(instance)

// Disconnect
// ethers cannot disconnect wallet programmically
// You can clear cached provider and reset the related states to null to pretend disconnection.
// see https://ethereum.stackexchange.com/q/83914
```


### Get Accounts

可用`listAccount()`取得帳號陣列
不知道為什麼是陣列，難道說可以同時連接不同帳號嗎？

```jsx
// 方法 1
const accounts = await provider.listAccounts()
console.log(account[0])

// 方法 2
const accounts = await provider.send('eth_requestAccounts',[])
console.log(accounts[0])
```


### Get Balance

return <[BigNumber](https://docs.ethers.io/v5/api/utils/bignumber/#BigNumber)> 

```jsx
const balance = await provider.getBalance(currentAccount)

const balanceInEther = ethers.utils.formatEther(balance)
console.log(balanceInEther)
```

- 回傳 BigNumber，單位是 Wei，顯示時記得換算成容易看的單位，並轉換成 string。
  `ethers.utils.formatEther` :  BigNumber        ⇒    string(in Ether)
  `ethers.utils.parseEther` :    string(in Ether)  ⇒    BigNumber 
  可參考[這裡](https://docs.ethers.io/v5/api/utils/display-logic/#unit-conversion)
    
- 如果用 walletconnect 必須加上 blocktag pending 
  `getBalance(currentAccount, ‘pending’)`
  否則可能不會抓到最新的數值


### Get Network

return <[Network](https://docs.ethers.io/v5/api/providers/types/#providers-Network)> : {name: string, ensAddress: string, chainId: number}
Ethereum mainnet 會顯示 homestead

```jsx
const network = await provider.getNetwork(currentAccount)
console.log(network.name)
```

用戶隨時能夠切換網路(Mainnet or Testnet)，如果網頁沒有做相對應的反應，很容易讓用戶疑惑，甚至下錯單，造成金錢上的損失
Best Practice: 偵測到網路改變時，直接 reload 頁面，確保所有的 state 被重置

[Network Changes Guideline](https://docs.ethers.io/v5/concepts/best-practices/#best-practices--network-changes)
https://github.com/ethers-io/ethers.js/issues/866



### Get Signer

```jsx
const signer = provider.getSigner(account: string)
```


## <font color="#f4a261">小結</font>

以上是一些常用的 function
還有如何讀取 native token
下一篇會介紹如何與合約互動

突然發現前面不小心忽略了 web3modal 
web3modal 是一個整合了各大錢包的接口
未來你想要多支援什麼 coinbase, walletconnect 等奇奇怪怪的錢包
只要 web3modal 有支援，都可以很快的添加進來
目前有 v1, v2 兩個版本

10 月底著手研究時，
v2 還在 early alpha，因此先跳過
但寫這篇文章的當下(12/13)，已經進展到 beta.9 了
有稍微試用一下，很多當初要花一個禮拜研究的東西他都幫我做好了...
之後可能會再專案用的升級到v2吧哈哈
好  其他細節留到未來某個篇章再紀錄吧