### 常用指令

```bash
#安装(安装最新版)
npm install <moduleName>
npm i <moduleName>
npm install <moduleName> -g #全局安装
npm install  #根据package.json中的信息自动安装node_modules

#安装指定版本
npm install <moduleName>@x.x.x # @后面加版本号

# dependencies：运行时的以来，项目发布后，还需要使用的模块。即生产环境下需要使用的模块
# devDpendencies：开发时以来，项目发布后，就不需要该模块了。比如压缩css、js或gulp这种模块

# -save，在package文件的dependencies节点写入依赖
npm install -save <moduleName>	

# -save-dev(-D)，在package文件的devDependencies节点写入依赖
npm install -save-dev <moduleName>
npm install -D <moduleName>
#更新到最新版
npm update <moduleName>

#卸载
npm uninstall <moduleName>
```



### 项目初始化

项目目录打开终端输入 `npm init` 生成 package 文件

或者 `npm init --yes` 快速生成

```bash
#配置信息

package name:	#项目名称
version:        #版本号
description:    #对项目的描述
entry point:    #项目的入口文件（一般你要用那个js文件作为node服务，就填写那个文件）
test command:   #项目启动的时候要用什么命令来执行脚本文件(script中的)
git repository: #如果你要将项目上传到git中的话，那么就需要填写git的仓库地址
keywords:		#项目关键字
author:
license:
```

```json
{
  "name": "npm_learning",
  "version": "1.0.0",
  "description": "",	#keywords
  "main": "index.js",	#entry point
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1" #test command
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "vue": "^2.6.14"
  },
  "devDependencies": {
    "bootstrap": "^5.1.3"
  }
}
```

### 包版本问题

`package-lock.json` 中的版本号为实际版本号

`package.json` 不一定，因为有 `^` & `~`

```bash
^a.b.c  
#主版本 a 不变，b & c按照可以安装的最新版本安装。若已安装的版本 A.B.C 中 B < b,则不会更新,即B&C不变

~a.b.c	
#主版本 a & b不变，c按照可以安装的最新版本安装。同上
```

若没有这两个前缀，则按照指定版本安装