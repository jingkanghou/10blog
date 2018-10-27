javascript
# Promise≠异步，async函数≠异步  
> 本文并不提供**Promise**和**async函数**的用法说明，仅尝试通过简单的示例代码对**Promise**和**async函数**的概念和本质作简单梳理。  
>  
**Promise**和**async函数**是为异步而生，但是，如果将它们和异步划等号，可能就有问题了。  

## 先说Promise  
#### 定义 promise  
``` javascript  
// 定义 promise  
const promise = new Promise((resolve, reject) => {  
  console.log('promise start');  

  // 用一个for循环简单模拟一下大数据量处理  
  for (let index = 0; index < 5; index++) {  
    console.log('promise process ' + index);    
  }  

  console.log('promise end');  
})  

console.log('1');  
// 按照惯例，then 一下  
promise.then(()=>{console.log('Promise done')});  
console.log('2');  

```  
运行结果：  
```  
promise start  
promise process 0  
promise process 1  
promise process 2  
promise process 3  
promise process 4  
promise end  
1  
2  
```  
在这个示例代码中，定义Promise的代码在定义时就已经同步执行了，后面的then调用没有任何输出。  
A:"说明什么？说明Promise不是异步的，它的定义和执行都不是异步的！"  
B:“你胡说，你连resolve都没用！”  

#### 加入resolve  
``` javascript  
const promise = new Promise((resolve, reject) => {  
  console.log('promise start');  

  for (let index = 0; index < 5; index++) {  
    console.log('promise process ' + index);    
  }  

  // 用resolve抛出一个值  
  resolve('promise done');  

  console.log('promise end');  
})  

console.log('1');  
// then 的回调函数中接到resolve抛出的值并输出到控制台  
promise.then((v)=>{console.log(v)});  
console.log('2');  
```  
运行结果：  
```  
promise start  
Promise0  
Promise1  
Promise2  
Promise3  
Promise4  
promise end  
1  
2  
promise done  
```  
B:“异步了吧？异步了吧？‘promise done’在2后面出来的。”  
A:“整个promise执行过程是同步的，把返回值异步出来，顶个X用！”  
B:"......"  
B:"我想起来了，阮大侠说：“Promise只是个**异步操作容器**”，所以你要在Promise中装个异步操作进去才行！"  
#### 塞个异步操作  
``` javascript  
const promise = new Promise((resolve, reject) => {  
  console.log('promise start');  

  // 这个setTimeout就是塞进来的异步操作  
  setTimeout(()=>{  
    
    // 原来的大数据量处理语句移到setTimeout中来  
    for (let index = 0; index < 5; index++) {  
      console.log('Promise' + index);    
    }  

    // 原来的结果返回语句也移到setTimeout中来  
    resolve('promise done');  

  },1000);  
  
  console.log('promise end');  
})  
```  
运行结果：  
```  
promise start  
promise end  
1  
2  
Promise0  
Promise1  
Promise2  
Promise3  
Promise4  
promise done  
```  
B:“异步了吧？异步了吧？整个业务逻辑都是在2后面出来的。”  
A:“是异步了，但是setTimeout本身就执行了一个异步操作，干嘛要多此一举的把它塞进Promise里呢？”  
B:“......”  
#### 为什么要用Promise  
我们在异步调用时经常会遇到"后一个异步调用的参数是前一个异步调用的结果"的情况，传统方式写出来是这样：  
``` javascript  
a(参数,function(){  
  b(result-from-a，function(){  
    //.....  
  })  
})  
```  
a和b嵌套起来了，如果关联关系更多，可能会出现这种情况：  
``` javascript  
a(参数,function(){  
  b(result-from-a，function(){  
    c(result-from-b，function(){  
      d(result-from-c，function(){  
        e(result-from-d，function(){  
           //.....  
        })  
      })  
    })  
  })  
})  
```  
这还只是非常理想的单一数据获取情况下的嵌套，如果有分支嵌套再加上异常处理，那干脆能乱成一锅粥。这就是那个有名的“**回调地狱**”。  
怎么破？Promise!  
``` javascript  
a.then((result-from-a)=>{  
  return b(result-from-a);  
})  
.then((result-from-b)=>{  
  return c(result-from-b);  
})  
.then((result-from-c)=>{  
  return d(result-from-c);  
})  
.then((result-from-d)=>{  
  return e(result-from-d);  
})  
.then((result-from-e)=>{  
  console.log(result-from-e);  
})  
.catch((error)=>{  
  console.log(error);  
})  

```  
这样舒心多了吧？  

所以，Promise是个包装器，把异步操作包起来，以更易读更有条理更符合人类思维习惯的方式让广大程序猿（嫒）来写异步代码。如果抛开异步操作使用Promise就是耍流氓。  
要点是：**Promise是用来包装异步操作的**  


## 再看async函数  
B:”async,就是异步的意思，async函数，当然就是异步执行的函数了。这个不存在二义性吧？“  
A:”这个......，咱们用代码说话......“  
#### 初探async函数  
``` javascript  
// 定义 async 函数  
async function asynFunction(){  
  console.log('asynFunction begin');  

  // 还是用一个for循环代表大数据量处理  
  for (let index = 0; index < 5; index++) {  
    console.log('asynFunction process ' + index);    
  }  

  console.log('asynFunction end');  
}  

console.log('1');  
// 调用 async 函数  
asynFunction();  
console.log('2');  
```  
执行结果：  
```  
1  
asynFunction begin  
asynFunction process 0  
asynFunction process 1  
asynFunction process 2  
asynFunction process 3  
asynFunction process 4  
asynFunction end  
2  
```  
B:”这个......，异步函数没有异步执行？会不会是什么地方写错了？“  
A:”哈哈，加点东西给你看看“  
#### await出场  

``` javascript  
async function asynFunction(){  
  console.log('asynFunction begin');  

  // 加入 await  
  await 'async go!';  

  for (let index = 0; index < 5; index++) {  
    console.log('asynFunction process ' + index);    
  }  

  console.log('asynFunction end');  
}  

console.log('1');  
// 调用 async 函数  
asynFunction();  
console.log('2');  
```  
执行结果：  
```  
1  
asynFunction begin  
2  
asynFunction process 0  
asynFunction process 1  
asynFunction process 2  
asynFunction process 3  
asynFunction process 4  
asynFunction end  
```  
B:"从await开始，后面语句都异步了？难道await表示异步开始的意思？......"  
B:"不对啊，wait是等待的意思，await明明是用来等待异步执行结果的，在这里怎么变成异步开始的标志了？......"  
A:"你说对了，await是用来等待异步执行结果的。但是await在使用时，后面需要跟一个Promise对象，如果不是，会被转为Promise对象，如上面的`await 'async go!'`相当于如下代码：  
``` javascript  
await new Promise((resolve, reject) => {  
      resolve('async go!');  
    })  
```  
A:”所以，await并不是异步开始的标志，而是因await对异步执行的等待，导致await之后语句延后执行。我们再写段代码看看......“  
#### 引入Promise  
``` javascript  
function a(arg){  
  return new Promise((resolve, reject) => {  
      resolve(arg);  
    })  
}  
function b(arg){  
  return new Promise((resolve, reject) => {  
      resolve('你好，' + arg);  
    })  
}  

async function asynFunction(){  
  var aResult = await a('北京');  
  var bResult = await b(aResult);  

  console.log(bResult);  
}  

console.log('1');  
asynFunction();  
console.log('2');  
```  
执行结果：  
```  
1  
2  
你好，北京  
```  
A:”当然，我们往往需要通过async函数返回需要的数据......“  
``` javascript  
function a(arg){  
  return new Promise((resolve, reject) => {  
      resolve(arg);  
    })  
}  
function b(arg){  
  return new Promise((resolve, reject) => {  
      resolve('你好，' + arg);  
    })  
}  

async function asynFunction(){  
  var aResult = await a('北京');  
  var bResult = await b(aResult);  

  // 返回执行结果  
  return bResult;  
}  

console.log('1');  
// 需要在then()方法中获取执行结果，原因是async函数的返回值同样会被包装成Promise对象  
asynFunction().then((v)=>{console.log(v)});  
console.log('2');  
```  
执行结果：  
```  
1  
2  
你好，北京  
```  
async函数其实也是一个包装器，async和await配合，包装的是Promise，与Promise相比易读性更好，更加符合代码语义。  
要点是：**async函数是用来包装Promise的**  

## 总结  
Promise是用来包装异步操作的，所以仅仅包含同步代码它是异步不了滴。  
Promise把异步操作包装起来，是为了让你的异步代码看起来更直观更优雅。  

async函数是用来包装Promise的，所以仅仅包含同步代码它也是异步不了滴。  
async函数把Promise包装起来，是为了让你的异步代码看起来比Promise还要直观还要  
优雅，能够做到望文生义......  

本文的初衷其实是希望大家不要望文生义......>_<  

## 题外话  
- 为什么示例中的异步操作都在主线代码之后执行？  
  因为event-loop,不了解点[这里](https://pan.baidu.com/s/1tIjs5zZ1a_P2cHDQ9UgjJA)(密码：yc3r)  
- 为什么Promise示例中总是有一个reject  
  当发生错误、异常，未获取到数据等情况时会用到它，不了解点[这里](http://es6.ruanyifeng.com/#docs/promise)  
- 为什么async函数中的await可以等待  
  因为它们是generator的语法糖，不了解点[这里](http://es6.ruanyifeng.com/#docs/generator)  





