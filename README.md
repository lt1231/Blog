# Blog
js面向对象之对象创建篇
创建对象的方式
1. Object构造函数或者对象字面量创建当对象
2. 构造函数创建对象
3. 工厂模式
4. 原型模式
5. 原型和构造函数组合模式
6. 动态原型模式
7. 寄生构造函数模式
8. 稳妥构造函数模式

## Object构造函数或者对象字面量创建当对象
缺点使用一个接口创建很多对象，会产生大量重复代码

## 工厂模式
这种模式抽象了创建具体对象的过程.考虑到在ECMA中无法创建类，开发人员就发明了一种函数，用函数封装以特定接口创建对象的细节
缺点：对象类型不可知
```
function createPerson(name, age, job){
var o = new Object();
o.name = name;
o.age = age;
o.job = job;
o.sayName = function(){
alert(this.name);
};
return o;
}
var person1 = createPerson("Nicholas", 29, "Software Engineer");
var person2 = createPerson("Greg", 27, "Doctor");
```
## 构造函数创建对象
创建自定义的构造函数，从而定义自定义对象类型的属性和方法

优点：对象类型可知

缺点：缺点：构造了多个相同功能的方法。（可以通过this.func=window.func,但这样函数变全局，失去封装性）

```
function Person(name, age, job){
this.name = name;
this.age = age;
this.job = job;
this.sayName = function(){
alert(this.name);
};
}
var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
```
## 原型模式
优点：可以共享属性的实例和方法，解决了构造函数的缺点

缺点：所有实例在默认情况下都将取得相同的属性值，最大问题是，由其共享属性的本性所导致，
当一个实例的属性发生变化时，所有实例属性都会发生变化

不必在构造函数中定义对象实例的信息，而是可以将这些信息直接添加到原型对象中

```
function Person(){
}
Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function(){
alert(this.name);
};
var person1 = new Person();
person1.sayName(); //"Nicholas"
var person2 = new Person();
person2.sayName(); //"Nicholas"
alert(person1.sayName == person2.sayName); //true
```
更简单的原型语句

常见的做法是用一个包含所有属性和方法的对象字面量来重写整个原型对象


```
function Person(){
}
Person.prototype = {
name : "Nicholas",
age : 29,
job: "Software Engineer",
sayName : function () {
alert(this.name);
}
};
```
这种写法本质上重写了默认的prototype 对象,constructor 属性也就变成了新
对象的constructor 属性（指向Object 构造函数），不再指向Person 函数。如果constructor 的值真的很重要，可以像下面这样特意将它设
置回适当的值。

```
function Person(){
}
Person.prototype = {
constructor : Person,
name : "Nicholas",
age : 29,
job: "Software Engineer",
sayName : function () {
alert(this.name);
}
};
```
## 组合使用构造函数模式和原型模式
优点:既有构造函数的优点又有原型模式的优点，构造函数用于定义实例属性，原型模式用于定义方法和共享属性，每个实例都会有自己的一份实例属性的副本，但同时又共享着对方法的引用，最大限度地节省了内存

缺点：不突出原型编程泛型的特点

```
function Person(name, age, job){
this.name = name;
this.age = age;
this.job = job;
this.friends = ["Shelby", "Court"];
}
Person.prototype = {
constructor : Person,
sayName : function(){
alert(this.name);
}
}
var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
person1.friends.push("Van");
alert(person1.friends); //"Shelby,Count,Van"
alert(person2.friends); //"Shelby,Count"
alert(person1.friends === person2.friends); //false
alert(person1.sayName === person2.sayName); //true
```
## 动态原型模式
通过检查某个应该存在的方法是否有效，来决定是否需要初始化原型

确保原型方法只定义一次，if检测只需一次。（若重写原型会切断与原有实例的联系,原型模式同坑）

```
function Person(name, age, job){
//属性
this.name = name;
this.age = age;
this.job = job;
//方法
if (typeof this.sayName != "function"){
Person.prototype.sayName = function(){
alert(this.name);
};
}
}
var friend = new Person("Nicholas", 29, "Software Engineer");
friend.sayName();
```
## 寄生构造函数模式
优点：可用于构造方法的重构。

缺点：对象类型不可知，此法少用

```
function Person(name, age, job){
var o = new Object();
o.name = name;
o.age = age;
o.job = job;
o.sayName = function(){
alert(this.name);
};
return o;
}
var friend = new Person("Nicholas", 29, "Software Engineer");
friend.sayName(); //"Nicholas"
```
## 稳妥构造函数模式
所谓稳妥对象，指的是没有公共属性，而且其方法也不引用this 的对象。稳妥对象最适合在
一些安全的环境中（这些环境中会禁止使用this 和new）

优点：专用于安全执行环境,构造函数的变量只能通过get函数来获取。

缺点：对象类型不可知

```
function Person(name, age, job){
//创建要返回的对象
var o = new Object();
//可以在这里定义私有变量和函数
//添加方法
o.sayName = function(){
alert(name);
};
//返回对象
return o;
```

