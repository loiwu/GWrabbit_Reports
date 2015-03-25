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

Item 41 Prefer Dispatch Queues to Locks for Synchronization
在同步锁上应用分发队列

有时候遇到多线程操作代码时，需要使用锁来，保证代码的同步。
在GCD之前，有两种方法
方法一 举例: 内置（build-in）同步块
	- (void)synchronizedMethod{
		@sychronized(self) {
			// Safe
		}
	}
	构造函数会为给定的对象自动加锁。
方法二 举例: 直接使用NSLock对象
	_lock = [[NSLock alloc] init];
	- (void)sychronizedMethod {
		[_lock lock];
		// Safe
		[_lock unlock];
	}

递归锁（Recursive lock）可通过NSRecursiveLock实现

所谓递归锁，就是在同一线程上该锁是可重入而不造成死锁的，对于不同线程则相当于普通的互斥锁。
则在同一线程上函数A是不会形成死锁的，但此时如果其他线程想要加锁，只有等待拥有锁的线程释放所有的锁。（加锁几次要释放几次）
互斥锁(mutexlock)：
最常使用于线程同步的锁；标记用来保证在任一时刻，只能有一个线程访问该对象，同一线程多次加锁操作会造成死锁；临界区和互斥量都可用来实现此锁，通常情况下锁操作失败会将该线程睡眠等待锁释放时被唤醒

自旋锁(spinlock)：

同样用来标记只能有一个线程访问该对象，在同一线程多次加锁操作会造成死锁；使用硬件提供的swap指令或test_and_set指令实现；同互斥锁不同的是在锁操作需要等待的时候并不是睡眠等待唤醒，而是循环检测保持者已经释放了锁，这样做的好处是节省了线程从睡眠状态到唤醒之间内核会产生的消耗，在加锁时间短暂的环境下这点会提高很大效率

读写锁(rwlock)：

高级别锁，区分读和写，符合条件时允许多个线程访问对象。处于读锁操作时可以允许其他线程和本线程的读锁， 但不允许写锁， 处于写锁时则任何锁操作都会睡眠等待；常见的操作系统会在写锁等待时屏蔽后续的读锁操作以防写锁被无限孤立而等待，在操作系统不支持情况下可以用引用计数加写优先等待来用互斥锁实现。 读写锁适用于大量读少量写的环境，但由于其特殊的逻辑使得其效率相对普通的互斥锁和自旋锁要慢一个数量级；值得注意的一点是按POSIX标准 在线程申请读锁并未释放前本线程申请写锁是成功的，但运行后的逻辑结果是无法预测

递归锁(recursivelock)：

严格上讲递归锁只是互斥锁的一个特例，同样只能有一个线程访问该对象，但允许同一个线程在未释放其拥有的锁时反复对该锁进行加锁操作； windows下的临界区默认是支持递归锁的，而linux下的互斥量则需要设置参数PTHREAD_MUTEX_RECURSIVE_NP，默认则是不支持

使用GCD，可以提供一种更简单更高效的加锁方式
举例: property属性设置为atomic，手动写acceeors
	get方法:
	- (NSString *)someString {
		@synchronized(self) {
			return _someString;
		}
	}
	set方法:
	- (void)setSomeString {
		@sychronized(self) {
			_someString = someString;
		}
	}
	
在相同的线程上分发（Dispatch）读写，可以保证同步

使用dispatch_queue_create创建Serial Dispatch Queue，该queue虽然每次只执行一个任务，
但是通过dispatch_queue_create 可以创建多个Serial Dispatch Queue，将处理追加到多个queue中，
每次就同时执行多个任务，系统对于一个Serial Dispatch Queue就生成一个线程，如果这样的线程过多，对资源耗费是相当大的，反而降低了系统的性能，因此需要注意创建的数量。
举例:
	_syncQueue = dispatch_queue_create("com.effectiveobjectivec.syncQueue",NULL);
	
	// get方法
	- (NSString *)someString {
		__block NSString *localSomeString;
		dispatch_sync(_syncQueue,^{
			localSomeString = _someString;
		});
		return localSomeString;
	}
	// set方法
	- (void)setSomeString:(NSString *)someString {
		dipatch_sync(_syncQueue,^{
			_someString = someString;
		});
	}
GCD队列将setter和getter方法在一个连续队列上运行。GCD在底层做了很多优化来处理加锁操作。

进一步举例: 这里setter方法其实没必要同步，用来设置实例变量的block代码不需要向setter返回任何值。
	- (void)setSomeString:(NSString *)someString {
		dispatch_async(__syncQueue, ^{
			_someString = someString;
		});
	}
	将同步分发（synchronous dispatch）修改成异步分发（asynchronous dispatch）后，
	读写操作的执行任然是彼此连续的，但从调用者的角度来说，代码被优化的更快了。
	其实，通过异步分发，block被拷贝了，如果拷贝小号的时间远大于block被执行的时间，那么执行速度就变慢了。
	所以在这个简单的例子中，执行速度是降低的。但对于大负荷的任务，这种方法是一种好的选择。
	
另一种优化速度的方法是，让getter方法都彼此并发执行，但getter和setter方法不并发。
举例:在连续队列中，看看使用并发队列会产生的效果
	_syncQueue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT,0);
	- (NSString *)someString {
		__block NSString *localSomeString;
		dispatch_sync(_syncQueue,^{
			localSomeString = _someString;
		});
		return localSomeString;
	}
	- (void)setSomeString:(NSString *)someString {
		dispatch_async(_syncQueue,^{
			_someString = someString;
		});
	}
	
	Global queues: 全局队列是并发队列，并由整个进程共享。进程中存在三个全局队列：高、中（默认）、低三个优先级队列。可以调用dispatch_get_global_queue函数传入优先级来访问队列。
	以上代码中，读写操作都发生在全局队列这个并发队列上，采用GCD的阻碍（barrier），来解决并发读写的问题。
	void dispatch_barrier_async(dispatch_queue_t queue, dispatch_block_t block);
	void dispatch_barrier_sync(dispatch_queue_t queue, dispatch_block_t block);
	
	在队列中，依据所有其它的块，barrier独立执行。barrier只与并行队列有关。
	当队列正在执行，即将执行的时一个阻碍块（barrier block）。队列会先等待所有单线块执行完，然后执行阻碍块，然后继续执行其它块。
	
	并发队列上读操作使用普通的block，写操作使用barrier block。
	读操作并发执行，写操作独立执行。
示例代码:
	_syncQueue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT,0);
	- (NSString *)someString {
		__block NSString *localSomeString;
		dispatch_sync(_syncQueue,^{
			localSoneString = _someString;
		});
		return localSomeString;
	}
	- (void)setSomeString:(NSString *)someString {
		dispatch_barrier_async(_syncQueue,^{
			_someString = someString;
		});
	}
	相比串行队列，以上采用并发队列加barrier block的方法更快速。