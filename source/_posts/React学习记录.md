---
title: React学习记录
date: 2019-06-28 15:20:17
tags:
---

前端框架：
React - facebook
Angular - Google
Vue - 尤雨溪

React 
用于构建前端UI组件 Component

render 渲染方法
props 属性
state 状态

如何使用React

1. 引入React库
    React：React顶层API
    React DOM：DOM相关方法
    Babel：让旧版浏览器支持ES6语法
    
```
<script crossorigin src="https://unpkg.com/react@16/umd/react.development.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
<script src="https://unpkg.com/babel-standalone@6.26.0/babel.min.js"></script>
```

```
<div id='root'>
        <script type="text/babel">
            //React 代码都写在这里
            class App extends React.Component {
                render() {
                    return (
                        <h1>第一个React组件!</h1>
                    )
                }
            }
            ReactDOM.render(<App />,document.getElementById('root'))
        </script>
    </div>
```

基于node.js 环境 
npm -v 查看是否安装node.js
下载并安装node.js

vscode 文件夹打开终端

```
npx create-react-app app1  //新建react脚手架项目
cd app1
npm start  //启动项目 
```

项目入口：`index.js`
```
import React, { Component } from 'react'
import React from 'react'
import ReactDOM from 'react-dom'

ReactDOM.render(<App />, document.getElementById('root'));
```

> chrome扩展 react devTools

属性传递： 只能从上往下一级一级传递
![-w592](media/15617106547263.jpg)

// React class关键字-> className ; onclick -> onClick

基本组件Component模板
快捷键 rce：
```
import React, { Component } from 'react'

export class App extends Component {
    render() { // 渲染
        return ( //渲染返回内容 , return中只能有一个父元素。
            <div>
                
            </div>
        )
    }
}
export default App
```

组件的引用
```
import Table from './Table' 

...
class App extends Component {
    render() {
        return (
            <div>
                <Table />
            </div>
        )
    }
}
```

组件简易写法 用箭头语法
```
//  简易写法中 render() 可以省略 ; 
const TableHeader = () => {
    return (
        <thead>
            <tr>
                <th>姓名</th>
                <th>职业</th>
            </tr>
        </thead>
    )
}
```

### props 
props 组件的属性，子组件用于接收来自父组件的数据、事件处理函数
给组件传递属性：
```
<Table items={items} removeItem={this.removeItem}/>
```
组件获取属性：`this.props`
```
export class Table extends Component {
    render() {
        let items = this.props.items
        return (
        )
    }
}

const Table = (props) => {
    return (
        <table className="table table-hover">
            <TableBody {...props} />
        </table>
    )
}
```
...语法，展开属性

state 状态， 根组件全局状态， 子组件自身的状态

```
 let {items} = this.state 
```

更改状态 setState
```
    this.setState({
        items: [...this.state.items, newItem]
    })
```

form 表单提交

```
handleSubmit = (event) => {
        event.preventDefault()  // 阻止页面刷新
        this.props.handleSubmit(this.state)
        this.setState({
            name: '', job: '',
        })
    }
```

网络请求
```
 async componentDidMount() {
        let url = 'https://zh.wikipedia.org/w/api.php?action=opensearch&search=bitcoin&format=json&orgin=*'

        try {
            this.setState({
                data: await (await fetch(url)).json()
            })
        } catch (error) {
            alert(error)
        }
    }
```

#### 一些公共UI、组件库
bootstrap
https://getbootstrap.com/docs/4.3/getting-started/introduction/
```
$ npm i bootstrap jquery popper.js --save-dev
```

```
import 'bootstrap/dist/css/bootstrap.min.css'
```
element  https://element.eleme.io/#/

### 构建React App(打包）
打包配置文件件：package.json 
文件最后增加` "homepage":"." `  打包到当前目录
打包命令
```
npm run build
```

## 状态管理器:Redux
React 只能逐级传递，不灵活。 Redux集中存储状态store
![-w972](media/15619662970063.jpg)

### 安装Redux和调试工具

```
npx create-react-app redux1 (新建脚手架应用)
cd redux1
npm i redux --save-dev (安装redux库到开发环境)
``` 
chrome 安装插件：redux devTools

### Redux使用
#### Action
目录：constants -> actions-types.js 定义action常量
```
export const ADD_ARTICLE = "ADD_ARTICLE" 
```

目录：actions -> index.js  定义action

*模板快捷键：rxaction*
```
import { ADD_ARTICLE } from '../constants/actions-types'

export const addArticle = (payload) => ({
    type: ADD_ARTICLE,
    payload //参数
})
```

#### Reducers
*模板快捷键：rxreducer*
```
import { ADD_ARTICLE } from '../constants/actions-types'

const initialState = {  //定义初始state
    articles: []
}

const rootReducer = (state = initialState, action) => { //action = { type, payload }
    switch (action.type) {

        case ADD_ARTICLE:
            return { ...state, articles: [...state.articles, action.payload] } //...state 对象展开  ...state.articles数组展开

        default:
            return state
    }
}

export default rootReducer
```

#### store

```
import { createStore } from 'redux'
import rootReducer from '../reducers/index'

const store = createStore(
    rootReducer,
    window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__() // redux devtools调试用代码
    ) 

export default store
```

#### 调用
index.js

```
import store from './store/index'   // 导入store
import { addArticle } from './actions/index'  //导入action

window.store = store
window.addArticle = addArticle
```

npm start

浏览器终端

```
store.getState()   //获取state
store.subscribe(() => { console.log('Redux状态变化了') })  //监听状态变化
store.dispath(addArticle{title:'React redux教程',id:1})   //触发 action

```


React redux 关联
React与Redux通过Provider（数据提供商）来整体对接
mapStateToProps //获取state转换为props
mapDispatchToProps  //dispatch是发送action方法 转化为props
connect

```
npm i react-redux --save-dev  //React redux 关联

npm i uuid --save-dev
```

```
import { Provider } from 'react-redux'
import store from './js/store/index'

ReactDOM.render(<Provider store={store}><App /></Provider>, document.getElementById('root'));

```

React-Redux组件
*模板代码：rcredux*
```import React, { Component } from 'react'
import PropTypes from 'prop-types'
import { connect } from 'react-redux'

export class List extends Component {
    static propTypes = {
        prop: PropTypes
    }

    render() {
        return (
            <div>
                
            </div>
        )
    }
}

const mapStateToProps = (state) => {
    return { articles: state.articles }
}

const mapDispatchToProps = (dispatch) => {
    return {
        addArticle: article => dispatch(addArticle(article))
    }
}

export default connect(mapStateToProps, mapDispatchToProps)(List)
```

