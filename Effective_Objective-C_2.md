4 Protocals and Categories
协议、分类
Objective-C没有多继承，而协议（Protocol）就提供了一种方式，为类定义一个实施（implement）的方法集合
分类（Category）提供了一种机制，不需要子类化某个类，就能为其增加新的方法（method）

Item 23: Use Delegate and Data Source Protocols for Interobject Communication
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

Item 24: Use Categories to Break Class Implementations into Manageable Segments
使用分类（Category）将类的执行分解成可管理的片段

整个类仍然能被定义在一个interface文件和一个implemention文件中，但随着分类的增多，单一的implemention文件也会逐渐变得不好管理。
此时，各个分类可以被单独写入到它们自己的文件中。

类被分解到一段段更便于管理的代码段中。一定要import原类的头文件。

即使一个类的规模并不是很大，使用分类将它分解为许多小部分，这是一种将代码分解为一个个功能模块的有效方式。
举例: Cocoa的NSURLRequest以及它的可变（mutable）副本NSMutableURLRequest
	这个类常用语执行request以便从一个URL来获取数据。它的使用常常伴随着HTTP协议从远程服务器获取数据。
	这个类是通用的，它也可以被用于其他的协议（protocol）
	除了标准的URL，HTTP request还需要更多的信息，比如HTTP方法（GET，POST等）或者HTTP header
NSURLRequest的子类化并非易事，因为，NSURLRequest封装了一个C函数的集合，它通过CFURLRequest数据结构起作用，其中包括了所有的HTTP方法。
HTTP特定方法（HTTP-specific methods）通过一个叫做NSHTTPURLRequest的分类（category）被简单的加入NSURLRequest
HTTP的变体方法（mutation methods）通过一个叫做NSHMutableTTPURLRequest的分类（category）被加入NSMutableURLRequest
通过这种方式，下层的CFURLRequest被封装进Objective-C类中，但HTTP-specific方法就被剥离出来。
这样就避免了使用者的困惑，如果不这么做，使用者就会觉得奇怪，在一个request对象中，为什么要使用FTP协议去设置HTTP方法

为了调试（debugging）的目的将类进行分类，因为分类名为所有该分类中的方法加了一个标志
举例: addFriend:方法紧跟着一个symbol名
	-[EOCPerson(Friendship)addFriend:]
	在调试器的追踪（backtrace）中，会显示成如下
	frame #2: 0x0001c50 Test'-[EOCPerson(Friendship)addFriend:]
	+ 32 at main.m:46
以上信息指出了，方法与类什么功能模块相关。当特定的方法需要私有化（private）时，这种调试方法很有用。
这种情况下，可以创建一个叫Private的分类，用它来包含所有私有化方法。这些分类方法可以只被用于类的内部，或者框架中。
如果使用者通过读取backtrace发现了这些方法，名称中的private可以暗示使用者，这些方法不应该被直接使用
这是一种自注释代码（self-documenting code）的方式

在写类库时，创建Private category是一种很有用的方法，也很便于开发者的理解与共享。
有一种很常见的情形，有些方法并不会作为公共API（public API），但却很适合类库自己内部的使用。
在这样的情形下，创建一个Private分类就是一个很好地选择，因为，在类库中，只要是在相关方法需要被使用的地方，该分类的头文件会被import。
如果分类的头文件不作为发布类库的一部分，类库的consumer就无法知道这些私有方法的存在。

Item 24 Things to Remember:
Use categories to split a class implementation into more manageable fragments.
Create a category called Private to hide implementation detail of methods that should be considered as pricate.

Item 25: Always Prefix Category Names on Third-Party Classes
为第三方库类的分类添加前缀

分类中的方法被加入类中，就如同该方法是这个类的一部分。在运行时（runtime），分类被加载，上述情况发生。
如果一个分类中方法已经存在，那么分类中的方法实现就会替代原有的实现。这样的重写可能会反复发生。最后加载的分类会最终重写该方法。

Item 25 Things to Remember:
Always prepend your naming prefix to the names of categories you add to classes that are not your own.
Always prepend your naming prefix to the method names within categories you add to classes that are not your own.
