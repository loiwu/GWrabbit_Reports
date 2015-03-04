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
