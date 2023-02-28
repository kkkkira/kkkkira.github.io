---
title: this指针和箭头函数
date: 2023-02-28 21:10:23
category: [Web前端, Javascript]
excerpt: this是一个引用，指向当前函数执行的上下文对象。是在运行时绑定的。
tags: <span class="label label-primary">this</span> <span class="label label-primary">箭头函数</span>
---
# 什么是`this`？

`this`是一个引用，指向当前函数执行的**上下文对象**。是在运行时绑定的。

>💡 执行上下文
当 JS 引擎解析到可执行代码片段（通常是函数调用阶段）的时候，就会先做一些执行前的准备工作，这个 “准备工作”，就叫做 "**执行上下文(execution context 简称 EC)**" 或者也可以叫做 **执行环境**。
执行上下文 为我们的可执行代码块提供了执行前的必要准备工作，例如变量对象的定义、作用域链的扩展、提供调用者的对象引用等信息。




![this.png](this.png)

# this的绑定规则

优先级：`new` > 显示绑定 > 隐式绑定 > 默认绑定

## 默认绑定

当函数被**独立调用**时，默认函数的`this`指向**全局对象**，此时在浏览器运行环境中，能获取到全局对象下使用[var操作符定义的变量，但不能获取到let定义的变量](https://www.notion.so/c80e2b095b104f7f8c53c745185de9d0)。

当在[严格模式](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Strict_mode)下时，`this`不能获取到全局对象下的变量。

```jsx
// 浏览器运行环境下
function foo(){
	console.log('name is: ', this.name);
	console.log('age is: ', this.age);
}

var name = 'kira';
let age = 3;

foo();
// name is:  kira
// age is:  undefined
```

```jsx
// 严格模式
"use strict";
function foo(){
	console.log('name is: ', this.name);
	console.log('age is: ', this.age);

}

var name = 'kira';
let age = 3;

foo();
/* 
   Uncaught TypeError: Cannot read property 'name' of undefined at foo 
*/
```

## 隐式绑定

使用**对象调用**时，函数的`this`会指向当前调用的对象。

`this` 指向链式调用的最后一层

```jsx
function foo(){
	console.log('name is: ', this.name);
	console.log('age is: ', this.age);

}

const person = {
	name: 'kira',
	age: 3,
	foo: foo
}

person.foo();
// name is:  kira
// age is:  3
```

**还需要注意的是隐式绑定丢失：**

当把对象 `o` 的方法 `o.method` 赋值给一个变量 `fn`，然后再调用这个变量 `fn` 的时候，`this` 不会指向这个对象 `o`，而会指向全局对象。
这是因为，赋值语句是静态执行，而 `this` 绑定是动态绑定。
当我们把一个函数，无论是【对象的方法】还是【普通函数】赋值给一个变量的时候，实际上我们是把当前函数的引用（也就是函数存放的地址）赋值给这个变量。
而当我们真正调用的时候，才会绑定 `this` ，此时对于这个函数变量来说，就是以独立的形式被调用了，所以此时 `this` 为默认绑定。

这种隐式丢失的情况有2种：
1. 直接把对象方法赋值给一个变量
2. 把一个对象方法作为参数传给另一个函数，这里因为存在给参数变量隐式赋值的步骤，所以也会丢失

```jsx
const obj = {
	name: 'kira',
	foo(){
		console.log('name is: ',this.name);
	}
}
var name = 'window';
var windowFoo = obj.foo;  // 隐式丢失：情况1

function showFoo(fn){
	fn();  // 隐式丢失：情况2
}

obj.foo();
windowFoo();
showFoo(obj.foo);
setTimeout(obj.foo, 1000) // 隐式丢失： 情况2

// name is:  kira
// name is:  window
// name is:  window
// name is:  window
```

## 显示绑定

显示绑定是指给函数直接指定上下文参数（`context`）。显示绑定有4种方式：

- `call`
- `apply`
- `bind` ：关于`bind`绑定，要注意以下2点：
    - 使用`bind`绑定过的函数，只有使用`new`方法调用才可以更改函数的`this`值
- `js`内置函数中的参数传递：例如数组原型方法中的可选参数`context`，会把**回调函数** [ 非箭头函数 ] 的`this`指向传入的`context`
  
    

- 这4种方式中，关于指定的`context` 的类型，`this`的指向：
    - `undefined`  `null` : 指向全局对象
    - `string` `number` `bool` ：指向当前值类型的原型实例
    - `function`：指向函数本身
    - 引用类型 `object` `array`：指向引用对象

```jsx
// js中内置函数参数传递
let arr = ['context'];
arr.forEach(function(){console.log('this', this)}, {name: '非箭头函数'});
arr.forEach(() => {console.log('this', this)}, {name: '箭头函数'});
```

![显示绑定：数组传入上下文](1.png)

```jsx
// 传入各种类型的context
// call, apply, bind, 内置函数中的参数传递同理
function getContext(){
    console.log('当前函数的上下文对象 this是：', this);
}

function fnContext(){
    console.log('fnContext函数作为上下文对象')
}

let arr = ['array', 'context'];
let obj = {
    name: 'objContext'
}

getContext.call(undefined) // window
getContext.call(null) // window
getContext.call('string') // String
getContext.call(123) // Number
getContext.call(true) // Boolean
getContext.call(fnContext) // fnContext
getContext.call(arr) // arr
getContext.call(obj) // obj
```
控制台打印结果：
![传入各种类型的上下文](2.png)

## new绑定

`new` 操作符的步骤
![new操作步骤](3.png)

注意，使用`new`创建对象时，如果构造函数返回的是引用类型，则`new`操作生成的对象会被这个引用类型取代。

```jsx
  function Foo(value){
      this.name = 'foo';
      this.value = value;
      return value;
  }

  let obj = {type: 'object'};
  let arr = [];
  let fn = function(){console.log('this is a function')}
  console.log(new Foo());
  console.log(new Foo(null));
  console.log(new Foo(undefined));
  console.log(new Foo(1));
  console.log(new Foo(true));
  console.log(new Foo('string'));
  console.log(new Foo(obj), obj);
  console.log(new Foo(arr), arr);
  console.log(new Foo(fn), fn);
```

![new创建对象，构造函数返回不同类型时的结果](7.png)

# 箭头函数
## 箭头函数`this`对象
箭头函数没有`this`对象，或者说箭头函数的`this`对象早在词法分析时就已经被“绑定”为上层词法作用域（也就是函数声明时所在的作用域）的`this`了。

例如下面这个例子：
1. 当`outer`调用时，`this`对象被绑定为`obj`；
2. 因为箭头函数是在`outer`方法中声明的，所以此时返回的箭头函数的`this`被“绑定”为`outer`实际执行时的`this`对象。所以无论该箭头函数在何处被调用，都不会发生改变。
3. 而如果返回的是普通函数，那么该函数的`this`对象就会根据调用方式而发生变化。
```jsx
  function outer() {
      let arrowFn = () => {
          console.log('当前在global环境下调用箭头函数，this：', this)
      }
      let fn = function () {
          console.log('当前在global环境下调用普通函数，this：', this)
      }
      return [arrowFn, fn];
  }

  let obj = { name: 'obj' };
  let obj1 = {name: 'obj1'};
  // 将outer的this绑定为obj
  let [arrowFn, fn] = outer.call(obj);
  arrowFn();
  fn();

  //绑定this
  arrowFn.call(obj1);
  fn.call(obj1);
```
控制台打印

![箭头函数与普通函数的this值对比](8.png)


## 箭头函数与普通函数的区别
（1）箭头函数的 `this` 值等于**箭头函数**在**声明**时所在的**上层函数**在**调用**时所绑定的`this`值。示例如上。

（2）箭头函数没有自己的 `this` `prototype` `arguments` `super` 和 `new.target`
```jsx
// 箭头函数和普通函数
let arrowFn = (arg1) => {};
function fn(arg1){};
console.dir(arrowFn)
console.dir(fn)
```
控制台打印：

![箭头函数没有this等属性](4.png)


（3）箭头函数的不能用作构造函数

![new一个箭头函数会报错](5.png)



# 总结
1. 对 `this` 对象的理解
this 是一个引用，指向当前函数的调用对象。对于非箭头函数来说，
   1. 当独立调用函数时，`this`指向全局对象；
   2. 当函数作为对象的方法调用时，`this`指向这个当前对象；
   3. 当函数以`call`，`apply`，`bind`调用，或者对于一些内置的函数例如数组的`forEach`，`map`方法，传入指定的上下文对象时，`this`指向绑定的上下文对象。其中根据传入的上下文对象类型，`this`会指向不同的对象：
      1. 如果传入是`null`或者`undefined`，则this指向全局对象；
      2. 如果传入的是`number`, `string`, `boolean`值，js会将当前值实例化，并将`this`指向其实例。（根据当前基本类型的原型对象创建实例，并将当前值作为value参数传入构造函数）；
      3. 如果传入的是函数，则会指向函数对象本身；
      4. 如果传入的是引用类型，则直接指向该引用对象。
   4. 如果用`new`调用函数，则`this`会指向该函数原型的实例对象。

2. 箭头函数和普通函数的区别？
箭头函数没有自己的 `this` `arguments` `prototype` `super`和`new.target` 以及箭头函数不能作为构造函数使用

3. 箭头函数的 `this` 指向哪里？
箭头函数的this等于其函数**声明**时所在的**上级函数被调用**时的`this`

4. 如果`new` 一个箭头函数，会发生什么？
会报错，提示箭头函数没有构造函数