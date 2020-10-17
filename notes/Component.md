# Vue 组件

一个组件就是一个拥有预定以选项的一个Vue实例

一个组件可以组成页面上一个功能完备的区域，包含脚本、样式、模板

## 组件注册过程

注册方式

- 全局组件

- 局部组件

## Vue.component静态属性定义

> core/global-api/index.js
>
> core/global-api/assets.js

```
initAssetRegisters(Vue)
```

将传入的组件的定义保存到vue实例的 options属性的components中

```
this.options['components']['comp'] = definition
```

## Vue.extend

> core/global-api/extends.js

- 缓存组件：闭包变量cid
- 创建基于当前实例的构造函数，通过当前示例的_init初始化，继承当前对象
- 原型指向Super，构造函数指向自己Sub
- 合并options选项
- 指定Sub的super为 Super
- 初始化props
- 初始化Computed
- 添加extend, mixin, use 为 Super的属性
- 继承components,directive,filter
- 通过名字递归注册自己 `Sub.options.components[name] = Sub`

## 组件创建过程

调用`Vue.component`全局注册组件，渲染时 createElement 中，根据tag不是string走组件创建逻辑

>  src/core/vdom/create-component.js

createComponent 中根据构造函数和选项数据，创建 VNode，包含

```
{ Ctor, propsData, listeners, tag, children }
```

## 父子组件

创建：先创建父组件，再创建子组件

挂载：先挂载子组件，再挂载父组件