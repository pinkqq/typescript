![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/60f4aead025d41bd8984999a41ff584d~tplv-k3u1fbpfcp-watermark.image)

2019 年 1 月，`TypeScript` 官方决定全面采用 `ESLint`，之后也发布 `typescript-eslint` 项目，以集中解决 `TypeScript` 和 `ESLint` 兼容性问题。而之前的两个 `lint` 解决方案都将弃用：

- `typescript-eslint-parser` 已停止维护
- 在完成 `ESLint` 功能后，将弃用 `TSLint` 并帮助用户迁移到 `ESLint`

### TS 官方转向 ESLint 的原因:

1. TSLint 执行规则的方式存在一些框架问题，从而**影响性能**，而修复这些问题会破坏现有的规则。
2. ESLint 的**性能更好**，并且社区用户通常拥有 ESLint 的规则配置（比如 React 和 Vue 的配置），而不会拥有 TSLint 的规则配置。

### 已经有 TypeScript，为什么需要 ESLint ？

![ts.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/399beef1cf5741fdb5da5a5e1ddff59c~tplv-k3u1fbpfcp-watermark.image)

`ts` 编译器主要做类型检查和语言转换，`ESLint` 则可以保持代码风格的统一；两者的功能有部分重合，但也各有职责。

但是，`ESLint` 处理 `ts` 代码会遇到一些问题？因为 `ts` 编译器和 `ESLint` 在开始各自的工作之前都会将代码转换成 `AST`（抽象语法树），而两种语法树是不兼容的。这时，我们可以使用 `typescript-eslint` 项目，它为 `ESLint` 提供了专门解析 ts 代码的编译器，来解决 `TypeScript` 和 `ESLint` 兼容性问题。

![未命名文件 (3).jpg](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e1969849afea428b953718cb9c7a411d~tplv-k3u1fbpfcp-watermark.image)

### 在 TypeScript 中使用 ESLint

#### 安装

```
npm i eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin -D
```

`@typescript-eslint/parser`为 `ESLint` 提供解析器。（别忘了同时安装 `typescript`）
`@typescript-eslint/eslint-plugin` 它作为 `ESLint` 默认规则的补充，提供了一些额外的适用于 `ts` 语法的规则。

#### 创建配置文件

`ESLint` 需要一个配置文件来决定对哪些规则进行检查，配置文件的名称一般是 .`eslintrc.js` 或 `.eslintrc.json`。

```json
// .eslintrc.json
{
  "parser": "@typescript-eslint/parser",
  "plugins": ["@typescript-eslint"],
  "parserOptions": {
    "project": "./tsconfig.json"
  },
  "extends": ["plugin:@typescript-eslint/recommended"],
  "rules": { "@typescript-eslint/no-inferrable-types": "off" }
}
```

`rules` 的取值一般是一个数组，其中第一项是 `off`、`warn` 或 `error` 中的一个，表示关闭、警告和报错。后面的项都是该规则的其他配置。

如果没有其他配置的话，则可以将规则的取值简写为数组中的第一项（上例中的 `@typescript-eslint/no-inferrable-types`）。

`off`、`warn` 和 `error`的含义如下：

- `off`：禁用此规则
- `warn`：代码检查时输出错误信息，但是不会影响到 `exit code`
- `error`：发现错误时，不仅会输出错误信息，而且 `exit code` 将被设为 `1`（一般 `exit code` 不为 `0` 则表示执行出现错误）

#### 检查整个项目的 ts/js 文件

```json
// package.json
{
  ...
  "script": {
    ...
    "lint": "eslint src --ext .js,.ts"
  }
}
```

```
npm run lint
```

#### 在 VSCode 中集成 ESLint 检查

安装 `ESLint` 插件，点击「扩展」按钮，搜索 `ESLint`，然后安装即可。

`VSCode` 中的 `ESLint` 插件默认是不会检查 `.ts` 后缀的，需要在「文件 => 首选项 => 设置 => 工作区」中（也可以在项目根目录下创建一个配置文件 `.vscode/settings.json`），添加以下配置：

```json
{
  ...
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    "vue",
    "html",
    "typescript"
  ]
}
```

### 比较 babel-eslint 和 typescript-eslint

- **babel-eslint:** 支持 `TypeScript` 没有的额外的语法检查，抛弃 `TypeScript`，不支持类型检查。
- **typescript-eslint:** 基于 `TypeScript` 的 `AST`，支持创建基于类型信息的规则（`tsconfig.json`）

两者底层机制不一样，不要一起用。`Babel` 体系建议用 `babel-eslint`，否则可以用 `typescript-eslint`。
