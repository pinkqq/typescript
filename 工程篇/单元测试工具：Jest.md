![ts.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/92fa03a6d6de442fa74ef28b406bf36d~tplv-k3u1fbpfcp-watermark.image)

### 什么是 `Jest` ？

`Jest` 是 `Facebook` 的一套开源的 `JavaScript` 测试框架， 它自动集成了断言、JSDom、覆盖率报告等开发者所需要的所有测试工具，是一款几乎零配置的测试框架。

`TypeScript` 的工具体系基本分为：`Babel系` 和 `非Babel系`。测试工具也不例外，分为 `ts-jest` 和 `babel-jest`。

## ts-jest & babel-jest

### 安装依赖

- ts-jest
  ```
  npm i jest ts-jest @types/jest -D
  ```
- ts-babel
  在 `babel` 环境中
  ```
  npm i jest babel-jest @types/jest -D
  ```
  插播一下：安装 `babel/jest`，可能会遇到这个报错
  ```
  The authenticity of host 'github.com (192.30.255.112)' can't be established
  ```
  这是少了密钥文件，需要 [将密钥添加到 SSH 代理](https://docs.github.com/cn/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)。

### 配置脚本

```json
// package.json
{
  ...
  "scripts": {
    "test": "jest"
  }
}
```

- `ts-jest`
  初始化 `jest.config.js`
  ```
  npx ts-jest config:init
  ```

### 编写测试用例

```ts
// src/math.ts
function add(a: number, b: number): number {
  return a + b;
}

function sub(a: number, b: number): number {
  return a - b;
}
module.exports = { add, sub };
```

```ts
// test/math.test.ts
const math = require("../src/math");

test("add 1+1=2", () => {
  expect(math.add(1, 2)).toBe(3);
});

test("sub 1-2=-1", () => {
  expect(math.sub(1, 2)).toBe(-1);
});
```

### 两者差异

`ts-jest` 在测试文件中，可以检查类型错误，而 `babel/jest` 不可以，需要 `tsc` 辅助（新开一个 `terminal）`。
