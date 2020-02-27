# Glossary

## 耦合度
耦合性(Coupling)，也叫耦合度，是对模块间关联程度的度量。耦合的强弱取决于模块间接口的复杂性、调用模块的方式以及通过界面传送数据的多少。模块间的耦合度是指模块之间的依赖关系，包括控制关系、调用关系、数据传递关系。模块间联系越多，其耦合性越强，同时表明其独立性越差。软件设计中通常用耦合度和内聚度作为衡量模块独立程度的标准。划分模块的一个准则就是高内聚低耦合。

## 方法签名
方法签名，顾名思义，方法的签名，来区分不同方法的标示符。
在Java编程语言，一个方法签名的方法名称和参数的数量和类型。返回类型和抛出异常并不认为是一个方法签名的一部分。

以下三种方法有相同的签名被认为是相同的,只是返回值不同。参数的名称不是方法签名的一部分,忽略编译器检验方法唯一性。

_The following three methods do have the same signatures and are considered the same, as only the return value differs. The name of the parameter is not part of the method signature and is ignored by the compiler for checking method uniqueness._
```
int doSomething(int y);
String doSomething(int x);
int doSomething(int z) throws java.lang.Exception;
```

## LOC

**源代码行数**（Source lines of code）简称**SLOC**，也称为**程序行数**（lines of code），简称**LOC**，是由计算程序[源代码](https://zh.wikipedia.org/wiki/源代码)的行数来估计[计算机程序](https://zh.wikipedia.org/wiki/计算机程序)大小的[软件度量](https://zh.wikipedia.org/wiki/軟體度量)。源代码行数一般会用来预计开发程序需要的人力及时间，若在软件完成后，也可以用来估计[程序开发生产力](https://zh.wikipedia.org/w/index.php?title=程式開發生產力&action=edit&redlink=1)或[可维护性](https://zh.wikipedia.org/wiki/可维护性)。

## EC2

**亚马逊弹性云计算**（英语：Amazon Elastic Compute Cloud，简称Amazon EC2） ，是由[亚马逊公司](https://zh.wikipedia.org/wiki/亞馬遜公司)提供的[Web服务](https://zh.wikipedia.org/wiki/Web服務)，是一个让用户可以租用[云端](https://zh.wikipedia.org/wiki/雲端運算)电脑运行所需应用的系统。EC2借由提供Web服务的方式让用户可以弹性地运行自己的Amazon机器映像档，用户将可以在这个[虚拟机](https://zh.wikipedia.org/wiki/虛擬機器)上运行任何自己想要的软件或应用程序。