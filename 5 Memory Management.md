5 Memory Management

Item 29: Understanding Reference Counting
理解引用计数

Item 29 Things to Remember:
1 - 引用计数内存管理依托于计数的增加和减少。计数至少为一时，对象被创造。
保留计数（retain count）为整数是，对象不会被释放（alive）。保留计数降到0时，对象会被销毁。
2 - 在对象的生命周期中，对象会被引用该对象的对象保留（retained）和释放（released）。
保留（Retaining）会增加保留计数（retain count），释放（Releasing）会减少保留计数。

Item 30: Use ARC to Make Reference Counting Easier
使用ARC使引用计数更容易

Item 30 Things to Remember:
1 - ARC自动引用计数（Automatic Reference Counting）将开发者从大部分的内存管理的工作中解放出来。
使用ARC可以减少类的样板文件（boilerplate）代码
2 - ARC通过在合适的地方保留（retain）和释放（release），来处理对象的生命周期。
变量限定词（variable qualifier）用以表示内存管理的语义。而在此之前，都是使用retain喝release来进行手动的管理。
3 - ARC只作用于Objective-C对象。这就意味着，CoreFoundation对象不会被处理，需要适时调用CFRetain/CFRelease。

Item 31: Release References and Clean Up Observation State Only in dealloc
只在dealloc中释放引用和清理观察状态（Observation State）

Item 31 Things to Remember:
1 - dealloc方法仅在释放对其他对象的引用和做必要注销的时候使用，
比如注销KVO（Key-Value Observing）和NSNotificationCenter的通知。
2 - If an object holds onto system resources, such as file descriptors, 
there should be a method for releasing these resources.
It should be the contract with the consumer of such a class to call this close method when finished using the resources.
3 - 要避免在dealloc中进行方法调用，因为某些方法会进行异步（asynchronous）操作，或是在假设对象处于正常状态的情况下结束。