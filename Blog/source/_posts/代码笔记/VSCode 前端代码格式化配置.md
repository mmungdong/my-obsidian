---
title: VSCode 前端代码格式化配置
date: 2026-01-16
categories:
  - 代码笔记
tags:
  - 前端
archive: false
hide: false
---


前言：
- **EditorConfig**：管**基础格式**（缩进、换行、编码、行尾空格等）
- **ESLint/Prettier**：管**语法层面风格**（命名、分号、语句换行等）
- 二者搭配使用，实现完整代码风格管控

# 1. EditorConfig

轻量的**跨编辑器 / IDE 代码基础格式统一工具**，解决团队 / 跨设备开发时，不同编辑器格式规则不一致导致的代码风格混乱、Git 格式冲突问题。

作用如下：

- 统一所有支持工具的基础代码格式（缩进、换行符、编码等）；
- 支持按文件类型精细化配置规则；
- 轻量无侵入，仅需项目根目录放配置文件，编辑器装插件即可生效。

使用：

- 在项目根目录下新建文件 `.editorconfig` , 文件内容如下：

```ini
# EditorConfig 核心配置文件，用于统一跨编辑器/IDE的代码基础格式
# 规则优先级：特殊文件类型（如.md/.json）> 全局规则（[*]）

# 核心标识：设为true表示这是项目根目录的配置文件，停止向上级目录查找.editorconfig
root = true

# ---------------------------------------------------------
# 所有文件的默认全局配置（无特殊声明的文件均遵循此规则）
# ---------------------------------------------------------
[*]
# 字符编码：统一强制使用UTF-8，避免中文乱码、编码不一致问题
charset = utf-8
# 缩进方式：全局默认使用空格（而非Tab），避免Tab宽度不一致导致格式混乱
indent_style = space
# 缩进大小：全局默认2个空格缩进（前端项目常用规范）
indent_size = 2
# 行尾换行符：统一使用LF（Linux/Mac风格），避免Windows(CRLF)和Linux/Mac换行符混用导致Git冲突
end_of_line = lf
# 文件末尾：强制添加一个空行，符合大多数编程语言的代码规范
insert_final_newline = true
# 行尾空格：默认自动删除行尾多余的空格（清理无效空白字符）
trim_trailing_whitespace = true

# ---------------------------------------------------------
# 针对不同语言/文件类型的覆盖配置（优先级高于全局规则）
# ---------------------------------------------------------

# Markdown 文件专属规则
[*.md]
# 关闭行尾空格自动删除：因为标准Markdown中，行尾加2个空格代表手动换行（软换行）
# 若开启trim_trailing_whitespace，会破坏Markdown的换行语法
trim_trailing_whitespace = false
# 关闭最大行长度限制：Markdown注重阅读性，无需强制换行，保留自然排版
max_line_length = off

# JSON/YAML/YML 文件专属规则
[*.{json,yaml,yml}]
# 强制缩进为2个空格：即使全局规则修改，这类文件也固定2空格（符合JSON/YAML的通用规范）
indent_size = 2

# Makefile 文件专属规则
[Makefile]
# 强制使用Tab缩进：Makefile语法要求必须用Tab分隔命令，用空格会导致语法报错（非风格问题，是语法强制要求）
indent_style = tab
```


参考： [EditorConfig](https://editorconfig.org/#overview)

vscode 使用 EditorConfig 需要下载插件：[Site Unreachable](https://marketplace.visualstudio.com/items?itemName=EditorConfig.EditorConfig)

# 2. 使用 prettier 工具

## 2.1. VSCode 安装 prettier 插件

- [Site Unreachable](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)

![image.png](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20260116224213525.png?imageSlim)


## 2.2. 使用方法

1. 安装 prettier

```SHELL
npm install prettier -D
```


2. 在 `package.json` 中配置运行命令：

```json
"scripts": {
	"format": "prettier --write ."
},
```

这样我们可以通过运行 `npm run format` 来对代码进行格式化。

但是，这样太麻烦了，我们可以选择在保存文件的时候对代码自动进行格式化保存：

VSCode 中设置：

1. 打开保存时格式化文件：

![image.png](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20260116225545606.png?imageSlim)

2. 配置通过 prettier 来对文件进行格式化：

![image.png](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20260116225709667.png?imageSlim)

## 2.3. 配置

配置 `.prettierrc` 文件：

```json
{
  // 缩进方式：false = 使用空格缩进（和 EditorConfig 的 indent_style = space 对应）
  "useTabs": false,
  // 缩进大小：2 个空格（和 EditorConfig 的 indent_size = 2 对应）
  "tabWidth": 2,
  // 每行最大长度：超过 80 个字符时自动换行，保持代码可读性
  "printWidth": 80,
  // 字符串引号：true = 统一使用单引号（如 'hello' 而非 "hello"，前端项目常见风格）
  "singleQuote": true,
  // 尾逗号："none" = 禁止在对象/数组最后一项后加逗号（如 {a:1, b:2} 而非 {a:1, b:2,}）
  "trailingComma": "none",
  // 语句分号：false = 禁止在语句末尾加分号（如 const a = 1 而非 const a = 1;，部分前端项目的风格选择）
  "semi": false
}
```

配置 `.prettierignore`,实际使用时选择自己需要的即可:

```text
# ------------------------------
# 依赖与构建产物（自动生成，无需格式化）
# ------------------------------
node_modules/
dist/
build/
out/
.next/          # Next.js 构建目录
.nuxt/          # Nuxt.js 构建目录
.output/        # VuePress/VitePress 构建目录
.eslintcache

# ------------------------------
# 包管理器与锁文件（由工具维护，禁止手动修改）
# ------------------------------
package-lock.json
yarn.lock
pnpm-lock.yaml
pnpm-workspace.yaml
bun.lockb

# ------------------------------
# 静态资源与二进制文件（Prettier 无法处理）
# ------------------------------
*.png
*.jpg
*.jpeg
*.gif
*.svg
*.webp
*.ico
*.woff
*.woff2
*.ttf
*.eot
*.mp4
*.avi
*.pdf
*.docx

# ------------------------------
# 配置与环境文件（格式敏感或自动生成）
# ------------------------------
.env
.env.local
.env.development
.env.production
.editorconfig
.prettierrc
.eslintrc
.gitignore
.npmignore
Dockerfile
docker-compose.yml

# ------------------------------
# 日志、缓存与临时文件
# ------------------------------
*.log
.cache/
.temp/
.tmp/
.DS_Store       # macOS 系统文件
Thumbs.db       # Windows 系统文件

# ------------------------------
# 版本控制与编辑器目录
# ------------------------------
.git/
.vscode/
.idea/
.gradle/
.mvn/

# ------------------------------
# 特定场景（可选）
# ------------------------------
CHANGELOG.md    # 手动维护的变更日志
*.min.js        # 已压缩的 JS 文件
*.min.css       # 已压缩的 CSS 文件
```


# 3. 使用 ESLint

## 3.1. 安装插件

- [Site Unreachable](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)

![image.png](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20260116232148841.png?imageSlim)

## 3.2. 安装依赖

安装 eslint 开发依赖基础包：

```SHELL
npm install eslint -D
```

为了避免 eslint 的检测规则和 prettier 的配置冲突，还需要安装它俩的整合包，避免在 eslint 中检测到不合规，但是 prettier 的配置确是合规的，让 eslint 按照 prettier 的规则进行检测：

```shell
npm install eslint-plugin-prettier eslint-config-prettier -D
```

在 `.eslintrc.json` 中需要加如下配置，其中我们常见的配置是 extends、plugins 和 rules：

```js
module.exports = {
  extends: [
    // 保留项目原有基础配置（如 'eslint:recommended'、'plugin:vue/vue3-essential' 等）
    'plugin:prettier/recommended' // 整合 Prettier 规则，避免冲突
  ]
}
```

参考 Next.js + TypeScript 完整 ESLint 配置：

```js
/**
 * Next.js + TypeScript ESLint 完整配置
 * 核心原则：官方规范 + 类型安全 + Hooks 合规 + Prettier 协同
 */
module.exports = {
  // ===================== 核心解析配置（TS/JSX/Next.js 适配） =====================
  // 解析器：让 ESLint 能识别 Vue/React 单文件组件（这里适配 Next.js 的 JSX/TSX）
  parser: '@typescript-eslint/parser',
  // 解析器选项：指定 ES 版本、模块类型、TS 配置文件路径
  parserOptions: {
    ecmaVersion: 'latest', // 支持最新 ES 语法
    sourceType: 'module', // 启用 ES 模块（import/export）
    project: './tsconfig.json', // 关联项目的 TS 配置，确保类型检查准确
    ecmaFeatures: {
      jsx: true // 支持 JSX/TSX 语法（Next.js 核心）
    }
  },

  // ===================== 继承的规则集（基础规则来源） =====================
  extends: [
    // 1. Next.js 官方核心规则：包含框架最佳实践 + Web Vitals 性能检查
    'next/core-web-vitals',
    // 2. TypeScript ESLint 推荐规则：TS 语法/类型相关的基础检查
    'plugin:@typescript-eslint/recommended',
    // 3. TypeScript ESLint 严格规则：增强 TS 类型安全（可选，追求高代码质量则保留）
    'plugin:@typescript-eslint/strict-type-checked',
    // 4. React Hooks 官方规则：强制 Hooks 使用规范（如依赖数组完整）
    'plugin:react-hooks/recommended',
    // 5. Prettier 协同规则：关闭 ESLint 中与 Prettier 冲突的规则，同时将 Prettier 格式问题转为 ESLint 警告
    'plugin:prettier/recommended'
  ],

  // ===================== 启用的插件（扩展 ESLint 检查能力） =====================
  plugins: [
    '@typescript-eslint', // TS 专属检查插件
    'react-hooks', // React Hooks 检查插件
    'import', // 模块导入规范检查（如未使用的 import、路径错误）
    'promise' // Promise 异步代码规范检查（如未处理 reject）
  ],

  // ===================== 自定义规则（覆盖默认配置，适配实际开发） =====================
  rules: {
    // -------------------- React Hooks 规则（人性化调整） --------------------
    // 关闭「禁止在 useEffect 中调用 setState」：Next.js 中常需在副作用中更新状态（如表单回显）
    'react-hooks/set-state-in-effect': 'off',
    // 依赖数组不完整从 error 改为 warn：避免 Next.js 中 useRouter 等场景的假阳性报错
    'react-hooks/exhaustive-deps': 'warn',

    // -------------------- TypeScript 规则（宽松适配 + 类型安全） --------------------
    // 关闭「必须使用接口而非类型别名」：TS 中 type 和 interface 场景不同，无需强制
    '@typescript-eslint/consistent-type-definitions': 'off',
    // 关闭「未使用变量报错」：开发阶段临时注释的变量无需报错（改为警告）
    '@typescript-eslint/no-unused-vars': ['warn', { argsIgnorePattern: '^_' }], // 忽略下划线开头的变量（如 _props）
    // 强制函数返回值类型定义：提升 TS 类型安全（仅警告，避免过度严格）
    '@typescript-eslint/explicit-function-return-type': 'warn',
    // 禁止 any 类型：逐步减少 any 提升代码质量（改为警告，兼容历史代码）
    '@typescript-eslint/no-explicit-any': 'warn',

    // -------------------- 通用代码质量规则 --------------------
    // 禁止 console.log 等打印：生产环境需清理，开发阶段警告即可
    'no-console': ['warn', { allow: ['warn', 'error'] }], // 允许 console.warn/error
    // 禁止 debugger：开发阶段警告，防止提交到生产环境
    'no-debugger': 'warn',

    // -------------------- Prettier 协同规则 --------------------
    // Prettier 格式问题转为警告（而非报错），避免打断开发流程
    'prettier/prettier': 'warn'
  },

  // ===================== 环境配置（指定代码运行环境，避免全局变量报错） =====================
  env: {
    browser: true, // 支持浏览器全局变量（window、document）
    node: true, // 支持 Node.js 全局变量（process、__dirname）
    es2025: true // 支持 ES2025 语法
  },

  // ===================== 全局变量（避免未定义报错） =====================
  globals: {
    React: 'readonly', // React 全局变量只读
    JSX: 'readonly' // JSX 全局变量只读
  },

  // ===================== 忽略特定文件/目录（可选） =====================
  ignorePatterns: [
    '.next/', // Next.js 构建目录
    'node_modules/', // 依赖目录
    'public/', // 静态资源目录
    '*.config.js' // 配置文件（如 next.config.js）
  ]
}
```