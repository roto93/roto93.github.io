---
title: Firebase database 
date: 2021-04-05 14:18:55
tags:
---

<font size="2" color="#aaa">

有了登入功能後，我們可能會希望儲存用戶的資料

無論是個人資料(頭像、帳號名、電話、信箱等等)
或是其他隨時間變多的資料，像是個人貼文

每發一篇貼文，我們就要把它記錄在資料庫裡
他可能需要建立在 users 的節點之下
也可以建立在 post 節點之下
或同時在多個節點做更新

這篇文章會告訴你 Firebase Realtime Database 的一些簡單操作：

#### 1. 寫入 `set()`、`push()`
#### 2. 讀取 `on()`、`get()`
#### 3. 更新 `update()`
#### 4. 刪除 `delete()`

在開始前，別忘了到 firebase console 啟用 realtime database



## <font color="#BC2C1A"> 取得 reference</font>

每次更動資料時
需要告訴程式你想要處理資料庫中什麼位置的資料

這個位置就叫做 `firebase.database.Reference`
可藉由`ref()`定義

```javascript
const Ref = firebase.database().ref()
```

`ref()`可傳入 path，如：

```javascript
const user = firebase.auth().currentUser
const userRef = firebase.database().ref('/users/'+ user.uid)

```

對於任一個 Reference
你可以用 `child()` 來得到下層的 Reference
假如我們要到 /user/user.uid/hobby/

```javascript
const userHobbyRef = userRef.child('hobby')
```

取得 Reference 後在後面掛上函數就能變更資料了



## <font color="#BC2C1A"> 寫入資料</font>

## <center> `set()`</center>

最簡單的方式
我們可以用`set()`來寫入資料

```javascript
userRef.set({
	name:'Tony',
	age:24
	hobby:['tennis','coding','eating']
	timeStamp: Date.now()
})
```
`set()`函數會直接複寫該節點的資料，包括所有子節點

## <center> `push()`</center>

除此之外
我們也能用 `push()`
帶入參數，就會在該節點的 last space 建立資料
不帶入也可以，就不會寫入
而後回傳該處的 Ref

```javascript
const postListRef = firebase.database().ref('/posts/')

//保留 Ref 以便之後使用
const newPostRef = postListRef.push()
newPostRef.set('New Post')

// 直接寫入
postListRef.push('New Post')
```

無論如何，`push()`都會產生一個key
這個 key 是依 timestamp 隨機產生的
也因此`push()`適合有多個用戶的 app 使用
避免同時接收多個寫入指令，造成問題

要取得key值，可使用`.key`

```javascript
const newPostKey = postListRef.push().key
```
 


## <font color="#BC2C1A"> 讀取資料</font>

## <center> `on()`</center>

要讀取資料，官方最推薦的方式是`on()`

```javascript
// userRef.on(eventtype,callback(snap))

userRef.on('value', (snap)=>{
	setUserEmail(JSON.stringify(snap.child('user_email').val()))
})
```

給予一個 Ref，`on()`會建立一個 listener

第一個參數`'value'`指的是只要這個 Ref 以下任何 value 發生變化時就會觸發 callback function (第二個參數)
其他還有 
- `'child_added'` 
- `'child_changed'` 
- `'child_removed'` 
- `'child_moved'` 

第二個參數是 callback，傳入 snap
這個 snap 的模樣會是該節點變更後的資料
記得要做`JSON.stringfy()`
並用`val()`取得數值

就我嘗試的結果
**`on()`需要放在 useEffect() 裡面**

## <center> `get()`</center>

如果你不需要監聽，只需取得資料一次
可以使用`get()`

```javascript
userRef.get()
  .then()
  .catch()
// return a Promise (dataSnapshot or rejection)
```

根據官方說法，過多的`get()`會導致效能問題
因此確保你只在需要的地方使用`get()`
`on()`才是預設選項



## <font color="#BC2C1A"> 更新資料</font>

## <center> `update()`</center>

`update()`函數讓你能以一次呼叫，更改資料庫的多個地方
第一個參數是object
其中的 child property 可以填入 simple property

```javascript
var adaNameRef = firebase.database().ref('users/ada/name');
// Modify the 'first' and 'last' properties, but leave other data at
// adaNameRef unchanged.
adaNameRef.update({ first: 'Ada', last: 'Lovelace' });
```

或是 path

```javascript
var ref = firebase.database().ref()
// Generate a new push ID for the new post
var newPostRef = ref.child("posts").push();
var newPostKey = newPostRef.key();
// Create the data we want to update
var updatedUserData = {};
updatedUserData["user/posts/" + newPostKey] = true;
updatedUserData["posts/" + newPostKey] = {
  title: "New Post",
  content: "Here is my new post!"
};
// Do a deep-path update
ref.update(updatedUserData, function(error) {
  if (error) {
    console.log("Error updating data:", error);
  }
});
```

第二個參數是 callback 並帶入 error

參考 [Multi-location updates](https://firebase.googleblog.com/2015/09/introducing-multi-location-updates-and_86.html)



## <font color="#BC2C1A"> 刪除資料</font>

## <center> `delete()`</center>

```javascript
Ref.Remove()
// return a Promise
```



