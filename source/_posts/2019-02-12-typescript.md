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

#### 为函数定义类型

函数的类型由参数类型和返回值类型决定

```typescript
function add(x: number, y: number): number {
    return x + y
}

let myAdd = function (x: number, y: number): number {
    return x + y
}
```

#### 书写完整函数类型

```typescript
let myAdd: (baseValue: number, increment: number) => number = function (x: number, y: number): number {
    return x + y
}
```

#### 可选参数和默认参数

* 传递给一个函数的参数个数必须与函数期望的参数个数一致

    ```typescript
    function buildName(firstName: string, lastName: string) {
        return firstName + " " + lastName;
    }

    let result1 = buildName("Bob");                  // error, too few parameters
    let result2 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
    let result3 = buildName("Bob", "Adams");         // ah, just right
    ```

* 在参数名旁边使用?实现可选参数的功能，可选参数必须跟在必须参数后面

    ```typescript
    function buildName(firstName: string, lastName?: string) {
        if (lastName)
            return firstName + " " + lastName;
        else
            return firstName;
    }

    let result1 = buildName("Bob");  // works correctly now
    let result2 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
    let result3 = buildName("Bob", "Adams");  // ah, just right
    ```

* 带默认值得参数不需要放在必须参数的后面。如果带默认值的参数出现在必须参数前面，用户必须明确的传入undefined值来获得默认值

    ```typescript
    function buildName(firstName = "Will", lastName: string) {
        return firstName + " " + lastName;
    }

    let result1 = buildName("Bob");                  // error, too few parameters
    let result2 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
    let result3 = buildName("Bob", "Adams");         // okay and returns "Bob Adams"
    let result4 = buildName(undefined, "Adams");     // okay and returns "Will Adams"
    ```

* 可选参数和末尾的默认参数共享参数类型

    ```typescript
    function buildName(firstName: string, lastName?: string) {
        //. ..
    }
    ```

    和

    ```typescript
    function buildName(firstName: string, lastName = 'Smith') {
        //. ..
    }
    ```

    共享同样的类型(firstName: string, lastName?: string) => string

#### 剩余参数

通过...操作符来获取剩余参数

```typescript
function buildName(firstName: string, ...restOfName: string[]) {
    return firstName + ' ' + restOfName.join(' ')
}

let buildNameFun: (fname: string, ...rest: string[]) => string = buildName
```

#### 重载

定义重载时，一定要把最精确的定义放在最前面

```typescript
let suits = ["hearts", "spades", "clubs", "diamonds"];

function pickCard(x: {suit: string; card: number; }[]): number;
function pickCard(x: number): {suit: string; card: number; };
function pickCard(x): any {
    // Check to see if we're working with an object/array
    // if so, they gave us the deck and we'll pick the card
    if (typeof x == "object") {
        let pickedCard = Math.floor(Math.random() * x.length);
        return pickedCard;
    }
    // Otherwise just let them pick the card
    else if (typeof x == "number") {
        let pickedSuit = Math.floor(x / 13);
        return { suit: suits[pickedSuit], card: x % 13 };
    }
}

let myDeck = [{ suit: "diamonds", card: 2 }, { suit: "spades", card: 10 }, { suit: "hearts", card: 4 }];
let pickedCard1 = myDeck[pickCard(myDeck)];
alert("card: " + pickedCard1.card + " of " + pickedCard1.suit);

let pickedCard2 = pickCard(15);
alert("card: " + pickedCard2.card + " of " + pickedCard2.suit);
```

注意，function pickCard(x): any并不是重载列表的一部分，是函数的具体实现。

### 泛型

含义：类型的参数化，就是可以把类型像方法的参数那样传递

```typescript
// 参数类型与返回值类型相同
function identity<T>(arg: T): T {
    return arg
}

let output = identity<string>('myString')
let output = identity('myString')
```

#### 使用泛型变量

使用泛型创建像identity这样的泛型函数时，编译器要求你在函数体必须正确的使用这个通用的类型。换句话说，你必须把这些参数当做是任意或所有类型

```typescript
function loggingIdentity<T>(arg: T): T {
    // Error: T doesn't have .length
    console.log(arg.length)
    return arg
}
```

#### 泛型类型

泛型函数的类型与非泛型函数的类型没什么不同，只是有一个类型参数放在最前面，像函数声明一样：

```typescript
function identity<T>(arg: T): T {
    return arg
}

let myIdentity: <U>(arg: U) => U = identity
```

还可以使用带有调用签名的对象字面量来定义泛型函数

```typescript
function identity<T>(arg: T): T {
    return arg
}

let myIdentity1: { <U>(arg: U): U } = identity
```

这引导我们去写一个泛型接口了

```typescript
interface GenericIdentityFn {
    <U>(arg: U): U
}

function identity<T>(arg: T): T {
    return arg
}

let myIdentity: GenericIdentityFn = identity
```

或者我们可以把泛型参数当做整个接口的一个参数

```typescript
interface GenericIdentityFn<U> {
    (arg: U): U
}

function identity<T>(arg: T): T {
    return arg
}

let myIdentity: GenericIdentityFn<number> = identity
```

#### 泛型类

泛型类指的是实例部分的类型，所以类的静态属性不能使用这个泛型类型

```typescript
class GenericNumber<T> {
    zeroValue: T
    add: (x: T, y: T) => T
}

let myGenericNumber = new GenericNumber<number>()
myGenericNumber.zeroValue = 0
myGenericNumber.add = function(x, y) { return x + y }
```

#### 泛型约束

如果我们想要限制函数去处理任意带有.length属性的所有类型。只要传入的类型中有这个属性，我们就允许。为此，我们需要累出对于T的约束要求。

为此，我们定义一个接口来描述约束条件。创建一个包含.length属性的接口，使用这个接口和extends关键字来实现约束：

```typescript
interface Lengthwise {
    length: number
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    // Now we konw it has .length property, so no more error
    console.log(arg.length)
    return arg
}
```

#### 在泛型约束中使用类型参数

你可以声明一个类型参数，且它被另一个类型参数所约束。 比如，现在我们想要用属性名从对象里获取这个属性。 并且我们想要确保这个属性存在于对象 obj上，因此我们需要在这两个类型之间使用约束。

```typescript
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
    return obj[key];
}

let x = { a: 1, b: 2, c: 3, d: 4 };

getProperty(x, "a"); // okay
getProperty(x, "m"); // error: Argument of type 'm' isn't assignable to 'a' | 'b' | 'c' | 'd'.
// export default {}
```

#### 在泛型里使用类类型

在TypeScript使用泛型创建工厂函数时，需要引用构造函数的类类型。比如：

```typescript
function create<T>(c: { new(): T }): T {
    return new c()
}
```

### 枚举

使用枚举我们可以定义一些带名字的常量。使用枚举可以清晰地表达意图或创建一组有区别的用例。TypeScript支持数字的和基于字符串的枚举。

#### 数字枚举

* 默认从0开始，往后依次加1
* 如果给其中一个枚举成员赋值n，这后续的枚举成员从n开始依次加1

```typescript
enum Direction {
    top,
    bottom = 4,
    left,
    right = 10,
    topLeft,
    topRight,
    bottomLeft,
    bottomRight
}

console.log(Direction.top) // 0
console.log(Direction.left) // 5
console.log(Direction.topLeft) // 11
console.log(Direction[4]) // 'bottom'
```

数字枚举可以被混入到**计算过的和常量成员（如下所示）**，简单地说，不带初始化器的枚举，要么被放在第一的位置，要么被放在使用了数字常量或者其它常量初始化了的枚举后面。换句话说，下面的情况是不被允许的：

```typescript
enum E {
    A = getSomeValue(),
    B, // error! 'A' is not constant-initialized, so 'B' needs an initializer
}
```

#### 字符串枚举

在一个字符串枚举里，每个成员都必须用字符串字面量，或另外一个字符串枚举成员进行初始化

```typescript
enum Direction {
    Up = 'UP',
    Down = 'DOWN',
    Left = 'LEFT',
    Right = 'RIGHT'
}
```

#### 异构枚举

从技术的角度来说，枚举可以混合字符串和数字成员，除非你真的想要利用JavaScript运行时的行为，否则我们不建议这样做

```typescript
enum BooleanLikeHetergeneousEnum {
    No = 0,
    Yes = 'YES'
}
```

#### 计算的和常量成员

每个枚举成员都带有一个值，它可以是*常量*或*计算*出来的。当满足如下条件时，枚举成员被当作是常量：

* 它是枚举的第一个成员且没有初始化器，这种情况下它被赋值于0
* 它不带有初始化器且它之前的枚举成员是一个*数字常量*。这种情况下，当前枚举成员的值为它上一个枚举成员的值加1
* 枚举成员使用*常量枚举表达式*初始化。常量枚举表达式是TypeScript表达式的子集，他可以在编译阶段求值。当一个表达式满足下面条件之一时，它就是一个常量枚举表达式：
  * 一个枚举表达式字面量（主要是字符串字面量和数字字面量）
  * 一个对之前定义的常量枚举成员的引用（可以是在不同的枚举类型中定义的）
  * 带括号的常量枚举表达式
  * 一元运算符+，-，~其中之一运用在了常量枚举表达式
  * 常量枚举表达式做为二元运算符 +，-，*，/，%，<<，>>，>>>，|，^的操作对象。若常数枚举表达式求值后为NaN或Inifity，则会在编译阶段报错

所有其它情况的枚举成员被当作是需要计算得出的值

#### 联合枚举与枚举成员的类型

存在一种特殊的非计算的常量枚举成员的子集：字面量枚举成员。字面量枚举成员是指不带有初始值的常量枚举成员，或者是值被初始化为

* 任何字符串字面量（例如："foo", "bar"）
* 任何数字字面量（例如：1，100）
* 应用了一元 - 符号的数字字面量（例如：-1，-100）

当所有枚举成员都拥有字面量枚举值时，它就带有了一种特殊的语义：

* 枚举成员成为了类型。例如，我们可以说某些成员只能是枚举成员的值：

    ```typescript
    enum ShapeKind {
        Circle,
        Square,
    }

    interface Circle {
        kind: ShapeKind.Circle
        radius: number
    }

    interface Square {
        kind: ShapeKind.Square
        sideLength: number
    }

    let c: Circle = {
        kind: ShapeKind.Square,
        //    ~~~~~~~~~~~~~~~~ Error!
        radius: 100,
    }

    // 当有成员为非字面量枚举值时
    enum ColorKind {
        Red,
        Green,
        Yellow = 2 << 3
    }

    interface Green {
        // 错误：枚举类型“ColorKind”包含具有不是文本的初始值设定项的成员
        kind: ColorKind.Green
    }
    ```

* 枚举类型本身变成了每个枚举成员的联合。通过联合枚举，类型系统能够利用这样一个事实，它可以知道枚举里的值得集合。因此，TypeScript能够捕获在比较值的时候犯的愚蠢的错误。例如：

    ```typescript
    enum E {
        Foo,
        Bar
    }

    function f(x: E) {
        if (x !== E.Foo || x !== E.Bar) {
            //
            // Error! Operator '!==' cannot be applied to types 'E.Foo' and 'E.Bar'.
        }
    }
    ```

    这个例子里，我们先检查 x是否不是 E.Foo。 如果通过了这个检查，然后 ||会发生短路效果， if语句体里的内容会被执行。 然而，这个检查没有通过，那么 x则 只能为 E.Foo，因此没理由再去检查它是否为 E.Bar

#### 运行时的枚举

枚举是在运行时真正存在的对象。例如下面的枚举：

```typescript
enum E {
    X, Y, Z
}

function f(obj: { X: number }) {
    return obj.X
}
// Works, since 'E' has a property named 'X' which is a number.
f(E)
```

##### 反向映射

从枚举值到枚举名字，但不会为字符串枚举成员生成反向映射，如：

```typescript
enum NumberEnum {
    A
}
enum StringEnum {
    A = 'a'
}

console.log(NumberEnum[0]) // 'A'
console.log(StringEnum['a']) // undefined
```

##### const枚举

常量枚举有两个特点：

* 常量枚举只能使用常量枚举表达式。

    ```typescript
    // OK
    enum NumberEnum {
        A,
        B = Math.random()
    }

    // Error
    const enum NumberEnum {
        A,
        B = Math.random()
    }
    ```

* 不同于常规的枚举，它们在编译阶段会被删除。常量枚举成员在使用的地方会被内联进来。之所以可以这么做是因为，常量枚举不允许包含计算成员。

    ```typescript
    const enum Directions {
        Up,
        Down,
        Left,
        Right
    }

    let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right]
    ```

    生成后的代码为：

    ```typescript
    var directions = [0 /* Up */, 1 /* Down */, 2 /* Left */, 3 /* Right */];
    ```

#### 外部枚举

外部枚举用来描述已经存在的枚举类型的形状

```typescript
declare enum Enum {
    A = 1,
    B,
    C = 2
}
```

外部枚举和非外部枚举之间有一个重要的区别，在正常的枚举里，没有初始化方法的成员被当成常数成员。 对于非常数的外部枚举而言，没有初始化方法时被当做需要经过计算的。

### 类型推论

这节介绍TypeScript里的类型推论。即，类型是在哪里如何被推断的

#### 基础

TypeScript里，在有些没有明确指出类型的地方，类型推论会帮助提供类型。如下面的例子

```typescript
let x = 3
```

变量x的类型为数字。这种推断发生在初始化变量和成员，设置默认参数值和决定函数返回值时。

#### 最佳通用类型

当需要从几个表达式中推断类型的时候，会使用这些表达式的类型来推断出一个最适合的通用类型，如果没有找到最佳通用类型的话，类型推断的结果为联合数组类型。

```typescript
let x = [0, 1, null] // (number)[]
let zoo = [new Rhino(), new Elephant(), new Snake()] // (Rhino | Elephant | Snake)[]
// 当候选类型不能使用的时候，我们需要明确的指出类型
let zoo: Animal[] = [new Rhino(), new Elephant(), new Snake()]
```

#### 上下文类型

TypeScript类型推论也可能按照相反的方向进行。这被叫做”按上下文归类“。按上下文归类会发生在表达式的类型与所处的位置相关时。比如：

```typescript
window.onmousedown = function (mouseEvent) {
    console.log(mouseEvent.message) // Error
}
```

如果上下文类型表达式包含了明确的类型信息，上下文的类型被忽略。重写上面的例子

```typescript
window.onmousedown = function(mouseEvent: any) {
    console.log(mouseEvent.message);  //<- Now, no error is given
};
```

上下文归类会在很多情况下使用到。 通常包含函数的参数，赋值表达式的右边，类型断言，对象成员和数组字面量和返回值语句。 上下文类型也会做为最佳通用类型的候选类型。比如：

```typescript
function createZoo(): Animal[] {
    return [new Rhino(), new Elephant(), new Snake()];
}
```

这个例子里，最佳通用类型由4个候选者：Animal，Rhino，Elephant和Snake。当然，Animal会被作为最佳通用类型

### 类型兼容性

TypeScript里的类型兼容性是基于结构子类型的。结构类型是一种值使用其成员来描述类型的方式。它正好与名义（nominal）类型形成对比。（在基于名义类型的类型系统中，数据类型的兼容性或等价性是通过明确的声明和/或类型的名称来决定的。这与结构性类型系统不同，它是基于类型的组成结构，且不要求明确地声明。） 看下面的例子：

```typescript
interface Named {
    name: string
}
class Person {
    name: string
}

// OK, because of a structural typing
let p: Named = new Person
```

TypeScript的结构性子类型是根据JavaScript代码的典型写法来设计的。 因为JavaScript里广泛地使用匿名对象，例如函数表达式和对象字面量，所以使用结构类型系统来描述这些类型比使用名义类型系统更好。

#### 开始

TypeScript结构化类型系统的基本规则是，如果x要兼容y，那么y至少具有x相同的属性，如果一个y类型的值赋给一个x类型的值，则会递归遍历x的属性是否在y中都存在，如果都存在，则这两者是兼容的，比如

```typescript
interface Named {
    props: {
        name: string
    }
}

function greet(n: Named) {
    console.log('Hello, ' + n.props.name)
}
let y = { props: {name: 'a'} }
greet(y) // OK
```

#### 比较两个函数

x(源函数) = y(目标函数)

* 目标函数的参数必须能在源函数里找到对应类型的参数
* 源函数的返回值类型必须是目标函数返回值类型的子类型
* 比较函数兼容性的时候，可选参数与必须参数是可互换的。源类型上有额外的可选参数不是错误，目标类型的可选参数在源类型里没有对应的参数也不是错误。
* 对于有重载的函数，源函数的每个重载都要在目标函数上找到对应的签名

```typescript
let x = (a: number) => ({ a: 1, b: 2 })
let y = (b: number, s: string) => ({ a: 1 })

y = x // OK
```

#### 枚举

数字枚举类型与数字类型兼容，并且数字类型与数字枚举类型兼容。不同枚举类型之间是不兼容的。比如

```typescript
enum Status { Ready, Waiting }
enum Color { Red, Blue, Green }

let st = Status.Ready
let a: number = 2
st = 2 // OK
a = Status.Ready // OK
st = Color.Green;  // Error
```

#### 类

类与对象字面量和接口差不多，但有一点不同：类有静态部分和实例部分的类型。比较两个类类型的对象时，只有实例的成员会被比较。静态成员和构造函数不在比较的范围内。

类的私有成员和受保护成员会影响兼容性。 当检查类实例的兼容时，如果目标类型包含一个私有成员，那么源类型必须包含来自同一个类的这个私有成员。 同样地，这条规则也适用于包含受保护成员实例的类型检查。 这允许子类赋值给父类，但是不能赋值给其它有同样类型的类。

#### 泛型

因为TypeScript是结构性的类型系统，类型参数只影响使用其做为类型一部分的结果类型。比如：

```typescript
interface Empty<T> {
}
let x: Empty<number>;
let y: Empty<string>;

x = y;  // OK, because y matches structure of x
```

上面代码里，x和y是兼容的，因为它们的结构使用类型参数时并没有什么不同。 把这个例子改变一下，增加一个成员，就能看出是如何工作的了：

```typescript
interface NotEmpty<T> {
    data: T;
}
let x: NotEmpty<number>;
let y: NotEmpty<string>;

x = y;  // Error, because x and y are not compatible
```

对于没指定泛型类型的泛型参数时，会把所有泛型参数当成any比较。 然后用结果类型进行比较，就像上面第一个例子。比如：

```typescript
let identity = function<T>(x: T): T {
    // ...
}

let reverse = function<U>(y: U): U {
    // ...
}

identity = reverse;  // OK, because (x: any) => any matches (y: any) => any
```

### 高级类型

本节主要介绍通过type关键字创建高级类型，若要详细了解此节，请[阅读源文档](https://www.tslang.cn/docs/handbook/advanced-types.html)

* 交叉类型：交叉类型是将多个类型合并为一个类型。 这让我们可以把现有的多种类型叠加到一起成为一种类型，它包含了所需的所有类型的特性。 例如， Person & Serializable & Loggable同时是 Person 和 Serializable 和 Loggable。 就是说这个类型的对象同时拥有了这三种类型的成员。
* 联合类型：表示一个值可以是几种类型之一。我们用竖线（|）分隔每个类型，所以number | string | boolean表示一个值可以是number，string，或boolean。如果一个值是联合类型，我们只能访问此联合类型的所有类型里共有的成员。
* 类型保护与区分类型
  * 类型谓词：parameterName is Type
  * typeof类型保护：只有两种形式能被识别： typeof v === "typename"和 typeof v !== "typename"， "typename"必须是 "number"， "string"， "boolean"或 "symbol"。 但是TypeScript并不会阻止你与其它字符串比较，语言不会把那些表达式识别为类型保护。
  * instanceof类型保护

### Symbols

### 迭代器和生成器

当一个对象实现了Symbol.iterator属性时，我们认为它是可迭代的。

#### for...of vs for...in

* for...of遍历的是值，for...in遍历的是属性
* 另一个区别是for..in可以操作任何对象；它提供了查看对象属性的一种方法。 但是 for..of关注于迭代对象的值。内置对象Map和Set已经实现了Symbol.iterator方法，让我们可以访问它们保存的值。

    ```typescript
    let pets = new Set(["Cat", "Dog", "Hamster"]);
    pets["species"] = "mammals";

    for (let pet in pets) {
        console.log(pet); // "species"
    }

    for (let pet of pets) {
        console.log(pet); // "Cat", "Dog", "Hamster"
    }
    ```

#### 代码生成

* 目标为ES5和ES3

    当生成目标为ES5或ES3，迭代器只允许在Array类型上使用。 在非数组值上使用for..of语句会得到一个错误，就算这些非数组值已经实现了Symbol.iterator属性。

    编译器会生成一个简单的for循环做为for...of循环，比如：

    ```typescript
    let numbers = [1, 2, 3];
    for (let num of numbers) {
        console.log(num);
    }
    ```

    生成的代码为：

    ```typescript
    var numbers = [1, 2, 3];
    for (var _i = 0; _i < numbers.length; _i++) {
        var num = numbers[_i];
        console.log(num);
    }
    ```

* 目标为ECMAScript 2015或更高

    当目标为兼容ECMAScript 2015的引擎时，编译器会生成相对于引擎的for..of内置迭代器实现方式

### 模块

同ECMAScript 2015的模块概念

#### export = 和 import

为了支持CommonJS和AMD的exports，TypeScript提供了export = 和import = require()语法

```typescript ZipCodeValidator.ts
let numberRegexp = /^[0-9]+$/;
class ZipCodeValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}
export = ZipCodeValidator;
```

```typescript Test.ts
import zip = require("./ZipCodeValidator");

// Some samples to try
let strings = ["Hello", "98052", "101"];

// Validators to use
let validator = new zip();

// Show whether each string passed each validator
strings.forEach(s => {
  console.log(`"${ s }" - ${ validator.isAcceptable(s) ? "matches" : "does not match" }`);
});
```

#### 危险信号

* 文件的顶层声明是export namespace Foo { ... }（删除Foo并把所有内容向上层移动一层）
* 文件只有一个export class 或 export function（考虑使用export default）
* 多个文件的顶层具有同样的export namespace Foo {（不要以为这些会被合并到一个Foo中！）

### 命名空间

### 命名空间和模块

在TypeScript中，每个文件都代表一个模块，通过import，export，export = 和import = require(...)进行导入和导出，在TypeScript1.5之前称为外部模块。命名空间namespace，在TypeScript1.5之前称为内部模块，是定义在全局空间下的一个JS对象，不存在导入和导出的概念，不同文件相同的命名空间里的变量会被整合到一起，因此需要把所有相同命名空间的文件通过--outFile输出到一个文件中，防止发生运行时错误，使用全局命名空间的时候，不能使用import导入其他模块。当把namespace通过export导出的时候，这个内部模块就变成了外部模块。

#### 命名空间

命名空间是位于全局命名空间下的一个普通的带有名字的JavaScript对象。 这令命名空间十分容易使用。 它们可以在多文件中同时使用，并通过 --outFile结合在一起。 命名空间是帮你组织Web应用不错的方式，你可以把所有依赖都放在HTML页面的&lt;script&gt;标签里。

但就像其它的全局命名空间污染一样，它很难去识别组件之间的依赖关系，尤其是在大型的应用中。

### 模块

像命名空间一样，模块可以包含代码和声明。 不同的是模块可以 声明它的依赖。

模块会把依赖添加到模块加载器上（例如CommonJs / Require.js）。 对于小型的JS应用来说可能没必要，但是对于大型应用，这一点点的花费会带来长久的模块化和可维护性上的便利。 模块也提供了更好的代码重用，更强的封闭性以及更好的使用工具进行优化。

### 模块解析

[官网原文](https://www.tslang.cn/docs/handbook/module-resolution.html)

### 声明合并

#### 接口合并

接口的非函数的成员应该是唯一的。如果它们不是唯一的，那么它们必须是相同的类型。如果两个接口中同时声明了同名的非函数成员且它们的类型不同，则编译器会报错。

于函数成员，每个同名函数声明都会被当成这个函数的一个重载。 同时需要注意，当接口 A与后来的接口 A合并时，后面的接口具有更高的优先级。

如下例所示：

```typescript
interface Cloner {
    clone(animal: Animal): Animal;
}

interface Cloner {
    clone(animal: Sheep): Sheep;
}

interface Cloner {
    clone(animal: Dog): Dog;
    clone(animal: Cat): Cat;
}
```

这三个接口合并成一个声明：

```typescript
interface Cloner {
    clone(animal: Dog): Dog;
    clone(animal: Cat): Cat;
    clone(animal: Sheep): Sheep;
    clone(animal: Animal): Animal;
}
```

这个规则有一个例外是当出现特殊的函数签名时。 如果签名里有一个参数的类型是 单一的字符串字面量（比如，不是字符串字面量的联合类型），那么它将会被提升到重载列表的最顶端。

比如，下面的接口会合并到一起：

```typescript
interface Document {
    createElement(tagName: any): Element;
}
interface Document {
    createElement(tagName: "div"): HTMLDivElement;
    createElement(tagName: "span"): HTMLSpanElement;
}
interface Document {
    createElement(tagName: string): HTMLElement;
    createElement(tagName: "canvas"): HTMLCanvasElement;
}

```

合并后的Document将会像下面这样：

```typescript
interface Document {
    createElement(tagName: "canvas"): HTMLCanvasElement;
    createElement(tagName: "div"): HTMLDivElement;
    createElement(tagName: "span"): HTMLSpanElement;
    createElement(tagName: string): HTMLElement;
    createElement(tagName: any): Element;
}

```

#### 合并命名空间

对于命名空间的合并，模块导出的同名接口进行合并，构成单一命名空间内含合并后的接口。

对于命名空间里值的合并，如果当前已经存在给定名字的命名空间，那么后来的命名空间的导出成员会被加到已经存在的那个模块里

Animals声明合并示例：

```typescript
namespace Animals {
    export class Zebra { }
}

namespace Animals {
    export interface Legged { numberOfLegs: number; }
    export class Dog { }
}
```

等同于：

```typescript
namespace Animals {
    export interface Legged { numberOfLegs: number; }

    export class Zebra { }
    export class Dog { }
}
```

除了这些合并外，你还需要了解非导出成员是如何处理的。 非导出成员仅在其原有的（合并前的）命名空间内可见。这就是说合并之后，从其它命名空间合并进来的成员无法访问非导出成员。

```typescript
namespace Animal {
    let haveMuscles = true;

    export function animalsHaveMuscles() {
        return haveMuscles;
    }
}

namespace Animal {
    export function doAnimalsHaveMuscles() {
        return haveMuscles;  // Error, because haveMuscles is not accessible here
    }
}
```

#### 命名空间与类和函数和枚举类型合并

* 合并命名空间和类

    ```typescript
    class Album {
        label: Album.AlbumLabel;
    }
    namespace Album {
        export class AlbumLabel { }
    }
    ```

    生成的代码

    ```javascript
    class Album {
    }
    (function (Album) {
        class AlbumLabel {
        }
        Album.AlbumLabel = AlbumLabel;
    })(Album || (Album = {}));
    ```

* 利用命名空间来添加函数的属性

    ```typescript
    function buildLabel(name: string): string {
        return buildLabel.prefix + name + buildLabel.suffix;
    }

    namespace buildLabel {
        export let suffix = "";
        export let prefix = "Hello, ";
    }

    console.log(buildLabel("Sam Smith"));
    ```

* 利用命名空间来扩展枚举型：

    ```typescript
    enum Color {
        red = 1,
        green = 2,
        blue = 4
    }

    namespace Color {
        export function mixColor(colorName: string) {
            if (colorName == "yellow") {
                return Color.red + Color.green;
            }
            else if (colorName == "white") {
                return Color.red + Color.green + Color.blue;
            }
            else if (colorName == "magenta") {
                return Color.red + Color.blue;
            }
            else if (colorName == "cyan") {
                return Color.green + Color.blue;
            }
        }
    }
    ```

#### 非法的合并

TypeScript并非允许所有的合并。目前，类不能与其它类或变量合并。

### JSX

#### 基本用法

想要使用JSX必须做两件事

1. 给文件一个.tsx扩展名
2. 启用jsx选项

TypeScript具有三种JSX模式：

1. preserve：在preserver模式下生成代码中会保留JSX以供后续的转换操作使用（比如：Babel），输出文件扩展名为.jsx。
2. react：会生成React.createElement，在使用前不需要再进行转换操作了，输出文件扩展名为.js。
3. react-native：相当于preserve，输出文件扩展名为.js。

#### 类型检查

* 固有元素：环境自带的某些东西（比如，DOM环境里的div或span组件），固有元素使用特殊的接口JSX.IntrinsicElements来查找。默认地，如果这个接口没有指定，会全部通过，不对固有元素进行类型检查。然而，如果这个接口存在，那么固有元素的名字需要在JSX.IntrinsicElements接口的属性里查找。 例如：

    ```typescript
    declare namespace JSX {
        interface IntrinsicElements {
            foo: any
        }
    }

    let a = <foo />; // 正确
    let b = <div />; // 错误
    ```

* 基于值得元素： 基于值得元素会简单的在它所在的作用域里按标识符查找

### 装饰器

装饰器是一种特殊类型的声明，它能够被附加到类声明，方法， 访问符，属性或参数上。 装饰器使用 @expression这种形式，expression求值后必须为一个函数，它会在运行时被调用，被装饰的声明信息做为参数传入。

例如，有一个@sealed装饰器，我们会这样定义sealed函数：

```typescript
function sealed(target) {
    // do something with "target" ...
}
```

#### 装饰器工厂

如果我们要定制一个修饰器如何应用到一个声明上，我们得写一个装饰器工厂函数。 装饰器工厂就是一个简单的函数，它返回一个表达式，以供装饰器在运行时调用。

我们可以通过下面的方式来写一个装饰器工厂函数：

```typescript
function color(value: string) { // 这是一个装饰器工厂
    return function (target) { //  这是装饰器
        // do something with "target" and "value"...
    }
}
```

#### 装饰器组合

多个装饰器可以同时应用到一个声明上，就像下面的示例

```typescript
// 书写在同一行上
@f @g x

// 书写在多行上
@f
@g
x
```

当多个装饰器应用于一个声明上，它们求值方式与复合函数相似。在这个模型下，当复合f和g时，复合的结果(f ∘ g)(x)等同于f(g(x))。

#### 装饰器求值

类中不同声明上的装饰器将按以下规定的顺序应用：

1. 参数装饰器，然后依次是方法装饰器，访问符装饰器，或属性装饰器应用到每个实例成员。
2. 参数装饰器，然后依次是方法装饰器，访问符装饰器，或属性装饰器应用到每个静态成员。
3. 参数装饰器应用到构造函数。
4. 类装饰器应用到类。

##### 类装饰器

参数：

1. 类的构造函数。

如果类装饰器返回一个值，它会使用提供的构造函数来替换类的声明。

```javascript
注意 如果你要返回一个新的构造函数，你必须注意处理好原来的原型链。在运行时的装饰器调用逻辑中不会为你做这些。
```

##### 方法装饰器

参数：

1. 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象。
2. 成员的名字。
3. 成员的属性描述符。

```javascript
注意  如果代码输出目标版本小于ES5，属性描述符将会是undefined。
```

如果方法装饰器返回一个值，它会被用作方法的属性描述符。

```javascript
注意  如果代码输出目标版本小于ES5返回值会被忽略。
```

##### 访问器装饰器

```javascript
注意  TypeScript不允许同时装饰一个成员的get和set访问器。取而代之的是，一个成员的所有装饰的必须应用在文档顺序的第一个访问器上。这是因为，在装饰器应用于一个属性描述符时，它联合了get和set访问器，而不是分开声明的。
```

参数：

1. 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象。
2. 成员的名字。
3. 成员的属性描述符。

```javascript
注意  如果代码输出目标版本小于ES5，属性描述符将会是undefined。
```

如果方法装饰器返回一个值，它会被用作方法的属性描述符。

```javascript
注意  如果代码输出目标版本小于ES5返回值会被忽略。
```

##### 属性装饰器

属性装饰器表达式会在运行时当作函数被调用，传入下列2个参数：

1. 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象。
2. 成员的名字。

```javascript
注意  属性描述符不会做为参数传入属性装饰器，这与TypeScript是如何初始化属性装饰器的有关。 因为目前没有办法在定义一个原型对象的成员时描述一个实例属性，并且没办法监视或修改一个属性的初始化方法。返回值也会被忽略。因此，属性描述符只能用来监视类中是否声明了某个名字的属性。
```

##### 参数装饰器

参数装饰器表达式会在运行时当作函数被调用，传入下列3个参数：

1. 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象。
2. 成员的名字。
3. 参数在函数参数列表中的索引。

```javascript
注意  参数装饰器只能用来监视一个方法的参数是否被传入。
```

参数装饰器的返回值会被忽略。

### Mixins

* 使用implements关键词，把类当做接口。
* 为将要mixin进来的属性方法创建出占位属性,告诉编译器这些成员在运行时是可用的。

```typescript
// Disposable Mixin
class Disposable {
    isDisposed: boolean;
    dispose() {
        this.isDisposed = true;
    }

}

// Activatable Mixin
class Activatable {
    isActive: boolean;
    activate() {
        this.isActive = true;
    }
    deactivate() {
        this.isActive = false;
    }
}

class SmartObject implements Disposable, Activatable {
    constructor() {
        setInterval(() => console.log(this.isActive + " : " + this.isDisposed), 500);
    }

    interact() {
        this.activate();
    }

    // Disposable
    isDisposed: boolean = false;
    dispose: () => void;
    // Activatable
    isActive: boolean = false;
    activate: () => void;
    deactivate: () => void;
}
applyMixins(SmartObject, [Disposable, Activatable]);

let smartObj = new SmartObject();
setTimeout(() => smartObj.interact(), 1000);

////////////////////////////////////////
// In your runtime library somewhere
////////////////////////////////////////

function applyMixins(derivedCtor: any, baseCtors: any[]) {
    baseCtors.forEach(baseCtor => {
        Object.getOwnPropertyNames(baseCtor.prototype).forEach(name => {
            derivedCtor.prototype[name] = baseCtor.prototype[name];
        });
    });
}
```

### 三斜线指令

三斜线指令是包含单个XML标签的单行注释。注释的内容会作为编译器指令使用。

三斜线指令仅可放在包含它的文件的最顶端。一个三斜线指令的前面只能出现单行或多行注释，这包括其它的三斜线指令。 如果它们出现在一个语句或声明之后，那么它们会被当做普通的单行注释，并且不具有特殊的涵义。

使用--noResolve编译选项，三斜线引用会被忽略

* /// &lt;reference path="..." /&gt;：用于声明文件间的依赖，告诉编译器在编译过程中要引入的额外的文件。
* /// &lt;reference types="..." /&gt;：用于声明对某个包的依赖，仅当在你需要写一个d.ts文件时才使用这个指令。若要在.ts文件声明一个对@types包的依赖，使用--types命令行选项或在tsconfig.json里指定。
* /// &lt;reference no-default-lib="true" /&gt;：这个指令把一个文件标记成默认库，告诉编译器在编译过程中不要包含这个默认库（比如，lib.d.ts）。 这与在命令行上使用 --noLib相似。还要注意，当传递了--skipDefaultLibCheck时，编译器只会忽略检查带有/// &lt;reference no-default-lib="true"/&gt;的文件。
* /// &lt;amd-module name="..." /&gt;：

    默认情况下生成的AMD模块都是匿名的。 但是，当一些工具需要处理生成的模块时会产生问题，比如 r.js。

    amd-module指令允许给编译器传入一个可选的模块名：

    ```typescript amdModule.ts
    ///<amd-module name='NamedModule'/>
    export class C {
    }
    ```

    这会将NamedModule传入到AMD define函数里：

    ```typescript amdModule.js
    define("NamedModule", ["require", "exports"], function (require, exports) {
        var C = (function () {
            function C() {
            }
            return C;
        })();
        exports.C = C;
    });
    ```