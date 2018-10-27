
# 作用域和闭包
源自“你不知道了javascript”系统“作用域和闭包”
## 什么是作用域
### 编译原理
- 编译三步骤
  （以`var a = 2;`为例）
  1. 分词/词法分析
    分解成词法单元：var、a、=、2、;。
  2. 解析/语法分析
    将词法单元转换成“抽象语法树”(Abstract Syntax Tree,AST)
    ![AST示例(var a = 2;)](images/AST-Demo.png)
  3. 代码生成
    将AST转换为可执行代码（机器代码）
- 变量赋值的两个步骤
  1. 编译器声明变量
    ![编译器声明变量](./images/Assignment1.png)
  2. 引擎为变量赋值
    ![引擎为变量赋值](images/Assignment2.png)

### LHS查询和RHS查询
- 全称 
  LHS（Left-Hand-Side）查询和RHS(Right-Hand-Side)查询
- 概念
  Left和Right是指赋值操作符的左侧和右侧。比如 a = b，对a进行查询就是LHS，对b进行查询就是RHS查询
  对LHS查询和RHS查询更准确的描述应该是：
    - LHS查询，对赋值目标的查询
    - RHS查询，对取值来源的查询
  原因是，并不是所有的赋值目标都出现的=左边，比如函数传参

- 查询什么？
  当代码执行时，遇到一个变量，引擎会在查询该变量是否被声明过？存在什么地方？查询到后对其进行赋值操作或取值操作。

- 查询过程
  ``` javascript
  function f1(){
    function f2(){
      a = b;
    }
  }
  ```
  - LHS，此例中即对a的查找。
    - 引擎会逐级向上查询，先在f2中查询是否声明过a，如果没有去上一级作用域(f1)中查询，直到查询到全局作用域。如果全局作用域也没有定义，非严格模式下，会自动在全局作用域中创建一个同名变量，严格模式下，会抛出ReferenceError。
    - 在任何一级找到名为a的变量，将不再向上查找，将b的值赋给a。
  - RHS,此例中即对b的查找。
    - 引擎同样会逐级向上查询，先在f2中查询是否声明过b，如果没有去上一级作用域(f1)中查询，直到查询到全局作用域。如果全局作用域也没有定义，会抛出ReferenceError。
    - 在任何一级找到名为b的变量，将不再向上查找，取到b的值。

### 特殊的作用域行为
- eval
  JavaScript 中的eval(..) 函数可以接受一个字符串为参数，并将其中的内容视为好像在书
写时就存在于程序中这个位置的代码。
  因此，
  ``` javascript 
  function a(){
    eval('var b = 0');
  }
  ```
  相当于在函数a的作用域中声明了一个名为b的变量，也就是说eval(..) 可以在运行期修改书写期的词法作用域。
- with
  with 块将对象及其属性放进一个作用域并同时分配标识符，但是这个块内部正常的var声明并不会被限制在这个块的作用域中，而是被添加到with 所处的函数作
用域中
- 性能问题
  由于eval和with是在**运行时**修改或创建新的作用域，在编译过程中未作优化，因此其性能不好。
- 不建议使用的原因
  - 非常规的作用域处理
  - 性能问题
  - 安全问题

### 块级作用域
#### 四个块级作用域
- with
- try/catch
  catch会创建一个块级作用域，其中声明的变量会在catch内部有效。
- let
- const
#### 块级作用域的垃圾回收
块级作用域的另一个重要作用--垃圾回收。
``` javascript
// 块中定义的内容出了块就可以销毁了
{
  let someData = {....};
  process(someData);
}
```
#### let和const不会提升？
看下面的代码：
``` javascript 
let b = 0;
function f(){
  console.log(b);// 0
}
f();
```
再看下面的代码：
``` javascript 
let b = 0;
function f(){
  console.log(b); // Uncaught ReferenceError: b is not defined
  let b = 1; // 在函数中定义b
}
f();
```
当在`console.log(b); `之后定义b,输出的不再是0,而是报异常。也就是说let对变量的声明被提升了，只是在定义语句所在位置之前的代码不能使用这个变量。
### 提升
- 提升的原理
在**“变量赋值的两个步骤”**中我们知道，编译器先声明变量，然后运行时引擎才会执行代码为其赋值，所以：
``` javascript
function a() {
  console.log(b);
  var b = 1;
}
```
上段代码在编译时b已经被声明，所以在运行时调用该函数时，其实是如下代码：
``` javascript
  // b已在编译时被声明，而未赋值
  console.log(b);//因此，此处是undefined
  b = 1;
```
为了直观，很多文章会说这个函数的字义相当于：
``` javascript
function a() {
  var b;
  console.log(b);
  b = 1;
}
```
看上去，b的定义被提前了，所以被称为**提升**。
- 函数声明提升
  函数声明包含了整个函数体，函数的提升因此相当于整个函数被提升，所以：
  ``` javascript
  foo();
  function foo() {
    console.log('hello');    
  }
  ```
  相当于：
  ``` javascript
  function foo() {
    console.log('hello');    
  }
  foo();
  ```
  另外函数提升优先于变量提升：
  ``` javascript
  foo(); // 1

  var foo;

  function foo() {
    console.log( 1 );
  }

  foo = function() {
    console.log( 2 );
  };
  ```
  相当于：
  ``` javascript
  function foo() {
    console.log( 1 );
  }

  // var foo;  // 它是重复的声明（因此被忽略了）
  
  foo(); // 1
  
  foo = function() {
    console.log( 2 );
  };

  ``` 


### 闭包
``` javascript
  function foo() {
    var a = 2;

    function bar() {
      console.log( a ); // 2
    }

    bar();
  }

  foo();
```
在上面的代码片段中，函数bar() 具有一个涵盖foo() 作用域的闭包（事实上，涵盖了它能访问的所有作用域，比如全局作用域）。也可以认为bar() 被封闭在
了foo() 的作用域中。为什么呢？原因简单明了，因为bar() 嵌套在foo() 内部。
``` javascript
  function foo() {
    var a = 2;
    
    function bar() {
      console.log( a );
    }
    
    return bar;
  }

  var baz = foo();
  
  baz(); // 2 —— 朋友，这就是闭包的效果。
```
foo()调用完成，但baz()引用了bar()，bar()依然持有对该作用域的引用，使得该作用域能够一直存活，而这个引用就叫作闭包。

下面的内容源自 [深入理解JavaScript系列（16）：闭包（Closures）](http://www.cnblogs.com/TomXu/archive/2012/01/31/2330252.html)
根据函数创建的算法，我们看到 在ECMAScript中，所有的函数都是闭包，因为它们都是在创建的时候就保存了上层上下文的作用域链（除开异常的情况） （不管这个函数后续是否会激活 —— [[Scope]]在函数创建的时候就有了）：
``` javascript
var x = 10;

function foo() {
  alert(x);
}

// foo是闭包
foo: <FunctionObject> = {
  [[Call]]: <code block of foo>,
  [[Scope]]: [
    global: {
      x: 10
    }
  ],
  ... // 其它属性
};
```