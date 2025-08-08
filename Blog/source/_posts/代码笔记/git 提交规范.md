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

在实际开发中，为了使 Commit Message 在 GitHub 或者其他 Git 工具上更加易读，我们往往会限制每行 message 的长度。根据需要，可以限制为 50/72/100 个字符，这里我将长度限制在 72 个字符以内（也有一些开发者会将长度限制为 100，你可根据需要自行选择）。

## Angular 规范格式

### Header

Header 部分只有一行，包括三个字段：type（必选）、scope（可选）和 subject（必选）。

#### type

我们先来说 **type**，它用来说明 commit 的类型。为了方便记忆，我把这些类型做了归纳，它们主要可以归为 Development 和 Production 共两类。它们的含义是：

- Development：这类修改一般是项目管理类的变更，不会影响最终用户和生产环境的代码，比如 CI 流程、构建方式等的修改。遇到这类修改，通常也意味着可以免测发布。
- Production：这类修改会影响最终的用户和生产环境的代码。所以对于这种改动，我们一定要慎重，并在提交前做好充分的测试。

我在这里列出了 Angular 规范中的常见 type 和它们所属的类别，你在提交 Commit Message 的时候，一定要注意区分它的类别。举个例子，我们在做 Code Review 时，如果遇到 Production 类型的代码，一定要认真 Review，因为这种类型，会影响到现网用户的使用和现网应用的功能。

|**类型**|**类别**|**说明**|
|---|---|---|
|feat|Production|新增功能|
|fix|Production|Bug 修复|
|perf|Production|提高代码性能的变更|
|refactor|Production|其他代码类的变更，这些变更不属于 feat、fix、perf 和 style，例如简化代码、重命名变量、删除冗余代码等|
|style|Development|代码格式类的变更，比如用 gofmt 格式化代码、删除空行等|
|test|Development|新增测试用例或是更新现有测试用例|
|ci|Development|持续集成和部署相关的改动，比如修改 Jenkins、GitLab CI 等 CI 配置文件或者更新 systemd unit 文件|
|docs|Development|文档类的更新，包括修改用户文档或者开发文档等|
|chore|Development|其他类型，比如构建流程、依赖管理或者辅助工具的变动等|

#### scope

scope 这里先不建议填写，因为把握不好颗粒度反而会让项目很难维护。

#### subject

subject 是 commit 的简短描述，必须以动词开头、使用现在时。比如，我们可以用 change，却不能用 changed 或 changes，而且这个动词的第一个字母必须是小写。通过这个动词，我们可以明确地知道 commit 所执行的操作。此外我们还要注意，subject 的结尾不能加英文句号。

## Body

Header 对 commit 做了高度概括，可以方便我们查看 Commit Message。那我们如何知道具体做了哪些变更呢？答案就是，可以通过 Body 部分，它是对本次 commit 的更详细描述，是可选的。

Body 部分可以分成多行，而且格式也比较自由。不过，和 Header 里的`<subject>`一样，它也要以动词开头，使用现在时。此外，它还必须**要包括修改的动机**，以及**和跟上一版本相比的改动点**。

除范围为 “docs” 的提交外，所有提交都必须包含正文。当正文为必填项时，其长度必须至少为 20 个字符。

## Footer

Footer 部分不是必选的，可以根据需要来选择，主要用来说明本次 commit 导致的后果。在实际应用中，Footer 通常用来说明不兼容的改动和关闭的 Issue 列表，格式如下：

```shell
BREAKING CHANGE: <breaking change summary>
// 空行
<breaking change description + migration instructions>
// 空行
// 空行
Fixes #<issue number>
```

接下来，我给你详细说明下这两种情况：

- 不兼容的改动：如果当前代码跟上一个版本不兼容，需要在 Footer 部分，以 BREAKING CHANG: 开头，后面跟上不兼容改动的摘要。Footer 的其他部分需要说明变动的描述、变动的理由和迁移方法，例如：

```
BREAKING CHANGE: isolate scope bindings definition has changed and
    the inject option for the directive controller injection was removed.

    To migrate the code follow the example below:

    Before:

    scope: {
      myAttr: 'attribute',
    }

    After:

    scope: {
      myAttr: '@',
    }
    The removed inject wasn't generaly useful for directives so there should be no code using it.
```

- 关闭的 Issue 列表：关闭的 Bug 需要在 Footer 部分新建一行，并以 Closes 开头列出，例如：Closes #123。如果关闭了多个 Issue，可以这样列出：Closes #123, #432, #886。例如:

```
Change pause version value to a constant for image
    
    Closes #1137
```

## Revert Commit

除了 **Header**、**Body** 和 **Footer 这 3 个部分，**Commit Message 还有一种特殊情况：如果当前 commit 还原了先前的 commit，则应以 revert: 开头，后跟还原的 commit 的 Header。而且，在 Body 中必须写成 This reverts commit `<hash>` ，其中 hash 是要还原的 commit 的 SHA 标识。例如：

```
revert: feat(iam-apiserver): add 'Host' option

This reverts commit 079360c7cfc830ea8a6e13f4c8b8114febc9b48a.
```

为了更好地遵循 Angular 规范，建议你在提交代码时养成不用 git commit -m，即不用-m 选项的习惯，而是直接用 git commit 或者 git commit -a 进入交互界面编辑 Commit Message。这样可以更好地格式化 Commit Message。

但是除了 Commit Message 规范之外，在代码提交时，我们还需要关注 3 个重点内容：提交频率、合并提交和 Commit Message 修改。

 # 参考

- [Angular提交信息规范 - Git Guide](https://zj-git-guide.readthedocs.io/zh-cn/latest/message/Angular%E6%8F%90%E4%BA%A4%E4%BF%A1%E6%81%AF%E8%A7%84%E8%8C%83/)
- [Git Commit Message 规范 | 云原生AI实战星球](https://konglingfei.com/onex/convention/commit.html#%E4%BB%80%E4%B9%88%E6%98%AF-angular-%E8%A7%84%E8%8C%83)