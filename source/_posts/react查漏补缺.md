---
title: react查漏补缺
date: 2019-08-15 15:37:28
tags: [React]
---

## setData

```js
constructor(props) {
    super(props)
    this.state = { a: 0 }
}


callback = () => {console.log(this.state.a)}

this.setState({a: 1} 或者 (prevState) => {
    console.log(prevState.a);
    return {a: 1}
}, callback)

callback()

this.setState({a: 2} 或者 (prevState) => {
    console.log(prevState.a);
    return {a: 2}
}, callback)

callback()

```

1. setData会整合执行
2. 同时执行的**所有**setData执行完渲染后, 才会开始走回调
3. prevState是每个阶段实时的state, 所以可以阶段性的拿到state
4. setData是异步的
5. 现在的打印顺序应该是: 0 0 0 1 2 2




super的作用

1. 构造函数中使用, 如果有构造函数constructor, 但是没有调用super(), this还没被初始化, 使用this会报错(super其实是父元素的构造函数)  es6的继承机制是先实例化一个父类(super直接继承父类的属性和方法), 然后加工这个实例
2. 如果只super(), 没有super(props), this.props在构造函数内部是无法使用的(执行 super(props) 可以使基类 React.Component 初始化 this.props。)
3. 如果没有定义构造函数,或者构造函数super()没有super(props) react编译时props会被自动加到实例上, 在除构造函数外的函数里可以使用this.props