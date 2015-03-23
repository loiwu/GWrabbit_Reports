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
