
# 1. 初识 TS
## 1.1. TS 的重要性

- TypeScript 是 JavaScript 的超集，解决了 JavaScript 在类型检测方面的缺失问题，使代码更加安全、健壮，适合大型项目开发。
- JavaScript 本身是一门优秀的语言，但缺乏类型约束，导致在开发过程中容易出现运行时错误，不利于代码维护。
- TypeScript 通过类型校验机制，在**开发阶段**就能发现错误，<u>避免了运行时错误的发生，提高了开发效率和代码质量</u>。

## 1.2. TS 定位

- TypeScript 是拥有类型系统的 JavaScript 超集，最终会被编译为干净、标准的 JavaScript 代码。
- 官方定义为“始于 JavaScript，归于 JavaScript”，即 TypeScript 代码最终仍需编译为 JavaScript 运行。
- TypeScript 不仅支持 ECMAScript 标准，还引入了如枚举、元组等高级类型系统特性。

## 1.3. 应用现状

- 当前主流前端框架如 Vue3、React、Angular 均已全面采用 TypeScript 进行开发。
- VS Code、React Native、Node.js 等项目也广泛使用 TypeScript。
- TypeScript 已成为前端开发中提升代码可维护性、类型安全性的重要工具。

# 2. 环境搭建

## 1.1. 全局安装 TypeScript

```shell
npm install typescript -g
```

使用 `tsc -v` 来检查an'zh