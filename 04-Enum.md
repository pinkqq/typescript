## 枚举类型

### 问题：一个判断角色的例子

- ```js
  function initByRole(role) {
    if (role === 1 || role === 2) {
      // do sth
    } else if (role === 3 || role === 4) {
      // do sth
    } else if (role === 5) {
      // do sth
    } else {
      // do sth
    }
  }
  ```
  - 可读性差：很难记住数字的含义
  - 可维护性差：硬编码（数字与角色绑定），牵一发而动全身

### 用枚举来解决它：

- ```ts
  const enum Role {
    Reporter,
    Developer,
    Maintainer,
    Owner,
    Guest,
  }
  function initByRole(role: Role) {
    if (role === Role.Reporter || role === Role.Developer) {
      // do sth
    } else if (role === Role.Maintainer || role === Role.Owner) {
      // do sth
    } else if (role === Role.Guest) {
      // do sth
    } else {
      // do sth
    }
  }
  ```

### 枚举知识点

- **枚举**：一组有名字的常量集合（如通讯录）

  - **数字枚举**

    ```ts
    // 数字枚举
    enum Role {
      Reporter, // 默认从 0 开始
      Developer = 3, // 3
      Maintainer, // 4
      Owner,
      Guest,
    }

    console.log(Role);
    /* 
      {
        0: "Reporter", 
        3: "Developer", 
        4: "Maintainer", 
        5: "Owner", 
        6: "Guest", 
        Reporter: 0, 
        Developer: 3, 
        Maintainer: 4, 
        Owner: 5, 
        Guest: 6
      }
    */
    ```

  - **字符串枚举**

    ```ts
    // 字符串枚举
    enum Message {
      Success = "恭喜你，成功了",
      Fail = "很遗憾，失败了",
    }
    console.log(Message);
    /*
      {Success: "恭喜你，成功了", Fail: "很遗憾，失败了"}
    */
    ```

  - **异构枚举（容易混淆，不推荐使用）**

    ```ts
    // 异构枚举（容易混淆，不推荐使用）
    enum Answer {
      N, // 0
      Y = "yes",
    }
    ```

  - **枚举成员**
    - 枚举成员的值**只可读**，不可修改
    - ```ts
      enum Char {
        // const member: 在编译过程中计算结果
        a,
        b = Char.a,
        c = 1 + 3,
        // computed member: 在执行过程中计算结果
        d = [1, 2, 3].length,
        e = Math.random(),
      }
      ```
      ```js
      var Char;
      (function (Char) {
        // const member: 在编译过程中计算结果
        Char[(Char["a"] = 0)] = "a";
        Char[(Char["b"] = 0)] = "b";
        Char[(Char["c"] = 4)] = "c";
        // computed member: 在执行过程中计算结果
        Char[(Char["d"] = [1, 2, 3].length)] = "d";
        Char[(Char["e"] = Math.random())] = "e";
      })(Char || (Char = {}));
      ```
  - **常量枚举**
    ```ts
    const enum Month {
      Jan,
      Feb,
      Web,
    }
    let month = [Month.Jan, Month.Feb, Month.Web];
    // var month = [0 /* Jan */, 1 /* Feb */, 2 /* Web */];
    ```
  - **枚举类型**

    - 有三种情况 枚举 和 枚举值 可以作为单独类型存在

      ```ts
      // 枚举类型
      enum E {
        a,
        b,
      }
      enum F {
        a = 1,
        b = 3,
      }
      enum G {
        a = "hello",
        b = "ts",
      }

      let e1: E = 3;
      let e2: E.a = 3;
      /* e1 === e2; error! */

      let f1: F = 5;
      let f2: F.b = 5;
      /* f1 === f2; error! */

      let g1: G = G.a;
      let g2: G.a = G.a; /* G.a类型 只能是 G.a */
      ```

- **实现原理**：

  ```js
  // 数字枚举
  /*
   ** 反向映射，
   ** 枚举的 key 作为对象的 value，枚举的 value 作为对象的 key。
   */
  var Role;
  (function (Role) {
    Role[(Role["Reporter"] = 0)] = "Reporter";
    Role[(Role["Developer"] = 1)] = "Developer";
    Role[(Role["Maintainer"] = 2)] = "Maintainer";
    Role[(Role["Owner"] = 3)] = "Owner";
    Role[(Role["Guest"] = 4)] = "Guest";
  })(Role || (Role = {}));

  // 字符串枚举
  var Message;
  (function (Message) {
    Message["Success"] = "\u606D\u559C\u4F60\uFF0C\u6210\u529F\u4E86";
    Message["Fail"] = "\u5F88\u9057\u61BE\uFF0C\u5931\u8D25\u4E86";
  })(Message || (Message = {}));
  console.log(Message);
  ```
