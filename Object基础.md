
###1.创建对象方法
```
let user = new Object(); // “构造函数” 的语法
let user = {};  // “字面量” 的语法
```
###对象基本总结
对象是具有一些特殊特性的关联数组。

它们存储属性（键值对），其中：
>* 属性的键必须是字符串或者 symbol（通常是字符串）
>* 值可以是任何类型。

我们可以用下面的方法访问属性：
>* 点符号: obj.property。
>* 方括号 obj["property"]，方括号允许从变量中获取键，例如 obj[varWithKey]。

其他操作：
>* 删除属性：delete obj.prop。
>* 检查是否存在给定键的属性："key" in obj。
>* 遍历对象：for(let key in obj) 循环。

****

###2.对象引用和复制
与原始类型相比，对象的根本区别之一是对象是“通过引用”被存储和复制的，与原始类型值相反：字符串，数字，布尔值等 —— 始终是以“整体值”的形式被复制的。

赋值对象的变量存储的不是对象的本身，而是该对象"在内存中的地址" 即对该对象的引用。

当一个对象被复制即引用被复制，该对象的自身并没有被复制（对象只有一个但有两个引用它的变量）

###通过引用来比较
* 当两个对象为同一对象时两者才相等
* 两个独立对象则并不相等，即使都为空
~~~JavaScript
let a = {};
let b = a; // 复制引用

alert( a == b ); // true，都引用同一对象
alert( a === b ); // true

let a = {};
let b = {}; // 两个独立的对象

alert( a == b ); // false
~~~

###拷贝克隆Object.assign
使用方法
~~~
Object.assign(dest, [src1, src2, src3...])
~~~
* 第一个参数 dest 是指目标对象。
* 更后面的参数 src1, ..., srcN（可按需传递多个参数）是源对象。
* 该方法将所有源对象的属性拷贝到目标对象 dest 中。换句话说，从第二个开始的所有参数的属性都被拷贝到第一个参数的对象中。
* 调用结果返回 dest。
* 如果被拷贝的属性的属性名已经存在那么它会自动覆盖
  
<font color=red size=2>使用 const 声明的对象也是可以被修改的  通过引用对对象进行存储的一个重要的副作用是声明为 const 的对象可以被修改。 作为一个整体进行赋值则会报错</font>

###3.垃圾回收

* 垃圾回收是自动完成的，我们不能强制执行或是阻止执行。
* 当对象是可达状态时，它一定是存在于内存中的。
* 被引用与可访问（从一个根）不同：一组相互连接的对象可能整体都不可达。
###4.this
==this==关键字的值就是在点之前的这个对象，即调用该方法的对象
==this==可以用于任何函数即使不是对象的方法 <font color=red>*this*</font>的值是在代码运行时计算出来 取决代码的上下文
* 存储在对象属性中的函数被称为“方法”。
* 方法允许对象进行像 object.doSomething() 这样的“操作”。
* 方法可以将对象引用为 this。
* 一个函数在声明时，可能就使用了 this，但是这个 this 只有在函数被调用时才会有值。
* 可以在对象之间复制函数
* 以“方法”的语法调用函数时：object.method()，调用过程中的 this 值是 object。
  
==错误使用方法==
~~~JavaScript
function makeUser() {
  return {
    name: "John",
    ref: this
  };
}

let user = makeUser();

alert( user.ref.name ); // Error: Cannot read property 'name' of undefined
~~~
<font color=red size=2>这是因为设置 this 的规则不考虑对象定义。只有调用那一刻才重要。

这里 makeUser() 中的 this 的值是 undefined，因为它是被作为函数调用的，而不是通过点符号被作为方法调用。

this 的值是对于整个函数的，代码段和对象字面量对它都没有影响。

所以 ref: this 实际上取的是当前函数的 this。</font>
==正确调用==
~~~JavaScript
function makeUser() {
  return {
    name: "John",
    ref() {
      return this;
    }
  };
}

let user = makeUser();

alert( user.ref().name ); // John
~~~

###5.构造器和操作符new
构造函数在技术上是常规函数有两个约定：
* 命名只能以大写字母开头
* 只能由==new==操作符来执行

调用方式
>let user = new User("Jack");

当一个函数被使用==new==操作符执行时
>1. 一个新的对象被创建并分配给==this==
>2. 函数体执行。通常它会修改*this*，为其添加新的属性
>3. 返回*this*的值
~~~JavaScript
 function User(name) {
  // this = {};（隐式创建）

  // 添加属性到 this
  this.name = name;
  this.isAdmin = false;

  // return this;（隐式返回）
}
~~~
构造器的主要目的——实现可重用的对象创建代码
####构造器中的return
<font color=red>return规则</font>
>* 如果==return==返回是一个对象，则返回这个对象，而不是==this== 
>+ 如果==return==返回的是一个原始类型，则忽略

例：
~~~JavaScript
//1.覆盖this
function BigUser() {

  this.name = "John";

  return { name: "Godzilla" };  // <-- 返回这个对象
}

alert( new BigUser().name );  // Godzilla，得到了那个对象
//return空
function SmallUser() {

  this.name = "John";

  return; // <-- 返回 this
}

alert( new SmallUser().name );  // John
~~~
###6.可选链 ?.
可选<font color =red size =2>?.</font>语法用三三种形式:
1. ==obj?.prop== —— 如果 ==obj==存在则返回==obj.prop==，否则返回undefined
2. ==obj?.[prop]== —— 如果 ==obj== 存在则返回 ==obj[prop]==，否则返回 undefined
3. ==obj.method?.()== —— 如果 ==obj.method ==存在则调用== obj.method()==，否则返回 undefined。

###Symbol类型
“Symbol”值表示唯一的标识符
可以使用 Symbol() 来创建这种类型的值：
~~~JavaScript
// id 是 symbol 的一个实例化对象
let id = Symbol();
// id 是描述为 "id" 的 Symbol
let id = Symbol("id"); 
//Symbol不会被自动转换为字符串
~~~
>对象的属性键只能是字符串类型或者Symbol类型

Symbol 总是不同的值，即使它们有相同的名字。如果我们希望同名的 Symbol 相等，那么我们应该使用全局注册表：Symbol.for(key) 返回（如果需要的话则创建）一个以 key 作为名字的全局 Symbol。使用 Symbol.for 多次调用 key 相同的 Symbol 时，返回的就是同一个 Symbol。

Symbol 有两个主要的使用场景：
>1. “隐藏” 对象属性。 如果我们想要向“属于”另一个脚本或者库的对象添加一个属性，我们可以创建一个 Symbol 并使用它作为属性的键。Symbol 属性不会出现在 for..in 中，因此它不会意外地被与其他属性一起处理。并且，它不会被直接访问，因为另一个脚本没有我们的 symbol。因此，该属性将受到保护，防止被意外使用或重写。
因此我们可以使用 Symbol 属性“秘密地”将一些东西隐藏到我们需要的对象中，但其他地方看不到它。
>2. JavaScript 使用了许多系统 Symbol，这些 Symbol 可以作为 Symbol.* 访问。我们可以使用它们来改变一些内建行为。例如，在本教程的后面部分，我们将使用 Symbol.iterator 来进行 迭代 操作，使用 Symbol.toPrimitive 来设置 对象原始值的转换 等等。

###原始值转换
为了进行转换，JavaScript尝试查找并调用三个对象方法:
1. 调用 obj[Symbol.toPrimitive](hint) —— 带有 symbol 键 Symbol.toPrimitive（系统 symbol）的方法，如果这个方法存在的话，
2. 否则，如果 hint 是 "string" —— 尝试 obj.toString() 和 obj.valueOf()，无论哪个存在。
3. 否则，如果 hint 是 "string" —— 尝试 obj.toString() 和 obj.valueOf()，无论哪个存在。

对象到原始值的转换是由许多期望以原始值作为值的内建函数和运算符自动调用的。
这里有三种类型（hint）:
* ==string==(对于alert和其他需要字符串的操作)
* ==number==（对于数学运算）
* ==default==（少数运算符）