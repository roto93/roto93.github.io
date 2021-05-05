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
```
<form action="https://formsubmit.co/your@email.com" method="POST" />
```

接著確保你的 input tag 都有加`name`的屬性
這個屬性會影響到你收到信件時的內容呈現方式
你可以設為: email message 之類的

接下來只要 submit 這個 form 就可以了
如果你的收件 email 是第一次在這個API使用的話
在第一次送出時你會收到一封認證信，認證完成之後接下來的每次 submit 就會順利寄信給你囉

- 補充