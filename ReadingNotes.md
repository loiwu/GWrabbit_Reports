# Reading Notes

HTML, HTTP, and the URL. HTML defined a way to add styling to text, HTTP defined a way to convey data between server and client, and the URL defined a way to uniquely locate a resource across a network of machines.

HTTPS encrypts the entire HTTP message.

UNDERSTANDING HTTP REQUESTS AND RESPONSES
The most significant difference between HTTP and HTTPS is during the connection establishment phase of the conversation. 
After the TCP connection is made but before HTTP requests are transmitted, an SSL session must be established between the client and the server. 
SSL session establishment includes various stages: the client and server negotiating over which ciphers to use, exchanging public keys, validating the negotiation, and optionally validating identity. 
After the SSL session is established, all the data transmitted over the TCP connection will be encrypted.

URL Structure
The URL provides a globally unique location name for any resource or content on
the Internet. 
As a rule, a single resource may be found with multiple URLs, but a single URL will not refer to different resources.
A URL is typically composed of five components:
Protocol,Credentials,Hostname,Port,Absolute-path,Query
Because the contents of the absolute-path and query string are restricted, URLs are usually encoded using percent encoding.

Request Contents
An HTTP request consists of three parts: the request line, the request headers, and the request body.

Common Request Methods and Their Uses
| METHOD |     STANDARD USES     |
|:--------|:------------|
|     GET    | Retrieves a piece of content, or entity in HTTP terminology, from the server. GET requests usually don’t contain a request body, but it is allowed. Some network caching appliances will cache only GET responses. GET requests usually do not cause data changes on the server.    |
|     POST    |Updates an entity with data provided by the client. A POST request usually has information in the body of the request that is used by the application server. POST requests are considered to be non-idempotent, meaning that if more than one request is processed, the result is different than if only one request is processed.   |
|     HEAD    |Retrieves metadata about a response without retrieving the entire contents of the response. This method is usually used to check a server for recent content changes without having to retrieve the full content.   |
|     PUT    |Adds an entity with data provided by the client. A PUT request usually has information in the body of the request that is used by the application server to create the new entity. Usually, PUT requests are considered to be idem- potent, meaning that the request can be repeatedly applied with the same results.    |
|     DELETE    |Removes an entity based on contents of the URI or request body provided by the client. DELETE requests are most frequently used in REST service interfaces.    |

The URI uniquely identifies the target of the request.
The URI may also contain query parameters, which must not contain a space or carriage return character.

Notice that the URI does not contain the protocol, host, or port that a user usually provides in the address field of a browser. The client uses the protocol portion of the URL to determine how to connect to the server. The hostname and port specified by the client is provided in the Host header of the request.

Even though it uses the stateful TCP transport layer, HTTP is defined as a stateless protocol. This means that the HTTP server does not retain any information about a request to use in future requests. Cookies were developed as a way to allow some simple state information to be stored on the client and communicated to the server on subsequent requests.

In iOS the NSURLRequest object and its subclass NSMutableURLRequest provide the methods and attributes necessary to build almost any HTTP request. 

Response Contents
After the HTTP server and any application servers supporting it finish processing the request, an HTTP response is returned to the client over the same TCP socket. An HTTP response is structured similarly to an HTTP request with the first line being the status line, followed by headers, and a response body.

The status line contains three fields, each separated by a space character. The first field is the HTTP version of the response. The next two fields provide status values indicating the outcome of the request. The first of the two fields is a three-digit integer value containing the result code of
the request. The last field is a reason phrase that provides a short text description of the code.

In iOS’s URL loading system, the NSURLResponse object and its subclass NSHTTPURLResponse encapsulate the data returned from a request. There are two objects in this hierarchy because the URL loading can fulfill requests for data based on non-HTTP URLs. For example, a request for a file:// URL will not contain any headers.

There are three primary methods to perform HTTP requests and receive responses using the URL loading system:
Synchronous — The thread on which the initiating code runs blocks until the entire response is loaded and returned to the calling method. This technique is the simplest to implement but has the most limitations.
Queued asynchronous — The initiating code creates a request and places it on a queue to be performed on a background thread. This method is slightly more difficult to implement and removes a significant limitation of the pure synchronous technique.
Asynchronous — The initiating code starts a request that runs on the initiating thread but calls delegate methods as the requests proceeds. This technique is the most complicated to implement but provides the most flexibility in handling responses.


