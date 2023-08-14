# 学习记录 8-14

### 原型

- JS 的每个函数在创建的时候，都会生成一个属性 **prototype**，这个属性指向一个对象，这个对象就是此函数的原型对象。该原型对象中有个属性为 constructor，指向该函数。这样原型对象和它的函数之间就产生了联系

### 原型链

- 每个通过构造函数创建出来的实例对象，其本身有个属性 **proto**，这个属性会指向该实例对象的构造函数的原型对象
- 当访问一个对象的某个属性时，会先在这个对象本身属性上查找，如果没有找到，则会通过它的 **proto** 隐式属性，找到它的构造函数的原型对象，如果还没有找到就会再在其构造函数的 prototype 的 **proto** 中查找，这样一层一层向上查找就会形成一个链式结构，称之为原型链。

#### Javascript 继承

1. 原型链继承

   - 优点：共享父类原型对象的方法
   - 缺点：
     - 实例化时，无法修改父类构造函数中的传参
     - 构造函数中的私有引用属性被共享

   ```js
   function Animal(name, color) {
     this.name = name;
     this.color = ["red", "green", "blue"]; //私有属性
     this.level = function () {
       console.log("动物保护级别！");
     };
   }

   Animal.prototype.habit = function () {
     console.log("动物行为！");
   };

   function Cat() {
     this.say = function () {
       console.log("喵喵叫！！");
     };
   }

   Cat.prototype = new Animal(); //将 Cat 的原型对象指向 Animal 的实例对象
   Cat.prototype.constructor = Cat; //修正constructor的指向

   let c1 = new Cat();
   let c2 = new Cat();

   c1.level(); //动物保护级别！
   c1.habit(); //动物行为！
   c1.color.push("white");
   console.log(c1.color); // ["red", "green", "blue","white"]
   console.log(c2.color); // ["red", "green", "blue","white"]
   console.log(c1); // Cat {say: ƒ}
   console.log(c2); // Cat {say: ƒ}
   console.log(c1 === c2); //false
   ```

2. 借用构造函数继承

   - 优点：
     - 子类构建时可以向父类传参
     - 不共享父类构造函数引用属性
   - 缺点：无法复用父类原型属性和方法

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

   function Dog(name) {
     //通过 call 方法改变 this 指向，使 this 指向父类Animal
     Animal.call(this, name);
   }

   let d = new Dog("阿拉斯加");
   let d2 = new Dog("哈士奇");
   d.level();
   // d.habit()   //报错 d.habit is not a function
   d.color.push("black");
   console.log(d.color); //["red", "green", "blue", "black"]
   console.log(d2.color); //["red", "green", "blue"]
   console.log(d);
   console.log(d2);
   console.log(d.level == d2.level); //false
   ```

3. 组合式继承

   - 优点：
     - 可以向父类构造函数传参
     - 可以共享父类原型的方法和属性
   - 缺点：调用两构造函数，会存在一份多余的父类实例属性

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

   function Panda(name) {
     //通过 call 方法改变 this 指向，使 this 指向父类Animal的构造函数
     Animal.call(this, name);
   }

   Panda.prototype = new Animal(); //将 Panda 的原型对象指向 Animal 的实例对象
   Panda.prototype.constructor = Panda; //修正 constructor 的指向，指向创建该实例的父类

   let p = new Panda("熊猫");
   let p2 = new Panda("小熊猫");

   p.level();
   p.habit();
   p.color.push("skyblue");
   console.log(p.color); //["red", "green", "blue", "skyblue"]
   console.log(p2.color); //["red", "green", "blue"]
   console.log(p);
   console.log(p2);
   console.log(p.habit == p2.habit); //true
   console.log(p.level == p2.level); //false
   ```
