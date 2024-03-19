---
layout: post
title: "Turtorial of Jekyll in windows"
author: 233yun
date: 2024-03-19
---


Jekyll是一个流行的静态站点生成器，可以让你使用Markdown、Liquid、HTML & CSS来构建网站。在Windows上配置Jekyll需要几个步骤，但通过以下指南，你可以轻松设置并运行你的Jekyll网站。

## 步骤1: 安装Ruby

Jekyll是用Ruby编写的，所以需要先装Ruby。

1. 下载并安装RubyInstaller
   - 访问 [RubyInstaller网站](https://rubyinstaller.org/)。
   - 下载适用于Windows的最新Ruby+Devkit版本。
   - 运行安装程序，确保在安装过程中勾选“Add Ruby executables to your PATH”选项,将ruby添加到环境变量中。
   - 在安装完ruby后会出现一个终端窗口，需要你选择一下安装MSYS2和MINGW toolchain，但这个时候由于中国地区限制，安装会很慢并且有可能失败。解决方法：1.使用vpn 2.提前下载MSYS2的安装包进行离线安装。

2. 安装完成后，在命令行中验证Ruby安装：

   ```bash
   ruby -v
   ```

## 步骤2: 安装Jekyll和Bundler

1. 打开cmd或PowerShell

- 使用以下命令安装Jekyll和Bundler：
  
  ```bash
  gem install jekyll bundler
  ```

- 验证Jekyll安装：
  
  ```bash
  jekyll -v
  ```

## 步骤3: 创建你的Jekyll网站

进入你的本地github.io仓库目录，初始化网站：

```bash
jekyll new . --force
```

这将在当前目录创建一个新的Jekyll网站。--force选项允许在非空目录中创建。

在本地进行网站构建及预览：

```bash
bundle exec jekyll serve
```

打开浏览器访问 <http://localhost:4000> 就可以查看你的新网站。
