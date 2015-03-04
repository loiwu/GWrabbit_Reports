# GWrabbit_Reports
C1 - Accustoming Yourseld to Objectice-C
Item 1: Familiarize Yourself with Objective-C's Roots
Things to Remember:
	(1) Objective-C is a superset of C, adding object-oriented features. 
	Objective-C uses a messaging structure with dynamic binding, meaning that the type of an object is discovered at runtime. The runtime, rather than the compiler, works out what code to run for a given message.
	(2) Understanding the core concepts of C will help you write effective Objective-C. 
	In particular, you need to understand the memory model and pointers.
Item 2: Minimize Importing Headers in Headers
Things to Remember:
	(1) Always import headers at the very deepest point possible. 
	This usually means forward declaring classes in a header and importing their corresponding headers in an implementation. 
	Doing so avoids coupling classes together as much as possible.
	(2) Sometimes, forward declaration is not possible, as when declaring protocol conformance. 
	In such cases, consider moving the protocol-conformance declaration to the class-continuation category, if possible. Otherwise, import a header that defines only the protocol.
Item 3: Prefer Literal Syntax over the Equivalent Methods
Thing to Remember:
	(1) Use the literal syntax to create strings, nubers, arrays, and dictionaries.
	It is clearer and more succinct than creating them using the normal object-creation methods.
	(2) Access indexes of an array or keys in a dictionary through the subscripting methods.
	(3) Attempting to insert nil into an array or dictionary with literal syntax will cause an exception to be thrown.
	Therefor, always wnsure that such values cannot be nil.
Item 4: Prefer Typed Constants to Preprocessor #define
Things to Remember:
	(1) Avoid preprocessor defines. 
	They don't contain any type information and are simply a find and replace executed before compilation.
	They could be redefined without warning, yielding inconsistent values throughout an application.
	(2) Define translation-unit-specific constants whitin an implementation file as static const.
	These constants will not be exposed in the global symbol table, so their names do not need to be namespaced.
	(3) Define global constants as external in a header file, and define them in the associated implementation file.
	These constants will appear in the global symbol table, so their names should be namespaced, usually by prefixing them with the class name to which they correspond.
Item 5: Use Enumerations for States, Options, and Status Codes
Things to Remember:
	(1) Use enumerations to give readable names to values used for the states of a state machine, options passed to methods, or error status codes.
	(2) If an enumeration type defines options to a method in which multiple options can be used at the same time, define its values as powers of 2 so that multiple values can be bitwise OR'ed together.
	(3) Use the NS_ENUM and NS_OPTIONS macros to define enumeration types with an explicit type.
	Doing so means that the type is guaranteed to be the one chosen rather than a type chosen by the compiler.
	(4) Do not implement a default case in switch statements that handle enumerated types.
	This helps if you add to the enumeration, because the compiler will warn that the switch does not handle all the values.