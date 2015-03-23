4 Protocals and Categories
协议、分类
Objective-C没有多继承，而协议（Protocol）就提供了一种方式，为类定义一个实施（implement）的方法集合
分类（Category）提供了一种机制，不需要子类化某个类，就能为其增加新的方法（method）

Item 23 ：Use Delegate and Data Source Protocols for Interobject Communication
使用代理（Delegate）和数据源协议（Data Source Protocol）进行对象内的通信

对象间的通信模式之一：代理模式（Delegate Pattern）- 定义一个界面（interface），为了成为另一个对象的代理（delegate），任何对象都可以遵循（conform）这个界面。
被代理的对象能够talk back它的代理以获得一些信息。或者当某些事件发生时，被代理的对象会通知（inform）它的代理。
使用代理模式，可以从业务逻辑（business logic）中去除数据耦合（decoupling）。

举例: 一个视图View上要显示一列数据
	View只负责数据如何显示的逻辑（logic of how data is displayed）
	视图对象（view object）有两种属性，data source负责显示什么数据，delegate负责事件处理
	在Objective-C中常通过协议来实现上述模式

举例: 一个类用来获取网络数据
	这个类要获取（retrieve）远程服务器上的数据，而服务器的响应需要时间。这样，当数据在获取的时候，用户体验就不太好。
	该场景（scenario）下适用Delegate pattern，whereby the fetcher class has a delegate that it calls back once the data has been retrieved.

代理协议（delegate protocol）的命名规则一般是，类名+delegate，整个名称采用驼峰式大小写规则（camel casing）。
在被代理的类中，协议提供一个属性（property），比如@property (nonatomic, weak) id<EOCNetworkFetcherDelegate> delegate;

协议还可以提供一个界面，通过它类所需要的数据可以被获取。这种代理模式称为数据源模式（Data Source pattern）