![ts-react.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8aebd83743ad48f0abadbbf29c0901e5~tplv-k3u1fbpfcp-watermark.image)

## 有两种初始化方案

我们可以通过两种方式创建项目：手动配置和使用脚手架；手动配置为了对项目的配置有一个基本了解，之后的开发还是基于脚手架开展。

## 手动配置

- **创建 [基本的 ts 环境](https://juejin.cn/post/6943156333642416164)**
- **安装 react 及其声明文件**
  ```
  npm i react react-dom
  ```
  ```
  npm i -D @types/react @types/react-dom
  ```
- **修改配置项**

  `TypeScript` 具有三种 `JSX` 模式：`preserve`、`react` 和 `react-native`。 这些模式只在代码生成阶段起作用 - 类型检查并不受影响。

  - 在 `preserve` 模式下生成代码中会保留 `JSX` 以供后续的转换操作使用（比如：`Babel`）。 另外，输出文件会带有 `.jsx` 扩展名。
  - `react` 模式会生成 `React.createElement`，在使用前不需要再进行转换操作了，输出文件的扩展名为`.js`。
  - `react-native` 相当于 `preserve`，它也保留了所有的 `JSX`，但是输出文件的扩展名是`.js`。

  | 模式         | 输入     | 输出                       | 输出文件扩展名 |
  | ------------ | -------- | -------------------------- | -------------- |
  | preserve     | \<div /> | \<div />                   | .jsx           |
  | react        | \<div /> | React.createElement("div") | .js            |
  | react-native | \<div /> | \<div />                   | .js            |

  ```json
  // tsconfig.json
  {
    "compilerOptions": {
      "jsx": "react"
    }
  }
  ```

- **修改入口文件配置**

  `src/index.ts` 修改文件拓展名 `src/index.tsx`

  ```js
  // webpack.base.config.js
  module.exports = {
    entry: "./src/index.tsx",
    ...
  };
  ```

  ```json
  // package.json
  {
    ...
    "main": "./src/index.tsx"
  }
  ```

- **调试一下，看看行不行**

  ```tsx
  // src/components/demo/Hello.tsx

  import React from "react";

  interface Greeting {
    name: string;
  }

  const Hello = (props: Greeting) => <h1>hello {props.name}</h1>;

  export default Hello;
  ```

  ```tsx
  // src/index.tsx

  import React from "react";
  import ReactDOM from "react-dom";
  import Hello from "./components/demo/Hello";

  ReactDOM.render(
    <Hello name="typescript" />,
    document.querySelectorAll(".app")[0]
  );
  ```

  `npm start` 后，在 `http://localhost:8080/` 可以看到 `hello typescript` 就 ok 。

- **优化 webpack 打包**

  因为 `react` 库体积是很大的，一般情况将库文件和业务文件拆分为两个文件，从而充分利用浏览器的缓存。

  **修改一下 `webpack` 配置：**

  ```js
  // webpack.base.config.js
  module.exports = {
    // 为入口文件单独提供一个名称
    entry: {
      app: "./src/index.tsx",
    },
    // 修改输出文件名，[文件名].[8位哈希: 为了防缓存加的版本号]
    output: { filename: "[name].[chunkhash:8].js" },
    ...
    optimization: {
      // webpack4 提供了新的拆包方式
      splitChunks: {
        // 默认抽离出 node_modules 打包成单独文件
        chunks: "all",
      },
    },
  };
  ```

  `npm run build` 后，得到 `dist`（生产环境下）：

  ```
  // mode=production
  --dist
    |--935.e5091ef8.js // 库代码
    |--935.e5091ef8.js.LICENSE.txt // 注释
    |--app.ec1f4b01.js // 业务代码
    |--index.html
  ```

  `--mode development` 和 `--mode production` 生成结果会不一样。

  `Webpack 5 `默认压缩代码工具为 `terser-webpack-plugin`，无需安装，默认使用，会将注释提取到单独的 `LICENSE` 文件中。不想要 `LICENSE` 文件的 [戳 这个配置](https://webpack.docschina.org/plugins/terser-webpack-plugin/)。

## 使用脚手架

一行命令定乾坤：

```
npx create-react-app ts-react-app --typescript
```

解读命令：基于 `typescript` 创建一个叫 `ts-react-app` 的 `react` 工程。

**`npx`** 只是安装到临时目录，使用完后自动删除；这样可以避免全局安装，每次使用（ `create-react-app` 脚手架）都是最新的版本。

**Create React App** 是一个官方支持的创建 `React` 单页应用程序的方法，它提供了一个零配置的现代构建设置。

### 目录结构

对工程按我们的需求进行一下改造：

```
|--public // 该目录下的内容 会被拷贝到 最终生成的项目的 根目录下
    |--index.html // 首页模版，所有构建后的脚本都会被注入到这里（必需）
    |--favicon.ico // 静态文件（图标、字体..），对静态文件的引用方式：如 href="%PUBLIC_URL%/favicon.ico"
|--src // 项目源码
    |--components
        |--demo
            |--Hello.tsx
    |--index.tsx // 整个工程的入口文件
    |--react-app-env.d.ts
|--.gitignore
|--config-overrides.js
|--package.json
|--README.md
|--tsconfig.json
|--yarn.lock
```

我们在 `package.json` 中并看不到 `Webpack` 或 `Babel` 等工具。 其实它们是预先配置好并且隐藏的，它们都被封装到了 `react-scripts` 中。

### package.json

我们自己加装的包:

```json
// package.json
{
  ...
  // 替换脚本 react-scripts -> react-app-rewired
  "scripts": {
    "start": "react-app-rewired start",
    "build": "react-app-rewired build",
    "test": "react-app-rewired test"
  },

  "dependencies": {
    ...
    "antd": "^4.15.3", // UI 组件库
    "axios": "^0.21.1", // 发送 ajax 请求
    "react-router-dom": "^5.2.0" // 路由
  },

  "devDependencies": {
    "@types/react-router-dom": "^5.1.7",
    "babel-plugin-import": "^1.13.3", // 实现 antd 的按需加载
    "customize-cra": "^1.0.0", // 看下面
    "http-proxy-middleware": "^1.3.0", // 把请求代理转发到其他服务器的中间件
    "http-server": "^0.12.3", // 简单的零配置命令行http服务器。
    "react-app-rewired": "^2.1.8"
  }
}
```

**如果项目需要单独配置或者覆盖原始 webpack 配置，该怎么办？**

- 方法一：使用 `npm run eject` 命令将 `webpack.config.js` 暴露出来。然后在该配置文件中进行修改。但这是极端且不推荐的，原因有二：
  - 该命令是不可逆的。一旦执行了此命令 `webpack.config.js` 文件就永久的暴露出来。
  - 如果只是修改一个很小的配置项。是不是有点大动干戈了？
- 方法二（**推荐**）：利用 **`customize-cra`** 和 **`react-app-rewired`** 通过 `config-overrides.js` 文件覆盖原配置项。

**如何搭建一个 mock-server ?**

**`http-proxy-middleware`** 是把请求代理转发到其他服务器的中间件。**`http-server`** 是简单的零配置命令行 http 服务器。它可以帮助我们开启本地服务器，方便局域网共享。这两者配合可以搭建一个 `mock-server`。

### config-overrides.js

```js
const { override, fixBabelImports } = require("customize-cra");

module.exports = override(
  fixBabelImports("import", {
    // antd 按需加载
    libraryName: "antd",
    libraryDirectory: "es",
    style: "css",
  })
);
```

### src/components/demo/Hello.ts

```tsx
import React from "react";
import { Button } from "antd";

interface Greeting {
  name: string;
}

const Hello = (props: Greeting) => <Button>hello {props.name}</Button>;

export default Hello;
```

### src/index.tsx

```tsx
import React from "react";
import ReactDOM from "react-dom";
import Hello from "./components/demo/Hello";

ReactDOM.render(
  <Hello name="typescript。" />,
  document.querySelectorAll(".app")[0]
);
```

### public/index.html

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>TypeScript In Action</title>
    <link rel="icon" href="%PUBLIC_URL%/favicon.ico" type="image/x-icon" />
  </head>
  <body>
    <div class="app"></div>
  </body>
</html>
```

### it's ok!

```
yarn start
```

![ts-react.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3a267e46611743099365065a639b2931~tplv-k3u1fbpfcp-watermark.image)
