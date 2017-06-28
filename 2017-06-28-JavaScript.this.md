在javascript当中每一个function都是一个对象，所以在这个里var temp=this 指的是function当前的对象。this是Javascript语言的一个关键字。它代表函数运行时，自动生成的一个内部对象，只能在函数内部使用。

this出现的场景分为四类

1)、有对象就指向调用对象

2)、没调用对象就指向全局对象：window是js中的全局对象

3)、用new构造就指向新对象

4)、通过apply或者call或bind来改变this的指向

 

1、函数有所属对象时：指向所属对象

函数有所属对象时，通常通过 . 表达式调用，这时 this 自然指向所属对象。比如下面的例子：

var myObject = {value:100};
myObject.getValue = function() {

console.log(this.value);//输出100

console.log(this);//输出{value：100，getValue:function},其实就是myObject对象本身

return this.value;

}

console.log(myObject.getValue());//100

getValue()属于对象myObject(),并由myObject进行.调用，因此this指向对象myObject。

2、 函数没有所属对象：指向全局对象

var myObject = {value:100};
myObject.getValue = function() {

var foo = function() {

console.log(this.value);//undefined

console.log(this);//输出全局对象Window

};

foo();

return this.value;

};

console.log(myObject.getValue());//100

在上述代码块中，foo 函数虽然定义在 getValue 的函数体内，但实际上它既不属于 getValue 也不属于 myObject。foo 并没有被绑定在任何对象上，所以当调用时，它的 this 指针指向了全局对象 window.(PS:foo对象没有被上一级对象所调用，所以在这里this指向的就是全局对象window。)

另一种写法：

window.name = ‘xiaoming’;
function foo() {

console.log(this.name); //xiaoming

console.log(this);//全局对象window

}

foo();

3、构造器中的this：指向新对象

Js中我们通过new关键词来调用构造函数，此时this会绑定在新对象上。

var someClass = function() {
this.value = 100;

}

var myCreate = new someClass();

console.log(myCreate.value);

 

4、 apply 和 call 调用以及 bind 绑定：指向绑定的对象

Apply()方法接受两个参数，第一个是函数运行的作用域，另一个是参数数组（arguments）。

Call()方法第一个参数的意义与apply()方法相同，只是其他的参数需要一个个列举出来。

简单的来说，call的方式更接近我们平时调用函数，而apply需要传递Array形式的数组给它，它们可以相互转换的。

var myObject = {value:100};
var foo = function(){

console.log(this);

}

foo();//Window

foo.apply(myObject);//Object {value: 100}

foo.call(myObject);//Object {value: 100}

 

var newFoo = foo.bind(myObject);

newFoo();//Object {value: 100}

 

5、关于this的指向

this的指向在函数定义的时候是解决不了的，只有在函数执行的时候才能确定this到底指向谁，实际上this的最终指向是那个调用它的对象。

例一：

function a() {
var user = ‘admin’;

console.log(this);//Window

console.log(this.user);//undefined

}

a();

按照我们上面说的this最终指向的是调用它的对象，这里的函数a实际是被window对象多点出来的，下面的代码可以证明：

function a() {
var user = ‘admin’;

console.log(this);//Window

console.log(this.user);//undefined

}

window.a();

例二：

var a={
user:’admin’,

fn:function(){

console.log(this.user)//admin

console.log(this)//Object {user: “admin”, fn: function}  this指向的是a对象

}

}

a.fn();

这里的this指的是a,因为你调用这个fn是通过a.fn()执行的，那自然指向就是对象a，this的指向在行数创建的时候是决定不了的，在调用的时候才可以决定。

例三：推翻上面的理论

var a = {
user:’adimin’,

foo:function(){

console.log(this.user);//adimin

console.log(this);//Object {user: “adimin”, foo: function}

}

}

window.a.foo();//输出的是a对象里面的user

按道理来说this会指向调用它的对象，window是js中的全局对象，而我们创建的变量其实是给window添加属性，所以我们可以用window.a

例如：

var a = {
b:10,

foo:{

b:20,

show:function(){

console.log(this);//Object {b: 20, show: function}

//this指向的是 foo对象

}

}

}

a.foo.show();

a.foo.show();输出的是foo对象的内容，为什么会是foo?而不是a!下面分析一下this的几种情况：

1、如果一个函数中有this，但是它没有被上一级的对象所调用，那么this指向的就是window,这里需要说明的是在js的严格版本中this指向的不是window,但是我们不讨论严格模式。

2、如果一个函数中有this，这个函数有被上一级对象调用，那么this指向的就是上一级的对象。

3、如果一个函数中有this,这个函数中包含多个对象，尽管这个函数是被最外层的对象调用，this指向的也只是它上一级的对象，上面的例子可以证明。

例四：继续探讨例三的情况

var a = {
b:10,

foo:{

show:function(){

console.log(this.b);//undefined

//this指向的是 foo对象

}

}

}

a.foo.show();

a.foo.show();输出的是undefined；从这里可以看出尽管对象foo中没有b这个属性，这个this指向的也是对象foo,因此this只会指向它的上一级对象，不管这个对象中有没有this要的东西。

例五：特殊情况

var a = {
b:10,

foo:{

b:20,

show:function(){

console.log(this.b);//undefined

console.log(this);//Window {stop: function, open: function, alert: function, confirm: function, prompt: function…}

}

}

}

var j = a.foo.show;

j();

这里的this指向的是window,为什么会这样？因为：this永远指向的是最后调用它的对象。也就是看它执行的时候是谁调用的，上面的案例中show被foo引用，但是在将show赋值给变量j的时候并没有执行，所以最终指向的是window
