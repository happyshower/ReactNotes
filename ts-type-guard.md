# 学习记录 8-17

### 关键字

- keyof；将一个类型映射为它所有成员名称的联合类型

  ```ts
  type Human = { name: string; location: string };
  type Person = keyof Human;

  const username: Person = "name";
  const address: Person = "location";

  type Books = { [index: number]: string };
  type Letter = keyof Books; //type Letter = number

  type Books = { [index: string]: string };
  type Letter = keyof Books; //type Letter = string | number
  ```

- typeof：在类型上下文中使用它来引用变量或属性的类型

  ```ts
  const obj = {
    name: "jojo",
    age: 18,
    hobbies: ["playgame", "swimming"],
    student: false,
  };

  type Person = typeof obj;
  // type Person = {
  //   name: string;
  //   age: number;
  //   hobbies: string[];
  //   student: boolean;
  // }
  ```

- extends：用于类型继承和类型约束

  ```ts
  interface Person {
    name: string;
    getName: () => void;
  }

  interface Chinese extends Person {
    location: string;
  }

  type Human = Chinese extends Person ? string : number;

  const human1: Human = 250; //Error 不能将类型“number”分配给类型“string”。
  const human2: Human = "test";
  ```

- infer：根据传入的参数推断类型，如有条件类型的推断 infer P

  ```ts
  //根据传入的类型 T 推断类型 U
  //若类型 T extends Array ，则返回{ name : U }，否则返回传入的类型 T
  type Animal<T> = T extends Array<infer U> ? { name: U } : T;

  type Cat = Animal<string>;
  type Dog = Animal<string[]>;

  const cat: Cat = "miao miao";
  const dog: Dog = { name: "alaska" };
  ```

### 类型守卫

- 类型守卫的作用在于在特定代码块内部提供更准确的类型信息，以便在代码后续部分进行类型检查和类型推断时能够更好地理解变量的类型。这有助于避免运行时错误和类型不匹配的问题。

  - 类型谓词 ( is )
    ```ts
    const isString = (value: unknown): value is string => {
      return typeof value === "string";
    };
    const isGuard = (value: number | string) => {
      if (isString(value)) {
        value.toUpperCase();
      }
    };
    ```
  - in 操作符类型守卫

    ```ts
    interface IHobbies {
      playBasketball: () => void;
      playFootball: () => void;
    }
    interface IInterests {
      swimming: () => void;
    }

    const getPersonHobbies = (hobbies: IHobbies | IInterests) => {
      if ("playFootball" in hobbies) {
        hobbies.playFootball();
      }
      if ("swimming" in hobbies) {
        hobbies.swimming();
      }
    };
    ```

  - typeof 类型守卫
    ```ts
    const typeofGuard = (value: number | string) => {
      if (typeof value === "number") {
        console.log("number=>", value);
      } else if (typeof value === "string") {
        value.toUpperCase();
      }
    };
    ```
  - instanceof 类型守卫

    ```ts
    class PersonA {
      playBasketball: () => {};
      playFootball: () => {};
    }
    class PersonB {
      playBasketball: () => {};
      swimming: () => {};
    }

    const getPersonHobbies2 = (hobbies: PersonA | PersonB) => {
      if (hobbies instanceof PersonA) {
        hobbies.playFootball();
      }
      if (hobbies instanceof PersonB) {
        hobbies.swimming();
      }
      hobbies.playBasketball();
    };
    ```
