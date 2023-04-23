---
title: 模拟接口-mockjs
date: 2023-04-23 19:30:00
categories: 模拟接口
tags: 项目搭建
---

### mockjs 是做什么用的呢

> mockjs 是为了我们前端人员在页面 ui 已经画好了的情况下，后端兄弟们没有及时跟上接口的时候不至于闲着。我们自己模拟接口请求数据做交互，提前将任务完善。

### mockjs 如何使用

> mockjs 的功能很强大，能够生成随机参数之类的。我就说说我实际使用情况吧，简单粗暴

1. 下载 mockjs
   > npm i mockjs -D
2. 创建 mock 文件夹新建 index.js

```
    import Mock from 'mockjs'
    const baseUrl = ' http://localhost:4000/'
    // 你要返回的数据你PRD上的数据结构
    const data ={}
    <!-- 你PRD上的接口地址 -->
  Mock.mock(baseUrl+'/a/b/c','post',(data)=>{
    return data
  })

```

3.  main.js 中引入你刚刚创建的 mock/index.js
    ` import '@/mock/index.js'`
4.  上面就是 mockjs 的使用这样就能拦截掉你的请求，对不在 mock/index.js 中的接口无影响，按照你需要的数据接口，你需要的接口构造一下就完全符合了你的接口请求要求
5.  注意哦，mock 的东西不要上传到库中。接口能联调之后直接将 main.js 中的引用注释掉

mockjs 是一款非常实用的接口模拟插件
