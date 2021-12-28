<!--
 * @Author: Mia
 * @Date: 2021-12-24 13:54:54
 * @LastEditors: Mia
 * @LastEditTime: 2021-12-28 11:12:08
 * @Description:
-->

# Vue3 组件间的通信

[父子组件通信](#父子组件通信)<br/>
[祖孙组件通信](#祖孙组件通信)<br/>
[全局组件通信](#全局组件通信)

# 父子组件通信

| 方案           | 父组件向子组件 | 子组件向父组件 |
| -------------- | -------------- | -------------- |
| props/emit     | props          | emit           |
| v-model/emit   | v-model        | emit           |
| ref/emit       | ref            | emit           |
| provide/inject | provider       | inject         |
| EventBus       | emit/on        | emit/on        |
| Vuex           | -              | -              |

## props/emit

1. Father.vue 通过 props 向 Child.vue 传值
2. Child.vue 通过 emit 向 Father.vue 触发父组件的事件执行

# 祖孙组件通信

| 方案           | 爷组件向孙组件 | 孙组件向爷组件 |
| -------------- | -------------- | -------------- |
| provide/inject | provide        | inject         |
| EventBus       | emit/on        | emit/on        |
| Vuex           | -              | -              |

## provide

- 在 3.x 中，provide 需要导入并在 setup 里启用
- 每次要 provide 一个数据的时候，就要单独调用一次

```javascript
import { defineComponent, provide } from 'vue'
export default defineComponent({
  setup() {
    // ts 的写法
    const msg: string = 'hello world',
    // provide 出去
    provide('msg', msg)
  }
})
```

<strong>需要注意的是，provide 不是响应式的，如果需要使其具备响应性，需要传入响应式数据</strong>

## inject

- 在 3.x，inject 和 provide 一样，也是需要先导入然后在 setup 里启用
- 每次要 inject 一个数据的时候，就要单独调用一次

```javascript
// 记得导入inject
import { defineComponent, inject } from "vue";
export default defineComponent({
  // ...
  setup() {
    const msg: string = inject("msg") || "";
  },
});
```

# 全局组件通信

| 方案     | 发起方 | 接收方 |
| -------- | ------ | ------ |
| EventBus | emit   | on     |
| Vuex     | -      | -      |
