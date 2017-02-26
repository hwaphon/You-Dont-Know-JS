# You-Dont-Know-JS

chapter-1: 

在非严格模式下，`LHS` 调用查找不到变量时会创建一个全局变量，`RHS` 查找不到变量时会抛出 `ReferenceError`。 在严格模式下，`LHS` 和 `RHS` 查找不到变量时都会抛出 `ReferenceError`。

对于 `var a = 10` 这条赋值语句，实际上是为了查找变量 `a`， 并且将 10 这个数值赋予它，这就是 `LHS` 查询。 对于 `console.log(a)` 这条语句，实际上是为了查找 `a` 的值并将其打印出来，这是 `RHS` 查询。

---
chapter-2:

`JavaScript` 引擎会在编译阶段进行数项的性能优化。其中有些优化依赖于能够根据代码的词法进行静态分析,并预先确定所有变量和函数的定义位置,才能在执行过程中快速找到标识符。

但如果引擎在代码中发现了 `eval(..)` 或 `with` ,它只能简单地假设关于标识符位置的判断都是无效的,因为无法在词法分析阶段明确知道 `eval(..)` 会接收到什么代码,这些代码会如何对作用域进行修改,也无法知道传递给 `with` 用来创建新词法作用域的对象的内容到底是什么。

如果代码中大量使用 eval(..) 或 with ,那么运行起来一定会变得非常慢。无论引擎多聪明,试图将这些悲观情况的副作用限制在最小范围内,也无法避免如果没有这些优化,代码会运行得更慢这个事实。

---

chapter-03:

匿名函数表达式书写起来简单快捷,很多库和工具也倾向鼓励使用这种风格的代码。但它也有几个缺点需要考虑。

1. 匿名函数在栈追踪中不会显示出有意义的函数名,使得调试很困难。

2. 如果没有函数名,当函数需要引用自身时只能使用已经过期的 `arguments.callee` 引用,比如在递归中。另一个函数需要引用自身的例子,是在事件触发后事件监听器需要解绑自身。

3. 匿名函数省略了对于代码可读性 / 可理解性很重要的函数名。一个描述性的名称可以让代码不言自明。

值得注意的是，在 `if` 和 `for` 块中创建的变量会被绑定在外部作用域。很可惜，`JavaScript` 表面上好像并没有创建块作用域的工具，除非你深入研究。在  `ES6` 之前， `with` 和 `try/catch` 可以创建块作用域，`ES6` 中引入了 `let` 关键字，let 关键字可以将变量绑定到所在的任意作用域中(通常是 { .. } 内部)。换句话说, `let` 为其声明的变量隐式地了所在的块作用域。

`ES6` 中引入了 `const` 关键字用于声明一个常量。比如 `const a = 10；` 。

---

chapter-04:

函数声明和变量声明都会被提升。但是一个值得注意的细节(这个细节可以出现在有多个“重复”声明的代码中)是函数会首先被提升,然后才是变量。

---

chapter-05:

当函数可以记住并访问所在的词法作用域,即使函数是在当前词法作用域之外执行,这时就产生了闭包。

模块有两个主要特征:
1. 为创建内部作用域而调用了一个包装函数;
2. 包装函数的返回值必须至少包括一个对内部函数的引用,这样就会创建涵盖整个包装函数内部作用域的闭包。

---

chapter-07: 

现在我们可以根据优先级来判断函数在某个调用位置应用的是哪条规则。可以按照下面的顺序来进行判断:

1. 函数是否在 new 中调用( new 绑定)?如果是的话 `this` 绑定的是新创建的对象。
	`var bar = new foo()`

2. 函数是否通过 `call` 、 `apply` (显式绑定)或者硬绑定调用?如果是的话, `this` 绑定的是指定的对象。
	`var bar = foo.call(obj2)`

3. 函数是否在某个上下文对象中调用(隐式绑定)?如果是的话, this 绑定的是那个上下文对象。
	`var bar = obj1.foo()`

4. 如果都不是的话,使用默认绑定。如果在严格模式下,就绑定到 undefined ,否则绑定到全局对象。
	`var bar = foo()`

如果你把 `null` 或者 `undefined` 作为 `this` 的绑定对象传入 `call` 、 `apply` 或者 `bind` ,这些值在调用时会被忽略,实际应用的是默认绑定规则。

一种“更安全”的做法是传入一个特殊的对象,把 `this` 绑定到这个对象不会对你的程序产生任何副作用。就像网络(以军队)一样,我们可以创建一个“DMZ”(demilitarizedzone,非军事区)对象——它就是一个空的非委托的对象 。`JavaScript` 中创建一个空对象最简单的方法都是 `Object.create(null)`。

`ES6` 中的箭头函数并不会使用四条标准的绑定规则,而是根据当前的词法作用域来决定 `this` ,具体来说,箭头函数会继承外层函数调用的 `this` 绑定(无论 `this` 绑定到什么)。这其实和 `ES6` 之前代码中的 `self = this` 机制一样。

---

chapter-08: 

你完全可以把数组当作一个普通的键 / 值对象来使用,并且不添加任何数值索引,但是这并不是一个好主意。数组和普通的对象都根据其对应的行为和用途进行了优化,所以最好只用对象来存储键 / 值对,只用数组来存储数值下标 / 值对。

即便属性是 `configurable:false` , 我们还是可以把 `writable` 的状态由 `true` 改为 `false` ,但是无法由 `false` 改为 `true` 。


