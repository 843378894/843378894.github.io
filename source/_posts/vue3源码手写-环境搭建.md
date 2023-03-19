---
title: vue3源码手写-环境搭建
date: 2023-03-16 22:00:00
categories: 源码
tags: VUE3

---
### vue3源码手写-环境搭建
> 工作中开始正式使用vue3，既然使用了就看看源码手写一个mini版。今天时间有限先从环境搭建开始，那么请往下看
###### 1. rollup打包monorepo
> 在我们查看源码文件的时候你会发现使用的是rollup打包monorepo。这样的方式有什么好处呢。能够让每个小的包能独立打包运行。也能整体打包运行，vue3源码的所有内容堵在packages文件夹下面，没包都是独立可打包运行的。

###### 2. 创建文件夹初始化文件
> 1. 创建一个my-mini-vue3文件
> 2. 进入到文件打开终端运行初始化命令， yarn init -y
> 3. 命令运行完之后会生成package.json 文件

###### 3. 依赖下载
> 1. 下载ts，运行 yarn add typescript -D -W
> 2. 生成ts配置文件 运行 npx tsc --init()
> 3. 安装我们打包需要的依赖运行 yarn add rollup rollup-plugin-typescript2 @rollup/plugin-node-resolve @rollup/plugin-json execa -D -W
```javascrpt
 "devDependencies": {
        "@rollup/plugin-json": "^6.0.0",
        "@rollup/plugin-node-resolve": "^15.0.1",
        "execa": "^7.1.1",
        "rollup": "^3.19.1",
        "rollup-plugin-typescript2": "^0.34.1"
    }
    @rollup/plugin-node-resolve 插件允许我们加载第三方模块
    @rollup/plugin-json 支持导入json，没有 json 插件的支持我们在导入 json 文件时会报错
    rollup-plugin-typescript2  typescript的差距 配合tsconfig.json
    execa 插件将它们转换为ES6版本

```
> 好了今天环境已经搭建好了，明天来写打包配置的东西，欢迎大家指点

