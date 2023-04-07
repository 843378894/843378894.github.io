---
title: 项目搭建-Commit 强制校验
date: 2023-04-07 19:00:00
categories: 强制校验
tags: 项目搭建
---

    1. husky安装  npm I -D husky
    2. 执行 npx husky install 自动创建 .husky/目录，并且指定该目录为git hooks 所在目录，
    3. 添加 git hooks，语法：husky add \<file\> \[cmd\] 运行命令 npx husky add .husky/pre-commit "git add . " 执行成功之后.husky 目录下会出现一个pre-commit的shell脚本文件，在执行commit之前会执行该脚本
    4. 添加commit-msg校验，安装commit规范校验commitlint 执行 npm install --save-dev @commitlint/config-conventional @commiylint/cli
    5. 在根目录下，创建文件 commitlint.config.js 配置相关的commit规范定义
    6. 绑定检验,执行命令
    npx husky add .husky/commit-msg 'npx --no-install commitlint --edit "$1"'
    来校验我们 commit 时添加的备注信息是否符合规范,执行成功之后 .husky 目录下面会有一个名为 commit-msg的 shell 脚本文件
