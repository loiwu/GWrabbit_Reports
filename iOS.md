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

We could make match: even more powerful by allowing it to match against 
multiple cards by passing an array of cards using the NSArray class in Foundation.

We’ll implement a very simple match scoring system here which is
to score 1 point if ANY of the passed otherCards’ contents
match the receiving Card's contents.
(You could imagine giving more points if multiple cards match.)

Note the for-in looping syntax here. 
This is called “fast enumeration.”
It works on arrays, dictionaries, etc.

￼￼
￼￼
￼￼