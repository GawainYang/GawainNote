## JDBC 的超时时间
>恰当的JDBC超时设置能够有效地减少服务失效的时间。

“恰当的JDBC超时设置能够有效地减少服务失效的时间。” 这句话的另外一层含义是，就算设置了合适的超时时间，也不能完全解决服务失效问题。但是设置了超时时间有利于提升服务质量。
<br/>
JDBC是Java应用中用来连接关系型数据库的标准API。Sun公司一共定义了4种类型的JDBC，我们主要使用的是第4种，该类型的Driver完全由Java代码实现，通过使用socket与数据库进行通信。 <br/>
![图片](.\images\JDBC Type 4.png)
<br/>
<br/>
应用与数据库间的timeout层级<br/>
![图片](.\images\Timeout Class.png)
<br/>
** 说明 **
+ 若没有合理地设置socket timeout，会有——连接被阻塞错误。
+ 高级别的timeout依赖于低级别的timeout，只有当低级别的timeout无误时，高级别的timeout才能确保正常。例如，当socket timeout出现问题时，高级别的statement timeout和transaction timeout都将失效。 （** 靠近底层的超时时间需要比上层的长，只有这样，上层的超时才能生效。 **）
+ timeout层级与DBCP是相互独立的。
+ statement timeout无法处理网络连接失败时的超时，它能做的仅仅是限制statement的操作时间。网络连接失败时的timeout必须交由JDBC来处理。
> 即使设置了statement timeout，当网络出错时，应用也无法从错误中恢复。
+
<br/>

### Transaction Timeout
transaction timeout一般存在于框架（Spring, EJB）或应用级。transaction timeout或许是个相对陌生的概念，简单地说，transaction timeout就是“statement Timeout * N（需要执行的statement数量） + @（垃圾回收等其他时间）”。transaction timeout用来限制执行statement的总时长。
<br/>

在Spring中的配置：
```
<tx:attributes>  
        <tx:method name=“…” timeout=“3”/>  
</tx:attributes>  
```
### Statement Timeout
** 说明 **
+ statement timeout用来限制statement的执行时长，timeout的值通过调用JDBC的java.sql.Statement.setQueryTimeout(int timeout) API进行设置。
+ statement timeout的具体值需要依据应用本身的特性而定，并没有可供推荐的配置。
<br/>

设置方法：通过调用JDBC的java.sql.Statement.setQueryTimeout(int timeout) API进行设置。不过现在开发者已经很少直接在代码中设置，而多是通过框架来进行设置。

### Socket Timeout
** 说明 **
+ JDBC的socket timeout在数据库被突然停掉或是发生网络错误（由于设备故障等原因）时十分重要。
+ 由于TCP/IP的结构原因，socket没有办法探测到网络错误，因此应用也无法主动发现数据库连接断开。如果没有设置socket timeout的话，应用在数据库返回结果前会无期限地等下去，这种连接被称为dead connection。
+ 为了避免dead connections，socket必须要有超时配置。socket timeout可以通过JDBC设置，socket timeout能够避免应用在发生网络错误时产生无休止等待的情况，缩短服务失效的时间。
+ 不推荐使用socket timeout来限制statement的执行时长，** 因此socket timeout的值必须要高于statement timeout **，否则，socket timeout将会先生效，这样statement timeout就变得毫无意义，也无法生效。

下面展示了socket timeout的两个设置项，不同的JDBC驱动其配置方式会有所不同。
+ socket连接时的timeout：通过Socket.connect(SocketAddress endpoint, int timeout)设置
+ socket读写时的timeout：通过Socket.setSoTimeout(int timeout)设置
+ connectTimeout和socketTimeout的默认值为0时，timeout不生效。
+ 除了调用DBCP的API以外，还可以通过properties属性进行配置。
<br/>

下面是iBatis中的properties配置。
```
<transactionManager type=“JDBC”>  
  <dataSource type=“com.nhncorp.lucy.db.DbcpDSFactory”>  
     ….  
     <property name=“connectionProperties” value=“oracle.net.CONNECT_TIMEOUT=6000;oracle.jdbc.ReadTimeout=6000″/>   
  </dataSource>  
</transactionManager>  
```

## 参考文献
1. 深入理解JDBC的超时设置，http://www.importnew.com/2466.html；英文原文，https://www.cubrid.org/blog/understanding-jdbc-internals-and-timeout-configuration
2. JDBC driver，Wikipedia，JDBC technology drivers fit into one of four categories. https://en.wikipedia.org/wiki/JDBC_driver
