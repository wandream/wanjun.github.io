<!--
 * @Author: Mia
 * @Date: 2022-01-12 16:48:30
 * @LastEditors: Mia
 * @LastEditTime: 2022-01-12 17:06:02
 * @Description: TS 定义React router懒加载类型
-->

如果想在 React router 中使用懒加载，React 也提供了懒加载方法

```typescript
export interface RouteType {
  pathname: string;
  component: LazyExoticComponnet<any>;
  exact?: boolean;
  title?: string;
  icon?: string;
  children?: RouteType[];
}

export const AppRoutes: RouteType[] = [
  {
    pathname: "/login",
    component: lazy(() => import("./view/login")),
    exact: true,
  },
  {
    pathname: "/404",
    component: lazy(() => import("./view/404")),
    exact: true,
  },
];
```

懒加载类型和 lazy 方法在声明文件中的定义

```typescript
type LazyExoticComponent<T extends ComponentType<any>> = ExoticComponent<
  ComponentPropsWithRef<T>
> & {
  readonly _result: T;
};

function lazy<T extends ComponentType<any>>(
  factory: () => Promise<{ default: T }>
): LazyExoticComponent<T>;
```
