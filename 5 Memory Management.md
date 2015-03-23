5 Memory Management

Item 29: Understanding Reference Counting

Item 29 Things to Remember:
1 - 引用计数内存管理依托于计数的增加和减少。计数至少为一时，对象被创造。
保留计数（retain count）为整数是，对象不会被释放（alive）。保留计数降到0时，对象会被销毁。
2 - 在对象的生命周期中，对象会被引用该对象的对象保留（retained）和释放（released）。
保留（Retaining）会增加保留计数（retain count），释放（Releasing）会减少保留计数。

 