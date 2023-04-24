---
title: webpack-入门使用
date: 2023-04-24 19:30:00
categories: webpack
tags: 项目搭建
---

### webpack 是什么

webpack 是代码编译工具，有入口、出口、loader 和插件。webpack 是一个用于现代 JavaScript 应用程序的静态模块打包工具。当 webpack 处理应用程序时，它会在内部构建一个依赖图(dependency graph)，此依赖图对应映射到项目所需的每个模块，并生成一个或多个 bundle。

### 使用 webpack 创建一个项目

1. 创建一个文件夹,以你的项目名称命名
2. 初始化 npm 用来安装依赖包
   `npm init -y`
3. 安装 webpack，接着安装 webpack-cli 执行下面命令就行
   `npm install webpack webpack-cli --save-dev`
4. 现在的项目什么都还没有我们要按需创建自己的文件夹和文件，
   1. 先创建 index.html 文件必不可少的
   2. 创建 src 文件夹与 index.html 同级
   3. src 文件夹下面创建一个入口文件 index.js 随便写点啥吧如下
   ```
   import _ from 'lodash';
   function component() {
   const element = document.createElement('div');
   element.innerHTML = \_.join(['Hello', 'webpack'], ' ');
   return element;
   }
   document.body.appendChild(component());
   ```
   4. 现在我们是不是就可以通过访问 html 文件打开项目了，但是和 vue-cli 那种 npm run dev 打开的怎么弄呢
   5. 创建 dist 文件夹这个是放置打包文件的
5. 现在我们就要来设置脚本了能够使用 npm run dev 启动项目

   1. 我们先安装一个插件 webpack-dev-server 插件会监听源代码，当修改了源代码 webpack 会自动对项目进行打包构建
      `npm install webpack-dev-server`
   2. 并且在 package.json 文件中添加脚本

      ```
      {
      "name": "my-micro-app",
      "version": "1.0.0",
      "description": "",
      "scripts": {
        "dev": "webpack server",
        "test": "echo \"Error: no test specified\" && exit 1",
        "build": "webpack"
      },
      "keywords": [],
      "author": "",
      "license": "ISC",
      "devDependencies": {
        "html-webpack-plugin": "^5.5.1",
        "webpack": "^5.80.0",
        "webpack-cli": "^5.0.2"
      },
      "dependencies": {
        "webpack-dev-server": "^4.13.3"
      }
      }

      ```

   3. 如果你认真看了你就会发现"main":"index.js"被我们去除了这个你往下看就知道了
   4. 我们现在执行 npm run dev 就能直接运行项目了如果不行先配置一下 webpack.config.js

6. 我们现在要创建 webpack.config.js

```
const path = require('path');
var htmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
  // 打包的入口文件
  entry: './src/index.js',
  // 打包时的出口
  output: {
    // 打包生成js的名称
    filename: 'main.js',
    // 打包的文件放置的地方__dirname是根目录可以去看看nodejs path了解
    path: path.resolve(__dirname, 'dist'),
  },
  // 这里是我们启动时的配置
  devServer: {
    open: true, //自动打开浏览器
    hot: true, //热更新
    host: 'localhost', //本地域名
    port: 8080, //端口号
    proxy: {}, //同vue.config.js 里面的代理配置
  },
  // 这里是环境配置
  mode: 'development',
  plugins: [
    // 添加plugins节点配置插件
    new htmlWebpackPlugin({
      title: 'Development',
      template: path.join(__dirname, 'src/index.html'), //模板路径
      filename: 'index.html', //自动生成的HTML文件的名称
    }),
  ],
};


```

7. 细心的朋友会发现我安装了 html-webpack-plugin 插件，该插件的作用是安装指定的模板在 dist 文件夹中生成一个 html 文件并且会将我们打包好的 main.js 文件引入进去。这样做的作用是什么呢，难道我们手写不行嘛，这样的原因是我们在二次打包的时候会先清除掉上一次打包的东西。动态生成就方便了不用再去关注了。
8. 安装 html-webpack-plugin 执行下面命令然后按上面的配置就行了。这样一个基本的项目就搭建起来了。能够打包能够本地运行了
   `npm i html-webpack-plugin --save-dev`

每天碎片化的学一点点我们就长进一点点。不管有没有用先学起来。
