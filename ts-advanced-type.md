# 学习记录 8-16

### TS type 和 interface

- type：类型别名 type 可以用来给一个类型起个新名字，常用于基本类型或联合类型等非对象类型时命名

  ```ts
  //基础数据类型、联合类型或元组类型定义别名
  type Name = string;
  type ID = string | number;
  type Point = [number, number];

  type Animal = {
    name: string;
    kind: string;
  };
  //类型扩展
  type Panda = Animal & {
    location: string;
  };
  //函数类型的别名
  type SetPoint = (x: number, y: number) => void;
  ```

- interface：接口 interface 是命名数据结构的另一种方式；与 type 不同，interface 仅限于描述对象类型

  ```ts
  //定义对象的接口
  interface IAnimal {
    name: string;
    kind: string;
  }
  //存在同名的接口时，会自动合并
  interface IAnimal {
    habit: string;
  }
  //类型扩展
  interface IPanda extends IAnimal {
    loaction: string;
  }
  //函数类型的接口
  interface ISetAnimal {
    (name: string, kind: string): void;
  }
  ```

### TS 高级类型

- 字面量类型

  - 字符串字面量类型：指定类型为具体的字符串，即字符串常量
  - 数字字面量类型：指定类型为具体的数值
  - 布尔字面量类型：布尔字面量类型就是指定类型为具体的布尔值（true 或 false）
  - 模版字面量类型：以字符串字面量类型为基础，可以通过联合类型扩展成多个字符串

  ```ts
  **字符串字面量量**
  type Direction = "East" | "South" | "West" | "North";

  const getDirection = (direction:Direction) => {
    return direction;
  }

  getDirection("Center")   //Error 类型“"center"”的参数不能赋给类型“Direction”的参数。
  getDirection("East")

  **数字字面量类型**
  type Age = 18 | 20 | 22;
  type Person = { name: string; age: Age };

  const person:Person = { name: "jojo", age: 25 };  //Error 不能将类型“25”分配给类型“Age”
  const person:Person = { name: "jojo", age: 22 };

  **布尔字面量类型**
  type Success = true;
  type Fail = false;

  const success: Success = false; //Error 不能将类型“false”分配给类型“true”
  const fail: Fail = false;

  **模版字面量类型**
  type Direction = "East" | "South" | "West" | "North";
  type Distinction = `Travel${Direction}`

  const distinction:Distinction = "East"  //Error 不能将类型“"East"”分配给类型“"TravelEast" | "TravelSouth" | "TravelWest" | "TravelNorth"”
  const distinction:Distinction = "TravelEast"
  ```

- 交叉类型 ( & )

  - 交叉类型是将多个类型合并成一个类型，这让我们可以把现有的多种类型叠加到一起成为一种类型，它包含了所需的所有类型的特性。当一个对象既拥有 IAnimal 类型的属性又拥有 IPanda 类型的属性时，可用通过交叉类型 **IAnimal & IPanda** 组成一个新的类型

  ```ts
  interface IAnimal {
    name: string;
    kind: string;
  }

  interface IPanda {
    location: string;
    habit: string;
  }

  const panda: IAnimal & IPanda = {
    name: "panda",
    kind: "Ursidae",
    location: "sichuan",
    habit: "bamboo",
  };
  ```

- 联合类型 ( | )

  - 联合类型表示一个值可以是几种类型之一。 用竖线 ( | ) 分隔每个类型，所以 number | string 表示一个值可以是 number 或 string。如果一个值是联合类型，我们只能访问此联合类型的所有类型里共有的成员。

  ```ts
  interface IDemoProps {
    id: string | number; //id 为数值类型或字符串类型
    name: string;
    age: number;
  }

  const getDemoData = ({ id, name, age }: IDemoProps) => {
    return `${id}-${name}-${age}`;
  };

  getDemoData({ id: "233", name: "jojo", age: 18 }); //传入的 id 为字符串类型
  getDemoData({ id: 233, name: "jojo", age: 18 }); //传入的 id 为数值类型

  interface IPersonA {
    playBasketball: () => void;
    playFootball: () => void;
  }
  interface IPersonB {
    playBasketball: () => void;
    swimming: () => void;
  }

  const getPersonHobbies = (hobbies: IPersonA | IPersonB) => {
    hobbies.swimming(); //Error 类型“IPersonA”上不存在属性“swimming”
    hobbies.playFootball(); //Error 类型“IPersonB”上不存在属性“playFootball”
    hobbies.playBasketball();
  };
  ```

- 映射类型

  - 将一个类型映射成另一个类型,新类型以相同的形式去转换旧类型里每个属性。

  ```ts
  //遍历指定接口的 key 或者是遍历联合类型
  //将属性转为 readonly 类型
  type Readonly<T> = {
    readonly [P in keyof T]: T[P];
  };
  //将属性转为 boolean 类型
  type Readonly<T> = {
    [P in keyof T]: boolean;
  };

  interface IAnimal {
    name: string;
    kind: string;
  }
  //将遍历指定类型并将该类型的属性转为只读属性
  type AnimalReadonly = Readonly<IAnmial>;
  //经过转换后的类型
  interface AnimalReadonly {
    readonly name: string;
    readonly age: number;
  }
  ```
