javascript
# 一句话明白箭头函数中的this
关于箭头函数中this值的问题，网上查查，会告诉你 “箭头函数的this固定化，箭头函数中的this绑定定义时所在的作用域......”，好绕！
> 箭头函数中this倒底指向谁？一句话，**箭头函数内的this就是箭头函数外的那个this！** 为什么？因为箭头函数没有自己的this。
如果你已明白这句话的意思，那么本文到此结束，你可以去看看别的更有营养的东西了。
如果我这句话描述的不够清楚，或你对它有怀疑，请继续往下看。如果说错了请使劲拍砖。

**直接上代码！**
> 注：以下代码全基于浏览器非严格模式运行，且假定你已经对非箭头函数中的this全然了解

咱先定义一个全局变量a，后面所有的示例都假定此全局变量已存在:
``` javascript
var a = 0;
```

#### 第一个示例：
``` javascript
// demo1
var obj =  {
  a: 1,
  b1: this.a,
  b2: () => this.a,
};

console.log(obj.b1);  // 0
console.log(obj.b2());  // 0
```
根据结果可以看到obj对象中的属性b1的值来自**全局变量a**，而不是**obj对象的属性a**,也就是说`b1: this.a,`中的this指向window。而`b2: () => this.a,`中的this同样指向window，返回全局变量a的值。

#### 第二个示例：
``` javascript
// demo2
(function fn(){
  var a = 2;
  console.log(this.a);  // 0

  const subf = ()=>this.a;
  console.log(subf()); // 0
})()
```

#### 第三个示例：
``` javascript
// demo3
var obj2 =  {
  a: 3,
  b1: function(){ return this.a;},
  b2: function(){ return ()=>this.a},
};
console.log(obj2.b1());  // 3
console.log(obj2.b2()());  // 3
```
这个示例中箭头函数外的this是哪个？我们在箭头函数外面加个this.a看看：
``` javascript
var obj2 =  {
  a: 3,
  b1: function(){ return this.a;},
  b2: function(){ return this.a,()=>this.a},
};
console.log(obj2.b1());  // 3
console.log(obj2.b2()());  // 3
```

#### 第四个示例：
``` javascript
// demo4
var obj3 =  {
  a: 4,
  b1: function(){ return this.a;},
  b2: function(){ return ()=> () => () => this.a},
};
console.log(obj4.b1());  // 4
console.log(obj4.b2()()()());  // 4
```
这里箭头函数返回箭头函数再返回箭头函数，最后一个箭头函数返回this.a。这个this是谁？就是最后箭头函数外面的外面的外面的那个this,也就是obj3.b1中的那个this，也就是obj3.a。:)
实情是这样：箭头函数没有自己的this，所以不管嵌套多少层，都不影响this是谁。

#### 第五个示例：
``` javascript
// demo5
function f0() {
  var a = 5;

  setTimeout(function() {
    var b1 = this.a;
    console.log(b1);  // 0
  }, 100);

  setTimeout(function() {
    var b2 = ()=> this.a;
    console.log(b2());  // 0
  }, 200);
}

f0(); // 0  0
```
我们都知道setTimeout中的那个function运行于全局环境下，因此里面的this指向window。而箭头函数没有自己的this，所以第二个setTimeout中箭头函数的this也指向window.

那么setTimeout的第一个参数使用箭头函数会是个什么情况？看下一个示例。


#### 第六个示例：
``` javascript
// demo6
function f1() {
  console.log(this.a); 
  setTimeout(() => {
    console.log(this.a);  
  }, 100);
}
f1(); // 0  0
```
这里箭头函数作为setTimeout的第一个参数，箭头函数外面的那个this是谁?把箭头函数换成`this.fn()`：
``` javascript
function f1() {
  console.log(this.a); // 0
  setTimeout(this.fn() , 100); 
}
```
我们这里不用了解this.fn()有什么功能，看到它我们就会明白了this.fn()中的this和前面一行中的this.a中的this是同一个this。

扩展一下：
``` javascript
// 这样执行
f1.call({a: 7});  // 7  7
```
结果是输出两个7,
为什么？
``` javascript
// demo6
function f1() {
  console.log(this.a);   // 因为通过call对this的绑定，f1中的this指向 {a: 7}
  setTimeout(() => {
    console.log(this.a);  // 同样这里this指向 {a: 7}
  }, 100);
}
```

同理：
``` javascript
var bindf1 = f1.bind({a: 8}); 
bindf1(); // 8 8
```

## 总结
箭头函数没有自己的this，箭头函数中用this和普通语句中的this没什么区别，所以，你知道非箭头函数下怎么用this,就知道箭头函数下怎么用this。
关于 “箭头函数对this固定化，箭头函数中的this绑定定义时所在的作用域，箭头函数不能通过 call() 或 apply() 方法绑定this” 等描述，都源于箭头函数没有自己的this。
