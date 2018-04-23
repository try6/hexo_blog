---
title: vuex简易版API
date: 2018-04-24 06:43:29
tags:
---


vue作为三大MVVM框架之一，一个核心竞争力就是它的组件机制。

而[vuex](https://vuex.vuejs.org/zh-cn/intro.html)可以理解为，针对大型项目组件通信的全局管理。

### 核心概念
这里先介绍一下vuex中的几个核心概念，`state, getter, mutation, action, module`，在使用的时候需要注册在全局的`store`中
```
new Vuex.Store({
  state,
  getters,
  actions,
  mutations,
  moduls
})
```
#### State
state可以理解为你要操作的数据，即状态。注册在全局，则所有的组件都能访问到
```
const state = {
  count: 0,
  message: "hello"
}
```
当然了，虽然说注册在全局是为了方便所有的组件共享，如果谁都能对它进行修改，那也会产生非常混乱的局面。所以一般情况下，它是只读的。
```
//组件使用单个state
data () {
  return {
    count: this.$store.state.count
  }
}
```
组件使用多个state，可使用mapState辅助函数,引入所有的state
```
compted : mapState({
  count: state => state.count,
  showMsg: state => state.message + "world"
}
```

#### Getter
有时候需要对state的值进行一些额外的操作，比如上述例子中的showMsg
```
computed : {
  showMsg: () => this.$store.state.message + "world"
}
```
如果有多个组件都需要使用这条计算属性，那么就需要把这个计算过程也放到全局。

vuex中的Getter方法就是放置state计算方法的地方
```
const getters = {
  showMsg: state => state.message + "world"
}
```
然后只需要在需要调用这个方法的组件中这样写，非常方便
```
computed : {
  showMsg : (){ this.$store.getter.showMsg }
}
```
组件中使用多个getter，可以使用辅助函数mapGetters获取所有getter
```
computed : mapGetters({
  "showMsg"
})
```

#### Mutation
上面也说到了，为了防止state值的不可控，在业务组件内部是无法修改state的具体值的，那么我们应该如何修改这个state呢？

这时候就需要`mutation`出场了。mutation是专门为了修改state而存在的工具，同时也是唯一修改state的方式。

你可以把mutation理解为事件，在想要修改state的地方，使用`store.commit('事件名')`去提交一个mutation（这里注意只有在`action`中才能提交mutation，下文会讲到）

比如说，我们想要对state.count执行+1的操作，那么就可以这么写
```
const mutations = {
  increase : state => state++
}
```
然后在`action`中去提交
```
const actions = {
  incres : ({ commit }) => {
    commit ("increase")
  }
}

//上面是解构的写法，它相当于
const actions = {
  incres : (contxt) => {
    //这个contxt是指上下文，这里是store本身
    contxt.commit ("increase")
  }
}
```
到现在为止都还没有把操作state的行为和组件联系起来，别着急，`action`来了
#### Action
`mutation`可以理解为操作`state`的“事件”机制，那么`action`就可以理解为操作`mutation`的“行为”机制。

也就是说，`store.commit("mutation名")`只能在action中执行
```
//还是上面的例子
const actions = {
  incres : ({ commit }) => {
    commit ("increase")
  }
}
```
这个action的`incres`就可以在组件中使用了
```
//模板template
<button @click = "incres">state加1</button>

//vue实例中的methods方法
method : {
  incres: {
    this.$store.action.incres
  }
}
```
组件中使用了多个action行为，可以使用辅助函数mapActions传入所有的action
```
method : mapActions({
    "incres"
  })
}
```
#### Module
当应用非常复杂时，我们还可以把整个store拆分成几个`模块（module）`，每个module中都可以含有`各自`的`state, getter, mutation, action`
```
const moduleA = {
  state : {...},
  getter: {...} ,
  mutation: {...},
  actions: {...}
}

const moduleB = {
  state : {...},
  getter: {...},
  mutation: {...},
  actions: {...}
}

const moduleC = {
  state : {...},
  getter: {...} ,
  mutation: {...},
  actions: {...}
}
```
然后在全局中调用
```
new Vuex.Store ({
  modules : {
    a : moduleA,
    b : moduleB,
    c : moduleC,
  }
})
```
这样就能在全局中访问模块
```
store.state.a    //  moduleA的状态
store.state.b    //  moduleB的状态
```

以上是VUEX简易版API，更多内容可以移步[vuex官网](https://vuex.vuejs.org/zh-cn/)