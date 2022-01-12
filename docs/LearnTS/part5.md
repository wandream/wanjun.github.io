<!--
 * @Author: Mia
 * @Date: 2022-01-12 16:13:23
 * @LastEditors: Mia
 * @LastEditTime: 2022-01-12 16:42:07
 * @Description: TS 定义React 组件
-->

### 使用 TS 定义 React 组件

[参考链接](https://jishuin.proginn.com/p/763bfbd6b8a8)

##### 使用 React.FC 声明函数组件和普通声明的区别如下：

- React.FC 显式的定义了返回类型，其他方式是隐式推导的；
- React.FC 对静态属性：displayName, propTypes, defaultProps 提供了类型检查和自动补全

##### 普通函数组件写法

```typescript
interface IProps {
  name: string;
}
const App = (props: IProps) => {
  const { name } = props;
  return (
    <div className="App">
      <h1>hello world</h1>
      <h2>{name}</h2>
    </div>
  );
};
export default App;
```

##### React.FC 函数组件写法

```typescript
interface IProps {
  name: string;
}
const App: React.FC<IProps> = (props) => {
  const { name } = props;
  return (
    <div className="App">
      <h1>hello world</h1>
      <h2>{name}</h2>
    </div>
  );
};
export default App;
```

##### 如果在定义组件时不知道 props 的类型，只有调用时才知道，那就还是使用泛型来定义 props 的类型

- 箭头函数写法
> 箭头函数不能直接调用，必须使用 extends 关键字来定义泛型参数才能被成功解析

```typescript
const MyComponent = <P extends any>(props: P) {
 return (
  <span>
    {props}
   </span>
 );
}
```

- function 定义写法

```typescript
// 定义组件
function MyComponent<P>(props: P) {
  return <span>{props}</span>;
}
// 使用组件
type IProps = { name: string; age: number };
<MyComponent<IProps> name="React" age={18} />; // Success
<MyComponent<IProps> name="TypeScript" age="hello" />; // Error
```
