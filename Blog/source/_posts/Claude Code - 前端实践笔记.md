---
title: Claude Code - 前端实践笔记
date: 2025-12-23
categories:
  - 代码笔记
tags:
  - AI
  - VibeCoding
archive: false
hide: false
---

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
   
2. 该项目属于纯前端静态/全栈项目？
    
3. 使用如下框架来对项目进行初始化：
    
    - 框架：Nextjs
    
    - 语言：Typescript （tsconfig.json 中设置 strict: true ，开启 TypeScript 严格模式）
    
    - 路由模式：app
    
    - css：tailwindcss
    
    - Emotion: CSS-in-JS 库
    
    - UI框架：shadcn UI
    
    - 动画库：react spring （复杂动画） / Framer Motion （简单动画效果）
    
    - 代码检查：ESLint （遵循 eslint-config-airbnb-typescript 规范）
    
    - 代码美化：prettier （安装 `eslint-config-prettier`（禁用 ESLint 中与 Prettier 冲突的规则）和 `eslint-plugin-prettier`（将 Prettier 作为 ESLint 规则运行），让两者协同工作。）
    
    - 状态管理：Zustand
    
    - 国际化：react-i18next
    
    - http客户端：Axios
    
    - 工具库：Lodash
    
    - 日期：Day.js
    
    - Zod: TypeScript-first 模式声明和验证

4. 项目初始化完成后遵循 CLAUDE.md 的规范把项目的关键信息填写到 CLAUDE.md 中，要求每次完成需求后都需要用 ESLint 和 prettier 来检查代码并美化代码
```

AI 建议：待纠正

你的前端项目技术栈选择整体上非常现代化且覆盖了主流场景，但从**项目工程化、生产环境稳定性、用户体验、特殊场景支持**等维度来看，仍有一些可以补充和优化的点，下面分维度梳理疏漏和建议：

### 一、工程化与开发效率维度

#### ~~1. **ESLint 与 Prettier 集成**~~

~~你分别选了 ESLint（Google TS 规范）和 Prettier，但**缺少两者的集成方案**。如果直接使用，可能会出现 ESLint 的代码风格规则与 Prettier 冲突的情况（比如引号、分号、行长度）。~~

- ~~**补充**：安装 `eslint-config-prettier`（禁用 ESLint 中与 Prettier 冲突的规则）和 `eslint-plugin-prettier`（将 Prettier 作为 ESLint 规则运行），让两者协同工作。~~
- ~~另外，Google 的 TypeScript 规范相对严格，也可以考虑 `eslint-config-airbnb-typescript`（更主流的 React/TS 规范）作为备选。~~

#### ~~2. **提交规范与自动化**~~

~~前端项目的团队协作中，**代码提交信息规范、提交前的代码校验**是工程化的重要环节，你目前的栈中未涉及。~~

- ~~**补充**：~~
    - ~~`husky`：用于管理 Git Hooks（如 pre-commit、commit-msg），在提交前执行 ESLint/Prettier 校验，提交时验证信息格式。~~
    - ~~`lint-staged`：只对暂存区的代码执行校验 / 格式化，提升效率（避免每次提交都检查整个项目）。~~
    - ~~`commitlint`：约束提交信息的格式（如遵循 Conventional Commits 规范：feat/fix/docs 等前缀），便于生成 CHANGELOG。~~

#### ~~3. **类型定义与类型检查增强**~~

~~虽然用了 TypeScript，但大型项目中**类型导入的便捷性、类型检查的严格性**可以优化：~~

- ~~**补充**：~~
    - ~~`tsconfig-paths`：配置路径别名（如 `@/components` 代替 `../../components`），Next.js 虽内置支持，但需在 `tsconfig.json` 中配置 `paths`，配合该包可提升兼容性。~~
    - ~~开启 TypeScript 严格模式（`tsconfig.json` 中 `strict: true`）：强制类型检查，减少隐式 any 等问题，这是生产环境项目的最佳实践。~~

### 二、生产环境与性能优化维度

#### 1. **HTTP 客户端的增强**

你选了 Axios，但 Next.js 有**服务端组件（RSC）** 和客户端组件的区分，Axios 在服务端组件中使用时存在一些限制（如无法直接获取浏览器 Cookie，且 Next.js 推荐使用原生 `fetch`）。

- **补充**：
    - 方案 1：使用 `ky`（基于 fetch 的轻量级客户端，支持 TypeScript，比 Axios 更适配现代浏览器 / Node.js）。
    - 方案 2：对 Axios 进行封装，区分服务端 / 客户端请求（如服务端请求时手动传递 Cookie，客户端请求时使用浏览器环境）。
    - 另外，可补充 `axios-mock-adapter`（开发环境接口模拟）或 `MSW`（Mock Service Worker，更强大的跨端接口模拟，支持浏览器 / Node.js，适配 RSC）。

#### 2. **性能监控与埋点**

生产环境中，前端性能（如首屏加载、LCP、FID）和用户行为埋点是必要的，但你的栈中未涉及。

- **补充**：
    - `web-vitals`：Google 官方的性能指标库，可收集 Core Web Vitals（LCP、FID、CLS 等），Next.js 有内置的 `reportWebVitals` 函数可直接集成。
    - 埋点工具：根据需求选择（如百度统计、友盟，或自研埋点时用 `@umami/react`）。

#### 3. **图片 / 媒体优化**

Next.js 有内置的 `next/image`（图片优化）和 `next/video`（视频优化），但如果项目中有大量媒体资源，**缺少对图片压缩、懒加载的精细化处理**（虽然 `next/image` 已内置懒加载，但第三方图片 / 图标仍需考虑）。

- **补充**：
    - `next-svg`：优化 SVG 图标（如将 SVG 作为组件导入，减少网络请求）。
    - `lqip-loader`：生成图片的低质量占位符（LQIP），提升用户体验。

### 三、用户体验与交互维度

#### 1. **表单处理**

你选了 Zod（数据验证），但**缺少表单状态管理、校验联动的方案**（如输入框防抖、表单提交状态、错误提示）。

- **补充**：
    - `react-hook-form`：轻量级的表单库，与 Zod 无缝集成（`@hookform/resolvers/zod`），性能优异，适配 React 组件和 TypeScript。
    - 备选：`formik`（功能更全，但体积稍大）。

#### 2. **加载状态与错误边界**

项目中不可避免会有异步请求的加载状态、错误处理，以及组件级别的错误捕获，你的栈中未涉及。

- **补充**：
    - 加载状态：`react-content-loader`（骨架屏）、`nextjs-progressbar`（页面加载进度条）。
    - 错误边界：`react-error-boundary`（捕获组件渲染错误，避免整个页面崩溃），Next.js 也支持自定义 `error.tsx`（路由级别的错误处理）和 `loading.tsx`（路由级别的加载状态），但组件级别的错误仍需 error-boundary。

#### 3. **无障碍访问（a11y）**

现代前端项目需要考虑无障碍访问（如屏幕阅读器、键盘导航），你的栈中未涉及相关校验工具。

- **补充**：
    - `eslint-plugin-jsx-a11y`：ESLint 插件，检查 JSX 中的无障碍问题（如缺少 alt 属性、标签嵌套错误），建议集成到 ESLint 配置中。
    - `react-aria`：Adobe 出品的无障碍组件库，shadcn/ui 底层已使用 `radix-ui`（同样注重无障碍），但自定义组件时可参考。

#### 4. **滚动与拖拽交互**

如果项目中有复杂的滚动（如无限滚动）、拖拽功能，你的栈中未涉及。

- **补充**：
    - 无限滚动：`react-infinite-scroll-component`、`next-infinite-scroll`（适配 Next.js 路由）。
    - 拖拽：`react-beautiful-dnd`（简单拖拽）、`dnd-kit`（现代拖拽库，支持 TypeScript，性能更好）。

### 四、依赖管理与构建维度

#### 1. **依赖分析与体积优化**

项目打包后，需要分析 bundle 体积，移除无用代码，你的栈中未涉及。

- **补充**：
    - `@next/bundle-analyzer`：Next.js 官方的 bundle 分析工具，可查看各依赖的体积占比。
    - `purgecss`：移除未使用的 CSS（虽然 Tailwind CSS 已内置 PurgeCSS，但第三方 CSS 仍需处理）。

#### 2. **环境变量管理**

Next.js 支持 `.env` 文件，但**缺少对环境变量的类型提示和验证**（如开发 / 生产环境的变量不一致导致的问题）。

- **补充**：
    - `zod` 本身可用于环境变量验证（如创建 `env.schema.ts`，验证 `process.env` 中的变量）。
    - `dotenv`（Next.js 已内置，但自定义脚本时可使用）。

### 五、特殊场景补充（根据项目需求可选）

1. **PWA（渐进式 Web 应用）**：如果需要将项目打包为 PWA，补充 `next-pwa`。
2. **图表可视化**：如果有图表需求，补充 `recharts`（轻量级）、`echarts-for-react`（功能全）。
3. **PDF / 文件处理**：如果需要预览 / 生成 PDF，补充 `react-pdf`。
4. **状态持久化**：Zustand 本身支持持久化（`zustand-persist`），但如果需要更灵活的存储（如 localStorage/sessionStorage/indexedDB），补充 `localforage`。

### 总结

你的技术栈已经覆盖了**基础框架、样式、状态管理、国际化、工具库**等核心部分，疏漏主要集中在**工程化自动化、生产环境性能 / 监控、表单处理、用户体验细节、特殊场景支持**等方面。

建议根据项目的 ** 规模（个人 / 团队）、需求（是否需要 PWA / 图表 / 拖拽）、预算（第三方工具是否付费）** 来选择性补充：

- **小型项目**：重点补充**ESLint+Prettier 集成、react-hook-form+Zod、web-vitals、react-error-boundary**即可。
- **中大型团队项目**：建议补充**husky+lint-staged+commitlint、MSW、bundle-analyzer、eslint-plugin-jsx-a11y、骨架屏、埋点工具**等，提升工程化和用户体验。