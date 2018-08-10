# SharpNodeSettings
一个设备及节点配置类库，基于HslCommunication.dll深度整合创建，用来实现对PLC配置信息的存储和加载解析，同时支持可配置化的PLC数据读取，支持数据显示，其中服务器示例如下：
* **SampleServer** 简单的演示了如何启动一个服务器程序，虽然示例是基于 **Console** 的应用程序，你也可以实现 **Winform** 或是 **Wpf** 应用程序，
* **RedisServer** 演示了如果创建一个服务器，并且将读取到数据存入Redis的服务器，详细参照下文的说明。
* **OpcUaServer** 由于OPC UA在工控界相当火爆，所以此处也集成了这个示例，采用最新的1.4.354.0版本的库实现，主要麻烦的地方在于数据解析，并创建节点，具体参照下文的说明。

## How to use
包括服务器和客户端都是围绕配置的Xml文件创建的，示例的Xml文件内容如下：
'''
<?xml version="1.0" encoding="utf-8"?>
<Settings>
  <NodeClass Name="Devices" Description="所有的设备的集合对象">
    <NodeClass Name="分厂一" Description="">
      <NodeClass Name="车间一" Description="" />
      <NodeClass Name="车间二" Description="">
        <DeviceNode Name="ModbusTcp客户端" Description="这是描述" DeviceType="10" ConnectTimeOut="1000" CreateTime="2018/8/9 19:58:49" InstallationDate="2018/8/9 19:58:49" IpAddress="127.0.0.1" Port="502" Station="1" IsAddressStartWithZero="true" IsWordReverse="false" IsStringReverse="false">
          <DeviceRequest Name="数据请求" Description="一次完整的数据请求" Address="0" Length="30" CaptureInterval="1000" PraseRegularCode="ABCD" />
        </DeviceNode>
      </NodeClass>
    </NodeClass>
    <NodeClass Name="分厂二" Description="位于西南方">
      <NodeClass Name="车间三" Description="">
        <DeviceNode Name="测试设备二" Description="这是测试设备二的描述" DeviceType="10" ConnectTimeOut="1000" CreateTime="2018/8/10 23:01:28" InstallationDate="2018/8/10 23:01:28" IpAddress="127.0.0.1" Port="502" Station="1" IsAddressStartWithZero="true" IsWordReverse="false" IsStringReverse="false">
          <DeviceRequest Name="数据请求" Description="一次完整的数据请求" Address="100" Length="10" CaptureInterval="500" PraseRegularCode="B" />
        </DeviceNode>
      </NodeClass>
    </NodeClass>
  </NodeClass>
  <NodeClass Name="Server" Description="所有挂载的服务器">
    <ServerNode Name="异形服务器" Description="这是一个异形服务器" CreateTime="2018/8/8 13:29:30" Port="1234" ServerType="2" Password="" />
  </NodeClass>
  <NodeClass Name="Regular" Description="所有的解析规则的信息">
    <RegularNode Name="ABCD" Description="">
      <RegularItemNode Name="温度" Description="" Index="0" TypeCode="3" TypeLength="1" />
      <RegularItemNode Name="风俗" Description="" Index="2" TypeCode="9" TypeLength="1" />
      <RegularItemNode Name="转速" Description="" Index="14" TypeCode="9" TypeLength="1" />
      <RegularItemNode Name="机器人关节" Description="" Index="18" TypeCode="9" TypeLength="6" />
      <RegularItemNode Name="cvsdf" Description="" Index="42" TypeCode="9" TypeLength="1" />
      <RegularItemNode Name="条码" Description="条码信息" Index="6" TypeCode="11" TypeLength="8" />
      <RegularItemNode Name="开关量" Description="设备的开关量信息" Index="368" TypeCode="1" TypeLength="8" />
    </RegularNode>
    <RegularNode Name="B" Description="">
      <RegularItemNode Name="温度" Description="" Index="0" TypeCode="3" TypeLength="1" />
      <RegularItemNode Name="压力" Description="" Index="2" TypeCode="3" TypeLength="1" />
    </RegularNode>
  </NodeClass>
</Settings>
'''
只要创建好这个xml文件，就可以调用 **SharpNodeServer** 来创建服务器应用了，可以生成相应的节点信息，并且根据配置信息来请求设备，更新对应的数据。创建服务器的代码如下：
'''
SharpNodeServer sharpNodeServer = new SharpNodeServer( );
sharpNodeServer.LoadByXmlFile( "settings.xml" );
sharpNodeServer.ServerStart( 12345 );
'''

这样就启动了一个最简单的服务器，主要包含实例化，加载配置，启动服务器，注意：加载配置必须放置到服务器启动之前。


怎样查看服务器的数据呢？内置了一个默认的 **SimplifyNet** 服务器，想要知道更多的这个服务器的内容，可以参照下面的博客：[https://www.cnblogs.com/dathlin/p/7697782.html](https://www.cnblogs.com/dathlin/p/7697782.html)

基于 **NetSimplifyClient** 实现了一个通用的数据节点查看器，需要指定服务器的Ip地址和端口号：
![Picture](https://raw.githubusercontent.com/dathlin/SharpNodeSettings/master/Imgs/NodeView.png)

如果你想实现访问单个的数据，可以使用 **NetSimplifyClient** 创建的Demo来访问，需要注意的是，此处请求的数据都是序列化的JSON字符串。
![Picture](https://raw.githubusercontent.com/dathlin/SharpNodeSettings/master/Imgs/SimplifyView.png)
