## 接口

> TypeScript 的核心原则之一是对值所具有的**结构**进行**类型检查**。
>
> **接口的作用**就是为这些类型命名和为你的代码或第三方代码定义契约。即可以约束对象、函数以及类的结构和类型。

### 接口初探

```ts
interface List {
  id: number;
  name: string;
}
interface Result {
  data: List[];
}

function render(result: Result) {
  result.data.forEach((item) => {
    console.log(item.id, item, name);
  });
}

const result = {
  data: [
    { id: 0, name: "js" },
    { id: 1, name: "ts" },
  ],
};

render(result);
```

### 额外属性

- **遇到问题：**

  ```ts
  render({
    data: [
      { id: 0, name: "js", sex: "male" },
      { id: 1, name: "ts" },
    ],
  });
  // Error: 'List'类型不存在属性'sex'
  ```

  - **解决方案有三**

    1. 变量形式传参：
       ```ts
       render(result);
       ```
    2. 使用类型断言
       ```ts
       render({
         data: [
           { id: 0, name: "js", sex: "male" },
           { id: 1, name: "ts" },
         ],
       } as Result);
       ```
       or
       ```ts
       // 不推荐，在react中会产生歧义
       render(<Result>{
        data: [
          { id: 0, name: "js", sex: "male" },
          { id: 1, name: "ts" },
        ],
       }
       ```
    3. **添加一个字符串索引签名（最佳的方式）**

       ```ts
       interface List {
         id: number;
         name: string;
         [x: string]: any; // 用任意的字符串索引List，可以得到一个任意结果
       }
       ```

### 可选属性

```ts
interface List {
  id: number;
  name: string;
  age?: number;
}
```

### 只读属性

```ts
interface List {
  readonly id: number;
  name: string;
}
```

### 可索引类型：不确定接口中的属性个数

- **数字索引签名**

```ts
interface StringArray {
  [index: number]: string;
}
```

- **字符串索引签名**

```ts
interface Names {
  [x: string]: string;
}
```

- **混用**

  > 可以同时使用两种类型的索引，但是**数字索引的返回值**必须是**字符串索引返回值类型的子类型**。
  > 这是因为当使用 number 来索引时，JavaScript 会将它转换成 string 然后再去索引对象。
  > 也就是说用 100（一个 number）去索引等同于使用"100"（一个 string）去索引，因此两者需要保持一致。

  ```ts
  interface Names {
    [x: string]: string;
    [index: number]: string;
  }
  ```

  - **问题**：当属性的类型与字符串索引类型**不匹配**

    ```ts
    interface NumberDictionary {
      [index: string]: number;
      length: number; // 可以，length是number类型
      name: string; // 错误，`name`的类型与索引类型返回值的类型不匹配
    }
    ```

- **只读**：防止了给索引赋值

  ```ts
  interface ReadonlyStringArray {
    readonly [index: number]: string;
  }
  let myArray: ReadonlyStringArray = ["A", "B"];
  myArray[0] = "C"; // error!
  ```
