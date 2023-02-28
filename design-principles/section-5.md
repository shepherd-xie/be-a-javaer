# 得墨忒耳定律/迪米特法则（Law of Demeter，LoD）

**得墨忒耳定律**（**Law of Demeter**，缩写**LoD**）亦被称作“最少知识原则（Principle of Least Knowledge）”，是一种[软件](https://zh.wikipedia.org/wiki/软件)开发的设计指导原则，特别是[面向对象的程序设计](https://zh.wikipedia.org/wiki/面向对象的程序设计)。得墨忒耳定律是[松耦合](https://zh.wikipedia.org/wiki/松耦合)的一种具体案例。该原则是[美国东北大学](https://zh.wikipedia.org/wiki/东北大学_(美国))在1987年末在发明的，可以简单地以下面任一种方式总结:

1. 每个单元对于其他的单元只能拥有有限的知识：只是与当前单元紧密联系的单元；
2. 每个单元只能和它的朋友交谈：不能和陌生单元交谈；
3. 只和自己直接的朋友交谈。

这个原理的名称来源于[希腊神话](https://zh.wikipedia.org/wiki/希腊神话)中的[农业](https://zh.wikipedia.org/wiki/农业)女神，孤独的[得墨忒耳](https://zh.wikipedia.org/wiki/得墨忒耳)。

很多面向对象程序设计语言用"."表示对象的域的解析算符，因此得墨忒耳定律可以简单地陈述为“只使用一个.算符”。因此，a.b.Method()违反了此定律，而a.Method()不违反此定律。一个简单例子是，人可以命令一条狗行走（walk），但是不应该直接指挥狗的腿行走，应该由狗去指挥控制它的腿如何行走。