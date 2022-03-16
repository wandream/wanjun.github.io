<!--
 * @Author: Mia
 * @Date: 2022-03-15 10:01:29
 * @LastEditors: Mia
 * @LastEditTime: 2022-03-16 13:48:24
 * @Description: vue3 官方文档学习
--> 
## prop
1. 传递静态或动态的 prop
```javascript
<blog-post title="hello world" />
// 动态赋予一个变量的值
<blog-post :title="post.title" />
// 动态赋予一个复杂表达式的值
<blog-post :title="post.title + by + post.author.name" />
```

## 单项数据流
父级 prop 的更新会向下流动到子组件中，但是反过来则不行。这样能防止从子组件意外变更父级组件的状态。
> 每次父级组件发生变更时，子组件中所有的 prop 都将会刷新为最新的值。这意味不应该在一个子组件内部改变 prop。


## 非 prop 的 Attribute
1. 禁用 Attribute 继承
`inheritAttrs: false`
<strong>禁用 Attribute 继承的常见场景是需要将 attribute 应用于根节点之外的其他元素</strong>

通过将 inheritAttrs 选项设置为 false，可以使用组件的 $attrs property 将 attribute 应用到其他元素上，该 property 包括组件 props 和 emits property 中未包含的所有属性值

## 响应性基础

1. 声明响应式状态
要为 JS 对象创建响应式状态，可以使用 reactive 方法
```JS
import { reactive } from 'vue
// 响应状态
const  state = reactive({
  count: 0
})
console.log(state.count === 0)
```
reactive  相当于 vue 2.x 中的 Vue.observable() API

## reactive 返回对象的响应式副本
- reactive 将解包所有深层的 refs，同时维持 ref 的响应性
- 当将 ref 分配给 reactive 的 property 时， ref 将会自动解包
```ts
const count = ref(1)
const obj = reactive({ count })
// ref 会被解包
console.log(obj.count === count.value)// true
count.value++
console.log(count.value)// 2
console.log(obj.count)// 2
obj.count++
console.log(obj.count)//3
console.log(count.value)//3
```
- 当将 ref 分配给 reactive 的 property 时，ref 将被自动解包
```ts
const count = ref(1)
const obj = reactive({})
obj.count = count
console.log(obj.count)// 1
console.log(obj.count == count.value)// true
```

## 创建独立的响应式值作为 refs
```js
import { ref } from 'vue'
const count = ref(0)
```
ref 返回的是一个可变的响应式对象，该对象作为一个响应式引用维护着它内部的值。`该对象只包含一个名为 value 的属性`
```js
import { ref } from 'vue'
const count = ref(0)
console.log(count.value) // 0
count.value++
console.log(count.value) //1
```

访问响应式对象
- 当 ref 作为响应式对象的 property 被访问或更改时，为使其行为类似于普通的 property，它会自动解包内部值
```js
const count = ref(0)
const state = reactive({
  count
})
console.log(state.count) //0
// 不需要写成 state.count.value 
```
- 如果将新的 ref 赋值给现有的 ref property，将会替换旧的 ref:
```js
const otherCount = ref(2)
state.count = otherCount
console.log(state.count) //2
console.log(count.value) //1
```
- ref 解包仅发生在被响应式 `Object` 嵌套的时候，当从 `Array` 或原生集合类型如 `Map` 访问 ref 时，不会进行解包
```js
const books = reactive([ref('vue3 guide')])
console.log(books[0].value)// 注意： 这里需要 .value
const map = reactive(new Map([['count', ref(0)]]))
console.log(map.get('count').value)// 注意，这里需要 .value
```

### 响应式状态解构
在对大型响应式对象进行解构的时候，会使得解构后的属性失去响应式。对于这种情况，需要将响应式对象转化为一组 ref, 这些 ref 将保留源对象的响应式关联：
```js
import { reactive, toRefs } from 'vue'
import { reactive, toRefs } from 'vue'
const book = reactive({
  author: 'Vue Team',
  year: '2020',
  title: 'Vue 3 Guide',
  description: 'You are reading this book right now ;)',
  price: 'free'
})
let { author, title } = toRefs(book)
title.value = 'Vue 3 Detailed Guide' // 我们需要使用 .value 作为标题，现在是 ref
console.log(book.title) // 'Vue 3 Detailed Guide'
```
> 使用 readonly 可以防止更改响应式对象

### 响应式计算和监听
1. computed
computed 函数：它接受 getter 函数并为 getter 返回的值返回一个不可变的响应式 ref 对象
```js
const count = ref(1)
const plusOne = computed(() => count.value + 1)
console.log(plusOne.value) //2
plusOne.value++ // error
```

```js
const count = ref(1)
const plusOne = computed({
  get: () => count.value + 1,
  set: val => {
    count.value = val - 1
  }
})
plusOne.value = 1
console.log(count.value) // 0
```

### 渲染函数
