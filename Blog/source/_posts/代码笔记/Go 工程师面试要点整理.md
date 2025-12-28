

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


### 1.1.2. GOVENDER (go version >= 1.5)


### 1.1.3. GOModules (go version >= 1.11)