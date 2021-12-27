<!--
 * @Author: Mia
 * @Date: 2021-12-24 13:54:54
 * @LastEditors: Mia
 * @LastEditTime: 2021-12-27 17:35:11
 * @Description:
-->

# Vue3 组件间的通信

[父子组件通信](#父子组件通信)

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
1. Father.vue 通过 props 向 Child.vue传值
2. Child.vue 通过 emit 向 Father.vue 触发父组件的事件执行

# 祖孙组件通信

| 方案           | 爷组件向孙组件 | 孙组件向爷组件 |
| -------------- | -------------- | -------------- |
| provide/inject | provide        | inject         |
| EventBus       | emit/on        | emit/on        |
| Vuex           | -              | -              |

## pR
# 全局组件通信

| 方案     | 发起方 | 接收方 |
| -------- | ------ | ------ |
| EventBus | emit   | on     |
| Vuex     | -      | -      |
