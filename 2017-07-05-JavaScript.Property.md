JavaSctipt中prototype

在典型的面向对象的语言中，如java，都存在类（class）的概念，类就是对象的模板，对象就是类的实例。但是在Javascript语言体系中，是不存在类（Class）的概念的，javascript中不是基于‘类’的，而是通过构造函数（constructor）和原型链（prototype chains）实现的。但是在ES6中提供了更接近传统语言的写法，引入了Class（类）这个概念，作为对象的模板。通过class关键字，可以定义类。基本上，ES6的class可以看作只是一个语法糖，它的绝大部分功能，ES5都可以做到，新的class写法只是让原型对象的写法更加清晰、更像面向对象编程的语法。

本文将涉及到六个内容：1、构造函数的简单介绍；2、构造函数的缺点；3、__proto__属性；4、prototype属性的作用；5、原型链（prototype chain）；6、constructor属性

1、构造函数的简单介绍：

构造函数：就是提供了一个生成对象的模板并描述对象的基本结构的函数。一个构造函数，可以生成多个对象，每个对象都有相同的结构。总的来说，构造函数就是对象的模板，对象就是构造函数的实例。

构造函数的特点：a:构造函数的函数名首字母必须大写。b:内部使用this对象，来指向将要生成的对象实例。c:使用new操作符来调用构造函数，并返回对象实例。

例如:
```
function Person(){
this.name = ‘xiaoming’;

}

var boy = new Person();

console.log(boy.name); //xiaoming
```
2、构造函数的缺点：

所有的实例对象都可以继承构造函数中的属性和方法。但是，同一个对象实例之间，无法共享属性。

例如：
```
function Person(name,height){
this.name = name;

this.height = height;

this.hobby = function() {

return ‘watching movies’;

}

}

 

var boy = new Person(‘keith’,180);

var girl = new Person(‘rescal’,150)

 

console.log(boy.name);//keith

console.log(girl.name);//rascal

console.log(boy.hobby === girl.hobby);//false

```

上面代码中，一个构造函数Person生成了两个对象实例boy和girl，并且有两个属性和一个方法。但是，它们的hobby方法是不一样的。也就是说，每当你使用new来调用构造函数放回一个对象实例的时候，都会创建一个hobby方法。因为所有的bobby方法都是同样的行为，完全可以被两个对象实例共享。所以构造函数的缺点就是：同一个构造函数的对象实例之间无法共享属性和方法。

3、__proto__属性:（隐式原型）

隐式原型：一个对象的隐式原型指向构造该对象的构造函数的原型，这也保证了实例能够访问在构造函数原型中定义的属性和方法。

对象__proto__属性的值就是它所对应的原型对象：

例如：
```
     var one = {x:1};
var two = new Object();

console.log(one.__proto__ === Object.prototype);//true

console.log(two.__proto__ === Object.prototype);//true

console.log(one.toString === one.__proto__.toString);//true

 
```
4、prototype属性的作用：（显式原型）

为了解决构造函数的对象实例之间无法共享属性的缺点，js提供了prototype属性。JavaScript 中的所有事物都是对象：字符串、数值、数组、函数…，而每个对象都继承自另外一个对象，后者称为“原型”（prototype）对象。（null除外，null没有自己的原型对象。）

例如:
```
function Person(name,height){
this.name = name;

this.height = height;

}

 

Person.prototype.hobby = function(){

return ‘watching movies’;

}

 

var boy = new Person(‘keith’,180);

var girl = new Person(‘rescal’,150)

 

console.log(boy.name);// keith

console.log(girl.name);// rescal

console.log(boy.hobby === girl.hobby); //true

```

上面代码中，如果将hobby方法放在原型对象上，那么两个实例对象都共享着同一个方法。对于构造函数来说，prototype是作为构造函数的属性；对于对象实例来说，prototype是对象实例的原型对象。所以prototype即是属性，又是对象。

原型对象的属性不是对象实例的属性。对象实例的属性是继承自 构造函数定义的属性，因为构造函数内部有一个this关键字来指向将要生成的对象实例。对象实例的属性，其实就是构造函数内部定义的属性。只要修改原型对象上的属性和方法，变动就会立刻体现在所有对象实例上。

例如：
```
function Person(name,height){
this.name = name;

this.height = height;

console.log(this)

}

 

Person.prototype.hobby = function(){

return ‘swimming’;

}

 

var boy = new Person(‘keith’,180);

var girl = new Person(‘rescal’,150)

 

console.log(boy.hobby()); //swimming

console.log(girl.hobby()); //swimming

console.log(boy.hobby === girl.hobby); //true
```
上面代码中，当修改了原型对象的hobby方法之后，两个对象实例都发生了变化。这是因为对象实例其实是没有hobby方法，都是读取原型对象的hobby方法。也就是说，当某个对象实例没有该属性和方法时，就会到原型对象上去查找。如果实例对象自身有某个属性或方法，就不会去原型对象上查找。
```
function Person(name,height){
this.name = name;

this.height = height;

}

 

Person.prototype.hobby = function(){

return ‘swimming’;

}

 

var boy = new Person(‘keith’,180);

var girl = new Person(‘rescal’,150);

 

boy.hobby = function() {

return ‘play backetball’;

}

console.log(boy.hobby()); //play backetball

console.log(girl.hobby()); //swimming

console.log(boy.hobby === girl.hobby); //false
```
上面代码中，boy对象实例的hobby方法修改时，就不会在继承原型对象上的hobby方法了。不过girl仍然会继承原型对象的方法。

总结一下：

a：原型对象的作用，就是定义所有对象实例所共享的属性和方法。

b：prototype，对于构造函数来说，它是一个属性；对于对象实例来说，它是一个原型对象。

（PS:对于__proto__和prototype做一个总结：

①JavaScript是通过__proto__和prototype的合作实现了原型链，以及对象的继承。

②构造函数是通过prototype来存储要共享的属性和方法，也可以设置prototype指向现存的对象来继承该对象。

③对象的__proto__指向自己构造函数的prototype。obj.__proto__.__proto__…的原型链由此产生，包括我们的操作符instanceof正是通过探测obj.__proto__.__proto__… === Constructor.prototype来验证obj是否是Constructor的实例。

④例如上面的例子：two = new Object()中Object是构造函数，所以two.__proto__就是Object.prototype。至于one，ES规范定义对象字面量的原型就是Object.prototype。）

5、原型链（prototype chains）

对象的属性和方法，有可能是定义在自身，也有可能是定义在它的原型对象。由于原型对象本身对于对象实例来说也是对象，它也有自己的原型，所以形成了一条原型链（prototype chain）。比如，a对象是b对象的原型，b对象是c对象的原型，以此类推。所有一切的对象的原型顶端，都是Object.prototype，即Object构造函数的prototype属性指向的那个对象。

当然，Object.prototype对象也有自己的原型对象，那就是没有任何属性和方法的null对象，而null对象没有自己的原型。

例如：
```
function Person(name,height){
this.name = name;

this.height = height;

}

 

Person.prototype.hobby = function(){

return ‘swimming’;

}

 

var boy = new Person(‘keith’,180);

var girl = new Person(‘rescal’,150);

 

boy.hobby = function() {

return ‘play backetball’;

}

// console.log(boy.hobby()); //play backetball

// console.log(girl.hobby()); //swimming

// console.log(boy.hobby === girl.hobby); //false

console.log(Object.getPrototypeOf(Object.prototype)); // null

console.log(Person.prototype.isPrototypeOf(boy)); // true

 
```
原型链（prototype chain）的特点有：

a：读取对象的某个属性时，JavaScript引擎先寻找对象本身的属性，如果找不到，就到它的原型去找，如果还是找不到，就到原型的原型去找。如果直到最顶层的Object.prototype还是找不到，则返回undefined。

b：如果对象自身和它的原型，都定义了一个同名属性，那么优先读取对象自身的属性，这叫做“覆盖”（overiding）。

c：一级级向上在原型链寻找某个属性，对性能是有影响的。所寻找的属性在越上层的原型对象，对性能的影响越大。如果寻找某个不存在的属性，将会遍历整个原型链。

看概念可能比较晦涩，我们来看一个例子。但是理解了概念真的很重要。

例如：
```
var arr=[1,2,3];
console.log(arr.length);//3

console.log(arr.valueOf());// [1, 2, 3]

console.log(arr.join(‘|’));//1|2|3
```
上面的代码中，定义了一个数组arr,数组里面有三个元素。我们并没有给数组添加任何属性和方法，可是却在调用length,join(),valueOf()时，却不会报错。

length属性是继承自Array.prototype的，属于原型对象上的一个属性。join方法也是继承自Array.prototype的，属于原型对象上的一个方法。这两个方法是所有数组所共享的。当实例对象上没有这个length属性时，就会去原型对象查找。

valueOf方法是继承自Object.prototype的。首先，arr数组是没有valueOf方法的，所以就到原型对象Array.prototype查找。然后，发现Array.prototype对象上没有valueOf方法。最后，再到它的原型对象Object.prototype查找。

Array.prototype对象和Object.prototype对象 所拥有的属性和方法:

（Object.getOwnPropertyNames()方法返回一个由指定对象的所有自身属性的属性名（包括不可枚举属性）组成的数组。）
```
console.log(Object.getOwnPropertyNames(Array.prototype));
// [“length”, “constructor”, “toString”, “toLocaleString”, “join”, “pop”, “push”, “reverse”, “shift”, “unshift”, “slice”, “splice”, “sort”, “filter”, “some”, “every”, “map”, “indexOf”, “lastIndexOf”, “reduce”, “reduceRight”, “copyWithin”, “find”, “findIndex”, “fill”, “includes”, “entries”, “forEach”, “keys”, “concat”]

console.log(Object.getOwnPropertyNames(Object.prototype));

// [“__defineGetter__”, “__defineSetter__”, “hasOwnProperty”, “__lookupGetter__”, “__lookupSetter__”, “propertyIsEnumerable”, “valueOf”, “__proto__”, “constructor”, “toString”, “toLocaleString”, “isPrototypeOf”]

 
```
6、constructor属性

prototype对象有一个constructor属性，默认指向prototype对象所在的构造函数。

例如：
```
function A(){}
console.log(A.prototype.constructor===A); //true
```
要注意的是：prototype是构造函数属性，而constructor则是构造函数的prototype属性所指向的那个对象，也就是原型对象的属性。