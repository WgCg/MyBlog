---
title: TypeScript笔记
tags: [typescript]
toc: true
mathjax: true
date: 2019-03-09 15:43:06
categories:
- 编程语言
- TypeScript
---

# TypeScript入门教程

1. null和undefined是所有类型的子类型，也就说null和undefined类型的变量可以赋值给任意类型的变量，而void类型的变量却不行。
2. 在any类型的变量上访问任何属性或者调用任何方法都是允许的，声明一个变量为any后，对它的任何操作，返回的内容的类型都是任意值。
3. 变量如果在声明的时候，未指定其类型，那么它会被识别为any类型。
4. 当TypeScript不确定一个联合类型的变量是哪个类型的时候，我们只能访问此联合类型的所有类型里共有的属性和方法。

    ```typescript
    function getLength(something: string | number): number {
        return something.length;
    }
    // index.ts(2,22): error TS2339: Property 'length' does not exist on type 'string | number'.
    //   Property 'length' does not exist on type 'number'.
    ```

5. 赋值的时候，变量的形状必须与接口的形状保持一致。
6. 接口一旦定义了索引签名，那么确定属性和可选属性的类型都必须是它的类型的子集。

    ```typescript
    interface Person {
        name: string;
        age?: number;
        [propName: string]: string;
    }
    // index.ts(3,5): error TS2411: Property 'age' of type 'number' is not assignable to string index type 'string'.
    ```

7. 只读属性的约束存在于第一次给对象赋值的时候，而不是第一次给只读属性赋值的时候。

    ```typescript
    interface Person {
        readonly id: number;
        name: string;
        age?: number;
        [propName: string]: any;
    }

    let tom: Person = {
        name: 'Tom',
        gender: 'male'
    };

    tom.id = 89757;

    // index.ts(8,5): error TS2322: Type '{ name: string; gender: string; }' is not assignable to type 'Person'.
    //   Property 'id' is missing in type '{ name: string; gender: string; }'.
    // index.ts(13,5): error TS2540: Cannot assign to 'id' because it is a constant or a read-only property.
    ```

8. 调用函数时，输入多余的（或者少于要求的）参数，是不被允许的，可以使用可选参数。
9. 可选参数后面不允许再出现必须参数了。
10. TypeScript会将添加了默认值的参数识别为可选参数，此时就不受「可选参数必须接在必需参数后面」的限制了。
11. 当发生函数重载的时候，TypeScript会优先从最前面的函数定义开始匹配，所以多个函数定义如果有包含关系，需要优先把精确的定义写在前面。
12. 类型断言不是类型转换，断言成一个联合类型中不存在的类型是不允许的。

    ```typescript
    function toBoolean(something: string | number): boolean {
        return <boolean>something;
    }

    // index.ts(2,10): error TS2352: Type 'string | number' cannot be converted to type 'boolean'.
    // Type 'number' is not comparable to type 'boolean'.
    ```

13. declare var a 与 var a的区别在于declare并没有对变量a进行声明，在编译结果中会被删除，而var a声明了这个变量。

    ```typescript
    decalre var a // 说明在某处已经声明过改变量
    a = 1 // 对变量重新进行赋值

    // 上述代码编译结果
    a = 1

    // -----------

    var a
    a = 1

    // 上述代码编译结果
    var a
    a = 1
    ```

14. 只有function、class和interface可以直接默认导出，其它的变量需要先定义出来，再默认导出

    ```typescript
    export default enum Directions {
    // ERROR: Expression expected.
        Up,
        Down,
        Left,
        Right
    }
    ```

15. 当为元组类型的变量添加越界的元素时，它的类型会被限制为元组每个类型的联合类型。
16. 枚举项有两种类型：常数项和计算所得项。如果紧接在计算所得项后面的是未手动赋值的项，那么它就会因为无法获得初始值而报错

    ```typescript
    enum Color {Red = "red".length, Green, Blue};

    // index.ts(1,33): error TS1061: Enum member must have initializer.
    // index.ts(1,40): error TS1061: Enum member must have initializer.
    ```

17. 常数枚举与普通枚举的区别是，它会在编译阶段被删除，并且不能包含计算成员。

    ```typescript
    declare enum Directions {
        Up,
        Down,
        Left,
        Right
    }
    let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right];

    // 上述代码编译为：
    var directions = [0 /* Up */, 1 /* Down */, 2 /* Left */, 3 /* Right */];
    ```