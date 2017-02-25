# You-Dont-Know-JS

chapter-1: 

在非严格模式下，`LHS` 调用查找不到变量时会创建一个全局变量，`RHS` 查找不到变量时会抛出 `ReferenceError`。 在严格模式下，`LHS` 和 `RHS` 查找不到变量时都会抛出 `ReferenceError`。

对于 `var a = 10` 这条赋值语句，实际上是为了查找变量 `a`， 并且将 10 这个数值赋予它，这就是 `LHS` 查询。 对于 `console.log(a)` 这条语句，实际上是为了查找 `a` 的值并将其打印出来，这是 `RHS` 查询。
