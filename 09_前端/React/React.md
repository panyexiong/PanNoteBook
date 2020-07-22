# React学习

```powershell
#初始化react项目
npx create-react-app react-demo

#启动项目
npm start

#安装路由
npm install react-router-dom --save

#前后端通信axios
npm install axios --save

npm install react-transition-group --save
```

## api

列表：a.tiying.com/api/productlist

## React常用

```jsx
import React, { Component, Fragment } from 'react';

<Fragment>
    
</Fragment>
```

## TodoList.js

```jsx
import React, { Component, Fragment } from 'react';
import '../assets/css/style.css';

export default class TodoList extends Component {

    constructor(props) {
        super(props);
        this.state = {
            list: [],
            inputValue: '',

        };
    }

    handleInputChange = (e) => {
        // console.log(e.target.value);
        this.setState({
            inputValue: e.target.value,
        });
    }

    handleBtnClick() {
        this.setState({
            list: [...this.state.list, this.state.inputValue],
            inputValue: '',
        })
    }

    handleItemDelete = (index) => {
        const list = [...this.state.list];
        list.splice(index, 1);
        this.setState({
            list: list,
        })
    }

    render() {
        return (
            <Fragment>

                <div>
                    <input className='input' value={this.state.inputValue} onChange={this.handleInputChange}></input>
                    <button onClick={this.handleBtnClick.bind(this)}>提交</button>

                    <ul>
                        {
                            this.state.list.map((item, index) => {
                                return (
                                    <li
                                        key={index}
                                        onClick={this.handleItemDelete.bind(this, index)}
                                        dangerouslySetInnerHTML={{__html: item}}
                                    >
                                        {/* {item} */}
                                    </li>
                                )
                            })
                        }
                    </ul>
                </div>
            </Fragment>
        )
    }
}

```

## 生命周期函数

组件即将被挂在到页面之前执行

`comonentWillMount()`



`render()`

组件被挂在到页面之后执行

`componentDidMount()`

组件被更新之前被加载

`shouldComponetUpdate`



`componentDidUpdate`



`componentWillUnmount`

## Redux

