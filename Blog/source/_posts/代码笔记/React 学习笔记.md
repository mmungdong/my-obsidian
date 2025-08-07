---
categories:
  - 代码笔记
title: React 学习笔记
date: 2025-08-08 00:12:50
tags:
  - react
  - 前端
archive: true
hide: false
---
# 1. 初始化 React 项目

## 1. **React 脚手架 create-react-app 初始化项目**

### 1. node 安装

下载地址：[https://nodejs.org/en/download](https://nodejs.org/en/download)

版本选择：最新版带有LTS（稳定版）的。

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802155412382.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802155412382.png)

  

node下载好之后会自动带一个npm的包管理工具。

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802155450481.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802155450481.png)

  

### 2. React脚手架 create-react-app

  

1. **下载脚手架**

npm 下载react脚手架

`npm install create-react-app -g`

  

查看react脚手架版本

`create-react-app --version`

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802155749281.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802155749281.png)

  

1. **通过脚手架创建项目**

create-react-app 【项目名】，项目名不可以包含大写字母

这里创建过慢需要设置下淘宝源：

```Shell
npm config set registry https://registry.npmmirror.com
```

  

`create-react-app learn_react_scaffold`

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802155856043.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802155856043.png)

  

1. **项目启动**

通过运行`npm run start`运行项目。

react-scripts 命令集成了webpack的打包方式。

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802155929953.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802155929953.png)

  

1. **项目目录结构**

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802160014826.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802160014826.png)

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802160035630.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802160035630.png)

  

# 2. 组件化开发

## 1. React组件划分

- 根据组件的定义方式，划分为**函数组件**和**类组件**。
- 根据组件内部是否有状体需要维护，划分为**无状态组件**和**有状态组件**。
- 根据组件的不同职责，划分为**展示型组件**和**容器型组件。**
- **函数组件，无状态组件和展示型组件**主要关注ui展示。
- **类组件，有状态组件和容器型组件主要关注数据逻辑。**

## 2. 类组件要求

- **组件的名称必须是大写字母开头**。（无论是类组件还是函数式组件）
- 类组件需要继承自 **React.Component**。
- 类组件必须实现 **render** 函数。

  

## 3. Helloworld（类组件的封装细节）

### 1. install

```Shell
# 切换至淘宝源
npm config set registry https://registry.npmmirror.com

# 使用 create-react-app 创建 react 项目
npx create-react-app 03-react-component
```

  

### 2. 项目结构

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802160546687.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802160546687.png)

  

```JavaScript
import ReactDom from "react-dom/client"
import App from "./App"

// 编写react代码，并进行渲染
const root = ReactDom.createRoot(document.getElementById("root"))
root.render(<App />)
```

  

```JavaScript
import react from "react"
import HelloWorld from "./components/HelloWorld"

class App extends react.Component {
  render() {
    return (
      <div>
        <h1>React App</h1>
        <HelloWorld />
      </div>
    )
  }
 }

 export default App
```

  

```JavaScript
import react from "react"

class HelloWorld extends react.Component {

    // 构造方法
    constructor() {
        super()
        this.state = {
            message: "hello world"
        }
    }

    // 渲染
    render() {
        const { message } = this.state

        return (
        <div>
            <h2>{message}</h2>
        </div>
        )
    }
 }

 export default HelloWorld
```

  

### 3. react-scripts 命令对应的 webpack 配置

  

webpack配置默认隐藏：

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802160828040.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802160828040.png)

  

- 弹出对应配置：

执行

```Shell
npm run eject
```

  

弹出webpack配置

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802160954820.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802160954820.png)

  

文件变动：

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802161033090.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802161033090.png)

  

### 4. render 函数的返回值

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802161107628.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802161107628.png)

  

## 4. **函数式组件的封装**

  

```JavaScript
export default function App() {

    // 函数式组件和 render 的返回值一样
    return (
      <div>
        <h1>React functional App</h1>
      </div>
    )
}
```

  

## 5. **类组件的生命周期**

  

> 参考：[Component – React 中文文档](https://zh-hans.react.dev/reference/react/Component)

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802161440462.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802161440462.png)

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802161502753.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802161502753.png)

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802161529169.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802161529169.png)

  

### **1. 常用的生命周期代码示例**

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802161715426.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802161715426.png)

  

```JavaScript
import React from "react"

import HelloWorld from "./HelloWorld"

class App extends React.Component {

  constructor() {
    super()
    this.state = {
      isShowHW: true
    }
  }

  switchHWShow() {
    this.setState({
      isShowHW: !this.state.isShowHW
    })
  }

  render() {
    const { isShowHW } = this.state

    return (
      <div>
        <h1>React App</h1>
        <button onClick={e => this.switchHWShow()}>切换</button>
        { isShowHW && <HelloWorld />}
      </div>
    )
  }
}

export default App
```

  

```JavaScript
import React from "react";

class HelloWorld extends React.Component {

    // 1. 构造方法
    constructor() {
        console.log("hello world constructor ~")

        super()
        this.state = {
            message: "hello world"
        }
    }

    changeText() {
        this.setState({
            message: "你好👋，李银河！"
        })
    }

    // 2. 执行render函数
    render() {
        console.log("hello world render ~")
        const { message } = this.state

        return (
            <div>
                <h1>{ message }</h1>
                <button onClick={ e => this.changeText() }>点击修改文本</button>
            </div>
        )
    }

    // 3. 组件被挂载到DOM中时，会执行该生命周期函数
    componentDidMount() {
        console.log("hello world componentDidMount ~")
    }

    // 4. 组件的DOM更新完成：DOM发生更新
    componentDidUpdate(prevProps, prevState, snapshot) {
        console.log("hello world componentDidUpdate: ", prevProps, prevState, snapshot)
    }

    // 5. 组件即将从DOM中卸载时：从DOM中移除
    componentWillUnmount() {
        console.log("hello world componentWillUnmount ~")
    }

    // 不常用到的生命周期
    // 判断是否需要重新渲染界面（是否需要重新执行render函数）
    shouldComponentUpdate() {
        console.log("hello world shouldComponentUpdate ~")
        return true
    }

    // 在更新前保存一些数据
    getSnapshotBeforeUpdate() {
        console.log("hello world getSnapshotBeforeUpdate ~")
        return {
            scrollPosition: 1000,
            prevMessage: this.state.message
        }
    }
}

export default HelloWorld
```

  

初次页面：

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802161840774.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802161840774.png)

  

点击修改文本：

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802161906731.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802161906731.png)

  

点击切换：

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802161947474.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802161947474.png)

  

### **2. 常用的生命周期详解**

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802162031720.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802162031720.png)

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802162105619.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802162105619.png)

  

### **3. 不常用的生命周期函数**

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802162229762.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802162229762.png)

  

## 6. 组件通信

### 1. 父传子

- 通过props传递数据

  

### 2. 父传子代码示例

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802162337980.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802162337980.png)

  

```JavaScript
import React, { Component } from 'react'

import Father from './c-cpns/Father'

class App extends Component {
  render() {
    return (
      <div>
        <Father />
      </div>
    )
  }
}

export default App
```

  

```JavaScript
import React, { Component } from 'react'
import Children from './Children'

class Father extends Component {

  constructor() {
    super()
    this.state = {
      title: '父组件',
      message: '我是父组件的数据'
    }
  }

  render() {
    const { message, title } = this.state

    return (
      <div>
        <p>我是父组件</p>
        <Children message={message} title={title} />
      </div>
    )
  }
}

export default Father
```

  

```JavaScript
import React, { Component } from 'react'

class Children extends Component {

  constructor(props) {
    console.log(props)
    super(props)
  }

  render() {
    const { message, title } = this.props

    return (
      <div>
        <span>我是子组件</span>
        <br/>
        <span>父组件中的tilte：{title}</span>
        <br/>
        <span>父组件中的message：{message}</span>
      </div>
    )
  }
}

export default Children
```

  

效果：

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802162500799.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802162500799.png)

  

### **3. 类型校验**

  

> 参考：[使用 PropTypes 进行类型检查 – React (reactjs.org)](https://zh-hans.legacy.reactjs.org/docs/typechecking-with-proptypes.html)

  

### **4. 子传父**

  
• 通过从父组件中传递过来的函数将参数传递进去实现通信

  

**子传父代码示例:**

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802162656554.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802162656554.png)

  

```JavaScript
import React, { Component } from 'react'
import AddCounter from './AddCounter'

class App extends Component {
  constructor() {
    super()

    this.state = {
      counter: 100
    }
  }

  render() {
    const { counter } = this.state

    return (
      <div>
        counter: { counter }
        <AddCounter addCounter={(number)=> this.setState({ counter: counter + number })} />
      </div>
    )
  }
}

export default App
```

  

```JavaScript
import React, { Component } from 'react'

class AddCounter extends Component {

  addCounter(count) {
    // 组件通信-子传父
    // 组件通信直接从props中获取父组件传递过来的函数来通信
    this.props.addCounter(count)
  }

  render() {
    return (
      <div>
        <button onClick={() => this.addCounter(1)}>+1</button>
        <button onClick={() => this.addCounter(5)}>+5</button>
        <button onClick={() => this.addCounter(10)}>+10</button>
      </div>
    )
  }
}

export default AddCounter
```

  

## **7. 组件的插槽实现**

### **7.1. 实现方式一（不推荐）**

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802162859283.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802162859283.png)

  

```JavaScript
import React, { Component } from 'react'

import NavBar from './nav-bar'

export class App extends Component {
  render() {
    return (
      <div>
        <NavBar>
          <span>插槽1</span>
          <span>插槽2</span>
          <span>插槽3</span>
        </NavBar>
      </div>
    )
  }
}

export default App
```

  

```JavaScript
import React, { Component } from 'react'
import PropTypes from 'prop-types';

export class NavBar extends Component {

  render() {
    // 实现方式一：从 this.props.children 中取得传递的组件/值
    const { children } = this.props

    return (
      <div>
        <h2>1. 可以通过数组的形式取得子组件</h2>
        <span>{children[0]}</span>
        <br />
        <span>{children[1]}</span>
        <br />
        <span>{children[2]}</span>

        <h2>2. 可以通过遍历的形式取得子组件</h2>
        {React.Children.map(children, (child, index) => {
          return (
            <div key={index}>{child}</div>
          )
        })}

        <h2>3. 直接获取 children</h2>
        <span>{children}</span>
      </div>
    )
  }
}

// 指定传入插槽的传入类型为数组，如果不符合，会在控制台报警告，
// 出于性能方面的考虑，propTypes 仅在开发模式下进行检查
NavBar.propTypes = {
  children: PropTypes.array,
}

export default NavBar
```

  

### **2. 实现方式二（推荐）**

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802163006211.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802163006211.png)

  

```JavaScript
import React, { Component } from 'react'

import NavBarTwo from './nav-bar-two'

export class App extends Component {
  render() {
    return (
      <div>
        <NavBarTwo firstSlot={<div>我是插槽1</div>} secondSlot={<div>我是插槽2</div>} thirdSlot={<div>我是插槽3</div>} />
      </div>
    )
  }
}

export default App
```

  

```JavaScript
import React, { Component } from 'react'

export class NavBarTwo extends Component {

  render() {
    const { firstSlot, secondSlot, thirdSlot } = this.props

    return (
      <div>
        <div>{firstSlot}</div>
        <div>{secondSlot}</div>
        <div>{thirdSlot}</div>
      </div>
    )
  }
}

export default NavBarTwo
```

  

### **3. 作用域插槽**

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802163145744.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802163145744.png)

  

```JavaScript
import React, { Component } from 'react'
import NavBar from './nav-bar'

export class App extends Component {

  selectChange(item) {
    console.log(item)
    switch (item) {
      case 'home':
        return <div style={{color: 'red'}}>首页</div>
      case 'about':
        return <div style={{color: 'skyblue'}}>关于</div>
      case 'contact':
        return <div style={{color: 'black'}}>联系我们</div>
      default:
        return <div>首页</div>
    }
  }

  render() {
    return (
      <div>
        <NavBar slot={item => this.selectChange(item)} />
      </div>
    )
  }
}

export default App
```

  

```JavaScript
import React, { Component } from 'react'

export class NavBar extends Component {
  render() {
    const { slot } = this.props

    return (
      <div>{slot("about")}</div>
    )
  }
}

export default NavBar
```

  

## **8. 非父子组件通信 - Context**

### **1. 作用**

  
• 非父子组件间的数据共享

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802163314731.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802163314731.png)

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802163330667.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802163330667.png)

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802163351806.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802163351806.png)

  

### **2. 类组件中使用 Context**

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802163442294.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802163442294.png)

  

```JavaScript
import { createContext } from "react";

// 1. 先声明一个context
const ThemeContext = createContext()

export default ThemeContext
```

  

```JavaScript
import React, { Component } from 'react'
import ThemeContext from './context/theme-context'
import Home from './Home'

export class App extends Component {
  constructor() {
    super()
    this.state = {
      info: { color: 'red', level: '高级' }
    }
  }

  render() {
    const { info } = this.state

    return (
      <>
        <div>App</div>
        // 2. 在context中存值
        <ThemeContext.Provider value={{color: "red", size: "30"}}>
          <Home {...info}></Home>
        </ThemeContext.Provider>
      </>
    )
  }
}

export default App
```

  

```JavaScript
import React, { Component } from 'react'
import ThemeContext from './context/theme-context'

export class Home extends Component {

  constructor() {
    super()
    this.state = {
      title: '我是Home组件'
    }
  }

  render() {
    console.log(this.context)

    const { title } = this.state

    const {color, size} = this.context

    return (
      <div>
        { title }
        <span>下面是 ThemeContext 中的数据：</span>
        {/* 第一种写法： */}
        <ThemeContext.Consumer>
          {
            (ctx) => {
              return (
                <div>
                  <span>color: { ctx.color }</span>
                  <br/>
                  <span>size: { ctx.size }</span>
                </div>
              )
            }
          }
        </ThemeContext.Consumer>
        {/* 第二种写法： */}
        <div>
          <span>第二种写法：</span>
          <br/>
          <span>color: {color}</span>
          <br/>
          <span>size: {size}</span>
        </div>
      </div>
    )
  }
}

Home.contextType = ThemeContext

export default Home
```

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802163548963.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802163548963.png)

  

### **3. 函数式组件获取 context 中的数据**

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802163547853.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802163547853.png)

  

```JavaScript
import React, { Component } from 'react'

import ThemeContext from './context/theme-context'
import Home from './Home'
import HomeBanner from './HomeBanner'

export class App extends Component {
  constructor() {
    super()
    this.state = {
      info: { color: 'red', level: '高级' }
    }
  }

  render() {
    const { info } = this.state

    return (
      <>
        <div>App</div>
        <ThemeContext.Provider value={{color: "red", size: "30"}}>
          <Home {...info}></Home>
          <HomeBanner/>
        </ThemeContext.Provider>
      </>
    )
  }
}

export default App
```

  

```JavaScript
import React from 'react'

import ThemeContext from './context/theme-context'

const HomeBanner = () => {
  return (
    <>
      <div>HomeBanner 函数式组件</div>
      <ThemeContext.Consumer>
        {
          (ctx) => {
            return (
              <div>
                <span>color: { ctx.color }</span>
                <br/>
                <span>size: { ctx.size }</span>
              </div>
            )
          }
        }
      </ThemeContext.Consumer>
    </>
  )
}

export default HomeBanner
```

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802163729208.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802163729208.png)

  

### **4. context 中的默认值设置**

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802163809368.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802163809368.png)

  

```JavaScript
import { createContext } from "react";

// 1. 在创建的conntext时候设置默认值，
// 注意后面使用时 ThemeContext.Provider 中只要设置了value属性，这里设置的所有默认值都会被覆盖掉
const ThemeContext = createContext({nickname: 'coderwhy', level: '高级'})

export default ThemeContext
```

  

```JavaScript
import React, { Component } from 'react'

import ThemeContext from './context/theme-context'

export class Profile extends Component {
  render() {
    return (
      <>
      <div>Profile</div>
        <ThemeContext.Consumer>
          {
            (ctx) => {
              return (
                <div>
                  <span>nickname: { ctx.nickname }</span>
                  <br/>
                  <span>level: { ctx.level }</span>
                </div>
              )
            }
          }
        </ThemeContext.Consumer>
      </>
    )
  }
}

export default Profile
```

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802163901155.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802163901155.png)

  

### **5. 多个 context 使用**

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802163932163.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802163932163.png)

  

```JavaScript
import { createContext } from "react";

const UserContext = createContext()

export default UserContext
```

  

```JavaScript
import React, { Component } from 'react'

import ThemeContext from './context/theme-context'
import Home from './Home'
import HomeBanner from './HomeBanner'
import Profile from './Profile'
import UserContext from './context/user-context'

export class App extends Component {
  constructor() {
    super()
    this.state = {
      info: { color: 'red', level: '高级' }
    }
  }

  render() {
    const { info } = this.state

    return (
      <>
        <div>App</div>
        <ThemeContext.Provider value={{color: "red", size: "30"}}>
          // 这里多个context嵌套使用
          <UserContext.Provider value={{name: "wuyanzu", age: "18"}}>
            <Home {...info}></Home>
            <HomeBanner/>
          </UserContext.Provider>
        </ThemeContext.Provider>
        <Profile></Profile>
      </>
    )
  }
}

export default App
```

  

```JavaScript
import React, { Component } from 'react'
import ThemeContext from './context/theme-context'
import UserContext from './context/user-context'

export class Home extends Component {

  constructor() {
    super()
    this.state = {
      title: '我是Home组件'
    }
  }

  render() {
    console.log(this.context)

    const { title } = this.state

    const {color, size} = this.context

    return (
      <div>
        { title }
        <span>下面是 ThemeContext 中的数据：</span>
        {/* 第一种写法： */}
        <ThemeContext.Consumer>
          {
            (ctx) => {
              return (
                <div>
                  <span>color: { ctx.color }</span>
                  <br/>
                  <span>size: { ctx.size }</span>
                </div>
              )
            }
          }
        </ThemeContext.Consumer>
        <UserContext.Consumer>
          {
            (ctx) => {
              return (
                <div>
                  <span>name: { ctx.name }</span>
                  <br/>
                  <span>age: { ctx.age }</span>
                </div>
              )
            }
          }
        </UserContext.Consumer>
        {/* 第二种写法： */}
        <div>
          <span>第二种写法：</span>
          <br/>
          <span>color: {color}</span>
          <br/>
          <span>size: {size}</span>
        </div>
      </div>
    )
  }
}

Home.contextType = ThemeContext

export default Home
```

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802164100120.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802164100120.png)

  

## **9. 非父子组件通信 - 事件总线**

### **1. Install**

```Shell
npm install hy-event-store
```

  

### **2. demo case**

  

1. 创建 eventBus 对象

```JavaScript
import { HYEventBus } from 'hy-event-store'

const eventBus = new HYEventBus()

export default eventBus
```

  

1. 使用

```JavaScript
import React, { Component } from 'react'

import eventBus from './utils/event-bus'
import HelloOne from './component/HelloOne'
import HelloTwo from './component/HelloTwo'

export class App extends Component {

  componentDidMount() {
    // 监听事件
    eventBus.on("helloOneSendEvent", (event) => {
      console.log('我是App组件，监听到了来自HelloOne组件的事件')
      console.log(event)
    })
  }

  componentWillUnmount() {
    // 在组件销毁时，一定要记得取消监听事件，养成好习惯
    eventBus.off("helloOneSendEvent", () => {})
  }

  render() {
    return (
      <>
        <h1>App</h1>
        <HelloOne/>
        <HelloTwo/>
      </>
    )
  }
}

export default App
```

  

```JavaScript
import React, { Component } from 'react'
import eventBus from '../utils/event-bus'

export class HelloOne extends Component {

  constructor() {
    super()
    this.state = {
      event: '我是来自HelloOne组件的一个事件'
    }
  }

  // 发送事件
  eventSend = () => {
    eventBus.emit('helloOneSendEvent', this.state.event)
  }

  render() {
    return (
      <>
        <h2>HelloOne</h2>
        <button onClick={this.eventSend}>click</button>
      </>
    )
  }
}

export default HelloOne
```

  

```JavaScript
import React, { Component } from 'react'
import eventBus from '../utils/event-bus'

export class HelloTwo extends Component {

  componentDidMount() {
    // 监听事件
    eventBus.on("helloOneSendEvent", (event) => {
      console.log('我是HelloTwo组件，监听到了来自HelloOne组件的事件')
      console.log(event)
    })
  }

  componentWillUnmount() {
    // 在组件销毁时，一定要记得取消监听事件，养成好习惯
    eventBus.off("helloOneSendEvent", () => {})
  }

  render() {
    return (
      <h2>HelloTwo</h2>
    )
  }
}

export default HelloTwo
```

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802164528890.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802164528890.png)

  

### **3. 存在this绑定问题**

  

**1. 问题复现**

```JavaScript
import React, { Component } from 'react'
import eventBus from '../utils/event-bus'

export class HelloTwo extends Component {

  componentDidMount() {
    // 监听事件
    eventBus.on("helloOneSendEvent", this.handleHelloOneEvent)
  }

  handleHelloOneEvent(event) {
    console.log('我是HelloTwo组件，监听到了来自HelloOne组件的事件')
    console.log(event)
    // 这里打印下this，是 undefined，证明我们在这里处理时无法通过this那倒state
    // 也就无法通过this.setState来更新状态
    console.log(this)
  }

  componentWillUnmount() {
    // 在组件销毁时，一定要记得取消监听事件，养成好习惯
    eventBus.off("helloOneSendEvent", () => {})
  }

  render() {
    return (
      <h2>HelloTwo</h2>
    )
  }
}

export default HelloTwo
```

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802164644764.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802164644764.png)

  

**2. 修复**

  

方法一和方法二任选一个就可以:

```JavaScript
import React, { Component } from 'react'
import eventBus from '../utils/event-bus'

export class HelloTwo extends Component {

  componentDidMount() {
    // 监听事件
    // 修复方法一：通过bind绑定this，才能通过this.setState来更新状态
    eventBus.on("helloOneSendEvent", this.handleHelloOneEvent)
  }

  // handleHelloOneEvent(event) {
  //   console.log('我是HelloTwo组件，监听到了来自HelloOne组件的事件')
  //   console.log(event)
  //   // 这里打印下this，是 undefined，证明我们在这里处理时无法通过this那倒state
  //   // 也就无法通过this.setState来更新状态
  //   console.log(this)
  // }

  // 修复方法二：通过箭头函数，通过箭头函数的this来获取this，从而通过this来更新状态
  handleHelloOneEvent = (event) => {
    console.log('我是HelloTwo组件，监听到了来自HelloOne组件的事件')
    console.log(event)
    console.log(this)
  }

  componentWillUnmount() {
    // 在组件销毁时，一定要记得取消监听事件，养成好习惯
    eventBus.off("helloOneSendEvent", () => {})
  }

  render() {
    return (
      <h2>HelloTwo</h2>
    )
  }
}

export default HelloTwo
```

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802164807534.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802164807534.png)

  

## **10. setState 用法及原理参考**

> [React(四) 事件总线，setState的原理，PureComponent优化React性能,ref获取类组件与函数组件_react事件总线-CSDN博客](https://blog.csdn.net/qq_44285582/article/details/142868211)

  

### **1. setState的三种用法**

  

  

- **用法一：基本使用**

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802165012910.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802165012910.png)

  

```JavaScript
import React, { Component } from 'react'

export class App extends Component {

  constructor() {
    super()
    this.state = {
      message: "Hello React"
    }
  }

  changeMsg() {

    // 用法一：直接修改state中的数据，这个是个异步动作，
    //        等所有的state的值修改好后才会去走render()函数
    //        底层其实是用了Object.assign(this.state,setState的新对象)，把两个对象做了合并。
    this.setState({
      message: "你好啊，李银河！"
    })
  }

  render() {
    const { message } = this.state

    return (
      <>
        <h2>{message}</h2>
        <button onClick={e => this.changeMsg()}>点击我</button>
      </>
    )
  }
}

export default App
```

  

```JavaScript
import ReactDom from "react-dom/client"
import App from "./10_setState的三种用法/App"

// 编写react代码，并进行渲染
const root = ReactDom.createRoot(document.getElementById("root"))
root.render(<App />)
```

  

- **用法二：传入回调函数**

> [!important] 好处一: 可以在回调函数中编写对新state处理的逻辑<br>好处二: 当前的回调函数会将之前的state和props传递进来

  

```JavaScript
import React, { Component } from 'react'

export class App extends Component {

  constructor() {
    super()
    this.state = {
      message: "Hello React"
    }
  }

  // changeMsg() {

  //   // 用法一：直接修改state中的数据，这个是个异步动作，
  //   //        等所有的state的值修改好后才会去走render()函数
  //   //        底层其实是用了Object.assign(this.state,setState的新对象)，把两个对象做了合并。
  //   this.setState({
  //     message: "你好啊，李银河！"
  //   })
  // }

  changeMsg() {
    // 用法二：可以传入一个函数，这个函数会接收到state和props两个参数，
    //        这个函数的返回值会作为state的新值
    this.setState((state, props) => {
      console.log(state.message, props)

      return {
        message: "你好啊，李银河！"
      }
    })
  }

  render() {
    const { message } = this.state

    return (
      <>
        <h2>{message}</h2>
        <button onClick={e => this.changeMsg()}>点击我</button>
      </>
    )
  }
}

export default App
```

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802165239253.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802165239253.png)

  

- **用法三：setState 是一个异步调用，传入 callback 来使用最新的 state 的值处理逻辑**

```JavaScript
import React, { Component } from 'react'

export class App extends Component {

  constructor() {
    super()
    this.state = {
      message: "Hello React"
    }
  }

  // changeMsg() {

  //   // 用法一：直接修改state中的数据，这个是个异步动作，
  //   //        等所有的state的值修改好后才会去走render()函数
  //   //        底层其实是用了Object.assign(this.state,setState的新对象)，把两个对象做了合并。
  //   this.setState({
  //     message: "你好啊，李银河！"
  //   })
  // }

  // changeMsg() {
  //   // 用法二：可以传入一个函数，这个函数会接收到state和props两个参数，
  //   //        这个函数的返回值会作为state的新值
  //   this.setState((state, props) => {
  //     console.log(state.message, props)

  //     return {
  //       message: "你好啊，李银河！"
  //     }
  //   })
  // }

  changeMsg() {
    // 用法三：可以传入一个函数，这个函数的返回值会作为state的新值，并且可以传入一个回调函数，
    //        这个回调函数会在state更新完毕后执行，可以获取到最新的state
    this.setState({ message: "你好啊，李银河！"}, () => {
      // 回调函数，在state更新完毕后执行，可以获取到最新的state
      console.log(this.state.message)
    })
  }

  render() {
    const { message } = this.state

    return (
      <>
        <h2>{message}</h2>
        <button onClick={e => this.changeMsg()}>点击我</button>
      </>
    )
  }
}

export default App
```

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802165357084.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802165357084.png)

  

### **2. react 为什么设计成异步的？**

> [https://github.com/facebook/react/issues/11527](https://github.com/facebook/react/issues/11527)

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802165500725.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802165500725.png)

  

📌 **注意事项一：setState是批量更新的，不是每次调用都会更新**

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802165602536.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802165602536.png)

  
• 错误使用

  

```JavaScript
import React, { Component } from 'react'

export class App extends Component {

  constructor() {
    super()
    this.state = {
      counter: 0
    }
  }

  increament() {
    // 这里的 counter 执行完方法后结果为1，
    // 因为setState是异步调用，每次获取的counter值都为0
    this.setState({
      counter: this.state.counter + 1
    })
    this.setState({
      counter: this.state.counter + 1
    })
    this.setState({
      counter: this.state.counter + 1
    })
  }

  render() {
    console.log("render ~")

    const { counter } = this.state

    return (
      <>
        <h2>{counter}</h2>
        <button onClick={e => this.increament()}>点击我</button>
      </>
    )
  }
}

export default App
```

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802165650009.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802165650009.png)

  

• 我们要想每次都拿到最新的 state 的值使用，应该用**回调函数**的方式:

  

```JavaScript
import React, { Component } from 'react'

export class App extends Component {

  constructor() {
    super()
    this.state = {
      counter: 0
    }
  }

  increment() {
    this.setState((state) => {
      return {
        counter: state.counter + 1
      }
    })
    this.setState((state) => {
      return {
        counter: state.counter + 1
      }
    })
    this.setState((state) => {
      return {
        counter: state.counter + 1
      }
    })
  }

  render() {
    console.log("render ~")

    const { counter } = this.state

    return (
      <>
        <h2>{counter}</h2>
        <button onClick={e => this.increment()}>点击我</button>
      </>
    )
  }
}

export default App
```

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802165743166.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802165743166.png)

  

📌 **注意事项二：setState 在 React 18 之前是同步的，React 18 之后是异步的**

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802165836696.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802165836696.png)

  

💡 **将setState异步变为同步**

1. 使用callback回调参数
2. 使用react-dom中的flushSync

```JavaScript
import React, { Component } from 'react'
import { flushSync } from 'react-dom'

export class App extends Component {

  constructor() {
    super()
    this.state = {
      counter: 0
    }
  }

  increment() {
    // 将setState的回调函数写到flushSync中，同步更新state后在执行后续代码
    flushSync(() => {
      this.setState({
        counter: this.state.counter + 1
      })
    })
    // 这里执行完flushSync中的方法并更新后才会执行这里的代码
    console.log(this.state.counter)
  }

  render() {
    console.log("render ~")

    const { counter } = this.state

    return (
      <>
        <h2>{counter}</h2>
        <button onClick={e => this.increment()}>点击我</button>
      </>
    )
  }
}

export default App
```

  

## **11. React 性能优化**

  

### **1. diff 算法和 key 的作用**

> [https://juejin.cn/post/6967626390380216334](https://juejin.cn/post/6967626390380216334)

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802170118844.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802170118844.png)

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802170134236.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802170134236.png)

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802170148798.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802170148798.png)

  

### **2. render 函数优化**

1. 问题

问题说明：在每个组件中包含的子组件，只要父组件的一个 state 或者 props 值被修改，该组件以及子组件都会重新执行 render 函数

  

👨🏻‍💻 code demo：

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802170400839.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802170400839.png)

  

```JavaScript
import React, { Component } from 'react'
import Home from './Home'
import Recommend from './Recommend'

export class App extends Component {

  constructor() {
    super()
    this.state = {
      msg: "Hello React",
      counter: 0
    }
  }

  changeMsg() {
    this.setState({
      msg: "你好啊，李银河！"
    })
  }

  increment() {
    this.setState({
      counter: this.state.counter + 1
    })
  }

  render() {
    console.log("App render ~")

    const { msg, counter } = this.state

    return (
      <>
        <div>App</div>
        <h2>{msg} - {counter}</h2>
        <button onClick={e => this.changeMsg()}>修改msg</button>
        <button onClick={e => this.increment()}>+1</button>

        <Home />
        <Recommend />
      </>
      
    )
  }
}

export default App
```

  

```JavaScript
import React, { Component } from 'react'

export class Home extends Component {
  render() {
    console.log("Home render ~")

    return (
      <div>Home</div>
    )
  }
}

export default Home
```

  

```JavaScript
import React, { Component } from 'react'

export class Recommend extends Component {
  render() {
    console.log("Recommend render ~")

    return (
      <div>Recommend</div>
    )
  }
}

export default Recommend
```

  

```JavaScript
import ReactDom from "react-dom/client"
import App from "./11_render函数的优化/App"

// 编写react代码，并进行渲染
const root = ReactDom.createRoot(document.getElementById("root"))
root.render(<App />)
```

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802170559574.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802170559574.png)

  

💡 **解法一：使用 shouldComponentUpdate 来阻止更新（SCU优化）**

```JavaScript
import React, { Component } from 'react'

export class Home extends Component {

  shouldComponentUpdate() {
    // 在这里执行一些判断，返回false，阻止更新，返回true，则执行该组件的render函数
    return false
  }

  render() {
    console.log("Home render ~")

    return (
      <div>Home</div>
    )
  }
}

export default Home
```

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802170730881.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802170730881.png)

  

• 同样，当我们的 state 的值没有被修改时，也可以这样来优化

```JavaScript
import React, { Component } from 'react'
import Home from './Home'
import Recommend from './Recommend'

export class App extends Component {

  constructor() {
    super()
    this.state = {
      msg: "Hello React",
      counter: 0
    }
  }

  changeMsg() {
    this.setState({
      // msg: "你好啊，李银河！"
      msg: "Hello React"
    })
  }

  increment() {
    this.setState({
      counter: this.state.counter + 1
    })
  }

  shouldComponentUpdate(nextProps, nextState) {
    // 这里我们可以判断state或者props值在变化后需不需要重新执行render函数
    if (this.state === nextState) {
      return true
    }
    return false
  }

  render() {
    console.log("App render ~")

    const { msg, counter } = this.state

    return (
      <>
        <div>App</div>
        <h2>{msg} - {counter}</h2>
        <button onClick={e => this.changeMsg()}>修改msg</button>
        <button onClick={e => this.increment()}>+1</button>

        <Home />
        <Recommend />
      </>
      
    )
  }
}

export default App
```

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802170834648.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802170834648.png)

  

💡 **解法二：（推荐）使用 PureComponent 和 memo 来进行 render 优化**

  

- 类组件继承 PureComponent， 函数式组件用memo包裹，目的还是当 state 或者 props 的值不发生变化时不进行 render 渲染

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802170948807.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802170948807.png)

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802171001997.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802171001997.png)

  

👨🏻‍💻 code demo：

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802171031792.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802171031792.png)

  

```JavaScript
import React, { PureComponent } from 'react'
import Home from './Home'
import Recommend from './Recommend'
import Profile from './Profile'

export class App extends PureComponent {

  constructor() {
    super()
    this.state = {
      msg: "Hello React",
      counter: 0
    }
  }

  changeMsg() {
    this.setState({
      // msg: "你好啊，李银河！"
      msg: "Hello React"
    })
  }

  increment() {
    this.setState({
      counter: this.state.counter + 1
    })
  }

  render() {
    console.log("App render ~")

    const { msg, counter } = this.state

    return (
      <>
        <div>App</div>
        <h2>{msg} - {counter}</h2>
        <button onClick={e => this.changeMsg()}>修改msg</button>
        <button onClick={e => this.increment()}>+1</button>

        <Home />
        <Recommend />
        <Profile />
      </>
      
    )
  }
}

export default App
```

  

```JavaScript
import React, { PureComponent } from 'react'

export class Home extends PureComponent {

  render() {
    console.log("Home render ~")

    return (
      <div>Home</div>
    )
  }
}

export default Home
```

  

```JavaScript
import React, { memo } from 'react'

const Profile = memo(() => {
  console.log("Profile render ~")
  return (
    <div>Profile</div>
  )
})

export default Profile
```

  

```JavaScript
import React, { PureComponent } from 'react'

export class Recommend extends PureComponent {
  render() {
    console.log("Recommend render ~")

    return (
      <div>Recommend</div>
    )
  }
}

export default Recommend
```

  

```JavaScript
import ReactDom from "react-dom/client"
import App from "./11_render函数的优化/App"

// 编写react代码，并进行渲染
const root = ReactDom.createRoot(document.getElementById("root"))
root.render(<App />)
```

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802171238980.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802171238980.png)

  

### **3. 数据不可变的力量**

> 参考：
> 
> - [https://zhuanlan.zhihu.com/p/24926838](https://zhuanlan.zhihu.com/p/24926838)
> - [https://segmentfault.com/a/1190000021162454](https://segmentfault.com/a/1190000021162454)

  
• 我们通常写的类组件或者函数式组件都继承自 PureComponent 或者被 memo 所包裹，这样在 setState 或者 useState 的时候，都需要保证新对象和旧对象的内存地址不一样，才可以触发页面渲染。

  

👨🏻‍💻 code case：

```JavaScript
import React, { PureComponent } from 'react'

export class App extends PureComponent {

  constructor() {
    super()
    this.state = {
      books: [
        {
          name: "计算机网络入门",
          price: 23,
          count: 10,
        },
        {
          name: "你不知道的javascript",
          price: 67,
          count: 7,
        },
        {
          name: "算法图解",
          price: 48,
          count: 3,
        }
      ]
    }
  }

  addBook() {
    var newBook = {
        name: "Golang高级程序语言设计",
        price: 48,
        count: 3,
    }

    // 不要这样直接修改state下面的值，否则不会触发页面渲染

    // 1. 直接修改原有的 state，再重新设着一遍 state
    //    在 PureComponent 是不能引起重新渲染的
    // this.state.books.push(newBook)
    // this.setState({
    //   books: this.state.books
    // })

    // 需要像下面这样在 setState 设置的时候赋值一个和之前不一样的对象，才可以触发页面渲染
    // 赋值一份新的books，在新的books 中做修改，可以造成重新渲染
    const books = [...this.state.books, newBook]
    this.setState({
      books: books
    })
  }

  render() {
    const { books } = this.state

    return (
      <>
        <div>App Books</div>
        <ul>
          {
            books.map((item, index) => {
              return (
                <li key={index}>name: {item.name}  price: {item.price} count: {item.count}</li>
              )
            })
          }
        </ul>
        <button onClick={e => {this.addBook()}}>添加一本书</button>
      </>
    )
  }
}

export default App
```

  

case: 修改books的count

```JavaScript
import React, { PureComponent } from 'react'

export class App extends PureComponent {

  constructor() {
    super()
    this.state = {
      books: [
        {
          name: "计算机网络入门",
          price: 23,
          count: 10,
        },
        {
          name: "你不知道的javascript",
          price: 67,
          count: 7,
        },
        {
          name: "算法图解",
          price: 48,
          count: 3,
        }
      ]
    }
  }

  addBook() {
    var newBook = {
        name: "Golang高级程序语言设计",
        price: 48,
        count: 3,
    }

    // 不要这样直接修改state下面的值，否则不会触发页面渲染

    // 1. 直接修改原有的 state，再重新设着一遍 state
    //    在 PureComponent 是不能引起重新渲染的
    // this.state.books.push(newBook)
    // this.setState({
    //   books: this.state.books
    // })

    // 需要像下面这样在 setState 设置的时候赋值一个和之前不一样的对象，才可以触发页面渲染
    // 赋值一份新的books，在新的books 中做修改，可以造成重新渲染
    const books = [...this.state.books, newBook]
    this.setState({
      books: books
    })
  }

  addCount(index) {
    const books = [...this.state.books]
    books[index].count++
    this.setState({ books: books })
  }

  render() {
    const { books } = this.state

    return (
      <>
        <div>App Books</div>
        <ul>
          {
            books.map((item, index) => {
              return (
                <li key={index}>
                  name: {item.name}  price: {item.price} count: {item.count}  <button onClick={e => { this.addCount(index) }}> +1 </button>
                </li>
              )
            })
          }
        </ul>
        <button onClick={e => {this.addBook()}}>添加一本书</button>
      </>
    )
  }
}

export default App
```

  

## **12. ref 获取原生 dom 的三种方式**

> • React 可以使用 ref 来获取原生 dom 对象

  

👨🏻‍💻 code case：

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802171642428.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802171642428.png)

  

```JavaScript
import React, { createRef, PureComponent } from 'react'

export class App extends PureComponent {

  constructor() {
    super()

    this.state = ({

    })

    // 方式二中创建ref对象
    this.yuyanRef = createRef()

    // 方式三中创建ref对象
    this.dehuaRef = null
  }

  getYanzuDom() {
    // 方式一：（已废弃）直接在 React 元素上绑定一个字符串
    console.log(this.refs.yanzuRef)
  }

  getYuyanDom() {
    // 方式二：（推荐）首先在 constructor() 中创建一个ref，然后绑定到 React 元素上
    console.log(this.yuyanRef.current)
  }

  getDehuaDom() {
    // 方式三：传入一个回调函数，当页面被渲染后，回调函数被执行，元素被传入
    console.log(this.dehuaRef)
  }

  render() {
    return (
      <>
        <div>App</div>
        <h1 ref="yanzuRef">hello, yanzu wu</h1> &nbsp; <button onClick={e => this.getYanzuDom()}>get yanzu's dom</button>
        <h1 ref={this.yuyanRef}>hello, yuyan peng</h1> &nbsp; <button onClick={e => this.getYuyanDom()}>get yuyan's dom</button>
        <h1 ref={element => this.dehuaRef = element}>hello, dehua liu</h1> &nbsp; <button onClick={e => this.getDehuaDom()}>get dehua's dom</button>
      </>
    )
  }
}

export default App
```

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802171720058.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802171720058.png)

  

## **13. ref 获取组件（类组件和函数式组件）**

- 通过 ref 获取组件信息，可以通过下面代码查看具体获取方式，注意函数式组件 forwardRef 和 memo 包裹函数的顺序。

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802171824222.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802171824222.png)

  

```JavaScript
import React, { PureComponent, createRef } from 'react'
import Helloworld from './components/Helloworld'
import MyFunction from './components/MyFunction'

export class App extends PureComponent {

  constructor() {
    super()

    // 类组件中的ref
    this.helloworldRef = createRef()
    // 函数式组件中的ref
    this.yanzuRef = createRef()
  }

  getHelloworldRef() {
    console.log(this.helloworldRef.current)
  }

  getYanzuRef() {
    console.log(this.yanzuRef.current)
  }

  render() {
    return (
      <>
        <div>App</div>
        {/* 类组件 */}
        <Helloworld ref={this.helloworldRef} />
        <button onClick={e => this.getHelloworldRef()}>获取组件</button>
        {/* 函数式组件 */}
        <MyFunction ref={this.yanzuRef}/>
        <button onClick={e => this.getYanzuRef()}>获取组件</button>
      </>
    )
  }
}

export default App
```

  

```JavaScript
import React, { PureComponent } from 'react'

export class Helloworld extends PureComponent {
  render() {
    return (
      <>
        <div>Helloworld</div>
        <span>👋 你好，李银河！（Helloworld类组件中的一个span）</span>
      </>
    )
  }
}

export default Helloworld
```

  

```JavaScript
import React, { memo, forwardRef } from 'react'

const MyFunction = memo(forwardRef((props, ref) => {
  return (
    <>
      <div>MyFunction (函数式组件)</div>
      <span>你好，彭于晏！</span>
      <span ref={ref}>你好，吴彦祖！</span>
    </>
  )
}))

export default MyFunction
```

  

[](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802172008611.png)](	https://images-1306852673.cos.ap-chengdu.myqcloud.com/20250802172008611.png)

  

## **14. 受控组件和非受控组件**

todo