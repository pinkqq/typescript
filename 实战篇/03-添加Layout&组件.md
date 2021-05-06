这一篇，我们来堆砌组件（不包含事件处理和数据请求）。

这部分的知识点 -- [React & Typescript：组件的入门实例](https://juejin.cn/post/6956524370336415780)

## 项目需求

一个简单的员工管理系统，分为两部分功能：员工管理和系统设置。在员工管理部分，可以通过员工的姓名和部门进行查询员工的信息；在系统设置部分，进行简单的系统配置。
![ts-react-app.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3f5298ff67a546e08b49ec14ab55fccc~tplv-k3u1fbpfcp-watermark.image)

## 目录

```
|--src
    |--components
        |--employee
            |--QueryForm.tsx
            |--index.tsx
            |--index.css
        |--setting
            |--index.tsx
            |--index.css
        |--App.css
        |--App.tsx
    |--index.tsx
    |--react-app-env.d.ts

```

## 实现

### Layout

- src/index.tsx

  ```tsx
  import React from "react";
  import ReactDOM from "react-dom";
  import App from "./components/App";

  ReactDOM.render(<App />, document.querySelectorAll(".app")[0]);
  ```

  引入 `<App />` 组件，`App` 是整个应用的框架。

- src/components/App.tsx

  ```tsx
  import React from "react";
  import { Menu, ConfigProvider, Layout } from "antd";
  import zhCN from "antd/lib/locale/zh_CN";

  import Employee from "./employee";
  import Setting from "./setting";

  import "./App.css";

  const { Header, Content, Footer } = Layout;

  const App = () => (
    <ConfigProvider locale={zhCN}>
      <Layout>
        <Header>
          <Menu
            mode="horizontal"
            theme="dark"
            className="menu"
            defaultSelectedKeys={["employee"]}
          >
            <Menu.Item key="employee">员工管理</Menu.Item>
            <Menu.Item key="setting"> 系统设置</Menu.Item>
          </Menu>
        </Header>
        <Content className="contentWrap">
          <div className="content">
            <Employee />
            {/* <Setting /> */}
          </div>
        </Content>
        <Footer className="footer">
          项目地址：{" "}
          <a href="https://github.com/pinkqq/ts-react-app">
            https://github.com/pinkqq/ts-react-app
          </a>
        </Footer>
      </Layout>
    </ConfigProvider>
  );

  export default App;
  ```

  我们拆分了两部分组件：`employee`（员工管理）和 `setting`（系统设置），基础组件用的是标准的 [antd](https://ant.design/components/overview-cn/) 组件。

  `Antd 4.0` 移除了 `LocaleProvider`，请使用 `ConfigProvider`（[全局化配置](https://ant.design/components/config-provider-cn/)） 替代。

  此时缺路由，在后面一篇再添加路由的设置。

- src/components/App.css

  ```css
  .menu {
    line-height: 64px;
  }
  .contentWrap {
    padding: 0 50px;
  }
  .content {
    background: #fff;
    padding: 24px;
    min-height: 480px;
  }
  .footer {
    text-align: center;
  }
  ```

### Employee

- src/components/employee/index.tsx

  ```tsx
  import React, { Component } from "react";

  import { Table } from "antd";

  import QueryForm from "./QueryForm";

  import "./index.css";

  const employeeColumns = [
    {
      title: "姓名",
      dataIndex: "name",
      key: "name",
    },
    {
      title: "部门",
      dataIndex: "department",
      key: "department",
    },
    {
      title: "入职时间",
      dataIndex: "hiredate",
      key: "hiredate",
    },
    {
      title: "职级",
      dataIndex: "level",
      key: "level",
    },
  ];

  class Employee extends Component {
    render() {
      return (
        <>
          <QueryForm />
          <Table columns={employeeColumns} className="table" />
        </>
      );
    }
  }

  export default Employee;
  ```

  `employee` 页面包括两个组件：`QueryForm`（搜索栏）、`Table`（结果表单）；

- src/components/employee/index.css

  ```tsx
  .table {
    margin-top: 20px;
  }
  .toolbar {
    margin: 20px 0;
  }

  ```

- src/components/employee/QueryForm.tsx

  ```tsx
  import React, { Component } from "react";
  import { Form, Input, Button, Select } from "antd";

  const { Option } = Select;

  class QueryForm extends Component {
    render() {
      return (
        <>
          <Form layout="inline">
            <Form.Item>
              <Input placeholder="姓名" style={{ width: 120 }} allowClear />
            </Form.Item>
            <Form.Item>
              <Select placeholder="部门" style={{ width: 120 }} allowClear>
                <Option value={1}>技术部</Option>
                <Option value={2}>运营部</Option>
                <Option value={3}>市场部</Option>
              </Select>
            </Form.Item>
            <Form.Item>
              <Button type="primary">查询</Button>
            </Form.Item>
          </Form>
        </>
      );
    }
  }

  export default QueryForm;
  ```

### Setting

`setting` 包含一个新员工入职提醒功能（`CheckBox` + `Button`）。

- src/components/setting/index.tsx

  ```tsx
  import { Checkbox, Button } from "antd";

  import "./index.css";

  const Setting = () => (
    <>
      <Checkbox>新员工入职邮件提醒</Checkbox>
      <div className="buttonWrap">
        <Button type="primary">保存</Button>
      </div>
    </>
  );

  export default Setting;
  ```

- src/components/setting/index.css

  ```css
  .buttonWrap {
    margin: 20px 0;
  }
  ```
