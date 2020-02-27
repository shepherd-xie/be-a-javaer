Druid 介绍及配置
1. Druid是什么？
Druid是Java语言中最好的数据库连接池。Druid能够提供强大的监控和扩展功能。

2. 在哪里下载druid
正式版本下载：
maven中央仓库: http://central.maven.org/maven2/com/alibaba/druid/ 
3. 怎么获取Druid的源码
Druid是一个开源项目，源码托管在github上，源代码仓库地址是 https://github.com/alibaba/druid。同时每次Druid发布正式版本和快照的时候，都会把源码打包，你可以从上面的下载地址中找到相关版本的源码

4. 怎么配置maven
Druid 0.1.18 之后版本都发布到maven中央仓库中，所以你只需要在项目的pom.xml中加上dependency就可以了。例如：

    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
        <version>${druid-version}</version>
    </dependency>
也可以选择 Maven仓库查找公共的仓库地址:http://www.mvnrepository.com/artifact/com.alibaba/druid

5. 怎么打开Druid的监控统计功能
Druid的监控统计功能是通过filter-chain扩展实现，如果你要打开监控统计功能，配置StatFilter，具体看这里：https://github.com/alibaba/druid/wiki/%E9%85%8D%E7%BD%AE_StatFilter

6. 怎样使用Druid的内置监控页面
内置监控页面是一个Servlet，具体配置看这里：https://github.com/alibaba/druid/wiki/%E9%85%8D%E7%BD%AE_StatViewServlet%E9%85%8D%E7%BD%AE

7. 内置监控中的Web和Spring关联监控怎么配置？
Web关联监控配置 
https://github.com/alibaba/druid/wiki/%E9%85%8D%E7%BD%AE_%E9%85%8D%E7%BD%AEWebStatFilter
Spring关联监控配置 
https://github.com/alibaba/druid/wiki/%E9%85%8D%E7%BD%AE_Druid%E5%92%8CSpring%E5%85%B3%E8%81%94%E7%9B%91%E6%8E%A7%E9%85%8D%E7%BD%AE

8. 怎么配置防御SQL注入攻击
Druid提供了WallFilter，它是基于SQL语义分析来实现防御SQL注入攻击的。具体配置看这里：https://github.com/alibaba/druid/wiki/%E9%85%8D%E7%BD%AE-wallfilter

9. Druid有没有参考配置
不同的业务场景需求不同，你可以使用我们的参考配置，但建议你仔细阅读相关文档，了解清楚之后做定制配置。https://github.com/alibaba/druid/wiki/%E9%85%8D%E7%BD%AE_DruidDataSource%E5%8F%82%E8%80%83%E9%85%8D%E7%BD%AE

10. 我想日志记录JDBC执行的SQL，如何配置
Druid提供了Log4jFilter、CommonsLogFilter和Slf4jFilter，具体配置看这里https://github.com/alibaba/druid/wiki/%E9%85%8D%E7%BD%AE_LogFilter

11. 我的程序可能产生连接泄漏了，有什么办法？
Druid提供了多种监测连接泄漏的手段，具体看这里：https://github.com/alibaba/druid/wiki/%E8%BF%9E%E6%8E%A5%E6%B3%84%E6%BC%8F%E7%9B%91%E6%B5%8B

12. 在Druid中使用PSCache会有内存占用过大问题么？
连接Oracle数据库，打开PSCache，在其他的数据库连接池都会存在内存占用过多的问题，Druid是唯一解决这个问题的连接池。具体看这里：https://github.com/alibaba/druid/wiki/Oracle%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%8BPreparedStatementCache%E5%86%85%E5%AD%98%E9%97%AE%E9%A2%98%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88

13. 有没有和其他数据库连接池的对比？
各种数据库连接池对比https://github.com/alibaba/druid/wiki/%E5%90%84%E7%A7%8D%E6%95%B0%E6%8D%AE%E5%BA%93%E8%BF%9E%E6%8E%A5%E6%B1%A0%E5%AF%B9%E6%AF%94

14. 从其他连接池迁移要注意什么？
不同连接池的参数参照对比： http://code.alibabatech.com/wiki/pages/viewpage.action?pageId=6947005
DBCP迁移 https://github.com/alibaba/druid/wiki/DBCP%E8%BF%81%E7%A7%BB

15. Druid中有没有类似Jboss DataSource中的ExceptionSorter
ExceptionSorter是JBoss DataSource中的优秀特性，Druid也有一样功能的ExceptionSorter，但不用手动配置，自动识别生效的。具体看这里：https://github.com/alibaba/druid/wiki/ExceptionSorter_cn

16. Druid中的maxIdle为什么是没用的？
maxIdle是Druid为了方便DBCP用户迁移而增加的，maxIdle是一个混乱的概念。连接池只应该有maxPoolSize和minPoolSize，druid只保留了maxActive和minIdle，分别相当于maxPoolSize和minPoolSize。

17. 我的应用配置的是JNDI数据源，可以用DruidDataSource么？
DruidDataSource支持JNDI配置，具体看这里：https://github.com/alibaba/druid/wiki/%E9%85%8D%E7%BD%AE_JNDI_Tomcat 
具体实现的类是这个：com.alibaba.druid.pool.DruidDataSourceFactory，你可以阅读代码加深理解。

18. 我的应用已使用DBCP，是代码中写死的，怎样更换为Druid？
可以的，Druid提供了一个中完全平滑迁移DBCP的办法。

1) 从http://repo1.maven.org/maven2/com/alibaba/druid/druid-wrapper/ 下载druid-wrapper-xxx.jar 
2) 加入druid-xxx.jar 
3) 从你的WEB-INF/lib/中删除dbcp-xxx.jar 
4) 按需要加上配置，比如JVM启动参数加上-Ddruid.filters=stat，动态配置druid的filters 
这种用法，使得可以在一些非自己开发的应用中使用Druid，例如在sonar中部署druid，sonar是一个使用jruby开发的web应用，写死了DBCP，只能够通过这种方法来更换。

19. 我想试用快照版本，怎么获取？
直接获取快照版本的地址是：http://code.alibabatech.com/mvn/snapshots/com/alibaba/druid/ ，使用快照版本建议加入我们QQ群 92748305，遇到问题直接反馈给我们。

20. 有一些SQL执行很慢，我希望日志记录下来，怎么设置？
在StatFilter配置中有慢SQL执行日志记录，看这里https://github.com/alibaba/druid/wiki/%E9%85%8D%E7%BD%AE_StatFilter

21. 我希望加密我的数据库密码怎么办？
运维和DBA都不希望把密码明文直接写在配置文件中，Druid提供了数据库秘密加密的功能。具体看这里：https://github.com/alibaba/druid/wiki/%E4%BD%BF%E7%94%A8ConfigFilter

22. 如何参与Druid的开发
Druid是一个通过github开源的项目，github的特性，使得你很容易参与其中。这里有详细说明https://github.com/alibaba/druid/wiki/%E5%A6%82%E4%BD%95%E5%8F%82%E4%B8%8E

23. Druid的发布周期是怎样？
Druid是一个活跃的项目，长期维护。每个月有一个发布窗口，除非遇到重大bug和非常紧急的需求，否则都是每个月最多发布一次。如果没有足够多的需求，发布窗口就不会被使用。

24. 如果DruidDataSource在init的时候失败了，不再使用，是否需要close
是的，如果DruidDataSource不再使用，必须调用close来释放资源，释放的资源包括关闭Create和Destory线程。

25. DruidDataSource支持哪些数据库？
理论上说，支持所有有jdbc驱动的数据库。实际测试过的有

数据库	支持状态
mysql	支持，大规模使用
oracle	支持，大规模使用
sqlserver	支持
postgres	支持
db2	支持
h2	支持
derby	支持
sqlite	支持
sybase	支持
26. Oracle下jdbc executeBatch时，更新行数计算不正确
使用jdbc的executeBatch 方法，如果数据库为oracle，则无论是否成功更新到数据，返回值都是-2，而不是真正被sql更新到的记录数，这是Oracle JDBC Driver的问题，Druid不作特殊处理。

27. Druid如何自动根据URL自动识别DriverClass的
Druid是根据url前缀来识别DriverClass的，这样使得配置更方便简洁。

前缀	DriverCLass	描述信息
jdbc:odps	com.aliyun.odps.jdbc.OdpsDriver	 
jdbc:derby	org.apache.derby.jdbc.EmbeddedDriver	 
jdbc:mysql	com.mysql.jdbc.Driver	 
jdbc:oracle	oracle.jdbc.driver.OracleDriver	 
jdbc:microsoft	com.microsoft.jdbc.sqlserver.SQLServerDriver	 
jdbc:sybase:Tds	com.sybase.jdbc2.jdbc.SybDriver	 
jdbc:jtds	net.sourceforge.jtds.jdbc.Driver	 
jdbc:postgresql	org.postgresql.Driver	 
jdbc:fake	com.alibaba.druid.mock.MockDriver	 
jdbc:mock	com.alibaba.druid.mock.MockDriver	 
jdbc:hsqldb	org.hsqldb.jdbcDriver	 
jdbc:db2	COM.ibm.db2.jdbc.app.DB2Driver	DB2的JDBC Driver十分混乱，这个匹配不一定对
jdbc:sqlite	org.sqlite.JDBC	 
jdbc:ingres	com.ingres.jdbc.IngresDriver	 
jdbc:h2	org.h2.Driver	 
jdbc:mckoi	com.mckoi.JDBCDriver	 
jdbc:cloudscape	COM.cloudscape.core.JDBCDriver	 
jdbc:informix-sqli	com.informix.jdbc.IfxDriver	 
jdbc:timesten	com.timesten.jdbc.TimesTenDriver	 
jdbc:as400	com.ibm.as400.access.AS400JDBCDriver	 
jdbc:sapdb	com.sap.dbtech.jdbc.DriverSapDB	 
jdbc:JSQLConnect	com.jnetdirect.jsql.JSQLDriver	 
jdbc:JTurbo	com.newatlanta.jturbo.driver.Driver	 
jdbc:firebirdsql	org.firebirdsql.jdbc.FBDriver	 
jdbc:interbase	interbase.interclient.Driver	 
jdbc:pointbase	com.pointbase.jdbc.jdbcUniversalDriver	 
jdbc:edbc	ca.edbc.jdbc.EdbcDriver	 
jdbc:mimer:multi1	com.mimer.jdbc.Driver	 
28. 如何保存监控记录
https://github.com/alibaba/druid/wiki/%E6%80%8E%E4%B9%88%E4%BF%9D%E5%AD%98Druid%E7%9A%84%E7%9B%91%E6%8E%A7%E8%AE%B0%E5%BD%95

29. 我想Log输出SQL执行的信息怎么办？
https://github.com/alibaba/druid/wiki/%E9%85%8D%E7%BD%AE_LogFilter

30. 如何配置Druid内置的log实现
https://github.com/alibaba/druid/wiki/%E9%85%8D%E7%BD%AEdruid%E5%86%85%E7%BD%AE%E7%9A%84log%E5%AE%9E%E7%8E%B0

 

附：示例项目：
https://github.com/windwant/spring-dubbo-service.git
https://github.com/windwant/spring-boot-service.git