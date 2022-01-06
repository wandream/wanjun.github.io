<!--
 * @Author: Mia
 * @Date: 2022-01-06 15:03:02
 * @LastEditors: Mia
 * @LastEditTime: 2022-01-06 16:04:04
 * @Description:
-->

## TS 高级类型

### 一、交叉类型

交叉类型是将多个类型合并为一个类型

```typescript
interface pointx {
  x: number;
  z: string;
}

interface pointy {
  y: number;
  z: string;
}

type Point = pointx & pointy;

const point: Point = {
  x: 1,
  y: 3,
  z: "123123",
};
```

将多个接口类型合并成为一个类型是交叉类型的一个常见场景

```typescript
type Person = {
  name: string;
  age: number;
} & {
  height: number;
  weight: number;
} & {
  id: number;
};

const person: Person = {
  name: "zhangsan",
  age: 18,
  height: 180,
  weight: 60,
  id: 123456,
};
```
但当接口中同一个属性定义了不同的类型
```typescript
type Person = {
	name: string;
  age: number;
} & {
  age: string;
	height: number;
  weight: number;
}
```
可以看到，两个接口中都拥有 age 属性，类型分别为 `number` 和 `string`, 那么在合并后，age 的类型就变成了 `number & string`，也就是一个 `never`类型.

如果同名属性的类型兼容，比如一个是 number，另一个是 number 的子类型、数字字面量类型，合并后 age 属性的类型就是两者中的子类型：
```typescript
type Person = {
	name: string;
  age: number;
} & {
  age: 18;
	height: number;
  weight: number;
}

const person: Person = {
	name: "zhangsan",
  age: 20,  // Type '20' is not assignable to type '18'.ts(2322)
  height: 180,
  weight: 60,
}
```

### 二、联合类型
在 ts 中，联合类型可以把接口类型连接起来，表示更复杂的类型结构
```typescript
interface A {
  name: string
  age: number
}

interface B {
  name: number
  id: string
}

type Union = A | B;
const c: Union
```


### never 类型
`never`：即不存在的值的类型，按照交叉类型中的说法，不存在 age 的值既是 `number` 又是 `string`