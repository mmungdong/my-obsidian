---
title: Go 工程师面试要点整理
date: 2026-01-16
categories:
  - 代码笔记
tags:
  - 面试题
  - golang
archive: false
hide: false
---


# 1. Go 基础

## 1.1. go 包的管理方式

### 1.1.1. GOPATH （go version < 1.5）
#### 1.1.1.1 GOPATH 模式是什么？
- 2009.11.10 随 go 语言诞生
- 通过统一包的存放路径来管理
- 不支持依赖包的版本控制

#### 1.1.1.2. GOPATH 模式 和 GOPATH 路径的区别
- GOPATH 模式是指我们通过 GOPATH 来管理我们的包
- GOPATH 路径值的是 GOPATH 这个环境变量的路径

#### 1.1.1.3. GOROOT 和 GOPATH 路径的区别
- GOROOT 是 Golang 的安装目录
- GOPATH 是 Go 语言指定的工作空间
注意：GOROOT 和 GOPATH 不能是同一个目录

#### 1.1.1.4. GOPATH 默认值
![image.png](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20251228145238820.png?imageSlim)

GOPATH 模式下，我们的工程代码必须放在`GOPATH/src`目录下。

#### 1.1.1.5. go get
1. **步骤 1：克隆远程代码到指定目录**
    
    将远程代码克隆到`$GOPATH/src`目录下。
    
    这是 GOPATH 模式的强制要求：Go 的工具链仅会识别`$GOPATH/src`作为源码根目录，远程代码只有存放在此目录，才能被后续的编译、安装命令识别。
    
2. **步骤 2：执行`go install`命令**
    
    `go install`在 GOPATH 模式下的作用是：编译当前源码，并将生成的可执行文件输出到`$GOPATH/bin`目录（可全局调用），同时将编译后的包文件（`.a`格式）存入`$GOPATH/pkg`目录（避免重复编译）。
    
3. **步骤 3：使用`-d`参数的场景**
    
    执行`go get`时指定`-d`参数，可实现 “仅下载不安装”—— 即只获取远程代码及依赖的源码，但不执行编译和安装操作，适用于仅需要获取源码的场景。

#### 1.1.1.6. go install
1. **可执行程序的输出**
    
    若目标代码是包含`main`包的可执行程序（能生成二进制文件），`go install`会将编译后的可执行文件存储到`$GOPATH/bin`目录。

- 作用：`$GOPATH/bin`下的可执行文件可直接全局调用（需将该目录加入系统环境变量），方便快速运行程序。

2. **普通包的输出**
    
    若目标代码是普通包（非`main`包，用于被其他代码引入），`go install`会将编译生成的`.a`后缀静态库文件，存放至`$GOPATH/pkg`目录。

- 作用：`.a`文件是包的编译产物，后续依赖该包的代码可直接使用此文件，避免重复编译，提升项目的构建效率。

#### 1.1.1.7. 怎么判断一个包能生成可执行行文件？

main 包中存在 main 函数的情况下。



### 1.1.2. GOVENDER (go version >= 1.5)


### 1.1.3. GOModules (go version >= 1.11)