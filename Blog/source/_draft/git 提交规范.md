---
title: git 提交规范
date: 2025-08-09 00:21:38
categories:
  - 代码笔记
tags:
  - git
archive: true
hide: false
---
# Commit Message 规范


这里选用 Angular 的提交规范

## Angular 规范是什么？

Angular 规范其实是一种语义化的提交规范（Semantic Commit Messages），所谓语义化的提交规范包含以下内容：

- Commit Message 是语义化的：Commit Message 都会被归为一个有意义的类型，用来说明本次 commit 的类型。
- Commit Message 是规范化的：Commit Message 遵循预先定义好的规范，比如 Commit Message 格式固定、都属于某个类型，这些规范不仅可被开发者识别也可以被工具识别。

## Angular 规范

在 Angular 规范中，Commit Message 包含三个部分，分别是 **Header**、**Body** 和 **Footer**，格式如下：

```
<type>[optional scope]: <description>
// 空行
[optional body]
// 空行
[optional footer(s)]
```

其中，header 是必需的，body 和 footer 可以省略。在以上规范中，<scope>必须用括号 () 括起来， `<type>[<scope>]` 后必须紧跟冒号 ，冒号后必须紧跟空格，2 个空行也是必须的。

例如： 

 # 参考

- [Angular提交信息规范 - Git Guide](https://zj-git-guide.readthedocs.io/zh-cn/latest/message/Angular%E6%8F%90%E4%BA%A4%E4%BF%A1%E6%81%AF%E8%A7%84%E8%8C%83/)
- [Git Commit Message 规范 | 云原生AI实战星球](https://konglingfei.com/onex/convention/commit.html#%E4%BB%80%E4%B9%88%E6%98%AF-angular-%E8%A7%84%E8%8C%83)
- 