6 Blocks and Grand Central Dispatch

Item 37: Understand Blocks
理解块（Block）

块提供了一种闭包。作为GCC编译器的一种拓展，这种语言特性被应用于所有现代Clang版本的GCC compiler。
使block正确执行为函数（function）所必须得运行时组件（runtime component），从Mac OS X 10.4和iOS 4.0开始，就被完全支持。
这种语言特性技术上是一种c级别（C-level）的特性，因此能同时被应用在C，C++，Objective-C和ObjectiveC++代码中。

Block Basics 块的基础知识

block和函数很相似，但它被内联（inline）地定义成另一个函数，并在它被定义处共享范围。
用一个插入符号caret（^）来表示block，紧跟着block的范围，其中包含着block的执行代码。
block可以是一个简单地值，并拥有相关的类型。
和int，float，或其它的Objective-C对象一样，block可以被赋值给一个变量，然后像其它的变量一样使用。
block类型的语法和函数指针类似。

return_type(^block_name)(parameters)

以下是一些block的实例：
(1)
^{
	//Block implementation here
}
(2)
void(^someBlock)() = ^{
	//Block implementation here
}
(3)
int (^addBlock)(int a, int b) = ^(int a, int b){
	return a+b;
}
block可以当做函数来使用
int add = addBlock(2,5); //<add=7

block的一个强大特性是，在它定义处可以获得范围。
这意味着，范围内任何可用的变量，在block内也可用。
举例:
	int additional = 5;
	int (^addBlock)(int a,int b) = ^(int a,int b){
		return a + b + additional;
	};
	int add = addBlock(2,5); //<add = 12
	
通过__block限定词（qualifier）将变量声明为可修改（modifiable），该变量可在block中被修改。
举例: 
	block可以被用于数组枚举
	NSArray *array = @[@0,@1,@2,@3,@4,@5];
	__block NSInteger count = 0;
	[array enumerateObjectsUsingBlock:
		^(NSNumber *number,NSUInteger idx,BOOL *stop){
			if([number compare:@2] == NSOrderedAscending){
				count++;
			}
		}];
		// count = 2;
	以上示例也展示了内联block的使用方法。声明一个内联block，意味着，代码的业务逻辑会集中在一处，不会被分散开。

当捕获了一个对象类型（object type）的变量，block会隐式地保留（retain）它。当block自身被释放时候，该变量也会被释放（release）。
block自身可被认为是一个对象。事实上，block和其他Objective-C对象一样，可以响应各种selector。它和其他对象一样，有引用计数的特性。
当最后的引用被移除时，block会被销毁。任何block捕获的对象都会被release，以平衡block对其的retain。

如果block被定义在Objective-C类的实例方法中，伴随着类的任何实例变量，self变量都是可用的。
实例变量一般是可写的，而且不需要显示的用__block限定词进行声明。但是，如果实例变量通过对其读写而被捕获，self变量就会同时被隐式的捕获，因为实例变量与该实例相关。
举例:
	存在一个叫EOCClass的类，有方法anInstanceMethod，其中包含block
	@interface EOCClass
	- (void)anInstanceMethod {
		//...
		void(^someBlock)() = ^{
			_anInstanceVariable = @"Something";
			NSLog(@"_anInstanceVariable = %@",_anInstanceVariable);
		};
		//...
	}
	@end
	重要点：_anInstanceVariable = @"Something"; 通过这样的方式，self变量被捕获。
	必须注意，self是一个对象，当self被block捕获时，它会被retain。
	这种情况可能会到时retain cycle。
	
The Guts of a Block
详解Block

Block本身也是一个对象，在在block定义所存在的内存范围内的第一个变量也是一个指向类对象的指针，叫做isa。
block占用的其余内存，包含了很多位的信息，以便使block正确运作。

Global，Stack，and Heap Blocks
全局，栈，堆块

块一旦被定义，相关内存就会被分配到栈上。这就意味着，块只在它被定义的范围内是有效的。
举例:
	void(^block)();
	if(/*some condition*/){
		block = ^{
			NSLog(@"Block A");
		};
	} else {
		block = ^{
			NSLog(@"Block B");
		};
	}
	block();
	在if和else语句中定义的两个block，被分配到栈内存。
	当编译器为两个block分配栈内存时，窄内存分配范围的结尾处，编译器可以重写这内存。
	所以，每个block只能在它们各自的if语句中保证合法性。
	代码可能会被成功编译，但在运行时可能会出错。
	通过向block发送copy消息，来解决上述问题。
	这样，block会从栈（stack）拷贝到堆（heap），block就能在它定义以外的范围被使用。
	而且，一旦被拷贝到堆，block就成为了可引用计数的对象。任何接下来的copy都不是内容拷贝而是增加block的引用计数。
	一个栈块不需要被显式的释放，因为栈内存会自动回收。
举例: 安全的写法
	void(^block)();
	if(/*some condition*/){
		block = [^{
			NSLog(@"Block A");
		} copy];
	} else {
		block = [^{
			NSLog(@"Block B");
		} copy];
	}
	block();
	
Global Block 全局块
void(^block)() = ^{
	NSLog(@"This is a block");
}
编译时执行。

Item 37 Things to Remember:
1 - 块是C，C++和Objective-C的词法闭包（lexical closures）
2 - 块可以可选的带有参数和返回值
3 - 块可以被分配在栈，堆，上，也可以是全局的。
一个被分配在栈上的块可以被拷贝到堆上，这时，块就如同标准的Objective-C对象一般，具有引用计数性。


Item 38: Create typedefs for Common Block Types
为一般的块类型创建类型定义

块拥有固有类型（inherent type）。块可以被赋给适当类型的变量。
这种类型又块带有的参数和块返回的类型组成。
举例:
	^(BOOL flag,int value){
		if(flag){
			return value*5;
		} else {
			return value*10;
		}
	}
类型和向变量的赋值:
	int (^variableName)(BOOL flag,int value) = 
		^(BOOL flag,int value){
			//implementation
			return someInt;
		}
块变量（block-variable）定义格式:
	return_type (^block_name)(parameters)
	
使用typedef: 定义一个新类型 EOCSomeBlock
	typedef int(^EOCSomeBlock)(BOOL flag,int value);
使用定义的新类型:
	EOCSomeBlock block = ^(BOOL flag,int value){
		// Implementation
	}	

上述方法使得使用block的API更易使用。
任何使用块作为方法参数的类，举个例子，异步操作的完成处理器（completion handler），都可以使用为块创造类型定义的方法，使得它更易理解。
举例:
	- (void)startWithCompletionHandler:(void(^)(NSData *data,NSError *error))completion;
	为块创建类型定义，使得上述代码更易理解
	typedef void(^EOCCompletionHandler)(NSData *data,NSError *error);
	- (void)startWithCompletionHandler:(EOCCompletionHandler)completion;
	
Item 38 Things to Remember:
1 - 使用类型定义，使块变量更易使用
2 - 在定义新类型是，遵循命名规则，以防止与其它类型相冲突
3 - 不要害怕定义为相同的块签名（block signature）定义多个类型。
有时候，你可能会在某一处通过更改块签名来使用特定的块。

Item 39: Use Handler Blocks to Reduce Code Separation
使用处理器块（Handler Block）减少代码的分离

编程中的通常范例是，用户界面（user interface）异步地（asynchronously）执行任务。
这样，服务于用户界面显示和触发（touch）的线程，就不会因为长时间运行任务（long-running tasks）的发生而被阻塞（be blocked），比如输入输出（I/O）或网络活动（network activity）。
UI线程通常是主线程。
当异步方法（Asynchronous methods）完成时，它需要通过某种方式进行通知。代理协议是一种常用的技术。
在相关事件发生后，比如一个异步操作的完成，作为代理的对象将接到通知。
举例: 
	有一个从URL获取数据的类。使用代理模式（Delegate pattern）编码。

Item 39 Things to Remember:
1 - 在创建对象时需要内联声明业务逻辑（business logic）的处理器（handler）时，采用处理器块（handler block）
2 - 对于直接相关的对象，Handler block比代理更具优势。如果多个实例被观察，代理经常需要根据对象进行切换操作们。
3 - 将队列（queue）作为参数来传递，当使用handler block来设计API时，指定队列，block处于该队列，而将被弹出队列。