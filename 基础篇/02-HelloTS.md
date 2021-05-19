### 多年之后，再来 Hello World ！

---

## 新建项目

```
// ts_in_action
 npm init -y
 npm i typescript -g
 tsc --init
```

#### 项目目录

```
---build
    |---webpack.config.js
    |---webpack.base.config.js
    |---webpack.dev.config.js
    |---webpack.pro.config.js
---src
    |---index.ts
    |---tpl
        |---index.html
---package.json
---package-lock.json
---tsconfig.json
```

## hello world

```ts
// src/index.ts
let hello: string = "Hello World";
```

#### 编译

```
$ tsc ./src/index.ts
```

- 还可以通过官网 [playground](https://www.typescriptlang.org/play?#code/FAAhQ) 查看

#### 结果

```js
// src/index.js
var hello = "Hello World";
```

## 配置构建工具（webpack5）

```
npm i webpack webpack-cli webpack-dev-server -D
```

```
---build
    |---webpack.config.js
    |---webpack.base.config.js
    |---webpack.dev.config.js
    |---webpack.pro.config.js
```

- **webpack.config.js**

  - 安装插件

    ```
    npm i -D webpack-merge
    ```

  ```js
  const { merge } = require("webpack-merge");
  const baseConfig = require("./webpack.base.config");
  const devConfig = require("./webpack.dev.config");
  const proConfig = require("./webpack.pro.config");

  module.exports = (env, argv) => {
    let config = argv.mode === "development" ? devConfig : proConfig;
    // 合并文件
    return merge(baseConfig, config);
  };
  ```

- **webpack.base.config.js**

  - 安装插件

    ```
    npm i -D html-webpack-plugin ts-loader
    ```

  ```js
  const HtmlWebpackPlugin = require("html-webpack-plugin");
  const path = require("path");

  module.exports = {
    entry: "./src/index.ts",
    output: {
      filename: "app.js",
      path: path.resolve(__dirname, "../dist"), // 搭配 clean-webpack-plugin 使用
      /*
      webpack 5.20.0+: Clean the output directory before emit.
      可以remove clean-webpack-plugin，直接使用👇
      
      clean: true, 
      
      */
    },
    resolve: {
      extensions: [".js", ".ts", ".tsx"],
    },
    module: {
      rules: [
        {
          test: /\.tsx?$/i,
          use: [
            {
              loader: "ts-loader",
            },
          ],
          exclude: /node_modules/,
        },
      ],
    },
    plugins: [
      // 通过一个模版帮助我们生成网站首页，并将输出文件嵌入
      new HtmlWebpackPlugin({
        template: "./src/tpl/index.html",
      }),
    ],
  };
  ```

- **webpack.dev.config.js**

  ```js
  module.exports = {
    /*
     ** source map: 是一个信息文件，里面储存着位置信息(转换后的代码的每一个位置，所对应的转换前的位置)。
     ** cheap: source map 会忽略文件的列信息
     ** module: 会定位到 ts源码，而不是转译后的 js源码。
     ** eval-source-map: 将source map以 DataURI 形式打包到文件中。
     */
    devtool: "eval-cheap-source-map",
  };
  ```

- **webpack.pro.config.js**

  - 安装插件
    ```
    npm i -D clean-webpack-plugin
    ```

  ```js
  const { CleanWebpackPlugin } = require("clean-webpack-plugin");

  module.exports = {
    plugins: [
      /*
       ** 背景：为了避免缓存，需要在文件后加入hash，如此多次构建会产生很多无用的文件
       ** 作用：通过 clean-webpack-plugin，在每次成功构建之后，帮助我们自动清空dist目录
       */
      new CleanWebpackPlugin(),
    ],
  };
  ```

## .gitignore

```
node_modules
```

## 配置命令

- **package.json**

  ```json
  {
    ...
    "main": "./src/index.ts",
    "scripts": {
      "start": "webpack serve --mode=development --config ./build/webpack.config.js",
      "build": "webpack --mode=production --config ./build/webpack.config.js",
      "test": "echo \"Error: no test specified\" && exit 1"
    },
    ...
  }
  ```

## 模版 html

```html
<!-- src/tpl/index.html -->
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>TypeScript In Action</title>
    <link rel="icon" href="../../static/favicon.ico" type="image/x-icon" />
  </head>
  <body>
    <div class="app"></div>
  </body>
</html>
```

## 修改 index.ts

```ts
let hello: string = "hello typescript";
document.querySelectorAll(".app")[0].innerHTML = hello;
```

```
npm start
```

**:-) 默认访问 `http://localhost:8080/`, 铛 铛 铛--，就可以看到了**
