### 函数类型接口

```ts
// 接口
interface Add {
  (a: number, b: string): number;
}
```

```ts
// 实现具体的函数
let add: Add = (a, b) => a + b;
```

### 混合类型接口

> 因为 JavaScript 其动态灵活的特点，有时你会希望一个对象可以同时具有上面提到的**多种类型**。

```ts
interface Lib {
  (): void; // 函数
  version: string; // 属性
  dosomething(): void; // 方法
}
function getLib(version: string) {
  let lib = (() => {}) as Lib;
  lib.version = version || "1.0";
  lib.doSomething = () => {
    console.log("do something");
  };
  return lib;
}

let l = getLib("2.0");
l();
l.version;
l.doSomething();
```

### 类接口

> 建议先食用 类 相关内容。
