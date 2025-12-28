
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

使用 `tsc -v` 来检查安装成功

![blog20251228131843843.png](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20251228131843843.png?imageSlim)


编写 ts 代码：

TypeScript 中字符串类型说明

- `string`：TypeScript 里定义标识符时使用的**字符串类型**（是 TS 的基础类型）
- `String`：JavaScript 中的**字符串包装类**（不推荐在 TS 中直接用于类型标注）

```typescript
// 定义string类型的变量并赋值

let message: string = "Hello World"

// 可以重新赋值为其他字符串

message = "Hello TypeScript"

// ❌ 错误：不能赋值为非string类型（比如布尔值true）

// message = true

  

console.log(message) // 输出：Hello TypeScript
```

使用 tsc 命令将 ts 文件转为 js 文件

![blog20251228133448969.png](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20251228133448969.png?imageSlim)

转换后的结果
```js
// 定义string类型的变量并赋值

var message = "Hello World";

// 可以重新赋值为其他字符串

message = "Hello TypeScript";

// ❌ 错误：不能赋值为非string类型（比如布尔值true）

// message = true

console.log(message); // 输出：Hello TypeScript
```

再使用转换后的 js 文件：

```html
<!DOCTYPE html>

<html lang="en">

<head>

  <meta charset="UTF-8">

  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <title>Document</title>

</head>

<body>

  <script src="./01_hello_ts.js"></script>

</body>

</html>
```

