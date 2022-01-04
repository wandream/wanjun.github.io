<!--
 * @Author: Mia
 * @Date: 2021-12-28 10:18:54
 * @LastEditors: Mia
 * @LastEditTime: 2022-01-04 10:37:56
 * @Description: Vue3 中的 ts 写法
-->

## Vue3 中 ref 和 reactive

### ref

ref 是最常用的一个响应式 API

```javascript
// 单类型
const msg = ref<string>('hello world')
// 多类型
const msg = ref<string | number>(123123123123)
// 基本类型
const msg = ref<boolean>(false)
// 接口定义
interface Member {
  id: number;
  name: string;
}
// 引用类型 - 对象
const userInfo = ref<Member>({
  id: 1,
  name: 'tom',
})
// 引用类型 - 对象数组
const memberList = ref<Member[]> {
  {id: 1, name: 'tom'},
  {id: 2, name: 'jerry'},
}
// 引用类型 - 数组
const names = ref<string[]>(['tom','jerry'])
```

DOM 元素与子组件
ref 处理可以定义数据之外，还可以用来挂载到节点或者挂载到子组件上

```javascript
<template>
  <!-- 挂载 dom 元素 -->
  <p ref="msg"></p>
  <!-- 挂载子组件 -->
  <Child ref="child" />
</template>
```

关于 DOM 和子组件的 TS 类型声明：
|节点类型|声明类型|
|--|---|
|DOM 元素|使用 HTML 元素接口|
|子组件|使用 typeof 获取子组件的类型|
