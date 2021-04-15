## 类

```ts
class Dog {
  constructor(name: string) {
    this.name = name;
  }
  name: string; // 类的属性
  run() {
    return this.name + " running!";
  }
}
let dog = new Dog("littlered");

console.log(Dog.prototype); // 原型方法：{run: ƒ, constructor: ƒ}
console.log(dog); // 实例属性：Dog {name: "babe"}
```

这个类有 3 个成员：一个`构造函数`，一个叫 name 的`属性`，一个 run `方法`。
我们在引用任何一个类成员的时候都用了 `this`。 它表示我们访问的是类的成员。
我们使用 `new` 构造了 `Dog` 类的一个实例。 它会调用之前定义的 `构造函数` ，创建一个 `Dog` 类型的新对象，并执行构造函数初始化它。

> **注意**：
> 无论在 ts 还是 es 中，类的成员 **属性** 都是 **实例属性**，而不是原型属性 。
> 类的成员 **方法** 都是**原型方法**。
>
> 解释一下：JS 的继承方式是原型式继承，原型上的属性和方法是所有实例共享的，不需要共享的就放在构造函数中（也就是实例自己的属性和方法）。当调用实例的属性或方法时，先看实例自身有没有，如果没有就会沿着原型链查找。

- **与 es 不同，属性必须有初始值，或在构造函数中被初始化**

  - 构造函数

    ```ts
    class Dog {
      constructor(name: string) {
        this.name = name;
      }
      name: string;
    }
    ```

  - 初始值/可选属性

    ```ts
    class Dog {
      constructor() {}
      name: string = "";
    }
    ```

    ```ts
    class Dog {
      constructor() {}
      name?: string;
    }
    ```

## 继承

```ts
class Husky extends Dog {
  constructor(name: string, color: string) {
    super(name);
    this.color = color;
  }
  color: string;
}
```

- `super` 作为**函数**调用时，代表**父类的构造函数**。
- `ES6` 要求，子类的构造函数必须执行一次 `super` 函数。并且必须在使用 `this` 关键字之前使用。

## 成员修饰符

> 成员修饰符是 ts 相对 es 的扩展

> ⚠️ 公有、私有、受保护的修饰符，都是控制可见性

- **默认为 `public`，公有**
  - 成员是可见的
- **`private`，私有**

  - 不能在声明它的类的外部访问。

    ```ts
    class Dog {
      constructor(name: string) {
        this.name = name;
        this.pri(); // ok!
      }
      private pri() {}
    }

    Dog.pri(); // error!
    let dog = new Dog("wang");
    dog.pri(); // error!

    class Husky extends Dog {
      constructor(name: string) {
        super(name);
        this.pri(); // error!
      }
    }
    ```

  - 为构造函数添加 `private`：此类不能被实例化，也不能被继承

    ```ts
    class Dog {
      private constructor(name: string) {
        this.name = name;
      }
    }

    let dog = new Dog("wang"); // error!
    class Husky extends Dog {} // error!
    ```

- **`protected`，受保护**

  - 只能在类和它的子类内部被访问。

    ```ts
    class Dog {
      constructor(name: string) {
        this.name = name;
        this.pro(); // ok!
      }
      protected pro() {}
    }

    Dog.pro(); // error!
    let dog = new Dog("wang");
    dog.pro(); // error!

    class Husky extends Dog {
      constructor(name: string) {
        this.pro(); // ok!
      }
    }
    ```

  - 为构造函数添加 `protected`：此类不能被实例化，可以被继承。**（作为基类）**

    ```ts
    class Dog {
      protected constructor(name: string) {
        this.name = name;
      }
    }

    let dog = new Dog("wang"); // error!
    class Husky extends Dog {
      ...
    } // ok!
    ```

#### `readonly` 修饰符

> 只读属性必须在声明时或构造函数里被初始化。

```ts
class Dog {
  constructor() {}
  readonly legs: number = 4;
}
let dog = new Dog();
dog.legs = 5; // error!
```

```ts
class Dog {
  constructor(legs: number) {
    this.legs = legs;
  }
  readonly legs: number;
}
```

#### 为构造函数的参数添加修饰符

- **作用：** 将参数自动变成实例的属性，可省略参数的定义。

## 静态属性

> 这些属性存在于**类本身**上面而不是类的实例上。
> 只能通过**类名**访问。

```ts
class Dog {
  constructor() {
    this.name; // error!
    Dog.name; // ok!
  }
  static name: string = "static-name";
}

class Huskey extends Dog {
  constructor() {
    Huskey.name; // ok!
  }
}
```

## 抽象类

> 只能被继承，不能被实例化的类。

```ts
abstract class Animal {
  constructor() {}
}
let animal = new Animal(); // error!
class Cat extends Animal {
  constructor() {
    super();
  }
} // ok!
```

- **抽象方法**：不做具体的实现（明确知道子类中会有不同的实现）

  - 抽离出代码的共性，有利于代码的复用和扩展
  - 实现多态

  ```ts
  abstract class Animal {
    constructor() {}
    abstract sleep(): void;
  }

  class Cat extends Animal {
    constructor() {
      super();
    }
    sleep() {
      console.log("cat sleep!");
    }
  }
  let cat = new Cat();

  class Bear extends Animal {
    constructor() {
      super();
    }
    sleep() {
      console.log("bear sleep!");
    }
  }
  let bear = new Bear();

  let animals: Animal[] = [cat, bear];

  animals.forEach((a) => a.sleep());
  // cat sleep! bear sleep!
  ```

### this 属性

- **链式调用**

```ts
class WorkFlow {
  step1() {
    return this;
  }
  step2() {
    return this;
  }
}
new WorkFlow().step1().step2();
```

- **原型链查找**

```ts
class MyWork extends WorkFlow {
  next() {
    return this;
  }
}
new MyWork().next().step1();
```
