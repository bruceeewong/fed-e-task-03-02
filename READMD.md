# 3-2 Vue.js 源码剖析-响应式原理、虚拟 DOM、模板编译和组件化

> 源码学习仓库：https://github.com/bruceeewong/vue/tree/learn
> 
> 作者：王思哲
>
> 时间：2020-10-17

## 一、简答题

### 1、请简述 Vue 首次渲染的过程。

- 初始化Vue实例：vm._init()
  - 合并传入的与构造参数选项
  - 初始化生命周期
  - 初始化事件
  - 初始化编译render
  - 触发 `beforeCreate` 钩子
  - 将参数注入实例
  - 触发 `created` 钩子
  - 调用 $mount 方法
- vm.$mount()：
  - 执行模板编译，将模板转为 render 函数
  - 调用 mountComponent
- mountComponent()：
  - 触发 `beforeMount` 钩子
  - 创建实例的watcher，定义update回调
  - watcher调用 getter 方法触发 组件更新回调函数
  - 执行render 函数 返回组件对应的VNode
  - update函数执行 patch 函数，将新vnode的差异 更新 旧的vnode 上。
  - 执行 createElm 创建 Vnode 对应的 DOM，并替换 el 节点挂载到DOM树上
  - 触发 `mounted` 钩子
  - 返回组件实例
- 首次渲染结束

### 2、请简述 Vue 响应式原理。

Vue响应式系统将数据与视图绑定，当数据发生变化时，视图会自动进行更新。

Vue实例初始化时，会对data中的数据执行 `observe` 创建响应式Observer对象：

- 为对象定义`__ob__`属性，不可枚举
- 数组的响应式处理
  - 修改数组原型链方法，增加 dep 的通知功能
  - 遍历成员，响应式处理
- 对象的响应式处理

响应式处理的过程：

- 为对象每一个属性创建 `dep`依赖对象
- 通过 Object.defineProperty 定义属性的 getter / setter
  - 定义 getter: 
    - 收集自身以及子项依赖，即添加属性的 dep 到 watcher  subs中 
  - 定义 setter: 
    - 如果新值是对象，调用observe转为响应式数据
    - 派发更新，即调用该属性的 dep.notify() 执行依赖的回调

而视图的指令与数据绑定经过模板编译，会创建属性的依赖以及相应的更新回调。

基于上述过程，当数据改变时，会通知视图依赖执行相应的更新函数，完成数据与视图的响应式变化。

### 3、请简述虚拟 DOM 中 Key 的作用和好处。

作用：用于虚拟DOM更新时优化，在比较新旧虚拟节点判断节点是否可以重用。

好处：优化新旧节点 diff 比较过程，复用节点，减少DOM操作次数，提升渲染性能。

### 4、请简述 Vue 中模板编译的过程。

- 首先获取vue实例对应的 template / el 的模板字符串，结合vue实例的选项传入编译函数中
- 合并传入选项以及默认选项
- 编译模板
  - 将 template 结合 options 转换为 AST 抽象语法树
  - 优化AST树，标记出其中的静态节点&静态根节点
  - 生成 AST 对应的代码（js字符串）
- 将 JS代码 转换为 渲染函数
- 完成模板编译