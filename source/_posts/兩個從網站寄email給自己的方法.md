---
title: 兩個從網站寄email給自己的方法
date: 2021-05-05 11:47:35
tags:
---

---

在建置網站的 contact 頁面時
為了讓訪客能聯繫自己
寄 email 應該是最簡單直接的方式了
這裡介紹兩種從網站寄 email 的方法

---

## <font color="#BC2C1A">1. FormSubmit</font>

[FormSubmit](https://formsubmit.co) 讓你可以用 RESTful API，由他們的 server 把訪客的訊息寄給你

優點是完全 free 沒有流量限制

首先在你的網站的 form tag 加入`action`和`method`
別傻傻直接貼上，記得把`your@email.com`改成你的信箱喔
```html
<form action="https://formsubmit.co/your@email.com" method="POST" />
```

接著確保你的 input tag 都有加`name`的屬性
這個屬性會影響到你收到信件時的內容呈現方式
你可以設為: email message 之類的

接下來只要 submit 這個 form 就可以了
如果你的收件 email 是第一次在這個 API 使用的話
在第一次送出時你會收到一封認證信，認證完成之後接下來的每次 submit 都會順利寄信給你囉!

- 補充

通常在訪客做出 submit 時，會自動跳出 reCAPTCHA 認證框

<img src="FormSubmit1.png" style="box-shadow: 2px 1px 6px gray" width="60%" />

並且在成功寄出郵件後會跳轉到他們提供的 thankyou page 

<img src="FormSubmit2.png" style="box-shadow: 2px 1px 6px gray" width="60%" />

你可以視情況決定是否要顯示這些訊息
這邊就來示範一下

#### 關閉 reCAPTCHA

在`<form>`裡面加入這個
```html
<input type="hidden" name="_captcha" value="false">
```

#### 跳轉到自己的 thankyou page

一樣在`<form>`中加入:
```html
<input type="hidden" name="_next" value="https://yourdomain.co/thanks.html">
```
並改成該網頁的路徑

還有很多其他的功能，就留給讀者自己去發掘囉

---

## <font color="#BC2C1A">2. emailJS </font>

比起第一個方法，[emailJS]() 好像比較多人用
而且網路上的教學大多是搭配 React 使用

首先，到官網註冊帳號，記得用你希望收到信的信箱註冊
(不過用別的也行，這個可以事後更改)

登入後會進到 dashboard 
我們先來到 Email Service 點擊 <b>Add New Service</b>
<img src="emailjs1.png" style="box-shadow: 2px 1px 6px gray" width="60%" />
先跟大家釐清一件事(因為我當初搞混)
emailJS 並不會幫大家寄信，而是幫你建立起網站與 email server 之間的橋樑
這個橋梁就是 email service，
至於這個 server 需要由第三方提供，如果你流量不大，可以用 gmail、yahho mail 之類的
如果潛在使用量很高，你可能就需要使用下方的 Transactional Services 
不過這些我目前都沒聽過XD Personal Services 應該就很夠用了
但是要記住，無論你選了什麼都不代表它是你的收件信箱，而是<b>寄信信箱</b>
就像我前面提的，你註冊的信箱才是

<img src="emailjs2.png" style="box-shadow: 2px 1px 6px gray" width="60%" />

做好選擇，依照指示進行，你就會得到一組 <font color="orange">service ID (記得它, 待會會用到)</font>

接下來到 Email Templates 設定你收件時所見的內容格式
這邊其實可以直接 Create New Template，什麼都不改直接

<img src="emailjs3.png" style="box-shadow: 2px 1px 6px gray" width="60%" />

一樣我們會得到一組 <font color="orange">template ID (記得它, 待會會用到)</font>

下一步，我們要把 emailJS 和網站做串接啦

如果你用的是 React，請先 npm 安裝
```
npm install emailjs-com
```
接著在專案中:
```javascript
import {init} from 'emailjs-com'
init("YOUR_USER_ID") // 在 EmailJS dashboard 的 integration 頁面可以找到自己的 user id
```

如果你用的是 html，就在 `<head>` 裡面加:
```html
<script type="text/javascript" src="https://cdn.jsdelivr.net/npm/emailjs-com@2/dist/email.min.js"></script>
<script type="text/javascript">
(function() {
emailjs.init("YOUR_USER_ID");
})();
</script>
```

最後，在你 submit 時運行 `emailjs.sendForm()`

```html
<form id="emailjs-form">
	<input type="email" name="email" />
	<input type="text" name="message" />
	<button type="submit"/>
</form>

<script>
	const emailJsForm = document.querySelector('#emailjs-form')
	emailJsForm.addEventListener('submit',(event)=>{
		event.preventDefault()
		emailjs.sendForm('YOUR_SERVICE_ID', 'YOUR_TEMPLATE_ID', THE_FORM_NODE)
		})
</script>
```

注意`emailjs.sendForm()`有三個必填參數，其中最容易忽略的是第三個:
- THE_FORM_NODE: 放入 node，以這個例子來說就是`emailJsForm`

`sendForm()` 會回傳 promise，所以可以接 `.then()` `.catch()`

---

好ㄌ 
寄 email 的方法就介紹到這邊
最近幾乎都在研究建置個人網站的眉眉角角
再過一陣子或許就能開始動工了!!!
不過網站的設計也是一份大工程
要練習 figma 要讀 design guideline
真的有好多東西要學ㄚ~~~