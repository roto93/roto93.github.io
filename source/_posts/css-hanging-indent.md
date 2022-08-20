---
title: CSS 怎麼從第二行開始縮排
date: 2021-01-31 00:31:04
tags:
---

<font size="2" color="#aaa">

從第二行開始縮排有個專有名詞，叫做 <font color=orange>**hanging indent**</font>，這篇文章會說明如何做到這點。

## <center>CSS 的 *text-indent 屬性*</center> ##
這個屬性是控制文字的縮排，語法如下：

```
text-indent: [縮排距離] [縮排模式] 
如：
text-indent: 30px hanging;
```

- 縮排距離： 如`10px`、`2em`、`-30px` 等等
- 縮排模式： 
	1. **`hanging`**: 反過來，除了第一行以外都會縮進去。
	2. **`each-line`**: 第一行會縮排，以及每個用`<br>`換行後的第一行也會縮排
	
不過縮排模式好像還在實驗階段，我目前還沒成功使用過，每次都沒作用XD

## <center>替代方案</center> ##

如我剛剛所說，打上 `hanging` 不會有任何效果，但是我們還是可以硬把它做成成'看起來'有 `hanging` 的樣子。
只要同時設定`padding-left`和負的`text-indent`就可以了：

```
padding-left: 2em;
text-indent: -2em;
```

&nbsp;
## React Native 的縮排 ##

```JS
<Text>{'\t'}{'\t'}Something you want to say</Text>
```
&nbsp;
## React Native 從第二行開始縮排 ##

我還沒找到方法 XD
