---
title: vite-配置
date: 2023-04-12 19:30:00
categories: vite
tags: 构建工具
---

### vite 自称是下一代前端构建工具不单单适用于 vue 也适用于 react

> 在笔者自身适用来看还是非常不错的相比俩年前还是完善不少。速度确实快有发展潜力可以学习一下直接上代码 vite.config.ts

```
// defineConfig起到提示作用
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import AutoImport from "unplugin-auto-import/vite";
import Components from "unplugin-vue-components/vite";
import { ElementPlusResolver } from "unplugin-vue-components/resolvers";
import path from "path";
const resolve = (dir) => path.resolve(__dirname, dir);
export default defineConfig({
  // ## 公共配置
  // 项目根目录
  root:process.cwd(),
  // 开发或生成的基础路径
  base:'./',
  // 环境
  mode:"development",
  // 静态文件夹
  publicDir:'public',
  // 缓存文件的目录
  cacheDir:'node_modules/.vite',
  // 配置别名
  resolve: {
    //配置根路径别名： import('@/pages/login/login.vue')
    alias: {
      "@": path.resolve(__dirname, "src"),
      // 注意一定不要随意命名，a b c这样的，项目的目录也不能为关键字保留字！！
      "comp": resolve(__dirname, "src/components"),
      // 配置图片要这样引用
      "/img": "./src/assets",
    },
    dedupe: [], // 强制 Vite 始终将列出的依赖项解析为同一副本
    conditions: [], // 解决程序包中 情景导出 时的其他允许条件
    mainFields: [], // 解析包入口点尝试的字段列表
    extensions: ['.mjs', '.js', '.ts', '.jsx', '.tsx', '.json'], // 导入时想要忽略的扩展名列表
    preserveSymlinks: false, // 启用此选项会使 Vite 通过原始文件路径确定文件身份
  },
  // 插件
  plugins: [vue(),  //引入vue 插件
  AutoImport({
    imports: ["vue"],
    dts: "src/auto-import.d.ts",
  }),
  //plus按需引入
  AutoImport({
    resolvers: [ElementPlusResolver()],
  }),
  //plus按需引入
  Components({
    resolvers: [ElementPlusResolver()],
  }),],
  // ## 打包配置
  build:{
    // 设置打包构建的文件最终兼容的浏览器版本 modules是支持es模块的
    target:'modules',
    // 性能优化  自动注入modulepreload-polyfill 资源在页面初始化之前预加载资源
    modulePreload:{
      polyfill:true
    },
    // 指定的输出路径
    outDir:'dist',
    // 指定生成的静态资源文件夹路径
    assetsDir:'assets',
    // 小于你设置值大小的导入或者引入资源文件会通过base64编码内联进去4096KB为默认值
    assetsInlineLimit:4096,
    // 启用或者禁用css拆分，如果禁用就会将css提取到一个css文件中 **注意：如果指定了lib cssCodeSplit默认为false
    cssCodeSplit:true,
    // 构建为库
    lib:false,
    // 构建打包成兼容浏览器的css
    cssTarget:'modules',
    // 构建打包时是否生成source map 文件
    sourcemap:true,
    // rollup 配置
    rollupOptions:{},
    // 传递给 @rollup/plugin-commonjs 插件的选项。
    commonjsOptions:{},
    // 传递给 @rollup/plugin-dynamic-import-vars 的选项。
    dynamicImportVarsOptions:{},
    // 构建后会生成manifest.json文件
    manifest:false,
    // gzip压缩大小报告
    reportCompressedSize:true,
    // 触发chunk警告的大小设置kbs
    chunkSizeWarningLimit:500
    // 与minify一致
    cssMinify:true,
    // 使用terserOptions必须开启
    minify:"terser",
    terserOptions:{
      compress:{
        // 打包生产环境时移除console
        drop_console:true,
        // 打包生产环境时移除debugger
        drop_debugger:true
      }
    }
  },
  // ## 服务器配置
  server:{
    // ip地址，0.0.0.0监听所有
    host:'0.0.0.0',
    // 指定端口
    port:8080,
    // 端口被占用时直接退出
    strictPort:true,
    // 启动服务时自动在浏览器打开应用
    open:true,
    // 配置cors
    cors:false,
    // 配置headers
    headers:{},
    //开启热更新
    hmr:true,
    // 代理
    proxy:{
      '/api': {
        target: 'http://jsonplaceholder.typicode.com',
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\/api/, ''),
      }
    }
  }
})


```
