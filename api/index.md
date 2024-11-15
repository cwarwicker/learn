# APIs

## Types of API
- SOAP API - Simple Object Access Protocol (XML-based) (Old and less used now)
- RPC API - Remote Procedure Call - Client completes a function/procedures on the server and server responds with output
	- Essentially, just a local method which does an AJAX request to call a function on a remote server and return the result. Nothing special.
- Websocket API - Uses JSON objects to pass data in 2-way communication between client and server
- GraphQL - Query language for APIs which require large/complex data
- REST API - Most common type of api. They are Stateless. Use a set of http methods such as GET, POST, PUT, DELETE

## API Security/Authentication
- Most common way to secure an API is with access tokens, e.g. via something like OAuth.
	- These represent specific users with system privileges. They are dynamically generated and short-lived.
- Can also use API keys.
	- Less secure as they are generic and not user-specific. Often longer-lived.
- For more info on authentication methods, see [Authentication](/auth/index.md)

## REST APIs
- In order for an API to be considered RESTful, it has to be/implement:
  	- Uniform interface - Resources should be uniquely identifiable through a single URL. Manipulating a resource should occur only via the network protocol methods DELETE, PUT and GET with HTTP.
  	- Stateless - No client information stored between requests. Each is separate.
  	- Layered system - Client can connect to other authorized intermediaries between the client and server, and it will still receive responses from the server. 
  	- Cachable data where appropriate
  	- Code on demand - Servers can temporarily extend client funcitonality by transferring software programming code to the client.

### HTTP Methods
- GET - Retrieve something
- POST - Create something
- PUT - Update something entirely
- PATCH - Update something partially
- DELETE - Delete something

