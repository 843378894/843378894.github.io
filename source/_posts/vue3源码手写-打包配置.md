---
title: vue3源码手写-打包配置
date: 2023-03-16 22:00:00
categories: 源码
tags: VUE3

---
### vue3源码手写-打包配置
###### 文件夹创建
> 在packages文件夹下面创建reactivity和shared文件夹，初始化创建对应的package.json并且创建src文件夹下面再创建一个index.tS文件
```javascript
// reactivity -> package.json
{
    //  打包更具这个名称生成文件在node_modules中
    "name": "@vue/reactivity",
    "version": "1.0.0",
    "main": "index.js",
    "type": "module",
    "license": "MIT",
    // 打包生成的文件类型格式打包后能在dist文件中体现
    "buildOptions": {
        "name": "vueReactivity",
        "formats": [
            "esm-bundler",
            "cjs",
            "global"
        ]
    }
}

```
```javascript
// shared -> package.json
{
    //  打包更具这个名称生成文件在node_modules中
    "name": "@vue/shared",
    "version": "1.0.0",
    "main": "index.js",
    "type": "module",
    "license": "MIT",
    // 打包生成的文件类型格式打包后能在dist文件中体现
    "buildOptions": {
        "name": "vueShared",
        "formats": [
            "esm-bundler",
            "cjs"
        ]
    }
}

```
> 在写源码之前我们要把项目搭建好，上次我们搭建了环境，那么解下来就是对项目进行打包配置。vue3是如何能做到每个文件独立运行也能打包在一块呢。
###### 1. 我们先对package.json动手
```javascript
    "private": true,//表示这是一个私有项目，不会被 publish 到 npm库
    "type": "module",// 这里是开启ES6模块，vue3不支持commonjs。你可以先不管他，等你在引入文件操作文件的时候会提醒你
    "workspaces": [//简单讲就是将每个项目的 node_modules 都提升到根目录的 node_modules 中，形成多个项目复用同一套依赖包效果。
        "packages/*"
    ],
     "scripts": {//书写脚本
        "build": "node scripts/build.js", // 打包脚本
        "dev": "node scripts/dev.js" // 打包dev环境
    },
```
###### 2. 在我们配置好了package.json 脚本之后我们要在根目录下面创建scripts文件夹和build.js与dev.js这俩个脚本文件
###### 3. 我们先对build.js书写打包时所需要执行的代码
```javascript
//  编辑打包设置文件 monerepo
// node 内置文件读写方法
import fs from "fs"
import { execa } from "execa"

//  fs.readdirSync 读取对应文件夹下面的目录
const dirs = fs.readdirSync("packages").filter((p) => {
        // 对应路径下的文件是否是文件夹
        if (!fs.statSync(`packages/${p}`).isDirectory()) {
            return false
        }
        return true
    })
    // 对所有的文件进行遍历并行执行打包动作
const runParallel = (targets, buildFn) => {
        const res = []
        for (const target of targets) {
            res.push(buildFn(target))
        }
        return Promise.all(res)
    }
    // 打包核心调用execa执行 rollup 配置打包
const build = async(pkg) => {
    // 这里执行完打包结束
        await execa('rollup', ['-c', '--environment', `TARGET:${pkg}`], { stdio: 'inherit' })
    }
    // 调用打包函数
runParallel(dirs, build)
```

###### 脚本书写完了就要接着上面的写rollup配置文件，我们在执行build脚本的时候会去调用配置文件，创建rollup.config.js文件，在根目录下创建。
```javascript

// 解析ts 插件
import ts from 'rollup-plugin-typescript2'
// 解下json的插件
import json from '@rollup/plugin-json'
// 解析第三方插件
import resolvePlugin from '@rollup/plugin-node-resolve'
// 创建 require 的方法
import { createRequire } from 'node:module'
// 获取更目录的
import { fileURLToPath } from 'node:url'
// 获取path 方法
import path from 'node:path'
const require = createRequire(
    import.meta.url)
const __dirname = fileURLToPath(new URL('.',
    import.meta.url))

//  获取文件路径
const packagesDir = path.resolve(__dirname, 'packages')
    //  获取需要打包的包
const packageDir = path.resolve(packagesDir, process.env.TARGET)


//  获取每个包的配置
const resolve = p => path.resolve(packageDir, p)
    // 获取每个包的 package.json
const pkg = require(resolve(`package.json`))
    //  取出包名
const name = path.basename(packageDir)
    //  创建格式对应表
const outputOpions = {
    "esm-bundler": {
        file: resolve(`dist/${name}.esm-bundler.js`),
        format: "es"
    },
    "cjs": {
        file: resolve(`dist/${name}.cjs.js`),
        format: "cjs"
    },
    "global": {
        file: resolve(`dist/${name}.global.js`),
        format: "iife"
    },

}

//  获取每个包下面设定的buildOptions格式
const options = pkg.buildOptions

// 设置打包函数
function createConfig(format, output) {
    //  进行打包
    output.name = options.name
    output.sourcemap = true
        //  生成rollup配置
    return {
        input: resolve("src/index.ts"), //导入
        // 打包配置
        output,
        // 这里是插件的调用
        Plugin: [
            json(),
            ts({
                tsconfig: path.resolve(__dirname, 'tsconfig.json')
            }),
            resolvePlugin()
        ]
    }
}
//  注意打包结束的时候一定要retun对象
export default options.formats.map(format => createConfig(format, outputOpions[format]))

```
> 上面的代码就是打包配置，rollup打包工具会根据你的打包进行

###### tsconfig文件修改
```javascript
{
  "compilerOptions": {
    /* Language and Environment */
    // 需要更改不支持ES5
    "target": "ESNext",                                

    // 需要更改不支持ES5
    "module": "ESNext",                                /* Specify what module code is generated. */

    "esModuleInterop": true,                             /* Emit additional JavaScript to ease support for importing CommonJS modules. 
    "forceConsistentCasingInFileNames": true,            /* Ensure that casing is correct in imports. */

    //    不使用严格模式  个人选择
    "strict": false,                                      /* Enable all strict type-checking options. */
  
    "skipLibCheck": true,
    // 下面这些是文件路径配置，在引用别的包的时候TS检索不会报错
    "baseUrl": ".",
    "moduleResolution": "node",
    "paths": {
      "@vue/*": ["packages/*/src"]
    }
  }
}


```
###### dev.js 脚本编写 

```javascript
//  编辑打包设置文件 monerepo
// node 内置文件读写方法
import { execa } from "execa"
const build = async(pkg) => {
        //  -cw 监听 相当于热跟新
        await execa('rollup', ['-cw', '--environment', `TARGET:${pkg}`], { stdio: 'inherit' })
    }
    //  打包函数
build("reactivity")

```

> 这个是打包配置文件，进行到这里就已经能将我们需要写的东西编译出来。后续文章开始我们进行源码书写，感兴趣的同学一起来啊。