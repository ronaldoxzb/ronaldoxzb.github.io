
---
title: 如何优雅的发布npm包
date: 2022-02-10 10:14:01
tags:
  - sh 
  - npm
categories:
  - FrontEnd-Tec
---
#### 一、nrm管理镜像源
#### 前提是搭好npm私有服务器
> 我们可以更改npm源为本地地址：
```sh
# 设置npm使用的源为本地私服
npm set registry http://localhost:4873/
```
> 或者针对某个依赖安装时选用自己的源地址：
```sh
npm install lodash --registry http://localhost:4873
```
> 　但是如果我们想再次切换到淘宝或者其他的镜像地址，就不那么方便了；我们可以通过nrm这个工具来管理我们的源地址，可以查看和切换地址；首先还是进行安装：
```sh
npm install -g nrm
```
> 安装后我们可以通过`nrm add [name] [address]`这个命令来新增一个源地址：
```sh
nrm add localnpm http://localhost:4873/
```
> 　使用nrm ls可以查看我们使用的所有源地址，带*是正在使用的地址；通过nrm use [name]来切换地址：

![WeChatc9c7dcbf4fa55e66761034cb45c3d0df.png](https://ronaldoxzb.com/upload/2022/02/WeChatc9c7dcbf4fa55e66761034cb45c3d0df-9dc29e918d2e483696ef9288f9bea7ba.png)

#### 二、npm发布包
> 首先我们需要准备一个账号，可以使用--registry来指定npm服务器，或者直接使用nrm来管理
```sh
npm adduser
npm adduser --registry=http://example.com
```
> 然后进行登录，输入你注册的账号密码邮箱：
```sh
npm login
```
> 还可以用下面命令退出当前账号：
```sh
npm logout
```
> 如果不知道当前登录的账号可以用who命令查看身份：
```sh
npm who am i
```
> 登录成功就可以将我们的包推送到服务器上去了，执行下面命令，会看到一堆的npm notice：
```sh
npm publish
```
> 如果某版本的包有问题，我们还可以将其撤回
```sh
npm unpublish [pkg]@[version]
```
