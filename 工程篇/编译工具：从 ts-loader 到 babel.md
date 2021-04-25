# TS 编译工具：从 ts-loader 到 Babel

## ts-loader

这是用于 `webpack` 的 `TypeScript` 加载器，将 `TypeScript` 编译成 `JavaScript`。

`ts-loader` 在内部是调用了 `TypeScript` 的官方编译器 -- `tsc`。所以，`ts-loader` 和 `tsc` 是共享 `tsconfig.json`。

### 安装

```
yarn add ts-loader --dev
```

or

```
npm install ts-loader --save-dev
```

如果还没有安装 `TypeScript`，你需要先安装一下：

```
yarn add typescript --dev
```

or

```
npm install typescript --save-dev
```

### 开始

正常使用 `webpack` 即可。

### Options

有两种类型的 `Options`：`TypeScript options`（也称为 “编译器 options” ）和 `loader options`。`TypeScript options` 应该通过 `tsconfig.json` 文件设置。`loader options` 可以通过 webpack 配置中的 options 属性指定：

```js
module.exports = {
  ...
  module: {
    rules: [
      {
        test: /\.tsx?$/,
        use: [
          {
            loader: 'ts-loader',
            options: {
              transpileOnly: true // 只做语言转换，而不做类型检查
            }
          }
        ]
      }
    ]
  }
}
```

`transpileOnly` 快速构建一个项目。

- `transpileOnly: false`
  语言转换 + 类型检查 = 3290 ms

  ```
  > webpack --mode production --config ./build/webpack.config.js

  clean-webpack-plugin: options.output.path not defined. Plugin disabled...
  asset index.html 327 bytes [emitted]
  asset app.js 89 bytes [emitted] [minimized] (name: main)
  ./src/index.ts 102 bytes [built] [code generated]
  webpack 5.27.2 compiled successfully in 3290 ms
  ```

- `transpileOnly: true`
  only 语言转换 = 598 ms

  ```
  > webpack --mode production --config ./build/webpack.config.js

  clean-webpack-plugin: options.output.path not defined. Plugin disabled...
  asset index.html 327 bytes [compared for emit]
  asset app.js 89 bytes [compared for emit] [minimized] (name: main)
  ./src/index.ts 102 bytes [built] [code generated]
  webpack 5.27.2 compiled successfully in 598 ms
  ```

- `transpileOnly: true` + `fork-ts-checker-webpack-plugin`
  `transpileOnly: true` 配合插件 `fork-ts-checker-webpack-plugin`，可以补全类型检查的功能。

  ```
  npm i fork-ts-checker-webpack-plugin -D
  ```

  ```js
  const ForkTsCheckerWebpackPlugin = require("fork-ts-checker-webpack-plugin");
  module.exports = {
    ...
    plugins:[
      ...
      new ForkTsCheckerWebpackPlugin()
    ]
  }
  ```

  这时，你的项目中有类型错误，编译就会报错。

  编译通过耗时：2289 ms。

  ```
  > webpack --mode production --config ./build/webpack.config.js

  clean-webpack-plugin: options.output.path not defined. Plugin disabled...
  asset index.html 327 bytes [compared for emit]
  asset app.js 89 bytes [emitted] [minimized] (name: main)
  ./src/index.ts 102 bytes [built] [code generated]
  webpack 5.27.2 compiled successfully in 2289 ms
  ```

## awesome-typescript-loader

`awesome-typescript-loader` 的创建主要是为了加快项目中的编译速度。

与`ts-loader`的主要区别：

- 更适合与 `Babel` 集成，使用 `Babel` 的转义和缓存。
- 不需要安装独立的插件，就可以把类型检查放在独立进程中。

### 安装

```
npm install awesome-typescript-loader --save-dev
```

```js
module.exports = {
  ...
  module: {
    rules: [
      {
        test: /\.tsx?$/,
        use: [
          {
            loader: 'awesome-typesscript-loader',
            options: {
              transpileOnly: false
            }
          }
        ]
      }
    ]
  }
}
```

### 跑一下

- `transpileOnly: false`
  ```
  webpack 5.27.2 compiled successfully in 3392 ms
  ```
- `transpileOnly: true`

  ```
  webpack 5.27.2 compiled successfully in 2411 ms

  ```

- `transpileOnly: true` + 自带插件 `CheckerPlugin`
  ```
  webpack 5.27.2 compiled successfully in 2529 ms
  ```

### ts-loader 与 awesome-typescript-loader 编译时间对比

|          loader           | 默认配置 | transpileOnly | transpileObly + 类型检查进程 |
| :-----------------------: | :------: | :-----------: | :--------------------------: |
|         ts-loader         |  3200+   |      600      |            2200+             |
| awesome-typescript-loader |  3300+   |     2400+     |    2500+ (类型检查有疏漏)    |

## Babel

为什么有了 `Typescript`，还需要 `Babel`？看一下对比：

|       |          编译能力           | 类型检查 | 插件 |
| :---: | :-------------------------: | :------: | :--: |
|  tsc  | ts(x),js(x) --> es 3/5/6... |    有    |  无  |
| Babel | ts(x),js(x) --> es 3/5/6... |    无    | 丰富 |

- Babel 7 之前，是不支持 TS 的

  编译流程是这样的：`TS > TS 编译器 > JS > Babel > JS (再次)`

- Babel 7

  实现了“只有一个 Javascript 编译器” 的梦想！通过允许 Babel 作为唯一的编译器来工作，就再也没必要利用一些复杂的 Webpack 魔法来管理、配置或者合并两个编译器。

### Babel 是如何处理 TypeScript 的？

它移除了 `TypeScript`。

是的，它将 `TypeScript` 全部转换为常规 `JavaScript`，然后再一如既往的操作。

因为 `Babel` 的优秀的缓存和单文件散发架构，`Babel` + `TypeScript` 的组合套餐会提供了更快的编译。这是 `Babel` 在编译过程中剥离 `TypeScript` 的第一个优势。

而 **类型检查** 呢？那只在当你准备好的时候进行。这是 `Babel` 在编译过程中剥离 `TypeScript` 的第二个优势。

### 创建一个 babel 工程

```json
// package.json
{
  ...
  // 指定输出文件 dist，指定扩展名 "ts,tsx"
  "scripts": {
    "build": "babel src --out-dir dist --extensions \".ts,.tsx\""
  },
  ...
  "devDependencies": {
    "@babel/cli": "^7.13.16",
    "@babel/core": "^7.13.16",
    "@babel/plugin-proposal-class-properties": "^7.13.0",  // 支持类属性
    "@babel/plugin-proposal-object-rest-spread": "^7.13.8", // 支持剩余扩展操作符
    "@babel/preset-env": "^7.13.15",
    "@babel/preset-typescript": "^7.13.0" // 编译 ts 文件
  }
}
```

```json
// .babellrc
{
  "presets": ["@babel/preset-env", "@babel/preset-typescript"],
  "plugins": [
    "@babel/plugin-proposal-class-properties",
    "@babel/plugin-proposal-object-rest-spread"
  ]
}
```

```ts
// src/index.ts
// 类的属性、剩余扩展操作符，正好对应两个插件
class A {
  a: number = 1;
}

let { x, y, ...z } = { x: 1, y: 2, a: 3, b: 4, c: 5 };
let obj = { x, y, ...z };
```

```
npm run build

> babel src --out-dir dist --extensions ".ts,.tsx"

Successfully compiled 1 file with Babel (632ms).
```

#### 结合 `TypeScript`

此时，只具备编译功能，再安装 `Typescript` 补全类型检查功能。

```
npm i typescript -D
tsc --init
```

```json
// tsconfig.json
{
  ...
  "compilerOptions":{
    "noEmit":true // 不输出文件，只做类型检查
  }
}
```

配置一下脚本

```json
// package.json
{
  ...
  "script":{
    ...
    "check-type": "tsc --watch"
  }
}
```

我们需要新开一个 terminal，跑 `npm run check-type`，就 ok 。

### ~~有四种语法~~，有两种语法，Babel 无法编译

- 常量枚举

  ```ts
  const enum A {
    X,
  }
  ```

- 历史遗留风格的 `import/export` 语法
  `import foo = require(...)` 和 `export = foo`。
- ~~`namespace` 命名空间~~:
  从 v7.6.0 起，支持 TypeScript 命名空间的编译。
- ~~类型断言~~
  支持 `as` 方式

## 如何选择 TypeScript 编译工具

- 如果没有使用 `Babel`，首选 `TypeScript` 自带编译器（配合 `ts-loader` 使用）
- 如果项目中有 `Babel`，安装 `@babel/preset-typescript`，配合 `tsc` 做类型检查。
- 两种编译器不要混用。

End.
