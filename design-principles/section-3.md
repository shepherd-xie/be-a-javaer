# **单一功能原则**（Single responsibility principle）

在[面向对象编程](https://zh.wikipedia.org/wiki/面向对象编程)领域中，**单一功能原则**（Single responsibility principle）规定每个类都应该有一个单一的功能，并且该功能应该由这个类完全封装起来。所有它的（这个类的）服务都应该严密的和该功能平行（功能平行，意味着没有依赖）。

这个术语由[罗伯特·C·马丁](https://zh.wikipedia.org/w/index.php?title=罗伯特·C·马丁&action=edit&redlink=1)（Robert Cecil Martin）在他的《敏捷软件开发，原则，模式和实践》一书中的一篇名为〈面向对象设计原则〉的文章中给出。 [[1\]](https://zh.wikipedia.org/zh-hans/单一功能原则#cite_note-1) 马丁表述该原则是基于的《结构化分析和系统规格》[[2\]](https://zh.wikipedia.org/zh-hans/单一功能原则#cite_note-2)一书中的[内聚原则](https://zh.wikipedia.org/wiki/内聚原则)（Cohesion）上。

马丁把功能（职责）定义为：“改变的原因”，并且总结出一个类或者模块应该有且只有一个改变的原因。如果你能够想到多于一个的动机去改变一个类，那么这个类就具有多于一个的职责。一个具体的例子就是，想象有一个用于编辑和打印报表的模块。这样的一个模块存在两个改变的原因。第一，报表的内容可以改变（编辑）。第二，报表的格式可以改变（打印）。这两方面的改变会因为完全不同的起因而发生：一个是本质的修改，一个是表面的修改。单一功能原则认为这两方面的问题事实上是两个分离的功能，因此他们应该分离在不同的类或者模块里。把有不同的改变原因的事物耦合在一起的设计是糟糕的。

保持一个类专注于单一功能点上的一个重要的原因是，它会使得类更加的健壮。继续上面的例子，如果有一个对于报表编辑流程的修改，那么将存在极大的危险性，因为假设这两个功能存在于同一个类中，修改报表的编辑流程会导致公共状态或者依赖关系的改变，打印功能的代码会因此不工作。