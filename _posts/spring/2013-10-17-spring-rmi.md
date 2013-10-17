---
layout: post
category : java
tags : [spring,rmi]
---
{% include JB/setup %}
RMI 问题 1 背景：
用 ./shutdown.sh 关闭 rmi 服务器的 tomcat ，然后 ./startup.sh 启动，客户端连接总是会导致如下错误：
org.springframework.remoting.RemoteLookupFailureException: Lookup of RMI stub failed; nested exception is java.rmi.UnmarshalException: error unmarshalling return; nested exception is:
        java.io.EOFException
com.ffcs.ieie.communicate.ieiemp.IeiempException: org.springframework.remoting.RemoteLookupFailureException: Lookup of RMI st
ub failed; nested exception is java.rmi.UnmarshalException: error unmarshalling return; nested exception is:
        java.io.EOFException
RMI 问题 1 分析：
The cause of the problem is the fact that Spring creates an RMIRegistry with the classloader of the server webapp. Then, when restarting the server, the RMIRegistry is not shut down. After the restart the Registry keeps its references to the old Stubs which do not exist anymore.
There are two solutions:
1) Start the rmiregistry in a seperate process without the classpath of the server app.
2) (better approach) Let spring start the RMIRegistry throu RmiRegistryFactoryBean, which shuts it down correctly.
参考： http://forum.springframework.org/showthread.php?t=33073
RMI 问题 1 解决：
将服务器中的 spring 配置代码，如下：
    < bean id = "incomingService" class = "com.ffcs.ieiemp.ieie.rmi.IncomingService" />
    < bean id = "rmiService" class = "org.springframework.remoting.rmi.RmiServiceExporter" >
        < property name = "serviceName" value = "IncomingService" />
        < property name = "service" ref = "incomingService" />
        < property name = "serviceInterface" value = "com.ffcs.ieiemp.ieie.rmi.Incoming" />
        < property name = "registryPort" value = "1099" />
</ bean >
替换为：
    <bean id="registry" class="org.springframework.remoting.rmi.RmiRegistryFactoryBean">
       <property name="port" value="1099"/>
    </bean>
    < bean id = "incomingService" class = "com.ffcs.ieiemp.ieie.rmi.IncomingService" />
    < bean id = "rmiService" class = "org.springframework.remoting.rmi.RmiServiceExporter" >
        < property name = "serviceName" value = "IncomingService" />
        < property name = "service" ref = "incomingService" />
        < property name = "serviceInterface" value = "com.ffcs.ieiemp.ieie.rmi.Incoming" />
        <!-- <property name="registryPort" value="1099"/> -->
        <property name="registry" ref="registry"/>
</ bean >
 
RMI 问题 2 背景：
          RMI 服务器重启，总是会出现客户端连接拒绝的问题。
RMI 问题 2 分析：
          服务器重启会影响到客户端？说明客户端有保存着重启之前的服务器连接相关记录。经研究发现，客户端 Stub 有缓存，所以只要刷新缓存即可解决问题。参考： http://forum.springsource.org/showthread.php?t=61575
RMI 问题 2 解决：
          在客户端连接代码中增加红色标识的代码：
       RmiProxyFactoryBean factory= new RmiProxyFactoryBean();
       factory.setServiceInterface(Incoming. class );
       factory.setServiceUrl(url);
       // XXX vincan: 解决重启 rmi 的服务器后会出现拒绝连接或找不到服务对象的错误
       factory.setLookupStubOnStartup(false );
       factory.setRefreshStubOnConnectFailure(true );
       factory.afterPropertiesSet();
    Incoming service=(Incoming)factory.getObject();
 
RMI 问题 3 背景：
          两台服务器， JDK 都是 1.6 ，在一个局域网内，内网 IP 分别为 192.168.39.11 ， 192.168.39.164 ，对应的的还有外网 IP 。写了一个 RMI 服务器和客户端，在本地调试没有问题。把服务器端布署到 11 这台服务器上后，在 164 客户端连接却总是抛错： 
java.rmi.ConnectException: Connection refused to host:127.0.0.1; nested exception is: 
java.net.ConnectException: Connection refused: connect 
at sun.rmi.transport.tcp.TCPEndpoint.newSocket(Unknown Source) 
at sun.rmi.transport.tcp.TCPChannel.createConnection(Unknown Source) 
at sun.rmi.transport.tcp.TCPChannel.newConnection(Unknown Source) 
at sun.rmi.server.UnicastRef.invoke(Unknown Source) 
at java.rmi.server.RemoteObjectInvocationHandler.invokeRemoteMethod 
(Unknown Source) 
at java.rmi.server.RemoteObjectInvocationHandler.invoke(Unknown Source) 
at $Proxy0.call(Unknown Source) 
at RMI.Client.callRMI(Client.java:39) 
at RMI.Client.main(Client.java:53) 
Caused by: java.net.ConnectException: Connection refused: connect 
at java.net.PlainSocketImpl.socketConnect(Native Method) 
at java.net.PlainSocketImpl.doConnect(Unknown Source) 
at java.net.PlainSocketImpl.connectToAddress(Unknown Source) 
at java.net.PlainSocketImpl.connect(Unknown Source) 
at java.net.SocksSocketImpl.connect(Unknown Source) 
at java.net.Socket.connect(Unknown Source) 
at java.net.Socket.connect(Unknown Source) 
at java.net.Socket.<init>(Unknown Source) 
at java.net.Socket.<init>(Unknown Source) 
at sun.rmi.transport.proxy.RMIDirectSocketFactory.createSocket 
(Unknown Source) 
at sun.rmi.transport.proxy.RMIMasterSocketFactory.createSocket 
(Unknown Source) 
... 9 more 
也就是 IP 被指向 127.0.0.1 了，而客户端发起连接的时候 IP 绝对是写的 IP 是 192.168.39.11 。
RMI 问题 3 分析：
这就是典型的服务器有多个 ip 引起的 rmi 连接问题。
RMI 问题 3 解决：
         解决方法有三：
<!---->1.       <!---->服务器端添加代码： System.setProperty("java.rmi.server.hostname" , "192.168.39.11" );
可写在全局监听器里成为全局变量。
<!---->2.  <!---->在 RMI 服务器上 root 身份登录，输入 Vi /etc/hosts ，在第一行添加 192.168.39.11     ieie
<!---->3.  <!---->若是用 spring, 则在 RmiServiceExporter 中添加属性 <property name="registryHost"  value="192.168.39.11" />
总结 ：RMI固然好用，但得慎用，以上问题困扰甚久，终得解决:)

<!-- more -->



