如果一个目录下存在一个 `tsconfig.json` 文件，那么它意味着这个目录是 `TypeScript` 项目的根目录。一个项目可以通过以下方式之一来编译：

- 不带任何输入文件的情况下调用 `tsc`，编译器会从当前目录开始去查找 `tsconfig.json` 文 件，逐级向上搜索父目录。
- 不带任何输入文件的情况下调用 `tsc`，且使用命令行参数 `--project`（或 `-p` ）指定一个包含 `tsconfig.json` 文件的目录。
- 当命令行上指定了输入文件时，`tsconfig.json` 文件会被忽略。

如果 `tsconfig.json` 中没有任何配置，会按照默认配置编译当前目录下所有 `ts` 文件（`ts`、`d.ts`、`tsx`）。

## 文件选项

### files

> 编译器需要编译的**单个文件列表**（相对或绝对文件路径）。

```json
{
  "files": ["src/a.ts"]
}
```

此时执行 `tsc` 命令，编译器会编译 `src/a.ts` 文件。

### include

> 编译器需要编译的文件或者目录

```json
{
  "include": ["src"]
}
```

此时执行 `tsc` 命令，编译器会编译 `src` 下的所有文件。

`includes` 配置支持 `glob` 通配符：`\*`、`?`、`\*\*`：

```json
{
  "include": ["src/*"]
}
```

此时执行 `tsc` 命令，编译器会编译 `src` 下一级目录的文件。

编译器会编译`include`和`files`合并后的内容。

### exclude

> 编译器需要排除的文件或者目录，默认会排除 `node_modules` 目录下的所有文件和声明文件。

```json
{
  "exclude": ["src/lib"]
}
```

- 如果 `exclude` 存在，`exclude` 配置优先级高于 `files` 和 `includes` 配置
- `exclude` 也配置支持 `glob` 通配符：`\*`、`?`、`\*\*`

### extend

> 配置文件之间是可以继承的，我们可以抽离基础部分方便复用。

```json
// tsconfig.base.json
{
  "files": ["src/a.ts"],
  "include": ["src"],
  "exclude": ["src/lib"]
}
```

```json
// tsconfig.json
{
  "extends": "./tsconfig.base.json"
}
```

在主配置文件中，设置选项会覆盖掉继承文件中的相同的配置项。

### compileOnSave

> 在保存文件时，编译器自动编译。

```ts
{
  "compileOnSave":true
}
```

然而，需要 TypeScript 1.8.4 及以上版本，且只有个别 `IDE` 支持：

- Visual Studio 2015 with Update 3
- JetBrains WebStorm
- Atom with atom-typescript

笔者亲测，vscode 安装插件 `TypeScript Auto Compiler` 可行。

## 编译选项

```json
{
  "compilerOptions": {
    "incremental": true, // 增量编译
    "tsBuildInfoFile": "./buildFile", // 增量编译文件的存储位置
    "diagnostics": true, // 打印编译信息

    "target": "es5", // 目标语言的版本
    "module": "commonjs", // 生成代码的模块标准
    "outFile": "./app.js", // 将多个相互依赖的文件生成一个文件，可以用在 AMD 模块中

    "lib": [], // TS 需要引用的库，即声明文件，es5 默认 "dom", "es5", "scripthost"

    "allowJs": true, // 允许编译 JS 文件（js、jsx）
    "checkJs": true, // 允许在 JS 文件中报错，通常与 allowJS 一起使用
    "outDir": "./out", // 指定输出目录
    "rootDir": "./", // 指定输入文件目录（用于输出）

    "declaration": true, // 生成声明文件
    "declarationDir": "./d", // 声明文件的路径
    "emitDeclarationOnly": true, // 只生成声明文件
    "sourceMap": true, // 生成目标文件的 sourceMap
    "inlineSourceMap": true, // 生成目标文件的 inline sourceMap
    "declarationMap": true, // 生成声明文件的 sourceMap
    "typeRoots": [], // 声明文件目录，默认 node_modules/@types
    "types": [], // 声明文件包

    "removeComments": true, // 删除注释

    "noEmit": true, // 不输出文件
    "noEmitOnError": true, // 发生错误时不输出文件
    "noEmitHelpers": true, // 不生成 helper 函数，需额外安装 ts-helpers，目前也可以用 importHelpers 解决。
    "importHelpers": true, // 通过 tslib 引入 helper 函数，文件必须是模块

    "downlevelIteration": true, // 降级遍历器的实现（es3/5）

    "strict": true, // 开启所有严格的类型检查
    "alwaysStrict": false, // 在代码中注入 "use strict";
    "noImplicitAny": false, // 不允许隐式的 any 类型
    "strictNullChecks": false, // 不允许把 null、undefined 赋值给其他类型变量
    "strictFunctionTypes": false, // 不允许函数参数双向协变
    "strictPropertyInitialization": false, // 类的实例属性必须初始化
    "strictBindCallApply": false, // 严格的 bind/call/apply 检查
    "noImplicitThis": false, // 不允许 this 有隐式的 any 类型

    "noUnusedLocals": true, // 检查只声明，未使用的局部变量
    "noUnusedParameters": true, // 检查未使用的函数参数
    "noFallthroughCasesInSwitch": true, // 防止 switch 语句贯穿，分支没有 break
    "noImplicitReturns": true, // 每个分支都要有返回值

    "esModuleInterop": true, // 允许 export = 导出，由import from 导入
    "allowUmdGlobalAccess": true, // 允许在模块中访问 UMD 全局变量
    "moduleResolution": "node", // 模块解析策略
    "baseUrl": "./", // 解析非相对模块的基地址
    "paths": {
      // 路径映射，相对于 baseUrl
      "jquery": ["node_modules/jquery/dist/jquery.slim.min.js"]
    },
    "rootDirs": ["src", "util"], // 将多个目录放在一个虚拟目录下，用于运行时

    "listEmittedFiles": true, // 打印输出的文件
    "listFiles": true // 打印编译的文件（包括引用的声明文件）
  }
}
```

### 增量编译（提高编译速度）

#### incremental

Typescript 编译器在第一次编译时，会生产一个可以存储编译信息的文件；编译器在二次编译时，可以根据此文件进行增量编译。
该文件默认会在根目录下名称为 `tsconfig.tsbuildinfo`

#### tsBuildInfoFile

可以修改编译文件的存储文件夹和文件名。

#### diagnostics

打印编译信息。

#### 组合使用

```json
{
  "compilerOptions": {
    "incremental": true, // 增量编译
    "tsBuildInfoFile": "./buildFile", // 增量编译文件的存储位置
    "diagnostics": true // 打印编译信息
  }
}
```

第一次编译信息：

```
Files:              113
Lines:            85109
Nodes:           263776
Identifiers:      97483
Symbols:          78297
Types:            26645
Instantiations:   20354
Memory used:    149278K
I/O read:         0.06s
I/O write:        0.00s
Parse time:       1.23s
Bind time:        0.58s
Check time:       2.17s
Emit time:        0.04s
Total time:       4.02s
```

第二次编译信息：

```
Files:             113
Lines:           85109
Nodes:          263776
Identifiers:     97483
Symbols:         59089
Types:              78
Instantiations:      0
Memory used:    99709K
I/O read:        0.09s
I/O write:       0.00s
Parse time:      1.58s
Bind time:       0.64s
Check time:      0.00s
Emit time:       0.00s
Total time:      2.22s
```

`Total Time` 可以相差 50%左右。

### 目标语言和模块

#### target

设置目标语言的版本，可设置为 ES3、ES5 和 ES2015 等等，默认为 ES3。

#### module

设置生成代码的模块标准，可以设置为 CommonJS、AMD 和 UMD 等等

#### outFile

将多个相互依赖的文件生成一个文件，可以用在 `AMD` 模块中

#### 组合使用

我们创建两个文件：

```ts
// ./src/amd.ts

let amd: string[] = ["a"];
export = amd;
```

```ts
// ./src/index.ts
import a = require("./amd");

let hello: string = "Hello TypeScript";
document.querySelectorAll(".app")[0].innerHTML = hello;
```

```json
{
  "compilerOptions": {
    "module": "amd",
    "outFile": "./app.js"
  }
}
```

编译后得到一个 `app.js` 文件：

```ts
define("amd", ["require", "exports"], function (require, exports) {
  "use strict";
  var amd = ["a"];
  return amd;
});
define("index", ["require", "exports"], function (require, exports) {
  "use strict";
  Object.defineProperty(exports, "__esModule", { value: true });
  var hello = "Hello TypeScript";
  document.querySelectorAll(".app")[0].innerHTML = hello;
});
var libs = {};
```

### 类库 lib

> 指定 `ts` 需要引用的库，即声明文件。

如果 `target: es5`，默认导入类库 `["dom", "es5", "scripthost"]`。

举例：如果要用到 `es2019` 的方法：

```ts
let array = [1, 2, 3, [4, 5]].flat();
```

```json
{
  "compilerOptions": {
    "lib": ["ES2019.Array"]
  }
}
```

### 编译 JS 文件

#### allowJS

允许编译器编译 JS 文件（js、jsx）。

#### checkJS

允许在 JS 文件中报错，通常与 allowJS 一起使用。

#### outDir

指定输出目录。

#### rootDir

指定输入文件目录（用于输出）。

#### 组合使用

```json
{
  "compilerOptions": {
    "allowJS": true,
    "checkJS": true
  }
}
// ERROR! Cannot write file '/Users/xqq/workspace/qq/code/geekUniversity/TypeScript/ts_in_action/build/webpack.base.config.js' because it would overwrite input file.
```

报错：编译器会默认编译所有的 `js` 文件，包括 `build` 下的 `webpack` 文件。我们需要 `include`，并且编译后的文件会覆盖原文件，所以还需要指定输出目录。

```json
{
  "include": "./src",
  "compilerOptions": {
    "allowJS": true,
    "checkJS": true,
    "outDir": "./out"
  }
}
```

编译后文件目录

```
|-- out
    |-- a.js
    |-- index.js
|-- src
    |-- a.js
    |-- index.ts
```

如果指定 `rootDir: './'` ，得到的目录结构：

```
|-- out
    |-- src
        |-- a.js
        |-- index.js
```

### 声明文件 & sourceMap

#### declaration

编译器编译时，允许生成声明文件（`.d.ts`）。

#### declarationDir

指定声明文件的生成的目录。

#### emitDeclarationOnly

编译器编译时，只允许生成声明文件。

#### sourceMap

编译器编译时，生成目标文件的 sourceMap 文件。

#### inlineSourceMap

编译器编译时，将 sourceMap 生成在 js 文件中。

#### declarationMap

编译器编译时，生成声明文件的 sourceMap。

#### typeRoots

设置声明文件目录，默认 `node_modules/@types` > **types，** 这是声明文件包，如果设置了某一个声明文件，那么编译器只会加载这个声明文件。

#### 组合使用

- 生成声明文件

  ```json
  {
    "declaration": true, // 生成声明文件
    "declarationDir": "./d", // 声明文件的路径
    "emitDeclarationOnly": true // 只生成声明文件
  }
  ```

  编译后，只在 `./d` 文件夹下生成 `index.d.ts` 声明文件。

- sourceMap 文件

  ```json
  {
    "sourceMap": true
  }
  ```

  编译后，生成 `index.js` 和 `index.js.map`。

- inlineSourceMap 文件

  ```json
  {
    "inlineSourceMap": true
  }
  ```

  编译后，生成 `index.js`：

  ```js
  // import a = require("./amd");
  var hello = "Hello TypeScript";
  document.querySelectorAll(".app")[0].innerHTML = hello;
  // let array = [1, 2, 3, [4, 5]].flat(); // 扁平化数组 [1,2,3,4,5]
  //# sourceMappingURL=data:application/json;base64,eyJ2ZXJzaW9uIjozLCJmaWxlIjoiaW5kZXguanMiLCJzb3VyY2VSb290IjoiIiwic291cmNlcyI6WyJpbmRleC50cyJdLCJuYW1lcyI6W10sIm1hcHBpbmdzIjoiQUFBQSwrQkFBK0I7QUFFL0IsSUFBSSxLQUFLLEdBQVcsa0JBQWtCLENBQUM7QUFDdkMsUUFBUSxDQUFDLGdCQUFnQixDQUFDLE1BQU0sQ0FBQyxDQUFDLENBQUMsQ0FBQyxDQUFDLFNBQVMsR0FBRyxLQUFLLENBQUM7QUFFdkQsNkRBQTZEIn0=
  ```

- 声明文件的 sourceMap
  ```json
  {
    "declaration": true,
    "declarationMap": true
  }
  ```
  编译后，生成 `index.js`、 `index.d.ts` 和 `index.d.ts.map`。

### helper 函数

#### noEmitHelpers

设置为 true 时，不生成 helper 函数。

举例子：

```ts
class A {}
class B extends A {}
export = A;
```

编译后：

```js
"use strict";
var __extends =
  (this && this.__extends) ||
  (function () {
    var extendStatics = function (d, b) {
      extendStatics =
        Object.setPrototypeOf ||
        ({ __proto__: [] } instanceof Array &&
          function (d, b) {
            d.__proto__ = b;
          }) ||
        function (d, b) {
          for (var p in b) if (b.hasOwnProperty(p)) d[p] = b[p];
        };
      return extendStatics(d, b);
    };
    return function (d, b) {
      extendStatics(d, b);
      function __() {
        this.constructor = d;
      }
      d.prototype =
        b === null
          ? Object.create(b)
          : ((__.prototype = b.prototype), new __());
    };
  })();
var B = /** @class */ (function () {
  function B() {}
  return B;
})();
var A = /** @class */ (function (_super) {
  __extends(A, _super);
  function A() {
    return (_super !== null && _super.apply(this, arguments)) || this;
  }
  return A;
})(B);
module.exports = A;
```

编译器会自动生成 \_\_extends。
如果配置 `noEmitHelpers: true`，编译后：

```js
"use strict";
var B = /** @class */ (function () {
  function B() {}
  return B;
})();
var A = /** @class */ (function (_super) {
  __extends(A, _super);
  function A() {
    return (_super !== null && _super.apply(this, arguments)) || this;
  }
  return A;
})(B);
module.exports = A;
```

编译后的 `__extends` 未定义。`ts` 为开发者定义了配置项 `importHelpers` 解决此问题。

#### importHelpers

通过 `tslib` 引入 `helper` 函数，文件必须是模块。

```js
"use strict";
var tslib_1 = require("tslib");
var A = /** @class */ (function () {
  function A() {}
  return A;
})();
var B = /** @class */ (function (_super) {
  tslib_1.__extends(B, _super);
  function B() {
    return (_super !== null && _super.apply(this, arguments)) || this;
  }
  return B;
})(A);
module.exports = A;
```

### downlevelIteration

之前迭代器只在编译目标为 `ES6/ES2015` 或者更新版本时可用. 此外, 设计迭代器协议的结构, 比如 `for..of`, 如果编译目标低于 `ES6/ES2015`, 则只能在操作数组时被支持.

`TypeScript 2.3` 在 `ES3` 和 `ES5` 为编译目标时由 `--downlevelIteration` 选项增加了完整的对生成器和迭代器协议的支持。

通过 `--downlevelIteration` 选项, 编译器会使用新的类型检查和输出行为, 尝试调用被迭代对象的 `[Symbol.iterator]()` 方法 (如果有), 或者在对象上创建一个语义上的数组迭代器.

> 注意这需要非数组的值有原生的 `Symbol.iterator` 或者 `Symbol.iterator` 的运行时模拟实现.

使用 `--downlevelIteration` 时, 在 `ES5/ES3` 中 `for..of` 语句, 数组解构, 数组中的元素展开, 函数调用, `new` 表达式在支持 `Symbol.iterator` 时可用, 但即便没有定义 `Symbol.iterator`, 它们在运行时或开发时都可以被使用到数组上.

```ts
// index.ts
let a = [1, 2, 3];
let b = [1, ...a];
```

```js
// downlevelIteration: false
var __spreadArray =
  (this && this.__spreadArray) ||
  function (to, from) {
    for (var i = 0, il = from.length, j = to.length; i < il; i++, j++)
      to[j] = from[i];
    return to;
  };
var a = [1, 2, 3];
var b = __spreadArray([1], a);
```

```js
// downlevelIteration: true
var __read =
  (this && this.__read) ||
  function (o, n) {
    var m = typeof Symbol === "function" && o[Symbol.iterator];
    if (!m) return o;
    var i = m.call(o),
      r,
      ar = [],
      e;
    try {
      while ((n === void 0 || n-- > 0) && !(r = i.next()).done)
        ar.push(r.value);
    } catch (error) {
      e = { error: error };
    } finally {
      try {
        if (r && !r.done && (m = i["return"])) m.call(i);
      } finally {
        if (e) throw e.error;
      }
    }
    return ar;
  };
var __spreadArray =
  (this && this.__spreadArray) ||
  function (to, from) {
    for (var i = 0, il = from.length, j = to.length; i < il; i++, j++)
      to[j] = from[i];
    return to;
  };
var a = [1, 2, 3];
var b = __spreadArray([1], __read(a));
```

### 严格模式

#### strict

表示开启所有严格的类型检查，若 `strict` 为 `true`，`alwaysStrict`、`noImplicitAny`、`strictNullChecks`、`strictFunctionTypes`、`strictPropertyInitialization`、`strictBindCallApply` 和 `noImplicitThis` 选项默认都为 `true`。

#### strictNullChecks

不允许把 `null`、`undefined` 赋值给其他类型变量。

```ts
// strictNullChecks: false
let a: number = 1;
a = null; // OK
a = undefined; // OK
```

#### strictFunctionTypes

不允许函数参数双向协变。

```ts
// strictFunctionTypes: false
interface point2D {
  x: number;
  y: number;
}
interface point3D {
  x: number;
  y: number;
  z: number;
}
let p2d = (value: point2D) => {};
let p3d = (value: point3D) => {};

p3d = p2d;
p2d = p3d; // OK
```

#### strictBindCallApply

严格的 `bind`、`call`、`apply` 检查。

```ts
// strictvindCallApply: true
function add(a: number, b: number) {
  return a + b;
}
add.call(undefined, 1, "2"); // Error!
```

```ts
// strictvindCallApply: false
function add(a: number, b: number) {
  return a + b;
}
add.call(undefined, 1, "2"); // OK!
```

#### noImplicitThis

不允许 `this` 有隐式的 `any` 类型。

```ts
// noImplicitThis: true
class A {
  a: number = 1;
  getA() {
    return function () {
      console.log(this.a); // ERROR
    };
  }
}
new A().getA()(); // Uncaught TypeError: Cannot read property 'a' of undefined
```

```ts
class A {
  a: number = 1;
  getA() {
    return () => {
      console.log(this.a); // OK
    };
  }
}
```

### 模块

#### esModuleInterop

允许 `export = ` 方式导出，也可以用 `import = ` 的方式导入。

```ts
// a.ts
let a: number = 1;
export = a;
```

```ts
// esModuleInterop: false
import a from "./a";
// ERROR: 必须要用 import a require('./a')
```

#### allowUmdGlobalAccess

允许我们在模块中以全局变量的方式访问 `umd` 模块。

#### moduleResolution

模块解析策略，这里提供两种解析策略 `node` 和 `classic`，`ts` 默认使用 `node` 解析策略。

- **classic 模块解析策略**
  适用于 `AMD`、`System`、`ES2015`。

  - **相对方式导入**，`ts` 就会依次解析同级目录 `.ts`、`.d.ts` 文件。

    ```ts
    // /root/src/moduleA.ts

    import { b } from "./moduleB";

    /**
     * /root/src/moduleB.ts
     * /root/src/moduleB.d.ts
     */
    ```

  - **非相对方式导入**，`ts` 会从当前目录的 `node_modules` 目录里查找，如果未找到，会依次向上级目录查找。

    ```ts
    // /root/src/moduleA.ts

    import { b } from "moduleB";

    /**
     * /root/src/node_modules/moduleB.ts
     * /root/src/node_modules/moduleB.d.ts
     *
     * /root/node_modules/moduleB.ts
     * /root/node_modules/moduleB.d.ts
     *
     * /node_modules/moduleB.ts
     * /node_modules/moduleB.d.ts
     */
    ```

- **node 模块解析策略**

  - **相对方式导入**

    ```ts
    // /root/src/moduleA.ts

    import { b } from "./moduleB";

    /**
     * /root/src/moduleB.ts
     * /root/src/moduleB.tsx
     * /root/src/moduleB.d.ts
     * /root/src/moduleB/package.json ( types 属性)
     * /root/src/moduleB/index.ts
     * /root/src/moduleB/index.tsx
     * /root/src/moduleB/index.d.ts
     */
    ```

  - **非相对方式导入**

    ```ts
    // /root/src/moduleA.ts

    import { b } from "moduleB";

    /**
     * /root/src/node_modules/moduleB.ts
     * /root/src/node_modules/moduleB.tsx
     * /root/src/node_modules/moduleB.d.ts
     * /root/src/node_modules/package.json ( “types” 属性)
     * /root/src/node_modules/index.ts
     * /root/src/node_modules/index.tsx
     * /root/src/node_modules/index.d.ts
     *
     * 依次向上目录查找
     *
     * /root/node_modules/moduleB.ts
     * /root/node_modules/moduleB.tsx
     * /root/node_modules/moduleB.d.ts
     * /root/node_modules/package.json ( “types” 属性)
     * /root/node_modules/index.ts
     * /root/node_modules/index.tsx
     * /root/node_modules/index.d.ts
     *
     * /node_modules/moduleB.ts
     * /node_modules/moduleB.tsx
     * /node_modules/moduleB.d.ts
     * /node_modules/package.json ( “types” 属性)
     * /node_modules/index.ts
     * /node_modules/index.tsx
     * /node_modules/index.d.ts
     */
    ```

#### baseUrl

解析非相对模块的基地址，默认为当前目录

#### paths

路径映射，相对于 baseUrl。比如想引入 jquery 的精简版，可以制定他的相对路径。

```json
{
  "compilerOptions": {
    "paths": {
      // 路径映射，相对于 baseUrl
      "jquery": ["node_modules/jquery/dist/jquery.slim.min.js"]
    }
  }
}
```

#### rootDirs（区别于 rootDir）

将多个目录放在一个虚拟目录下，用于运行时。

```json
// ./tsconfig.json
{
  "compilerOptions": {
    "rootDirs": ["src", "out"]
  }
}
```

新建 `out` 目录，用于存放构建之后的文件。

```ts
// ./out/util.ts
let a: number = 1;
export = a;
```

构建之后，`index` 也会被放入 `out` 目录。为了编译之后不需要重新修改路径，我们可以提前虚拟他们在一个目录下。

```ts
// ./src/index.ts
import a require("./util")
```

注意在引入 `a` 时，是引入的当前目录。因为当 `rootDirs` 设置了 `src` 和 `out` 目录时，编译器默认它们属于同级目录。
