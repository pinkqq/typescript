## 项目需求

一个简单的员工管理系统，分为两部分功能：员工管理和系统设置。在员工管理部分，可以通过员工的姓名和部门进行查询员工的信息；在系统设置部分，进行简单的系统配置。

## 初始化项目

### 手动撸

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

  **开始修改 `webpack` 配置：**

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

### 使用脚手架

一行命令定乾坤：

```
npx create-react-app ts-react-app --typescript
```
