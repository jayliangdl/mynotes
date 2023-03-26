# typescript笔记

### main?.appendChild(selection);请问这一句的?是什么意思或作用？

这里的?是可选链操作符，它的作用是在main对象不为null或undefined时才会执行appendChild方法。如果main为null或undefined，那么这个方法就不会执行，也不会抛出异常。这个操作符可以避免在对象为null或undefined时出现TypeError异常。

### const btn = document.getElementById('btn')!;中的!是什么意思和作用？