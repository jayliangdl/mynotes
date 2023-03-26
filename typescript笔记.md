# typescript笔记

### main?.appendChild(selection);请问这一句的?是什么意思或作用？

这里的?是可选链操作符，它的作用是在main对象不为null或undefined时才会执行appendChild方法。如果main为null或undefined，那么这个方法就不会执行，也不会抛出异常。这个操作符可以避免在对象为null或undefined时出现TypeError异常。

### const btn = document.getElementById('btn')!;中的!是什么意思和作用？

在这段代码中，`!`是非空断言操作符，它的作用是告诉 TypeScript 编译器，`document.getElementById('btn')` 方法返回的对象一定不是 `null` 或 `undefined`，因此可以放心地将其转换为 `HTMLElement` 类型。如果不使用非空断言操作符，TypeScript 编译器会认为 `document.getElementById('btn')` 方法返回的对象可能为 `null` 或 `undefined`，从而在编译时报错。因此，使用非空断言操作符可以避免这种错误。