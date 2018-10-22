javascript
# void的用法test

## void的说明1  test
我们来看看 MDN 的解释：

The void operator evaluates the given expression and then returns undefined.

意思是说 void 运算符能对给定的表达式进行求值，然后返回 undefined。也就是说，void 后面你随便跟上一个表达式，返回的都是 undefined，都能完美代替 undefined！那么，这其中最短的是什么呢？毫无疑问就是 void 0 了。其实用 void 1，void (1+1)，void (0) 或者 void “hello”，void (new Date()) 等等，都是一样的效果。更重要的前提是，void 是不能被重写的（cannot be overidden）。

那么，ES5 大环境下，void 0 就没有用武之地了吗？答案是否定的，用 void 0 代替 undefined 能节省不少字节的大小，事实上，不少 JavaScript 压缩工具在压缩过程中，正是将 undefined 用 void 0 代替掉了。


## void的说明2

事实上，void的返回值都是undefined。 https://developer.mozilla.org...

在ES5之前，window下的undefined是可以被重写的，于是导致了某些极端情况下使用undefined会出现一定的差错。

所以，用void 0是为了防止undefined被重写而出现判断不准确的情况。

注： ES5之后的标准中，规定了全局变量下的undefined值为只读，不可改写的，但是局部变量中依然可以对之进行改写。

补充一下：非严格模式下，undefined是可以重写的，严格模式则不能重写


## 用void(0)表示死链接
#### href="#"与href="javascript:void(0)"的区别

在页面很长的时候会使用 # 来定位页面的具体位置，格式为：# + id。
href="#"包含了一个位置信息，默认的锚是#top 也就是网页的上端。
如果你要定义一个死链接请使用 javascript:void(0) 。

#### javascript:void(0)可能出现的问题及解决方法
##### 方案摘抄1
链接（href）直接使用javascript：void(0)在IE中可能会引起一些问题，比如：造成gif动画停止播放等，所以，最安全的办法还是使用“####”。为防止点击链接后跳转到页首，onclick事件return false即可。 

##### 方案摘抄2
火狐和IE下href="javascript:void(0) 会弹出空白页

经过排查，发现是href="javascript:void(0);"导致的问题，本来javascript:void(0);的用处是不用整体刷新网页且返回一个空值，但这儿由于DOM本身的冒泡事件所以会最后执行HREF属性内的javascript:void(0);导致执行函数返回了一个空值，所以覆盖掉了前面正常执行函数所返回的值引起的错误。
一般情况下，IE会先运行DOM本身绑定的事件，如ONCLICK;如果没有阻止冒泡，则会顺序执行HREF属性。如果想正确运行，可以在前面用RETURN FALSE终止冒泡，例如：
<a target="_blank" class="prev" onclick="return false;"   href="javascript:void(0);"></a>
或者直接删去也行，如：
<a target="_blank"  class="prev" ></a> 


这篇文较全：
https://www.jb51.net/article/37904.htm

