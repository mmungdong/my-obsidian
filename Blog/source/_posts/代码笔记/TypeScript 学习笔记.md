
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

# 1.4 环境搭建

### 1.4.1 全局安装 TypeScript

```shell
npm install typescript -g
```

使用 `tsc -v` 来检查安装成功

![blog20251228131843843.png](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20251228131843843.png?imageSlim)

> 安装了 typescript 后，我们可以使用 tsc 来将 ts 文件转化为 js 文件后再执行

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

综上所述：

传统查看 TypeScript 代码运行效果的流程存在繁琐性，需执行两步操作：

1. 编译环节：借助 `tsc` 工具，将 TypeScript 代码转换为 JavaScript 代码；
2. 运行环节：在浏览器或 Node.js 环境中，执行转换后的 JavaScript 代码。

### 1.4.2 TypeScript 运行流程简化

#### 1.4.2.1 基于 webpack 的配置

- 核心操作：通过配置 webpack，搭建本地 TypeScript 编译环境，并启动本地服务；
- 运行载体：最终代码可直接在浏览器中运行；
- 适用场景：前端项目开发（尤其是需在浏览器预览效果的场景），同时可结合 webpack 的资源处理、打包等能力，适配复杂前端工程。

搭建参考： [mp.weixin.qq.com/s/wnL1l-ERjTDykWM76l4Ajw](https://mp.weixin.qq.com/s/wnL1l-ERjTDykWM76l4Ajw)

#### 1.4.2.2 基于 ts-node 库

- 核心作用：为 TypeScript 代码提供直接的执行环境，无需手动编译为 JavaScript；
- 运行载体：可直接在 Node.js 环境中运行 TypeScript 脚本；
- 适用场景：Node.js 端的 TypeScript 脚本开发、快速调试（如工具脚本、后端服务原型开发），大幅简化开发中的编译步骤。

安装：

```shell
npm install ts-node -g
# 同时也需要安装 ts-node 的依赖包：tslib 和 @types/node
npm install tslib @types/node -g
```

使用 ts-node 执行 ts 文件：

```shell
ts-node ./01_ts语法基础/01_hello_ts.ts
```


## 1.5. ts 变量声明和类型注解

### 1.5.1. 变量声明

1. 支持的关键字：与 ES6 后规范一致，可通过`var`、`let`、`const`声明变量，示例：

```typescript
var myname: string = "why";
let myage: number = 20;
const myheight: number = 1.88;
```

2. 不推荐使用`var`：
    - tslint 明确禁止使用 `var`，建议替换为 `let` / `const`；
    - 原因：`var` 无块级作用域，易引发作用域相关问题（与 ES6 中 `var` 和 `let` 的区别一致）。

### 1.5.2. 变量声明的完整格式与类型注解

1. 核心要求：声明变量时需指定**类型注解（Type Annotation）**，TypeScript 会基于类型注解做类型检测；

2. 完整格式：

```text
var/let/const 标识符: 数据类型 = 赋值;
```

3. 示例：

```typescript
let message: string = "Hello World";
```

### 1.5.3. 类型注解的注意事项

1. 基础类型与包装类的区分：
    - `string`（小写）：TypeScript 定义的字符串基础类型，用于类型注解；
    - `String`：ECMAScript 标准的字符串包装类，不用于 TypeScript 类型标注。

2. 类型不匹配的报错：若赋值类型与类型注解不一致，TypeScript 会抛出类型错误，例如：

```typescript
let message1: string;
message1 = 123; // 报错：Type 'number' is not assignable to type 'string'
```

## 1.6. 类型推导

TypeScript 具备 **类型推导（类型推断）** 特性：开发中无需为每个变量显式声明类型注解，TS 会自动根据代码逻辑推断变量的类型，既简化代码，又保持类型检测的严格性。

在开发中，我们有时候为了方便没有必要在声明每一个变量时都写上对应的数据类型，可以借助 ts 的类型推导来约束。

推导规则：变量的类型由**第一次赋值的内容类型**决定：TS 会在变量首次赋值时，基于赋值内容的类型，自动确定变量的类型。

- 示例：声明变量时不写类型注解，直接赋值
```typescript
let message = "Hello World"; // TS自动推断message为string类型
```

- 类型检测：若后续给该变量赋值非推导类型的值，TS 会抛出类型错误，例如：
```typescript
message = 123; // 报错：Type 'number' is not assignable to type 'string'
```

# 2. TS 数据类型

## 2.1. 数组 Array

- `类型[]`： 表示该数组为对应类型的元素集合，例如`string[]`代表 “字符串类型的数组”。
- `Array<类型>`： 是泛型形式的数组类型注解，例如`Array<number>`代表 “数字类型的数组”。

```typescript
// 写法1：string[]类型，数组元素需为字符串
let names: string[] = ["abc", "cba", "nba"]
names.push("aaa") // 合法：添加字符串元素
// names.push(123) // 非法：无法向string数组添加数字元素

// 写法2：Array<number>类型，数组元素需为数字
let nums: Array<number> = [123, 321, 111]
```

注意：真实开发中，数组应**存放相同类型的元素**，避免混合不同类型（TypeScript 的类型检测会限制不同类型元素的混入）。

## 2.2. Object

- **对象字面量类型**（适合临时使用）：
```typescript
let info: { name: string; age: number; height: number } = {
  name: "why",
  age: 18,
  height: 1.88
}
```
    
- **接口（interface）**（适合类型复用，更常用）：
```typescript
interface Info {
  name: string;
  age: number;
  height: number;
}

let info: Info = {
  name: "why",
  age: 18,
  height: 1.88
}

console.log(info.name)
    ```

通过`对象.属性名`的方式访问对象属性（如图片中的`info.name`、`info.age`）。

TypeScript 会检测属性的合法性：若访问对象**不存在的属性**，会抛出类型错误（例如`info.gender`会报错，因为`info`类型中无`gender`属性）。