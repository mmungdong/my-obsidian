---
title:
  "{ title }":
date:
  "{ date }":
categories:
  - 代码笔记
tags:
  - python
archive: false
hide: false
---
# 1. 下载 python 安装包

> 下载地址： [Download Python \| Python.org](https://www.python.org/downloads/)

## 1.1. 版本差异

![Fetching Title#5cv9](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20251109160950058.png?imageSlim)

- pre-release：预发布版本，适合个人开发使用，有一些新特性
- bugfix：缺陷修复版本，还没到稳定状态
- security：安全稳定版本，公司项目和个人项目应该使用这种类型的版本

我们这里选择 3.10 版本就可以了

## 1.2. 安装

![Fetching Title#v78v](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20251109162233696.png?imageSlim)

![Fetching Title#9obf](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20251109162518483.png?imageSlim)

然后就 OK 了


输入 `python --version` 和 `pip --version` 确认安装是否成功

![Fetching Title#wnrp](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20251109162820850.png?imageSlim)

# 2. Python 虚拟环境的创建与优缺点

- **创建命令**：`python -m venv [环境名]`，例如：`python -m venv env`，支持 Python 2.7 + 或 Python 3.3 + 以上版本。
- **隔离性**：虚拟环境与系统环境相互隔离，不同项目的虚拟环境互不影响，可最大程度避免包冲突。
- **缺点**：
    - 占用较大存储空间；
    - 需通过`activate`命令激活才能使用，使用完毕需用`deactivate`命令退出。


假如我们安装的虚拟环境为 env， 则需要执行 `env\Scripts\activate.bat` 这个脚本用来激活虚拟环境，如果需要退出虚拟环境，则执行 `env\Scripts\deactivate.bat` 这个脚本即可。

假如我们不需要虚拟环境，则直接删除这个 env 文件夹即可。

# 3. pip 镜像加速

- **镜像加速原理**：国内网站拷贝了 pip 的所有包，使 pip 直接连接国内网站下载包，从而提升下载速度。
- **使用方式**
    - 临时使用：`pip install -i 镜像地址 <some-package>`
    - 全局使用：`pip config set global.index-url 镜像地址`
- **腾讯云镜像地址**：`https://mirrors.cloud.tencent.com/pypi/simple`
- **阿里云镜像地址**：`https://mirrors.aliyun.com/pypi/simple`
- **清华镜像地址**：`https://pypi.tuna.tsinghua.edu.cn/simple`

# 4. pycharm

## 4.1. 下载

- 下载地址：[PyCharm，您需要的唯一 Python IDE](https://www.jetbrains.com/zh-cn/pycharm/)

下载这里使用 pycharm 社区版本的直接下载安装即可

## 4.2. 关联虚拟环境解释器

![Fetching Title#w6nl](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20251109165250072.png?imageSlim)


![Fetching Title#omaf](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20251109165349328.png?imageSlim)


![](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20251109170204599.png?imageSlim)

然后点击 ok 就完成了

最后我们新建一个 `app.py` 文件测试运行一下

![](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20251109170453539.png?imageSlim)

## 4.3. pycharm 的一些设置


- 格式化代码和优化导入

![](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20251109171011846.png?imageSlim)


- 编辑器字体设置

![](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20251109171200893.png?imageSlim)

- 代码模板设置

![](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20251109171435894.png?imageSlim)

模板代码

```
#!/usr/bin/env python 
# -*- coding: utf-8 -*- 
""" 
@Time : ${DATE} ${TIME} 
@Author : 510195171@qq.com 
@File : ${NAME}.py 
"""
```

- 插件安装

![](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20251109171841883.png?imageSlim)

这个插件可以帮助我们读取 env 中环境变量的值


![](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20251109172016485.png?imageSlim)

代码右侧展示缩略图

![](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20251109172149125.png?imageSlim)

中文汉化插件包

## 4.4 导出依赖包到 requirements.txt

- 地址： [pipreqs · PyPI](https://pypi.org/project/pipreqs/)

安装脚本：

- 不需要支持  jupyter notebooks

```shell
pip install --no-deps pipreqs
pip install yarg==0.1.9 docopt==0.6.2
```

- 支持 jupyter notebooks

```shell
pip install pipreqs
```

