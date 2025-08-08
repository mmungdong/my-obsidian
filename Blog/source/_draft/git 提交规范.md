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

以下是一个符合 Angular 规范的 Commit Message：

```shell
fix($compile): couple of unit tests for IE9
# Please enter the Commit Message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# On branch master
# Changes to be committed:
# ...


Older IEs serialize html uppercased, but IE9 does not...
Would be better to expect case insensitive, unfortunately jasmine does
not allow to user regexps for throw expectations.


Closes #392
Breaks foo.bar api, foo.baz should be used instead
```

## Angular 规范格式

### header

Header 部分只有一行，包括三个字段：type（必选）、scope（可选）和 subject（必选）。

我们先来说 **type**，它用来说明 commit 的类型。为了方便记忆，我把这些类型做了归纳，它们主要可以归为 Development 和 Production 共两类。它们的含义是：

- Development：这类修改一般是项目管理类的变更，不会影响最终用户和生产环境的代码，比如 CI 流程、构建方式等的修改。遇到这类修改，通常也意味着可以免测发布。
- Production：这类修改会影响最终的用户和生产环境的代码。所以对于这种改动，我们一定要慎重，并在提交前做好充分的测试。

我在这里列出了 Angular 规范中的常见 type 和它们所属的类别，你在提交 Commit Message 的时候，一定要注意区分它的类别。举个例子，我们在做 Code Review 时，如果遇到 Production 类型的代码，一定要认真 Review，因为这种类型，会影响到现网用户的使用和现网应用的功能。



 # 参考

- [Angular提交信息规范 - Git Guide](https://zj-git-guide.readthedocs.io/zh-cn/latest/message/Angular%E6%8F%90%E4%BA%A4%E4%BF%A1%E6%81%AF%E8%A7%84%E8%8C%83/)
- [Git Commit Message 规范 | 云原生AI实战星球](https://konglingfei.com/onex/convention/commit.html#%E4%BB%80%E4%B9%88%E6%98%AF-angular-%E8%A7%84%E8%8C%83)
- 