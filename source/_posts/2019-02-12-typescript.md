---
title: TypeScript 语法
date: 2019-02-12 10:27:23
tags: [typescript]
categories:
- 编程语言
- TypeScript
---

## 简介

### TypeScript是什么

#### 背景

TypeScript是微软开发的一款开源的编程语言，本质上是向JavaScript增加**静态类型系统**。它是JavaScript的超集，所有现有的JavaScript都可以不加改变就在其中使用。它是为大型软件开发而设计的，最终编译产生JavaScript，所以可以运行在浏览器、Node.js等等的运行时环境。

#### 什么是类型系统

下面是摘自《Types And Programming Languages》中的定义：
> A type system is a tractable syntactic method for proving the absence of certain program behaviors by classifying phrases according to the kinds of values they compute.

第一个重点是Proving the absence of certain program behaviors，所以我们亦可将类型检查器看做一个程序推理工具，可以静态的证明程序成立。

另一个重点是Classifying phrases according to the kinds of values they compute，对词语（比如变量）的值得性质进行分类，比如说TypeScript中的Interface、Class等。

#### 静态类型系统是什么

增加**静态**这个定语，是为了和运行时的类型检查机制加以区分，强调**静态类型系统**是在编译时进行类型分析。

#### TypeScript和JavaScript的关系

和一些基于JavaScript的激进语言不同（比如CoffeScript），TypeScript的语法设计首先考虑的就是兼容JavaScript，或者说对JavaScript的语法做扩展。基本上是在JavaScript的基础上增加了一些类型标记语法，以实现静态类型分析。把这些类型标注语法去掉之后，仍是一个标准的JavaScript语言。

### 为什么要使用TypeScript

* 提高代码质量，降低bug率，隐式类型转换带来大量不可预知的类型错误
* 静态类型检测，减少编译阶段引起的错误
* 模块化，es6已支持
* 让代码更加清晰易读
* 语法糖()=>，接口，枚举，泛型，方法重载

## TypeScript 语法介绍

### 基础类型

* 布尔值（Boolean）
    ```typescript
    let isDone: boolean = false
    ```
* 数字（Number）
    ```typescript
    let decLiteral: number = 6
    let hexLiteral: number = 0xf00d
    let binaryLiteral: number = 0b1010
    let octalLiteral: number = 0o744
    ```
* 字符串（String）
    ```typescript
    let name: string = 'bob'
    ```
* 数组（Array）
    ```typescript
    // 在元素类型后面接上 []，表示由此类型元素组成的一个数组
    let list: number[] = [1, 2, 3]

    // 数组泛型，Array<元素类型>
    let list: Array<number> = [1, 2, 3]
    ```
* 元组（Tuple）：元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同
    ```typescript
    let x: [string, number]
    x = ['hello', 10] // OK
    x = [10, 'hello'] // Error
    ```
* 枚举（Enum）
    ```typescript
    enum Color { Red = 2, Green, Blue }
    let c: Color = Color.Green // 3
    let colorName: string = Color[2] // 'Red'
    ```
* Any：允许你在编译时可选择地包含或移除类型检查，与Object的区别在于，Object类型的变量只是允许你给它赋任意值，但是却不能够在它上面调用任意的方法
    ```typescript
    let notSure: any = 4
    notSure.ifItExits() // OK
    notSure.toFixed() // OK

    let prettySure: Object = 4
    prettySure.toFixed() // Error
    ```
* Void：声明一个void类型的变量，只能赋值为undefined和null
* Null和Undefined：默认情况下null和undefined是所有类型的子类型。就是说你可以把null和undefined赋值给number类型的变量。然而，当你指定了--strictNullChecks标记，null和undefined只能赋值给void和它们各自
* Never：表示的是那些永不存在的值的类型。never类型是任何类型的子类型，也可以赋值给任何类型；然而，没有类型是never的子类型或可以赋值给never类型（除了never本身之外）。
* Object：表示非原始类型，也就是除number，string，boolean，symbol，null或undefined之外的类型
    ```typescript
    let o: object = { prop: 0 } // OK
    let o1: object = 4 // Error
    // 注意，object与Object的区别
    let o1: Object = 4 // OK
    ```
* 类型断言：好比其它语言里的类型转换
    ```typescript
    // “尖括号”语法
    let someValue: any = 'this is a string'
    let strLength: number = (<string>someValue).length

    // as语法
    // 当在TypeScript中使用JSX时，只有as语法断言是被允许的
    let someValue: any = 'this is a string'
    let strLength: number = (someValue as string).length
    ```

### 变量声明

* var、let、const
* 解构

### 接口

TypeScript的核心原则之一就是对值所具有的结构进行类型检查在。在TypeScript里，接口的作用就是为这些类型命名和为你的代码或第三方代码定义契约。相对于其它语言的接口而言，在ts中只会关注值得外形，而不能说某个对象实现了这个接口。

```typescript
 interface SquareConfig {
     width: number
     height: number
     // 可选属性的好处：1. 可以对可能存在的属性进行预定义；2. 可以捕获引用了不存在的属性时的错误（例如：color属性拼写错误成clor）
     color?: string
     // 只读属性
     readonly borderColor: string
     // 只读数组，把ReadonlyArray类型的变量赋值给普通数组是不被允许的，但是可以用类型断言重写
     arr: ReadonlyArray<number>
     // 字符串索引签名：避免额外的属性检查
     [propName: string]: any
 }
```

#### 函数类型

接口能够描述JavaScript中对象拥有的各种各样的外形。除了描述带有属性的普通对象外，接口也可以描述函数类型。

```typescript
interface SearchFunc {
    (source: string, subString: string): boolean
}

let mySearchFunc: SearchFunc
mySearchFunc = function (src, sub): boolean {
    let result = src.search(sub)
    return result > -1
}
```

#### 可索引的类型

与使用接口描述函数类型差不多，我们也可以描述那些能够“通过索引得到”的类型。TypeScript支持两种索引签名：字符串和数字。可以同时使用两种类型的索引，但是数字索引的返回值必须是字符串索引返回值的子类型。这是因为当使用number来索引时，JavaScript会将它转换成string然后再去索引对象。

```typescript
class Animal {
    name: string
}
class Dog extends Animal {
    breed: string
}

// 错误：使用数值型的字符串索引，有时得到的会是Dog类型的数据
interface NotOkay {
    [x: number]: Animal
    [x: string]: Dog
}
```

#### 类类型

与C#或Java里接口的基本作用一样，TypeScript也能够用它来明确的强制一个类去符合某种契约。

```typescript
interface ClockInterface {
    currentTime: Date
    setTime(d: Date)
}

class Clock implements ClockInterface {
    currentTime: Date
    setTime(d: Date) {
        this.currentTime = d
    }
    constructor(h: number, m: number) {}
}
```

#### 继承接口

和类一样，接口也可以相互继承。

```typescript
interface Shape {
    color: string
}
interface PenStroke {
    penWidth: number
}
interface Square extends Shape, PenStroke {
    sideLength: number
}

let square = <Square>{}
square.color = 'blue'
square.penWidth = 5.0
square.sideLength = 10
```

#### 混合类型

因为JavaScript其动态灵活的特点，有时你会希望一个对象可以同时具有上面提到的多种类型。

一个例子就是，一个对象可以同时做为函数和对象使用，并带有额外的属性

```typescript
interface Counter {
    (start: number): string
    interval: number
    reset(): void
}

function getCounter(): Counter {
    let counter = <Counter>function (start: number) {}
    counter.interval = 123
    counter.reset = function () {}
    return counter
}

let c = getCounter()
c(10)
c.reset()
c.interval = 5.0
```

#### 接口继承类

当接口继承了一个类类型时，它会继承类的成员但不包括其实现。接口同样会继承到类的private和protected成员。这意味着当你创建了一个接口继承了一个拥有私有或受保护的成员的类时，这个接口类型只能被这个类或其之类所实现。

```typescript
class Control {
    private state: any
}
interface SelectableControl extends Control {
    select(): void
}
class Button extends Control implements SelectableControl {
    select() {}
}
class TextBox extends Control {
    select() {}
}

// 错误： “Image”类型缺少“state”属性
class Image implements SelectableControl {
    select() {}
}
```

### 类

#### 继承

* 子类的构造函数里必须调用super()，且要在访问this的属性之前
* 子类可以重写基类的函数

```typescript
class Animal {
    name: string;
    constructor(theName: string) { this.name = theName; }
    move(distanceInMeters: number = 0) {
        console.log(`${this.name} moved ${distanceInMeters}m.`)
    }
}

class Snake extends Animal {
    constructor(name: string) { super(name) }
    move(distanceInMeters = 5) {
        console.log("Slithering...")
        super.move(distanceInMeters)
    }
}

class Horse extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 45) {
        console.log("Galloping...")
        super.move(distanceInMeters)
    }
}

let sam = new Snake("Sammy the Python")
let tom: Animal = new Horse("Tommy the Palomino")

sam.move()
tom.move(34)
```

#### 公有，私有与受保护的修饰符

* public(默认)
* private
    TypeScript使用的是结构性类型系统。当我们比较两种不同的类型时，并不在乎它们从何而来，如果所有成员的类型都是兼容的，我们就认为它们的类型是兼容的。

    然而，当我们比较带有private或protected成员的类型的时候，情况就不同了。如果其中一个类型里包含一个private成员，那么只有当另外一个类型中也存在这样一个private成员，并且它们都是来自同一处声明时，我们才认为这两个类型是兼容的。

    ```typescript
    class Animal {
        private name: string
        constructor(theName: string) { this.name = theName }
    }
    class Rhino extends Animal {
        constructor(theName: string) { super('Rhino') }
    }
    class Employee {
        private name: string
        constructor(theName: string) { this.name = theName }
    }

    let animal = new Animal('Goat')
    let rhino = new Rhino()
    let employee = new Employee('bob')

    animal = rhino
    // 错误：Animal 与 Employee不兼容，私有成员name的定义不是来自同一处
    animal = employee
    ```
* protected：protected修饰符与private修饰符的行为很相似，但有一点不同，protected成员在派生类中仍然可以访问。构造函数也可以被标记成protected，这意味着这个类不能再包含它的类外被实例化，但是能被继承。

    ``` typescript
    class Person {
        protected name: string;
        protected constructor(theName: string) { this.name = theName; }
    }

    // Employee 能够继承 Person
    class Employee extends Person {
        private department: string;

        constructor(name: string, department: string) {
            super(name);
            this.department = department;
        }

        public getElevatorPitch() {
            return `Hello, my name is ${this.name} and I work in ${this.department}.`;
        }
    }

    let howard = new Employee("Howard", "Sales");
    let john = new Person("John"); // 错误: 'Person' 的构造函数是被保护的.

    export default {}
    ```

#### readonly修饰符

使用readonly关键字可以将属性设置为只读的

```typescript
class Octopus {
    readonly name: string
    readonly numberOfLegs: number = 8
    constructor (theName: string) {
        this.name = theName
    }
}

let dad = new Octopus('Man with the 8 strong legs')
dad.name = 'Man with the 3-piece suit' // 错误！name是只读的
```

##### 参数属性

可以使用如下代码替代上述方法：

```typescript
class Octopus {
    readonly numberOfLegs: number = 8
    constructor (readonly name: string) {
    }
}
```

注意看我们是如何舍弃了theName，仅在构造函数里使用*readonly name: string*参数来创建和初始化name成员。我们把声明和赋值合并至一处。

参数属性通过给构造函数前面添加一个访问限定符来声明。使用private限定一个参数属性会声明并初始化一个私有成员；对于public和protected来说也一样

#### 存取器

对于存取器有下面几点需要注意的：

* 编译器设置为输出ECMAScript 5或更高
* 只带有get不带有set的存取器自动被推断为readonly

```typescript
let passcode = 'secret passcode'

class Employee {
    private _fullName: string

    get fullName(): string {
        return this._fullName
    }

    set fullName(newName: string): {
        if (passcode && passcode === 'secret passcode') {
            this._fullName = newName
        } else {
            console.log("Error: Unauthorized update of employee!");
        }
    }
}

let employee = new Employee()
employee.fullName = 'Bob Smith'
if (employee.fullName) {
    alert(employee.fullName)
}
```

#### 静态属性

```typescript
class Grid {
    static origin = { x: 0, y: 0 }
}

console.log(Grid.origin.x)
```

#### 抽象类

* abstract关键字用于定义抽象类和在抽象类内部定义抽象方法
* 抽象类作为其它派生类的基类使用，一般不会被实例化
* 不同于接口，抽象类可以包含成员的实现细节
* 抽象类中的抽象方法不包含具体实现并且必须在派生类中实现

```typescript
abstract class Department {

    constructor(public name: string) {
    }

    printName(): void {
        console.log('Department name: ' + this.name);
    }

    abstract printMeeting(): void; // 必须在派生类中实现
}

class AccountingDepartment extends Department {

    constructor() {
        super('Accounting and Auditing'); // 在派生类的构造函数中必须调用 super()
    }

    printMeeting(): void {
        console.log('The Accounting Department meets each Monday at 10am.');
    }

    generateReports(): void {
        console.log('Generating accounting reports...');
    }
}

let department: Department; // 允许创建一个对抽象类型的引用
department = new Department(); // 错误: 不能创建一个抽象类的实例
department = new AccountingDepartment(); // 允许对一个抽象子类进行实例化和赋值
department.printName();
department.printMeeting();
department.generateReports(); // 错误: 方法在声明的抽象类中不存在
```

#### 把类当接口使用

类的定义会创建两个东西：类的实例类型和一个构造函数。因为类可以创建出类型，所以你能够在允许使用接口的地方使用类

```typescript
class Point {
    x: number
    y: number
}
interface Point3d extends Point {
    z: number
}

let point3d: Point3d = { x: 1, y: 2, z: 3 }
```

### 函数

### 泛型

### 枚举

### 类型推论

### 类型兼容性

### 高级类型

### Symbols

### 迭代器和生成器

### 模块

### 命名空间

### 命名空间和模块

### 模块解析

### 声明合并

### JSX

### 装饰器

### Mixins
