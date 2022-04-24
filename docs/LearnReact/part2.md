<!--
 * @Author: Mia
 * @Date: 2022-03-21 13:22:13
 * @LastEditors: Mia
 * @LastEditTime: 2022-03-22 11:20:48
 * @Description: Refs 的转发
-->
## Refs转发(forwardRef)

Ref 转发是一项将 ref 自动通过组件传递到其一子组件的技巧(允许某些组件接收 ref, 并将其向下传递给子组件)

React.forwardRef 字面意思理解为转发 Ref
- 它会创建一个 React 组件，这个组件能够将其接收的 ref 属性转发到其组件树下的另一个组件中

1. 转发 refs 到 DOM 组件(ref 不想 props 作为参数可以传递，所以要想传递 ref 得用 forwardRef)

