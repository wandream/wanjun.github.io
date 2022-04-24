<!--
 * @Author: Mia
 * @Date: 2022-03-16 13:48:14
 * @LastEditors: Mia
 * @LastEditTime: 2022-03-16 15:41:08
 * @Description: 组合式API基础
-->

组合式API： 能够将同一逻辑关注点相关代码收集在一起会更好

### `setup` 的调用发生在 data, computed, method 被解析之前，所以它们无法在 setup 中被获取

## setup

setup 是一个 接收 props 和 context 的函数，我们能够将 setup 返回的所有内容都暴露给组件的其他部分（计算属性，方法，生命周期钩子等等）以及组件的模板

### props
在setup 中，因为 props 是响应式的，所以不能使用 ES6 结构，它会消除 props 的响应性

如果需要结构，可以使用 toRefs 函数来完成操作
```ts
import { toRefs } from 'vue'
setup(props) {
  const { title } = toRefs(props)
  console.log(title.value)
}
```
如果 title 是一个可选的 props，则传入的 props 中可能没有 title。在这种情况下， toRefs 将不会为 title 创建一个 ref。则需要使用 toRef 来替代它
```ts
import { toRef } from 'vue'
setup(props) {
  const title = toRef(props, 'title')
  console.log(title.value)
}
```

### context
context 是一个普通的JavaScript 对象(因为其实一个普通的js对象，所以可以放心的对其进行解构)，暴露了其它可能在 setup 中有用的值
```js
export default {
  setup(props, context) {
    // Attribute = $attrs 非响应式对象
    console.log(context.attrs)
    // 插槽 = $slots 非响应式对象
    console.log(context.slots)
    // 触发事件 = $emit 方法
    console.log(context.emit)
    // 暴露公共的 property 函数
    console.log(context.expose)
  }
}
```

 - 注意 : 从 setup 返回的 refs 在模板中访问时，是会被自动浅解包的，因此不应在模板中使用 .value

### 在 setup 中使用渲染函数
```js
import { h, ref, reactive } from 'vue'
export default {
  setup() {
    const readerNumber = ref(0)
    const book = reactive({
      title: 'vue 3 guide'
    })
    return () => h('div', [readersNumber.value, book.title])
  }
}
```
在返回一个渲染函数时，将阻止我们返回任何其它的东西。

### 生命周期函数
1. 可以通过在生命周期钩子前面加上 "on" 来访问组件的生命周期钩子
2. setup 是围绕 beforeCreate 和 created 生命周期钩子运行的，所以不需要显式地定义他们。
3. 生命周期函数接受一个回调函数，当钩子被组件调用的时候，会被执行
```js
export default {
  setup() {
    onMounted(() => {
      console.log('component is mounted')
    })
  }
}
```

