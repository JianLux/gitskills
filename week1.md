# 我了解（查到）的this_(:зゝ∠)_

想要知道函数在执行过程中是如何绑定this的，首先要知道函数的调用位置。
```
function baz() {  
    //当前调用栈：baz  
   //调用位置是全局作用域  
    console.log("baz");  
    bar();//bar的调用位置  
}  
function bar() {  
    //当前调用栈：baz -> bar  
   //调用位置是baz中  
    console.log("bar");  
    foo();//foo的调用位置  
}  
function foo() {  
    //当前调用栈：baz -> bar -> foo  
   //调用位置是bar中  
    console.log("foo");  
}  
baz();//baz的调用位置  
```
执行foo函数是的调用栈为baz -> bar -> foo 。

通过函数的互相调用找出调用栈，便可以找出函数的真正调用位置:
![imamge](http://images.cnitblog.com/blog2015/628067/201505/101951434546534.png)
上图右侧向上的箭头所滑过的就是foo函数执行的调用栈，栈中的第二个元素就是真正的调用位置。

### this的绑定符合的绑定规则：

#### 1.隐式绑定： 

看调用位置是否有上下文对象，即是否被某个对象拥有或包含（这里说的包含可不是用花括号包起来呦）。
```
var a = 3;  
function foo() {  
    console.log(this.a);  
}  
var obj = {  
    a: 2,  
    foo: foo  
};  
obj.foo(); 
```
foo函数的调用位置是obj的上下文来引用函数，也就是说他的落脚点是obj对象，那他的this自然也就指向他的上下文对象。

（ps：如果对象属性的引用是多层的，只有最后一层会影响调用位置）。

***
#### 2.显式绑定： 

   上面的隐式绑定是通过调用一个对象中绑定了函数的属性来把this隐式的绑定在这个对象上的。所以显示绑定就是使用一些方法强制将函数绑定在某个对象上，这些方法就是 call(...)、apply(...) 这两种方法的工作方式是类似的。
   
   这两个方法第一个参数是一个对象，而被绑定的函数的this就会绑定在这个对象上。
```
   function foo() {  
    console.log(this.a);  
}  
var obj = {  
    a: 2  
};  
foo.call(obj); 
```

***
#### 3.new 绑定
```
function Foo(a) {  
   this.a = a;  
}  
var bar = new Foo(2);  
console.log(bar.a); 
```
使用 new 来调用函数，即发生构造函数调用时，会执行下面操作

- 创建一个全新的对象
- 这个对象会被执行[__proto__]的链接(bar.__proto__=Foo.prototype)
- 这个新对象会绑定到函数调用的this上
- 如果函数没有返回其他对象，那么new表达式中的函数调用会自动返回这个新对象

***
#### 4.默认绑定   

默认绑定就是不符合上面任何一种规则是的默认规则
```
function foo() {  
    console.log(this.a);  
}  
var a = 2;  
foo();  
```
 上面这段代码foo函数不带任何修饰的直接使用，不符合1-3中的任何一种绑定规则，所以只能使用默认绑定规则，而默认绑定规则就是在非严格模式下被绑定在全局对象上（严格模式下与foo函数的调用位置无关为undefined）。

思考：
//第一种写法
```
var a = 3;  
function foo() {  
    console.log(this.a);  
}  
var obj = {  
    a: 2,  
    foo: foo  
};  
var bar = obj.foo;  
bar();  
```
//第二种写法
```
var a = 3;  
function foo() {  
    console.log(this.a);  
}  
var obj = {  
    a: 2,  
    foo: foo  
};  
obj.foo(); 
```
