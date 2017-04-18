## JDK&JRE

JDK（Java Development Kit，Java开发工具包）：JDK是Java语言的软件开发工具包，JDK是整个java开发的核心，它包含了JAVA的运行环境，JAVA工具和JAVA基础的类库。

JRE（Java Runtime Environment，Java运行环境）：运行JAVA程序所必须的环境的集合，包含JVM标准实现及Java核心类库。即，如果你的电脑上没有JDK但是有JRE也是可以运行Java程序，但是不能进行开发。

## JDK的安装及配置

要想进行Java的开发就安装JDK。安装时（包括以后的一些软件）尽量在关闭安全卫士的状态下进行。

[http://www.oracle.com/technetwork/java/javase/downloads/index.html](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

        进行Java开发最重要的是javac和java两个命令，但是由于这两个命令不是电脑自身提供的而是安装JDK 之后之后才有的，所以在使用的时候要进行环境变量的配置。

安装JDK后需要配置环境变量（1.8版本，默认安装路径）：

`JAVA_HOME=C:\Program Files\Java\jdk1.8.0_101;`

`CLASS_PATH=.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;`

`Path=;%JAVA_HOME%\bin;`

