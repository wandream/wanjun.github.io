<!--
 * @Author: Mia
 * @Date: 2021-12-28 10:18:54
 * @LastEditors: Mia
 * @LastEditTime: 2022-01-04 10:22:38
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

DOM元素与子组件
ref处理可以定义数据之外，还可以用来挂载到节点或者挂载到子组件上
