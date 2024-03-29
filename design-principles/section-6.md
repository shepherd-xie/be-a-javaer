# **里氏替换原则**（Liskov Substitution principle）

在[面向对象的程序设计](https://zh.wikipedia.org/wiki/面向对象的程序设计)中，**里氏替换原则**（Liskov Substitution principle）是对[子类型](https://zh.wikipedia.org/wiki/子类型)的特别定义。它由[芭芭拉·利斯科夫](https://zh.wikipedia.org/wiki/芭芭拉·利斯科夫)（Barbara Liskov）在1987年在一次会议上名为“数据的抽象与层次”的演说中首先提出。[[1\]](https://zh.wikipedia.org/zh-cn/里氏替换原则#cite_note-FamilyValues-1)

里氏替换原则的内容可以描述为： “派生类（子类）对象可以在程序中代替其基类（超类）对象。” 以上内容并非利斯科夫的原文，而是译自[罗伯特·马丁](https://zh.wikipedia.org/wiki/罗伯特·马丁)（Robert Martin）对原文的解读。其原文为：

Let q(x) be a property provable about objects x of type T. Then q(y)\ should\ be true for object y of type S where S is a subtype of T.

[芭芭拉·利斯科夫](https://zh.wikipedia.org/wiki/芭芭拉·利斯科夫)与[周以真](https://zh.wikipedia.org/wiki/周以真)（Jeannette Wing）在1994年发表论文并提出以上的**Liskov代换原则**。