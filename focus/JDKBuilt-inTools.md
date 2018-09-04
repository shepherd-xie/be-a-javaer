## JDK内置工具

* javah命令(C Header and Stub File Generator)
* jps命令(Java Virtual Machine Process Status Tool)
* jstack命令(Java Stack Trace)
* jstat命令(Java Virtual Machine Statistics Monitoring Tool)
* jmap命令(Java Memory Map)
* jinfo命令(Java Configuration Info)
* jconsole命令(Java Monitoring and Management Console)
* jvisualvm命令(Java Virtual Machine Monitoring, Troubleshooting, and Profiling Tool)
* jhat命令(Java Heap Analyse Tool)
* Jdb命令(The Java Debugger)
* Jstatd命令(Java Statistics Monitoring Daemon)

### javah命令

javah是用于根据JAVA本地方法，生成对应的c语言头文件及相应的stub文件的命令，使用比较简单，使用示例可以查看这篇文章：[JNI简单示例,包括C语言实现及调用](https://blog.csdn.net/fenglibing/article/details/4300381)


### jps命令

1、介绍

用来查看基于HotSpot的JVM里面中，所有具有访问权限的Java进程的具体状态, 包括进程ID，进程启动的路径及启动参数等等，与unix上的ps类似，只不过jps是用来显示java进程，可以把jps理解为ps的一个子集。

使用jps时，如果没有指定hostid，它只会显示本地环境中所有的Java进程；如果指定了hostid，它就会显示指定hostid上面的java进程，不过这需要远程服务上开启了jstatd服务，可以参看前面的jstatd章节来启动jstad服务。


2、命令格式

```
jps [ options ] [ hostid ]
```
 
3、常用参数说明

* -q 忽略输出的类名、Jar名以及传递给main方法的参数，只输出pid。
* -m 输出传递给main方法的参数，如果是内嵌的JVM则输出为null。
* -l 输出应用程序主类的完整包名，或者是应用程序JAR文件的完整路径。
* -v 输出传给JVM的参数。
* -V 输出通过标记的文件传递给JVM的参数（.hotspotrc文件，或者是通过参数-XX:Flags=<filename>指定的文件）。
* -J 用于传递jvm选项到由javac调用的java加载器中，例如，“-J-Xms48m”将把启动内存设置为48M，使用-J选项可以非常方便的向基于Java的开发的底层虚拟机应用程序传递参数。

4、服务器标识

hostid指定了目标的服务器，它的语法如下：
```
[protocol:][[//]hostname][:port][/servername]
```
* protocol - 如果protocol及hostname都没有指定，那表示的是与当前环境相关的本地协议，如果指定了hostname却没有指定protocol，那么protocol的默认就是rmi。
* hostname - 服务器的IP或者名称，没有指定则表示本机。
* port - 远程rmi的端口，如果没有指定则默认为1099。
* Servername - 注册到RMI注册中心中的jstatd的名称。

5、使用示例

5.1、列出本地的Java进程

不带任何参数
```shell
fenglibin@libin:~$ jps
11644 Main
1947 
12843 Jps
```
带-v参数
```shell
fenglibin@libin:~$ jps -v
11644 Main -agentlib:jdwp=transport=dt_socket,suspend=y,address=localhost:43467 -Dfile.encoding=GBK
1947  -Dosgi.requiredJavaVersion=1.5 -XX:MaxPermSize=256m -Xms40m -Xmx512m
12858 Jps -Denv.class.path=/home/fenglibin/java6/lib/dt.jar:/home/fenglibin/java6/lib/tools.jar::/usr/bin/libtool:/usr/bin/autoconf:/usr/local/BerkeleyDB.4.8/lib -Dapplication.home=/home/fenglibin/java6 -Xms8m
```
带-l参数
```shell
fenglibin@libin:~$ jps -l
11644 com.alibaba.china.webww.core.Main
12870 sun.tools.jps.Jps
1947
```

5.2、列出远程的Java进程

在jstatd章节，我们有通过：
```shell
rmiregistry 2020&jstatd -J-Djava.security.policy=all.policy -p 2020 -n AlternateJstatdServerName
```
启动了名为AlternateJstatdServerName的jstatd服务，那么我们此时就可以通过该服务列出其有权限访问的Java进程。
```shell
fenglibin@libin:~$ jps 10.1.1.234:2020/AlternateJstatdServerName
29556 Bootstrap
28671 WSPreLauncher
2602 RegistryImpl
18272 Test
2603 Jstatd
```


### jstack命令

1、介绍
jstack用于打印出给定的java进程ID或core file或远程调试服务的Java堆栈信息，如果是在64位机器上，需要指定选项"-J-d64"，Windows的jstack使用方式只支持以下的这种方式：
```
jstack [-l] pid
```
如果java程序崩溃生成core文件，jstack工具可以用来获得core文件的java stack和native stack的信息，从而可以轻松地知道java程序是如何崩溃和在程序何处发生问题。另外，jstack工具还可以附属到正在运行的java程序中，看到当时运行的java程序的java stack和native stack的信息, 如果现在运行的java程序呈现hung的状态，jstack是非常有用的。

2、命令格式
```
jstack [ option ] pid
jstack [ option ] executable core
jstack [ option ] [server-id@]remote-hostname-or-IP
```
3、常用参数说明
1)、options： 

* executable Java executable from which the core dump was produced.(可能是产生core dump的java可执行程序)
* core 将被打印信息的core dump文件
* remote-hostname-or-IP 远程debug服务的主机名或ip
* server-id 唯一id,假如一台主机上多个远程debug服务 

2）、基本参数：

* -F当’jstack [-l] pid’没有相应的时候强制打印栈信息
* -l长列表. 打印关于锁的附加信息,例如属于java.util.concurrent的ownable synchronizers列表.
* -m打印java和native c/c++框架的所有栈信息.
* -h | -help打印帮助信息
* pid 需要被打印配置信息的java进程id,可以用jps查询.

### jstat命令

1、介绍
Jstat用于监控基于HotSpot的JVM，对其堆的使用情况进行实时的命令行的统计，使用jstat我们可以对指定的JVM做如下监控：

* 类的加载及卸载情况
* 查看新生代、老生代及持久代的容量及使用情况
* 查看新生代、老生代及持久代的垃圾收集情况，包括垃圾回收的次数及垃圾回收所占用的时间
* 查看新生代中Eden区及Survior区中容量及分配情况等

jstat工具特别强大，它有众多的可选项，通过提供多种不同的监控维度，使我们可以从不同的维度来了解到当前JVM堆的使用情况。详细查看堆内各个部分的使用量，使用的时候必须加上待统计的Java进程号，可选的不同维度参数以及可选的统计频率参数。

它主要是用来显示GC及PermGen相关的信息，如果对GC不怎么了解，先看这篇文章：http://blog.csdn.net/fenglibing/archive/2011/04/13/6321453.aspx，否则其中即使你会使用jstat这个命令，你也看不懂它的输出。

2、语法
```
jstat [ generalOption | outputOptions vmid [interval[s|ms] [count]] ]
```
* generalOption - 单个的常用的命令行选项，如-help, -options, 或 -version。
* outputOptions -一个或多个输出选项，由单个的statOption选项组成，可以和-t, -h, and -J等选项配合使用。

statOption：
根据jstat统计的维度不同，可以使用如下表中的选项进行不同维度的统计，不同的操作系统支持的选项可能会不一样，可以通过-options选项，查看不同操作系统所支持选项，如：

| Option | Displays... |
| :---: | :---: |
| class | 用于查看类加载情况的统计 |
| compiler | 用于查看HotSpot中即时编译器编译情况的统计 |
| gc | 用于查看JVM中堆的垃圾收集情况的统计 |
| gccapacity | 用于查看新生代、老生代及持久代的存储容量情况 |
| gccause | 用于查看垃圾收集的统计情况（这个和-gcutil选项一样），如果有发生垃圾收集，它还会显示最后一次及当前正在发生垃圾收集的原因。 |
| gcnew | 用于查看新生代垃圾收集的情况 |
| gcnewcapacity	| 用于查看新生代的存储容量情况 |
| gcold | 用于查看老生代及持久代发生GC的情况 |
| gcoldcapacity | 用于查看老生代的容量 |
| gcpermcapacity | 用于查看持久代的容量 |
| gcutil | 用于查看新生代、老生代及持代垃圾收集的情况 |
| printcompilation | HotSpot编译方法的统计 |
