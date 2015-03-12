Lecture 01:

Public Declarations
Private Implementation
The name of the class
Uts superclass

NSObject is the root of class from which pretty much all iOS classes inherit.

Superclass's header file

If the superclass is in iOS itself, we import the entire "framework" that includes the superclass.
Foundation cantains basic non-UI objects like NSObject.

In fact, in iOS 7 (only), there is special syntax for importing an entire framework called @import.
However, the old framework importing syntax is backwards-compatible in iOS 7 ( "#import" ).

Our own header file must be imported into our implementation file.

Private declaration

In iOS, we don't access instance variables directly.
Instead, we use an @property which declares two methods: a "setter" and a "getter".
It is with those two methods that the @property's instance variable is accessed 
(both publicly and privately).
This particular @property is a pointer.
Specially, a pointer to an object whose class is (or inherits from) NSString.
ALL objects live in the heap (i.e. are pointed to) in Objective-C!
Thus you would never have a property of type "NSString" (rather,"NSString *").
Because this @property is in this class’s header file, it is public.
Its setter and getter can be called from outside this class’s @implementation block.

strong means:
"keep the object that this property points to in memory until I set this property to nil (zero)
(and it will stay in memory until everyone who has a strong pointer to it sets their property to nil too)"
weak would mean:
"if no one else has a strong pointer to this object,then you can throw it out of memory
and set this property to nil (this can happen at any time)"

nonatomic means:
"access to this property is not thread-safe".
We will always specify this for object pointers.
If you do not, 
then the compiler will generate locking code that will complicate your code elsewhere.

This @synthesize is the line of code that actually creates the
backing instance variable that is set and gotten.
Notice that by default the backing variable’s name is the same as
the property’s name but with an underbar in front.
This is the @property implementation that the compiler generates automatically for you (behind the scenes).
You are welcome to write the setter or getter yourself, 
but this would only be necessary if you needed to do something in addition 
to simply setting or getting the value of the property.

Because the compiler takes care of everything you need to implement a property, 
it’s usually only one line of code (the @property declaration) to add one to your class.

Notice no strong or weak here.
Primitive types are not stored in the heap, so there’s no need to
specify how the storage for them in the heap is treated.
Let’s look at some more properties. These are not pointers.
They are simple BOOL.
Properties can be any C type.
That includes int, float, etc., even C structs.
C does not define a “boolean” type.
This BOOL is an Objective-C typedef
It’s values are YES or NO.

It is actually possible to change the name of the getter that is generated.
The only time you’ll ever see that done in this class (or anywhere probably) is boolean getters.
(￼￼@property (nonatomic, getter=isChosen) BOOL )
This is done simply to make the code “read” a little bit nicer.

Remember, unless you need to do something besides setting or getting when a property is being set or gotten,
the implementation side of this will all happen automatically for you.

Enough properties for now. Let’s take a look at defining methods.
Here’s the declaration of a public method called match: 
which takes one argument (a pointer to a Card) and returns an integer.
What makes this method public? Because we’ve declared it in the header file.

match: is going to return a “score” which says 
how good a match the passed card is to the Card that is receiving this message.
0 means “no match”, higher numbers mean a better match.

There’s a lot going on here!
For the first time, we are seeing the “calling” side of properties (and methods).
For this example, 
we’ll return 1 if the passed card has the same contents as we do or 0 otherwise 
(you could imagine more complex scoring).

Notice that we are calling the “getter” for the contents @property
(both on our self and on the passed card)
This calling syntax is called “dot notation.”
It's only for setters and getters.

isEqualToString: 
is an NSString method which takes another NSString as an argument and
returns a BOOL (YES if the 2 strings are the same).
Also, we see the “square bracket” notation we use to
send a message to an object.
In this case, the message isEqualToString: 
is being sent to the NSString returned by the contents getter.

The object the message is being sent to (receiver).
Name of the message being sent.
Argument being passed along with the message.

We could make match: even more powerful by allowing it to match against 
multiple cards by passing an array of cards using the NSArray class in Foundation.

We’ll implement a very simple match scoring system here which is
to score 1 point if ANY of the passed otherCards’ contents
match the receiving Card's contents.
(You could imagine giving more points if multiple cards match.)

Note the for-in looping syntax here. 
This is called “fast enumeration.”
It works on arrays, dictionaries, etc.



Lecture 02:

this method has 2 arguments (and returns nothing).
It’s called "addCard:atTop".
this one takes no arguments and returns a Card
(i.e. a pointer to an instance of a Card in the heap).
We must #import the header file for￼any class we use in this file(e.g.Card).

Arguments to methods (like the atTop: argument) are never “optional.”
However, if we want an addCard: method without atTop:, we can define it separately.
And then simply implement it in terms of the the other method.

Declaring a @property makes space in the instance for the￼pointer itself,
but not does not allocate space in the heap for the object the pointer points to. 
The place to put this needed heap allocation is in the￼getter for the cards @property.

All properties start out with a value of 0 (called nil for pointers to objects).
So all we need to do is allocate and initialize the object if the pointer to it is nil.
This is called “lazy instantiation”.

arc4random() returns a random integer.

Calling objectAtIndexedSubscript: with an argument of zero on an empty array will crash.
(array index out of bounds)!
So let’s protect against that case.

NSUInteger is a typedef for an unsigned integer.

We could just use the C type unsigned int here. It’s mostly a style choice.
Many people like to use NSUInteger and NSInteger in public API and unsigned int and int inside implementation.
But be careful, int is 32 bits, NSInteger might be 64 bits.
If you have an NSInteger that is really big (i.e. > 32 bits worth)
it could get truncated if you assign it to an int.
Probably safer to use one or the other everywhere.

Even though we are overriding the implementation of the contents method, 
we are not re-declaring the contents property in our header file. 
We’ll just inherit that declaration from our superclass.

But there’s a problem here now.
A compiler warning will be generated if we do this.
Why?
Because if you implement BOTH the
setter and the getter for a property, 
then you have to create the instance variable for the property yourself.

All of the methods we’ve seen so far are “instance methods”.
They are methods sent to instances of a class.
But it is also possible to create methods
that are sent to the class itself. 
Usually these are either creation methods
(like alloc or stringWithFormat:
or utility methods.

Class methods start with + Instance methods start with -.
Since a class method is not sent to an instance, we cannot reference our properties in here 
(since properties represent per-instance storage).

Initialization in Objective-C happens immediately after allocation.
We always nest a call to init around a call to alloc.
Only call an init method immediately after calling alloc 
to make space in the heap for that new object.
And never call alloc without immediately calling some
init method on the newly allocated object

Notice this weird "return type" of instancetype. 
It basically tells the compiler that this method 
returns an object which will be the same type as the object that this
message was sent to.
We will pretty much only use it for init methods. Don’t worry about it too much for now.
But always use this return type for your init methods.

This sequence of code might also seem weird. 
Especially an assignment to self!
This is the ONLY time you would ever assign something to self
The idea here is to return nil if you cannot initialize this object.
But we have to check to see if our superclass can initialize itself.
The assignment to self is a bit of protection against our trying to
continue to initialize ourselves if our superclass couldn't initialize.
Just always do this and don't worry about it too much.

Sending a message to super is how we send a message to ourselves, 
but use our superclass’s implementation instead of our own.
Standard object-oriented stuff.

Action methods are allowed to have either no arguments,
one argument (the object sending the message),
or even two arguments
(the sender and the touch event provoking the action).
In this case, though, what we want is just the sending button 
so that we can change it when it is touched.

This method’s return type is actually void, 
but Xcode uses the typedef IBAction instead 
just so that Xcode can keep track that this is not just a
random method that returns void, but rather, it’s an action method.
Apart from that, IBAction is exactly the same thing as void.

The @property created by this process is weak because the
MVC’s View already keeps a strong pointer to the UILabel,
so there’s no need for the Controller to do so as well.
And if the UILabel ever left the View, 
the Controller most likely wouldn’t want a pointer to it anyway
(but if you did want to keep a pointer to it even if it left the View,
 you could change this to strong (very rare)).
 
 IBOutlet is a keyword Xcode puts here
 (similar to IBAction) to remind Xcode that this
is not just a random @property, 
it’s an outlet (i.e. a connection to the View).
The compiler ignores it.
 Otherwise this syntax should all be familiar to you.



Lecture 03:

You should always comment which initializer is your designated initializer
if it is different from your superclass’s.

Often a subclass’s implementation of a method will call its superclass’s 
implementation by invoking super

firstObject is an NSArray method. It is just like [array objectAtIndex:0]
except that it will not crash if the array is empty
(it will just return nil). Convenient.

Outlet Collection arrays are always strong
so Xcode has removed that option from the dialog.
While the View will point strongly to all of the buttons 
inside the array, it will not point to the array itself at all, 
(only our Controller will) so our outlet needs to be strongly 
held in the heap by our Controller.

This IBOutletConnection(UIButton) is again just something Xcode puts in there
to remember that this is an outlet not just a random NSArray.
The compiler ignores this.

Review (MVC)
1 - Model is UI-independent
Cards and Decks, not UIButtons and UILabels
2 - View is (so far) completely generic UI elements
UIButton
UILabel
3 - Controller interprets Model for View (and vice-versa) 
Example: converting isChosen to selected state of a button
Example: converting isMatched to enabled state of a button
Example: taking a button touch and turning it into a chooseCardAtIndex: 
in the Model Target/Action and Outlets (so far)

Review (Xcode)
1 - Create a Project and maneuver through Xcode’s UI
Hide/Show Navigator, Utilities, Assistant Editor, etc., etc. 
Also how to run in the Simulator.
2 - Edit
Not just code, but also your storyboard, 
use Attributes Inspector to edit buttons, labels, et. al.
Ctrl-drag to make connections (actions and outlets).
Right click on buttons, etc., 
to find out about and disconnect connections.
Look at warnings and errors (and get rid of them hopefully!). 
3 - Add classes to your project
e.g. you added the Card, etc., Model classes in your Homework assignment.
3 - Use the documentation
Many ways to get to documentation, 
but ALT-clicking on a keyword is one of the coolest. 
Once there, search and click on links to find what you want.
Crucial to being a good iOS programming to become familiar with all the documentation.

Review (Basic Objective-C)

Lecture 04:

Creating Objects
nil
Dynamic Binding
Object Typing
Introspection
Foundation Framework
Enumeration
Property List
Colors
Fonts
Attributed Strings
NSAttributedString
UILabel
