# 学习记录 8-15

### Object.create()

- Object.create(proto[,propertiesObject])接收两个参数：一是用作新对象原型的对象和一个为新对象定义额外属性的对象。在传入一个参数的情况下，此方法返回一个继承 **proto** 的原型的对象。在传入第二个参数的情况下，指定的任何属性都会覆盖原型对象上的同名属性。

### class

- ES6 提供了更接近传统语言的写法，引入了 Class（类）这个概念，作为对象的模板。通过 class 关键字，可以定义类
- constructor()方法是类的默认方法，通过 new 命令生成对象实例时，自动调用该方法。
  - 在一个类中只能有一个名为“constructor”的特殊方法。
  - 在一个构造方法中可以使用 super 关键字来调用一个父类的构造方法。
  - 如果没有显式指定构造方法，则会添加默认的 constructor 方法。
- 通过 extends 实现父类的继承。

### javascript 继承

4. 原型式继承
   - 优点：共享父类原型对象的方法
   - 缺点：
     - 子类创建实例时不能向父类传递参数
     - 构造函数中的私有引用属性被所有实例共享

```js
function Animal(name, color) {
  this.name = name;
  this.color = ["red", "green", "blue"];
  this.level = function () {
    console.log("动物保护级别！");
  };
}

Animal.prototype.habit = function () {
  console.log("动物行为！");
};

const animal = new Animal("shake");
//利用Object.create(animal)，返回一个继承 Animal 原型的对象
const obj = Object.create(animal);
const obj2 = Object.create(animal);

console.log(animal, Animal.prototype);

obj.level(); //动物保护级别！
obj.habit(); //动物行为！
obj.color.push("pink"); //父类私有引用属性会共享
console.log(obj.color); //["red", "green", "blue", "pink"]
console.log(obj2.color); //["red", "green", "blue", "pink"]
console.log(obj); //  Animal{ [[Prototype]]: Animal }
console.log(obj2); //  Animal{ [[Prototype]]: Animal }
```

5. 寄生式继承
   - 优点：在原型式继承的基础上，增强对象，返回构造函数
   - 缺点：
     - 无法做到函数复用
     - 构造函数中的私有引用属性被所有实例共享

```js
function Animal(name, color) {
  this.name = name;
  this.color = ["red", "green", "blue"];
  this.level = function () {
    console.log("动物保护级别！");
  };
}

Animal.prototype.habit = function () {
  console.log("动物行为！");
};

const animal = new Animal("cowl");

//通过封装函数的方式增强对象
const createAnother = (obj) => {
  //利用Object.create(animal)，返回一个继承 Animal 原型的对象
  const newObj = Object.create(animal);
  newObj.location = () => {
    console.log("美洲大陆!");
  };
  return newObj;
};
const c = createAnother(animal);
const c2 = createAnother(animal);

c.level(); //动物保护级别！
c.habit(); //动物行为！
c.location(); //美洲大陆!
console.log(c.color); //['red', 'green', 'blue']
c.color.push("gray");
console.log(c.color); //['red', 'green', 'blue', 'gray']
console.log(c2.color); //['red', 'green', 'blue', 'gray']
console.log(c.color === c2.color); //true
console.log(c.level === c2.level); //true
console.log(c.habit === c2.habit); //true
console.log(c); //Animal {location: ƒ ,[[Prototype]]: Animal }
console.log(c2); //Animal {location: ƒ ,[[Prototype]]: Animal }
```

6. 寄生组合式继承
   - 优点：只调用了一次父类的构造函数，并避免了在子类原型上创建不必要的、多余的属性

```js
function Animal(name, color) {
  this.name = name;
  this.color = ["red", "green", "blue"];
  this.level = function () {
    console.log("动物保护级别！");
  };
}

Animal.prototype.habit = function () {
  console.log("动物行为！");
};

function Sheep(name) {
  Animal.call(this, name);
}

//利用Object.create(Animal.prototype)，返回一个继承 Animal 原型的对象，使Sheep的 proto 指向 Animal 的 prototype
Sheep.prototype = Object.create(Animal.prototype);
Sheep.prototype.constructor = Sheep;

const s = new Sheep("山羊");
const s2 = new Sheep("藏羚羊");

s.level();
s.habit();
s.color.push("purple");
s2.color.push("lightyellow");
console.log(s.color); //['red', 'green', 'blue', 'purple']
console.log(s2.color); //['red', 'green', 'blue', "lightyellow"]
console.log(s.color == s2.color); //false
console.log(s.habit == s2.habit); //true
console.log(s.level == s2.level); //false
console.log(s); //Sheep {name: '山羊', color: Array(4), level: ƒ, [[Prototype]]: Animal }
console.log(s2); //Sheep {name: '山羊', color: Array(3), level: ƒ, [[Prototype]]: Animal }
```

7. ES6 class 使用 extends 继承

```js
class Animal {
  constructor(name, color) {
    this.name = name;
    this.color = ["red", "green", "blue"];
    this.level = function () {
      console.log("动物保护级别！");
    };
  }
}

Animal.prototype.habit = function () {
  console.log("动物行为！");
};

class Dolphin extends Animal {
  constructor(name, location) {
    super(name); //通过super关键字调用父类的构造函数
    this.location = location;
  }
}

const dp1 = new Dolphin("海豚", "大海");
const dp2 = new Dolphin("河豚", "淡水河");

dp1.color.push("pink");
dp1.habit(); //动物行为！
dp1.level(); //动物保护级别！
console.log(dp1.color); //["red", "green", "blue","pink"]
console.log(dp2.color); //["red", "green", "blue"];
console.log(dp1.__proto__ === dp2.__proto__); //true
console.log(dp1.constructor === dp2.constructor); //true
console.log(dp1); //Dolphin {name: "海豚", color: Array(4), location: "大海", level: ƒ}
console.log(dp2); //Dolphin {name: "河豚", color: Array(3), location: "淡水河", level: ƒ}
```
