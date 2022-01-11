## TS 内置类型

 - [Partial](#Partial)
 - [Required](#Required)
 - [Readonly](#Readonly)
 - [Pick](#Pick)
 - [Exclude](#Exclude)
 - [Extract](#Extract)
 - [Omit](#Omit)
 - [NonNullable](#NonNullable)
 - [Uppercase](#Uppercase)
 - [Lowercase](#Lowercase)

### Partial

让传入类型中的所有属性变成都是可选的

```typescript
type partial<T> = {
  [P in keyof T]?: T[P];
};
export interface Student {
  name: string;
  age: number;
}

// Type '{}' is missing the following properties from type 'Student': name, age(2739)
const student1: Student = {};

const student2: partial<Student> = {};
```

### DeepPartial

```typescript
type DeepPartial<T> = T extends Function
  ? T
  : T extends object
  ? { [P in keyof T]?: DeepPartial<T[P]> }
  : T
type PartialedWindow = DeepPartial<Window>
```

### Required

让传入类型中的所有属性都变成是必填的

```typescript
type require<T> = {
  [P in keyof T]-?: T[P];
};

export interface Student {
  name?: string;
  age?: number;
}

const student1: Student = {};

// Type '{}' is missing the following properties from type 'require<Student>': name, age(2739)
const student2: require<Student> = {};
```

### Readonly

让传入类型中的所有属性都变成只读

```typescript
type myreadonly<T> = {
  readonly [P in keyof T]: T[P];
};

export interface Student {
  name: string;
  age: number;
}

const student1: Student = {
  name: "张三",
  age: 20,
};
student1.age = 21;

const student2: myreadonly<Student> = {
  name: "李四",
  age: 20,
};
// Cannot assign to 'age' because it is a read-only property.(2540)
student2.age = 21;
```

### Pick

让传入类型中的部分属性组成新类型

```
type pick<T, K extends keyof T> = {
  [P in K]: T[P]
}

export interface Student {
  name: string;
  age: number;
}

const student1: Student = {
  name: '张三',
  age: 20
}

const student2: pick<Student, 'name'> = {
  name: '李四'
}

// Type '{ name: string; age: number; }' is not assignable to type 'pick<Student, "name">'.
// Object literal may only specify known properties, and 'age' does not exist in type 'pick<Student, "name">'.(2322)
const student3: pick<Student, 'name'> = {
  name: '王五',
  age: 20
}
```

### Record

作用是构建一个类型，这个类型用来描述一个对象，这个对象的属性都具有相同的类型

```typescript
type record<K extends keyof any, T> = {
  [P in K]: T;
};

export const student1: record<string, any> = {
  name: "张三",
  age: 20,
};
```

Record 应该是日常使用频率较高的内置类型了，主要用来描述对象，一般建议是不用 Object 来描述对象，而是用 Record 代替，`Record<string, any>`几乎可以说是万金油

### Exclude

针对联合类型，排除相同的，留下不同的

```typescript
type exclude<T, U> = T extends U ? never : T;

export type PersonAttr = "name" | "age";

export type StudentAttr = "name" | "age" | "class" | "school";

// 'const' declarations must be initialized.(1155)
// const student1: "class" | "school"
const student1: exclude<StudentAttr, PersonAttr>; // student1就只能被赋值为'class' 或者'school'

// 'const' declarations must be initialized.(1155)
// const student2: never
const student2: exclude<PersonAttr, StudentAttr>;
```

### Extract

针对联合类型，排除不同的，取出相同的

```typescript
type extract<T, U> = T extends U ? T : never;

export type PersonAttr = "name" | "age";

export type StudentAttr = "name" | "age" | "class" | "school";

// const student1: "name" | "age"
const student1: Extract<StudentAttr, PersonAttr>;//student1就只能被赋值为'name'或者'age'

// const student1: PersonAttr
const student2: Extract<PersonAttr, StudentAttr>;
```
### Omit
传入一个类型，和这个类型的几个属性，把传入的属性省略掉，组成一个新的类型
```typescript
// 找到T中除K之外的属性，然后在T中挑选出来，组成一个新的类型
type omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>

export interface Student {
  name: string;
  age: number;
  class: string;
  school: string;
}

export type PersonAttr = 'name' | 'age'

export type StudentAttr = 'name' | 'age' | 'class' | 'school'

// Type '{}' is missing the following properties from type 'Omit<Student, PersonAttr>': class, school(2739)
const student1: omit<Student, PersonAttr> = {}

// Type '{ class: string; school: string; age: number; }' is not assignable to type 'omit<Student, PersonAttr>'.
// Object literal may only specify known properties, and 'age' does not exist in type 'omit<Student, PersonAttr>'.(2322)
const student2: omit<Student, PersonAttr> = {
  class:'123123',
  school: '123123',
  age: 123123,
}
```

### NonNullable
不能为空
```typescript
type nonNullable<T> = T extends null | undefined ? never : T;

export interface Student {
  name: string;
  age: number;
}

// Type 'null' is not assignable to type 'Student'.
const student1: nonNullable<Student | undefined | null> = null
```

### Uppercase
大写
```typescript
type uppercase<S extends string> = intrinsic

export type StudentSexType = 'male' | 'female'

// Type '"male"' is not assignable to type '"MALE" | "FEMALE"'. Did you mean '"MALE"'?(2820)
const studentSex: Uppercase<StudentSexType> = 'male'
```

