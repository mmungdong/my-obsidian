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

[Fetching Title#5cv9](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20251109160950058.png?imageSlim)

- pre-release：预发布版本，适合个人开发使用，有一些新特性
- bugfix：缺陷修复版本，还没到稳定状态
- security：安全稳定版本，公司项目和个人项目应该使用这种类型的版本

我们这里选择 3.10 版本就可以了

## 1.2. 安装

[Fetching Title#v78v](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20251109162233696.png?imageSlim)

[Fetching Title#9obf](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20251109162518483.png?imageSlim)

然后就 OK 了


输入 `python --version` 和 `pip --version` 确认安装是否成功

[Fetching Title#wnrp](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20251109162820850.png?imageSlim)

# 2. Python 虚拟环境的创建与优缺点

- **创建虚拟环境命令**：`python -m venv [环境名]`，该命令在 Python 2.7 + 或 Python 3.3 + 以上可以使用。
- **隔离性**：*Python 虚拟环境与系统环境之间是隔离的*，一个项目一个虚拟环境，不同的虚拟环境不会相互影响，最大程度避免包冲突。

