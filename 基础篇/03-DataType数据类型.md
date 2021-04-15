## TypeScript 的数据类型

![01.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e768ef5cac7f4288a921e9ef26967b5f~tplv-k3u1fbpfcp-watermark.image)

#### 类型注解

- **作用**
  > 相当于强类型语言中的类型声明
- **语法**
  `(函数/变量):type`

#### 语法

```ts
// 原始类型
let bool: boolean = true;
let string: string = "123";
let number: number = 23;

// 数组
let arr01: number[] = [1, 2, 3];
let arr02: Array<number> = [1, 2, 3];
let arr03: Array<number | string> = [1, 2, 3];

// 元组：限定了数组元素的类型和个数
let tuple: [number, string] = [0, "1"];
console.log(tuple.length); // 2
tuple.push(2);
console.log(tuple); // [0, "1", 2]
console.log(tuple.length); // 3
/*
 **  元组的越界问题
 **
 **  console.log(tuple)
 **  console.log(tuple[2]);
 **  error: Tuple type '[number,string]' of length '2' has no element at index '2'.
 **  原则上不可以改变，允许 push 应该是一个TypeScript 的一个缺陷
 */

// 函数
/*
 ** 类型推断：函数的类型可以省略
 */
let add01 = (x: number, y: number): number => x + y;
let add02 = (x: number, y: number) => x + y;
/* 定义函数类型 */
let compute: (x: number, y: number) => number;
compute = (a, b) => a + b;

// 对象
let objError: object = { x: 1, y: "2" };
/* objError.x; */
let obj: { x: number; y: string } = { x: 1, y: "2" };
obj.x;

// symbol
let s1: symbol = Symbol();
let s2 = Symbol();
console.log(s1 === s2); // false

//undefined, null
/*
 ** 默认情况下null和undefined是所有类型的子类型。
 ** 就是说你可以把 null和undefined赋值给number类型的变量。
 ** 然而，当你指定了--strictNullChecks标记，null和undefined只能赋值给void和它们各自。
 */
let u: undefined = undefined;
let n: null = null;

// void：表示没有任何类型。
let noReturn = (): void => {};

// any: 默认类型，表示任何类型，与js没有区别
let a;

// never
let error = () => {
  throw new Error("Something failed");
};
let endless = () => {
  while (true) {}
};
```
