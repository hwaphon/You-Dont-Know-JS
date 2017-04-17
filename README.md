# You-Dont-Know-JS(上)

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

如 果 你 想 禁 止 一 个 对 象 添 加 新 属 性 并 且 保 留 已 有 属 性, 可 以 使 用 `Object.preventExtensions(..)` 。

`Object.seal(..)` 会创建一个“密封”的对象,这个方法实际上会在一个现有对象上调用 `Object.preventExtensions(..)` 并把所有现有属性标记为 `configurable:false` 。所以,密封之后不仅不能添加新属性,也不能重新配置或者删除任何现有属性(虽然可以修改属性的值)。

`Object.freeze(..)` 会 创 建 一 个 冻 结 对 象, 这 个 方 法 实 际 上 会 在 一 个 现 有 对 象 上 调 用 `Object.seal(..)` 并把所有“数据访问”属性标记为 `writable:false` ,这样就无法修改它们的值。

# You Don't Kown JS(中)

chapter 2:

二进制浮点数最大的问题（不仅 `JavaScript`， 所有遵循 `IEEE754` 规范的语言都是如此）就是会出现下面这个问题。

    0.1 + 0.2  === 0.3    // false 

简单的说，二进制浮点数中的 0.1 和 0.2 并不是非常的准确，他们相加的结果也不是正好等于 0.3 ，而是一个比较接近的数字 0.30000000000000004。那么怎么才能解决上述问题呢？最常见的方法是设置一个误差范围值，通常成为机器精度，对 `JavaScript` 来说，这个值通常是 `2^-52`，不过在 `ES6` 中，这个值定义在 `Number.EPSILON` 中，而且这个值为 `2.220446049250313e-16`。所以我们可以写一个判断在误差范围内两数是否相等的函数。

    function numbersCloseEnoughToEqual(n1, n2) {
    	if(!Number.EPSILON) {
    		Number.EPSILON = Math.pow(2, -52);
    	}
    	return Math.abs(n1 - n2) < Number.EPSILON;
    }
    
 `JS` 能够表示的数字范围在 [5e-324 , 1.7976931348623157e+308] 之间，这两个值保存在 `Number.MIN_VALUE`和 `Number.MAX_VALUE`中，不过值得注意的是，`JS` 能够确保安全呈现的最大整数为 `2^53 -1`，最小整数为 `-2^53-1`，这两个值从 `ES6` 开始分别保存在 `Number.MAX_SAFE_INTEGER` 和 `Number.MIN_SAFE_INTEGER`, 不过这里所谓的安全到底是指什么呢？意思就是说，在安全范围中的整数在运算的时候可以保证其精确度，而超过了安全范围，两个值仍然可以运算，但是不再保证其精确度。
 
     console.log(9007199254740991 + 10);     // 结果为 9007199254741000
     
 
 `ES6` 中定义了两个方便使用的函数，一个是 `Number.isInteger()` 用于检测一个值是否为一个整数，还有一个是 `Number.isSafeInteger()`用于判断一个整数是否为安全整数。由于在 `ES6` 之前并不支持这两个函数，所以我们可以写两个等价的函数。
 
     if(!Number.isInteger) {
    	Number.isInteger = function(num) {
    		return typeof num === "number" && num % 1 === 0;
    	};
    }

    if(!Number.isSafeInteger) {
    	Number.isSafeInteger = function(num) {
    		if(!Number.MAX_SAFE_INTEGER) {
    			Number.MAX_SAFE_INTEGER = Math.pow(2, 53) - 1;
    		}
    		return Number.isInteger(num) && Math.abs(num) <= Number.MAX_SAFE_INTEGER;
    	}
    }
    
`void` 运算符通常用来返回 `undefined`，比如我们不想让表达式返回任何结果，可以像下面这样应用。

    function doSomeThing() {
    	if(!ready) {
    		return void setTimeOut(doSomeThing, 100);
    	}	

    	var result = 10;
    	return result;
    }
    
这么做的优点在哪里呢？那么我们就得考虑一下如果不使用 `void` 而完成同样的功能应该怎么做！我们会将上述一条语句写成如下格式，因为 `setTimeOut`会返回一个数值，所以利用 `void` 的主要功能就是简化语句。

    	if(!ready) {
    		setTimeOut(doSomeThing, 100);
    		return;
    	}	
    	
在 `ES6` 之前，我们可能会使用 `window.isNaN()` 去检测一个变量是否为 `NaN`, 不过这个方法的检查方式死板。

    console.log(isNaN("a"));    // true
    
所以在 `ES6` 中，我们可以使用 `Number.isNaN()` 来检测一个值是否为 `NaN`，就不会出现上述问题。如果不支持 `ES6`, 也可以利用 `NaN` 自己不等于自己的特性，写出替代方法。

    if (!Number.isNaN) {
    	Number.isNaN = function(num) {
    		return num !== num;
    	}
    }

对于零值，在 `JS` 中有个奇怪的现象，那就是如果你将 `-0` 转换为字符串，那么它就会自动的将负号省略，但是反过来却不成立，也就是说如果你将一个带负号的字符串形式的 `-0` 转换成整数，它就不会省略负号。而且表达式 `0 === -0` 也是成立的，所以对于需要区分 `0` 与 `-0` 的场合，我们可以写一个辅助函数，如下所示。

    function isNegZero(num) {
    	num = Number( num );
    	return ( num === 0 ) && ( 1 / num === -Infinity );
    }
    
为了能够方便的比较两个数，比如这两个数都是 `NaN`, 由于其本身的特性，所以 `NaN === NaN` 是不成立的，所以在 `ES6` 中新增了一个 `Object.is()` 用于判断两个值是否绝对相等。

    console.log(Object.is(NaN, NaN));    // true
    console.log(Object.is(0, -0));    // false
    
对于 `ES6` 之前，我们可以写一个替代函数。

    if (!Object.is) {
    	Object.is = function(v1, v2) {
    		// 判断是否是 -0
    		if (v1 === 0 && v2 === 0) {
    			return 1 / v1 === 1 / v2;
    		}

    		// 判断是否是 NaN
    		if (v1 !== v1) {
    			return v2 !== v2;
    		}

    		// 其他情况
    		return v1 === v2;
    	}
    }
    
----

chapter 3:

从功能和性能而言，都没有理由直接使用 `String(), Array(), Object(), Function(), RegExp()`原生构造函数去创建对象。不过 `Date()`和 `Error()` 不同，因为它们没有对应的常量形式去替代。

为了获取当前的 `Unix` 时间戳（从 1970 年 1 月 1 日计算，以秒为单位），可以使用 `new Date().getTime()`, 不过在 `ES5` 中引入了一个更加简单的方法 `Date.now()`,对于不支持 `ES5` 的浏览器来说，我们可以写出如下替代函数。

    if (!Date.now) {
    	Date.now = function() {
    		return (new Date()).getTime();
    	}
    }
    

`sybmol` 并非对象，而是一种简单标量类型。

---

chapter 4:

1. `ToString`
 基本类型值的字符串化规则为： `null` 转换为 `"null"`，`undefined` 转换为 `"undefined"`，`true` 转换为 `"true"`，数字的字符串化则遵循通用规则。 对于普通对象来说，除非自行定义，否则 `toString` 返回内部属性 `[[Class]]` 的值，如 `[object Object]`。
 
2. `ToJSON`
所有安全的 `JSON` 值都可以使用 `JSON.stringify()` 字符串化。安全的 `JSON` 值是指能够呈现为有效 `JSON` 格式的值。所谓不安全的 `JSON` 值，就是 `undefined, function, symbol` 和包含循环引用的对象。`JSON.stringify()` 遇到这些值就会自动忽略，如果这些值在数组中则会返回 `null`以保证单元位置不变。

3. `ToNumber`
转换规则如下： `true` 转换为 1，`false`转换为 0， `undefined` 转换为 `NaN`，`null` 转换为 0。对于对象，首先检测是否有 `valueOf()`方法，如果有并且返回基本类型的值，就使用该值做强制类型转换。如果没有就使用 `toString()`的返回值来进行强制类型转换。如果 `valueOf()` 和 `toString()`都不返回基本类型额值，会产生 `TypeError`错误。如果已经存在了 `valueOf`，那么在转换过程中将不再考虑 `toString`。

4. `ToBoolean`
以下为假值列表： `undefined, null, false, +0, -0, NaN, ""`，还有一种称之为假值对象的概念，比如 `new Boolean(false)` 这个对象，其实它的值为 `true`。

字符串和数字的转换，可以简单的通过 `Number()` 和 `String()` 来转换，也可以采用 `+`运算符将字符串转换为数字，比如 `+"48"` 返回的就是 `48`。

`JS` 中有一处奇特的语法，那就是构造函数没有参数时，可以不用带 `()`，不过这样做可读性不好，不建议使用。

`～x` 操作大致等同于 `-(x + 1)`, 这有什么用呢？这样的话，我们就可以将 `-1` 这个值转换成 `-0`，而 `-0` 转换成布尔值为 `false`，这又有什么用呢？用处就在于 `-1` 一般作为一个 "哨位值"， 比如在 `indexOf()` 方法中，如果没有匹配项则会返回 `-1`，那么我们直接判断 `string.indexOf(x) !== -1`不就行了吗？这样写法是可以，不过不是很好，这样的写法会出现 ”抽象渗漏“的问题，意思就是暴露了底层的实现细节。所以我们可以改写为如下形式：

    var a = "Hello World";

    if(!~a.indexOf("Hello")) {
    	// 没有找到匹配项
    }
    
计算过程很简单，当找到 `Hello` 这个字符串时，返回的数不是 `-1`，`～a.indexOf("Hello)`转换成不为 `0` 的值，在利用 `!` 转换成布尔值，就为 `false`。（`!` 会将一个值强制转换成布尔值，并且取反）。

`～～` 可以完成字位截除的功能，其功能类似与  `Math.floor()`  却又不尽相同。`～～`中第一个 `~`执行 `ToInt32`并反转字符，然后第二个 `~`再进行一次字位反转，即将所有字位反转为原值，最后得到的仍然是 `ToInt32` 的结果。不过值得注意的是，对于负数的处理， `~~` 和 `Math.floor()` 并不相同，比如 `Math.floor(-49.6)` 的结果为 `-50`， 而 `～～49.6` 返回的结果为 `-49`。其实 `～～`字位截除的功能就依赖于它能够将值转换成 32 位的整数，而能够完成同样功能的是 `x | 0`。

`Number()` 和 `parseInt()` 的区别就在于后者支持参数中包含非数字字符，而前者如果参数中含有非数字字符，就会返回 `NaN`。

`parseInt()` 在传入非字符数值时会出现一些奇怪的效果，比如说 `parseInt(1 / 0, 19)` 会返回 `18`，因为 `1/0` 被认为是 `Infinity`, 在 `19` 进制中，`i` 代表值为 `18`, 遇到 `n` 停止解析，所以会返回数值 `18`。所以在使用 `parseInt()` 时需要特别注意传进来的参数是否有歧义！

一般的想要将一个非布尔值转换成布尔值，我们可以采用的方法有两种，一种是直接利用 `Boolean()` 进行转换，而第二种是借助于 `!`符号，就像 `+` 能将一个非数字值转换成数字值一样，`!` 能将一个非布尔值转换成布尔值，同时将真值反转为假值（或者将假值反转为真值）。所以将非布尔值转换成布尔值最常用的方法就是 `!!`，因为第二个 `!` 会将结果反转回原值。

`||` 和 `&&` 首先会对第一个操作数执行条件判断，如果其不是布尔值，就先执行 `toBoolean` 强制类型转换，然后再执行条件判断。

对于 `||` 来说，如果第一个参数条件判断的结果为 `true` 就返回第一个操作数的值，如果为 `false` 就返回第二个操作数的值。举例如下：

    console.log(0 || null);    // null
    console.log(undefined || "Hello");    // Hello
    
这有什么作用呢？这可以帮助我们提供默认值，比如下面这个函数。

    function sayHello(name) {
    	var name = name || "World";
    	console.log("Hello " + name);
    }

    sayHello("Hwaphon");    // Hello Hwaphon
    sayHello();    // Hello World
    
 对于 `&&` 来说，如果整体结果为 `true`，则会返回第二个数，如果整体为 `false`，则会返回第一个数，如果一边为真一边为假，则返回为真的那个数。

---

chapter05: 

- 语句和表达式在英语中能找到对比 - 语句就像英语中的句子，而表达式就像短语。

- 语句都有一个返回值，而且规范定义 `var` 的结果值是 `undefined`，比如在控制台输入 `var a = 42;` 会返回 `undefined`。代码块返回的结果值是最后一个语句的结果值。 `{ var a = 10, b; b = a;}` 会返回 `10`。

- 值得注意的是，虽然每条语句都会默认的返回一个结果值，但是语法不允许我们获得语句的结果值赋值给另一个变量，不过我们可以通过 `eval`来完成这一功能（极其不建议使用）。 `ES7` 中有一项 `do` 表达式的提议，其功能就是执行一个代码块，并且返回最后一个语句的结果值。

- `++x++`这种同时利用前置和后置自增的表达式会抛出 `ReferenceError`. 要想完成同样的功能，我们可以使用 `var b = (a++, a)`来完成。

-  对于下述代码，`continue foo `并不是指跳到标签 `foo` 所在位置继续执行，而是指执行 `foo`循环的下一轮循环。

   		 foo: for(var i = 0; i < 5; i++) {
    			for(var j = 0; j < 5; j++) {
    				if (i === j) {
    					continue foo;
    				}
    				console.log(i, j);
    			}
    		}

 - 对于下述代码，`break foo` 不是指跳转到 `foo` 标签所在的位置继续执行，而是指跳出 `foo` 所在的循环或者代码块。
 
   		 foo: for(var i = 0; i < 5; i++) {
    			for(var j = 0; j < 5; j++) {
    				if (i === j) {
    					break foo;
    				}
    				console.log(i, j);
    			}
    		}
    
- 从 `ES6` 开始， `{}` 也可以用于解构赋值，其应用如下：

   		 function doSomething() {
   		 	return {
   		 		a: 2,
   		 		b: 4
   		 	};
   		 }
		
   		 var { a, b } = doSomething();
   		 console.log( a, b );
   		 
    或者像下面这样当做参数使用：
    
   		 function foo({ a, b, c }) {
   		 	console.log( a, b, c );
   		 }

   		 foo({ a: 2, b: 4, c: 6 });
   		 
    重点在哪里？在于我们可以直接提取对象中的数，而不是利用类似 `obj.a` 或者 `obj.b` 这样的语法。
    
- 很多人误以为 `JS` 中存在 `else if` 语法，不过事实上 `JS` 中不存在这样的语法，比如像下面这样的代码会给你存在 `else if` 的错觉。

    	if (a) {
    		console.log(a)
    	} else if(b) {
    		console.log(b);
    	} else {
    		console.log();
    	}
    	
    其实这种写法，`JS` 会认为你在 `else` 后面省略了 `{`括号，所以上面的语法实际上只是下面这种形式而已。
    
     	if (a) {
    		console.log(a)
    	} else  {
    		if(b) {
    			console.log(b);
    		} else {
    			console.log();
    		}
    	}
    	
- `&&` 的优先级高于 `||`。合理的利用 `&&` 和 `||` 的短路特性，不仅可以简化我们的代码，还可以减少代码出错的几率。比如以下代码，将 `opts` 作为守护条件。

    	function doSomething(opts) {
    		if (opts && opts.cache) {
    			
    		}
    	}
    	
    或者利用 `||` 判断一个值是否初始化，如果没有初始化，就调用初始化函数。
    
        function doSomething(opts) {
    		if (opts || opts.init()) {
    			
    		}
    	}
    	
 - `ES6` 规范定义了一个新的概念，叫做 `TDZ`(暂时性死区), 是指由于在代码中的变量还没有初始化而不能被引用的情况。比如以下情况。
 
     	{
    		a = 2;
    		let a;
    	}
    	
    这里的 `a` 就是 `TDZ`, 所以在 `let` 声明之前使用会出现错误。对未声明变量使用 `typeof` 不会出现错误，而在 `TDZ` 中却会出现错误。
    
- `ES6` 中支持为参数设置默认值，比如下面这个例子。

    	function foo(a = 2, b = 4) {
    		console.log(a, b);
    	}

    	foo();
    	
- `finally` 中的代码总会在 `try` 之后执行或者在 `catch` 之后执行，如果 `try {}` 块中存在 `return` 语句，正确的解答是，`try ` 中的 `return` 值会保存在函数的返回值中，然后再去执行 `finally` 语句。而且如果 `finally` 语句中设置了 `return` 语句，会覆盖之前的值，而且如果 `finally` 中抛出了异常，那么之前设置的返回值都会失效。


- `switch` 可以看作是 `if ... else if ... else ` 的替代，不过值得注意的是在 `case` 判断式中必须严格的接收 `true` 或者 `false`，他不会自动转换类型，比如 `case 1`也永远不会执行到，因为 `1` 并不会自动转换成 `true`。

---