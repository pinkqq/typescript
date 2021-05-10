我们来为应用加入状态管理，使其更利于维护。

## 安装包

- `redux-thunk`：`Redux` 的异步解决方案

- `react-redux` 的两个最主要功能：
  1.  `Provider`：提供包含 `store` 的 `context`
  2.  `connect`：连接容器组件和展示组件；

```
yarn add redux react-redux redux-thunk
```

## 入口文件

在入口文件中，我们引入了 `react-redux` 提供的 `Provider` 组件。将 `Provider` 作为应用的外层容器并传入 `store`，容器内的所有组件都可以访问 `store`。

```ts
/*
 ** src/index.tsx
 */
import React from "react";
import ReactDOM from "react-dom";
import { Provider } from "react-redux";
import Root from "./routers";
import { store } from "./redux/store.ts";

ReactDOM.render(
  <Provider store={store}>
    <Root />
  </Provider>,
  document.querySelectorAll(".app")[0]
);
```

## Redux

### 创建 store

调用 `createStore` 创建 `store`，第一个参数是所有 `reducer` 的集合，第二个参数通过函数组合为 `redux` 添加 `thunk` 中间件。

```ts
/*
 ** src/redux/store.ts
 */
import { createStore, compose, applyMiddleware } from "redux";
import thunk from "redux-thunk";

import rootReducer from "./rootReducer";

const store = createStore(rootReducer, compose(applyMiddleware(thunk)));
export default store;
```

### rootReducer

通过 `combineReducers` 将所有 `reducer` 整合。

```ts
/*
 ** src/redux/rootReducer.ts
 */
import { combineReducers } from "redux";
import employee from "./employee";

const reducers = { employee };
export default combineReducers(reducers);
```

### employee

新建文件夹 `src/reduce/employee`， 存放 `employee` 与 `store` 相关的所有操作，便于管理。

```ts
/*
 ** src/redux/employee/index.ts
 */

import { Dispatch } from "redux";
import { EmployeeRequest, EmployeeResponse } from "../../interface/employee";

import { get } from "../../utils/request";
import { GET_EMPLOYEE_URL } from "../../constans/urls";
import { GET_EMPLOYEE } from "../../constans/actions";

type State = Readonly<{ employeeList: EmployeeResponse }>;
type Action = { type: string; payload: EmployeeResponse };

// 初始状态
const initialState: State = { employeeList: undefined };

export function getEmployee(param: EmployeeRequest) {
  return (dispatch: Dispatch) => {
    get(GET_EMPLOYEE_URL, param).then((res) => {
      dispatch({ type: GET_EMPLOYEE, payload: res.data });
    });
  };
}

// eslint-disable-next-line import/no-anonymous-default-export
export default function (state = initialState, action: Action) {
  switch (action.type) {
    case GET_EMPLOYEE:
      return { ...state, employeeList: action.payload };
    default:
      return state;
  }
}
```

`Readonly` 只对一级属性发生作用，比如数组内的元素不受作用。期待未来官方推出 `DeepReadonly`。

## 改造组件

### 最外层容器

- 将 `redux/store` 上的状态映射到组件上的状态。
- 将更新状态的 `action` 映射到组件的属性上，并透传到 `QueryForm` 组件里。

修改代码：

```ts
/*
 ** src/components/employee/index.tsx
 */

interface Props {
  onGetEmployee(param: EmployeeRequest): void;
  employeeList: EmployeeResponse;
}

// 状态映射
const mapStateToProps = (state: any) => ({
  employeeList: state.employee.employeeList,
});

// action映射
const mapDispatchToProps = (dispatch: Dispatch) =>
  bindActionCreators({ onGetEmployee: getEmployee }, dispatch);

export default connect(mapStateToProps, mapDispatchToProps)(Employee);
```

### QueryForm

```ts
interface Props extends FormProps {
  getData(data: EmployeeRequest): void;
}
class QueryForm extends Component<Props, EmployeeRequest> {

  ...

  queryEmployee = (param: EmployeeRequest) => {
    this.props.getData(param);
    // get(GET_EMPLOYEE_URL, param).then((response) => {
    //   this.props.onDataChange(response.data);
    // });
  };
}
```
