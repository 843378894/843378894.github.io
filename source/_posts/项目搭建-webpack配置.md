---
title: webpack-配置详解
date: 2023-04-24 19:30:00
categories: webpack
tags: 项目搭建
---

### webpack css 处理

如果你看了上一篇文章之后自己引入了 css 文件并且打包的话你会发现报错。这是为什么呢。因为 webpack 是一个打包工具不具备识别 css 的能力。我们需要通过 loader 来识别编译我们的 css 代码或者是 scss 和 less

1. 根据你代码中使用的是 css 还是 scss 还是 less 对应下载 loader
   ` npm install css-loader style-loader -D`
   ` npm install node-sass sass-loader`
   ` npm install less less-loader -D`
2. 下载了依赖包接下来就是对打包配置，webpack.config.js 进行如下配置
   > 注意 loader 执行是有先后顺序的，如果错了会报错

```
module.exports = {
  // ......
  module: {
    rules: [
      {
        test: /\.less$/,
        // loader 从右到左执行，经过 less-loader 转为 css，再经 css-loader 转为 webpack 识别的模块，最后插入到页面中
        use: ['style-loader', 'css-loader', 'less-loader'],
      },

      {
        test: /\.s[ac]ss$/,
        use: ['style-loader', 'css-loader', 'sass-loader'],
      }
    ]
  }
}


```
