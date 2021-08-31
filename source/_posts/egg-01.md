---
date: 2021-08-26 18:03:32
title: egg  + sequelize + mysql + jwt + vue 实现全栈服务（一）
tags:
  - egg
  - sequelize
  - jwt
  - vue
categories:
  - BackEnd-Tec
---
# Egg.js的常用配置
### 一、egg.js常用配置（在config目录下根据不同环境配置 *config.default.或config.prod.js*）
#### 1:数据库链接配置
    // mysql config
      config.dbConfig = {
        database: process.env.DB_DATABASE || 'user',
        host: process.env.DB_HOST || '124.70.215.***',
        port: process.env.DB_PORT || '3306',
        username: process.env.DB_USER || 'root',
        password: process.env.DB_PASSWORD || '******',
        charset: 'utf8mb4',
        collate: 'utf8_general_ci'
      }
#### 2:jwt配置
    // jwt 配置
      config.jwt = {
        secret: 'zzestlgcjwtsecret',
        getToken(ctx) {
          if (
            ctx.headers.authorization &&
            (ctx.headers.authorization.split(' ')[0] === 'Bearer' ||
              ctx.headers.authorization.split(' ')[0] === 'Token')
          ) {
            return ctx.headers.authorization.split(' ')[1]
          } else if (ctx.query && ctx.query.token) {
            return ctx.query.token
          }
          return null
        }
      }
#### 3:跨域配置
    // 跨域配置
      config.cors = {
        origin: '*',
        allowMethods: 'GET, HEAD, PUT, POST, DELETE, PATCH, OPTIONS'
      }
#### 4:部署指定端口配置
    // 指定端口启动
    config.cluster = {
        listen: {
          path: '',
          port: 3005,
          hostname: '0.0.0.0',
        }
      };
#### 5:常用插件的配置
        // Sequelize插件
      sequelize: {
        enable: true,
        package: 'egg-sequelize'
      },
      // 校验插件
      validate: {
        enable: true,
        package: 'egg-validate'
      },
      // jwt插件
      jwt: {
        enable: true,
        package: 'egg-jwt'
      },
      // 子路由
      routerPlus: {
        enable: true,
        package: 'egg-router-plus'
      },
      // 跨域
      cors: {
        enable: true,
        package: 'egg-cors'
      },
```javascript
egg-scripts start --daemon 启动于 egg-bin dev 的区别在于前者是在后台运行
```
##### 注意项目在dev环境启动的时候要注意package.json中所对应的指令。
##### 由于框架的配置功能比较强大，有不同环境变量，又有框架、插件、应用等很多地方配置，如果配置不生效时可以查看下 ${root}/run/application_config.json（worker 进程配置） 和 ${root}/run/agent_config.json（agent 进程配置） 这两个文件。



