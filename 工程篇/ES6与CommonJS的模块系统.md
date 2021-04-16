## 前端模块化

随着前端系统越来越复杂，多人协作开发成为了常态，模块化开发方式得到了广泛的认可。
关于模块化开发方式也出现过很多尝试，目前流行的 js 模块化规范有 `CommonJS`、`AMD`、`CMD` 以及 `ES6` 的模块系统。

## 回顾基本操作

### ES6

- 目录
  ```
  -- a.ts;
  -- b.ts;
  -- c.ts;
  ```
  `c` 依赖 `b` , `b` 依赖 `a`.
- a.ts

  ```ts
  export const src = "hello";
  ```

- b.ts（导入情况）

  ```ts
  // 单独导出
  export const a = "i am a.";

  // 批量导出
  let b = "b";
  let c = "c";
  export { b, c };

  // 导出接口
  export interface Person {
    name: string;
    age: number;
  }

  // 导出函数
  export function func() {}

  // 导出时，改别名
  let g = "g";
  export { g as G };

  // 默认导出
  export default function () {}

  // 引入外部模块，改别名
  export { src as Hello } from "./a";
  ```

- c.ts（导出情况）

  ```ts
  import { a, b, c } from "./b"; // 批量导入
  import { Person } from "./b"; // 导入接口
  import { func as Func } from "./b"; // 导入时，起别名
  import * as All from "./b"; // 导入模块中所有成员，绑定到 All 上
  import Default from "./b"; // 不加{}，导入默认模块

  let lily: Person = {
    name: "lily",
    age: 18,
  };
  ```

### CommonJS

`node` 是 `CommonJS` 的一种实现

- 目录
  ```
  -- a.node.ts
  -- b.node.ts
  -- c.node.ts
  ```
  `c` 依赖 `a` 和 `b`。
- a.node.ts
  ```ts
  // 整体导出
  let a = "a";
  module.exports = a;
  ```
- b.node.ts

  ```ts
  // exports === module.exports
  // 导出多个变量
  module.exports.a = "a";
  exports.b = "b";
  ```

- c.node.ts
  ```ts
  const c1 = require("./a.node");
  const c2 = require("./b.node");
  ```

`node` 命令默认寻找 `js` 文件，所以我们需要安装 `ts-node` 来帮我们查找 `ts` 文件。

```
ts_in_action xqq$ npm i ts-node -g
ts_in_action xqq$ ts-node ./src/node/c.node.ts
a
{ a: 'a', b: 'b' }
```

### 生产环境

- tsconfig.json

  ```json
  "target": "es5",          /* Specify ECMAScript target version: 'ES3' (default), 'ES5', 'ES2015', 'ES2016', 'ES2017', 'ES2018', 'ES2019', 'ES2020', or 'ESNEXT'. */
  "module": "commonjs",     /* Specify module code generation: 'none', 'commonjs', 'amd', 'system', 'umd', 'es2015', 'es2020', or 'ESNext'. */
  ```

- `tsc`
  ```
  // 默认 -target es3
  ts_in_action xqq$ tsc ./src/es6/b.ts -t '目标'
  // target 是 es3 或 es5，module 默认指定为 commonjs。
  // target 是 es6，module 默认指定为 es6。
  ```

#### typescript 对模块兼容性的处理

- **模块间的冲突在哪里？**
  用 `tsc` 默认策略处理 `ts` 文件时，输出如下

  ```js
  ...
  // 默认导出，无需函数名
  exports["default"] = (function () {
      console.log("i am default");
  });
  ...
  ```

  为什么这里要把默认导出处理为 `exports["default"]` 呢？
  因为 `ES6` 允许顶级导出（export default）和次级导出同时存在。但是`CommonJS` 不允许。

  - `module.exports` 覆盖 `exports`

    ```ts
    module.exports = {};
    exports.a = "a";
    exports.b = "b";
    ```

    ```
    ts_in_action xqq$ ts-node ./src/node/c.node.ts
    {}
    ```

  - `module.exports` 后者覆盖前者

    ```ts
    module.exports = {};
    module.exports.a = "a";
    ```

    ```
    ts_in_action xqq$ ts-node ./src/node/c.node.ts
    { a: 'a' }
    ```

- **什么时候发生模块冲突？**
  当模块用 `ES6` 导出，以 `CommonJS` 导入时。
  - a.ts
    ```ts
    export let a = "i am a";
    export default function () {
      console.log("i am default");
    }
    ```
  - b.node.ts
    ```ts
    const c1 = require("./a.ts");
    console.log(c1);
    // {a: 'i am a', default: [Function (anonymous)]}
    c1.default(); // "i am default"
    ```
- **typescript 解决方案: `export =` 和 `import = require()`**
  首先，我们尽量避免模块的混用。如果无法避免，我们就这么用 `ts` 来解决兼容性。

  为了支持 `CommonJS` 和 `AMD` 的 `exports`, `TypeScript` 提供了 `export =` 语法。

  - c.ts
    ```ts
    // export == module.exports
    export = function () {
      console.log("i am default");
    };
    ```
    此时，不允许有其他次级导出，有其他成员可以合并一起导出。
    若使用 `export =` 导出一个模块，则必须使用 `TypeScript` 的特定语法 `import module = require("module")` 来导入此模块。
  - b.node.ts

    ```ts
    import c1 = require("./d.ts");
    // "esModuleInterop": true 时，也可以使用下面这种写法：
    // import c1 from "./d"
    ```
