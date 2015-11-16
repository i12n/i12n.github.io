---
layout: post
title: "npm build and publish a package"
description: ""
category: web
tags: ['npm']
---
{% include JB/setup %}
## 准备工作

在 github 上建一个仓库，用于托管代码。在这里建一个 [simple-should](https://github.com/unknownGe/simple-should.git) 仓库。
在这个代码仓库中要实现一个简单的 should 断言功能，并将其发布为 npm 包。

> 关于断言 [shouldjs](https://github.com/tj/should.js/)

为了方便代码管理，将仓库 clone 到本地，并在本地仓库中进行代码开发。

## 配置 npm 

npm 的配置比较简单，首先配置用户名和邮箱，然后在 npm 上进行注册：

  npm set init.author.name "yourname"
  npm set init.author.email "email@example.com"

  npm adduser
  


## 生成 package.json

执行以下命令：

    npm init 

根据提示，配置并生成 package.json

    name: (simple-should)
    version: (1.0.0)
    description: a simple should
    entry point: (index.js)
    test command: make test
    git repository: (https://github.com/unknownGe/simple-should.git)
    keywords: should
    license: (ISC) MIT
其中 `entry point` 是实现功能的入口，`test command` 是启动测试的命令  

最终生成的 package.json：

    {
      "name": "simple-should",
      "version": "1.0.0",
      "description": "a simple should",
      "main": "index.js",
      "scripts": {
        "test": "make test"
      },
      "repository": {
        "type": "git",
        "url": "git+https://github.com/unknownGe/simple-should.git"
      },
      "keywords": [
        "should"
      ],
      "author": "gewenmao <gewenmao@gmail.com>",
      "license": "MIT",
      "bugs": {
        "url": "https://github.com/unknownGe/simple-should/issues"
      },
      "homepage": "https://github.com/unknownGe/simple-should#readme"
    } 
  
## 实现 npm 包功能
  
  首先要创建入口文件 index.js ，与 package.json 中的入口文件名要保持一致。


## 测试

  测试依赖于其他 npm 包，这里使用 mocha 和 chai。

  npm install mocha --save-dev
  npm install chai --save-dev

  新建 test/index.js，并编写测试用例。
