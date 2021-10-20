---
title: 微信js-sdk配置流程（测试号与公众号配置）
date: 2021-10-14 15:03:32
tags:
  - javascript
  - wx-jssdk
  - wx-config
categories:
  - FrontEnd-Tec
---

### 一、微信公号后台配置

#### 1、获取 AppID、AppSecret

> ##### `测试号`可从[微信测试号申请](http://mp.weixin.qq.com/debug/cgi-bin/sandboxinfo?action=showinfo&t=sandbox/index)获取,这样就获得了 AppID 与 AppSecret

<img width="40%" align="ceter" src="https://z3.ax1x.com/2021/10/14/5lMgQx.png" alt="ronaldoxzb" />
</p>

> ##### `公众号申请自行操作`申请完成后在公众号后台`设置与开发`基本配置中获取

<img width="40%" align="ceter" src="https://z3.ax1x.com/2021/10/20/5BJtIK.png" alt="ronaldoxzb" />

#### 2、配置 AppID、AppSecret

> ##### `获取到AppID、AppSecret后，可在程序中配置`

#### 3、IP 白名单添加（需要服务器 ip）

> ##### `添加IP白名单来调用获取ACCESS_TOKEN的接口`

<img width="40%" align="ceter" src="https://z3.ax1x.com/2021/10/20/5BYUwq.png" alt="ronaldoxzb" />

#### 4、配置服务器地址（与微信通信检查）

> ##### `程序中写好通信接口，配置好路由，可查看图片中标记`
> ##### `慎重操作下图中的启用/停用按钮，会把公众号配置好的菜单下架掉（点击停用后可重新找回）`

<img width="40%" align="ceter" src="https://z3.ax1x.com/2021/10/20/5BYYOs.png" alt="ronaldoxzb" />

#### 5、配置接口安全域名，成功调用 jssdk （需要域名）

> ##### `配置安全域名目录见下图`

<img width="40%" align="ceter" src="https://z3.ax1x.com/2021/10/20/5BYNmn.png" alt="ronaldoxzb" />
</p>

> ##### `配置域名前注意下图中的表述，上传文件至服务器，验证服务器是否是你本人所有，域名的合规性。`

<img width="40%" align="ceter" src="https://z3.ax1x.com/2021/10/20/5BYJyj.png" alt="ronaldoxzb" />

#### 到此配置流程已结束，之后会出一片代码配置文章。

