//Messaging (Objective-C)
Object *obj = [Object new];
[obj performWith:parameter1 and:parameter2];

//Function calling (C++)
Object *obj = new Object;
obj->perform(parameter1, parameter2);

//messaging structure - runtime决定什么代码被执行
//function calling - compiler决定什么代码被执行

//When you declare a variable that is to hold a reference to an object
NSString *someString = @"The string";

//The memory for objects is always allocated in heap space and never on the stack.
//It is illegal to declare a stack-allocated Objective-C object
NSString stack String;
//error: interface type cannot be statically allocated

NSString *someString = @"The string";
NSString *anotherString = someString;

//variables that don't have a * in the definition and might use stack space, these variables are not holding Objective-C objects
CGRect farme;
frame.origin.x = 0.0f;
frame.origin.y = 10.0f;
frame.size.width = 100.0f;
frame.size.height = 150.0f;

//A CGRect is a C structure
struct CGRect {
	CGPoint origin;
	CGSize size;
};





@class EOCEmployer;
#import "EOCEmployer"





NSString *someString = @"Effective Objective-C 2.0";
// Literal Numbers
NSNumber *someNumber = [NSNumber numberWithInt:1];
NSNumber *someNumber = @1;
NSNumber *intNumber = @1;
NSNumber *floatNumber = @2.5f;
NSNumber *doubleNumber = @3.14159;
NSNumber *boolNumber = @YES;
NSNumber *charNumber = @'a';
// Literal Syntax
int x = 5;
float y = 6.32f;
NSNumber *expressionNumber = @(x * y);

// Literal Arrays
NSArray *animals = [NSArray arrayWithObjects:@"cat", @"dog", @"mouse", @"badger", nil];
NSArray *animals = @[@"cat", @"dog", @"mouse", @"badger"];
NSString *dog = [animals objectAtIndex:1];
// subscripting
NSString *dog = animals[1];
// syntactic sugar and literals are much safer
id object1 = /* ... */;
id object2 = /* ... */;
id object3 = /* ... */;
NSArray *arrayA = [NSArray arrayWithObjects:object1,object2,object3,nil];
NSArray *arrayB = @[object1,object2,object3];
//***Terminating app due to uncaught exception
//'NSInvalidArgumentException', reason:'***
//-[__NSPlaceholderArray initWithObjects:count:]: attempt to
//insert nil object from objects[0]'

// Literal Dictionaries
NSDictionary *personData = [NSDictionary dictionaryWithObjectsAndKeys:@"Matt",@"firstName",@"Galloway",@"lastName",[NSNumber numberWithInt:28],@"age",nil];
NSDictionary *personData = @{@"firstName":@"Matt",@"lastName":@"Galloway",@"age":28};
NSString *lastName = [personData objectForKey:@"lastName"];
NSString *lastName = personData[@"lastName"];

// Mutable Arrays and Dictionaries
[mutableArray replaceObjectAtIndex:1 withObject:@"dog"];
[mutableDictionary setObject:@"Galloway" forKey:@"lastName"];
// Setting through subscripting
mutableArray[1] = @"dog";
mutableDictionary[@"lastName"] = @"Galloway";

// Limitations
NSMutableArray *mutable = [@[@1,@2,@3,@4,@5] mutableCopy];