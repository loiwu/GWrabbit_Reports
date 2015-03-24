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
