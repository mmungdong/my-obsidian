
# 1. 插件 & agents 安装

```shell
npx claude-code-templates@latest --agent=development-team/frontend-developer --yes

npx claude-code-templates@latest --agent=development-tools/code-reviewer --yes


```


# 2. 实战笔记

## 2.1. Nextjs

框架选择：

```text
使用如下规则来进行项目初始化，并将必要的信息补充到 CLAUDE.md 中:

1. 代码提交规范：使用 Angular 规范
    
2. 使用如下框架来对项目进行初始化：
    
    - 框架：Nextjs
    
    - 语言：Typescript
    
    - 路由模式：app
    
    - css：tailwindcss
    
    - Emotion: CSS-in-JS 库
    
    - UI框架：shadcn UI
    
    - 动画库：react spring （复杂动画） / Framer Motion （简单动画效果）
    
    - 代码检查：ESLint eslint-config-airbnb-typescript
    
    - 代码美化：prettier
    
    - 状态管理：Zustand
    
    - 国际化：react-i18next
    
    - http客户端：Axios
    
    - 工具库：Lodash
    
    - 日期：Day.js
    
    - Zod: TypeScript-first 模式声明和验证

3. 项目初始化完成后遵循 CLAUDE.md 的规范把项目的关键信息填写到 CLAUDE.md 中，要求每次完成需求后都需要用 ESLint 和 prettier 来检查代码并美化代码
```
