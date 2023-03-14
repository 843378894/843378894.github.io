---
title: BOM世界
date: 2023-03-14 19:30:00
categories: 知识总结
tags: BOM

---

###### BOM描述
>    浏览器对象模型，他提供独立于内容而与浏览器窗口进行交互的对象，其核心window。

###### BOM构成
>    document location navigation screen history

###### window对象的常见事件
1.  窗口加载事件
>    window.onload() 是窗口（页面）加载事件，当文档内容完全加载完毕会(包括图像，css，脚本文件等)触发调用该处理函数。
    ```javascript
    window.onload =function(){};
    // 或者
    window.addEventListener("load",function(){})

    ```
2. 调整窗口大小事件（window.onresize()）
    window.onresize()是调整窗口大小变化时会触发onresize事件
    ```javascript
        window.onresize = function(){};
        // 或者
        window.addEventListener("resize",function(){});

    ```

###### 定时器
>   window对象提供了两种定时器方法:1. setTimeout 2. setInterVal
1. setTimeot()定时器，定时多少时间之后才执行
2. window.clearTimeout()停止定时器
3. setInterval()每隔一段时间执行
4. window.clearInterval()停止定时器


###### navigator对象
    navigator对象包含有关浏览器的信息，有很多属性，常见的有userAgent，该属性返回user-agent的值，判断是那个终端打开页面
```javascript
    if(navigator.userAgent.match(/phone|pad|pod|iphone|ios|Anroid/)){
        window.location.href= "" ;// 手机
    }

```