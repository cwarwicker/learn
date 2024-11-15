# Authentication

## Basic Authentication
![Example](/images/basicauth.png)
- In a web browser this is usually represented by a popup asking for a username/password before being able to access a resource. If incorrect details are given, a 401 is returned.
- Process:
	- When the client requests the resource, the server checks the `Authorization` header.
	- This is missing on the first request, so server responds `401 Unauthorized` and sends the `WWW-Authenticate` header value `Basic` which tells the client to initiate Basic auth flow.
	```
	401 Unauthorized
	WWW-Authenticate: Basic realm='user_pages'	
	```
	- The `realm` is the value assigned to a group of pages sharing the same credentials.
	- Upon receiving the response, the client will show the authentication window
	- After user submits the credentials, they are base64 encoded and sent in the Authorization header of the request. The value encoded is `username:password` in that format.
	- Server receives and decodes the credentials and then verifies and sends the relevant response.

## Digest Authentication
- Digest builds on Basic Auth but is more secure
- Process:
	- Client requests resource. Server checks `Authorization` header.
	- Missing on first request, so server responds 401 with random values for fields `nonce` and `opaque`, as well as `realm` as before.
	```
	HTTP/1.0 401 Unauthorized
	WWW-Authenticate: Digest
	realm="user_pages"
	nonce="dcd98b7102dd2f0e8b11d0f600bfb0c093"
	opaque="5ccc069c403ebaf9f0171e9517f40e41"
	```
	- Upon receiving the response, the client will show the authentication window.
	- Client submits credentials and request is sent back to server with `username`, `realm`,  `response`, `nonce` and `opaque` values.
	```
	GET /assest HTTP/1.0
	Host: localhost
	Authorization: Digest username="someuser",
	realm="user_pages",
	nonce="dcd98b7102dd2f0e8b11d0f600bfb0c093",
	uri="/assests",
	response="6629fae49393a05397450978507c4ef1",
	opaque="5ccc069c403ebaf9f0171e9517f40e41"
	```
 	- The `response` is generated in the following way:
    	```
		HA1 = MD5(username:realm:password)
		HA2 = MD5(method:digestURI)
		response = MD5(HA1:nonce:HA2)
     	```
- So this is slightly more secure than Basic auth, however MD5 hashing is basically obsolete these days, so not much better.
- It's recommended to use a more secure method, such as OAuth.


## Token-Based Authentication
- In Token authentication, a secondary service verifies a server request and issues a token, which can be used to access the service without having to re-enter credentials for anything protected by the same token.
- They work like a stamped ticket. Once authorised, the user is allowed into anything which the token can get them into. Once they leave (logout) they need to buy a new ticket.
- Process:
	- User requests access to a resource. They login with a password or other method.
	- Server verifies authentication method, e.g. username/password.
	- Server issues a token to the client (browser, phone app, etc...).
	- The token sits within the client's local storage and continues to work until they logout or it expires.
	- If the user tries to access different parts of the server, the token is exchanged and access granted/denied based on that.

## JWT (JSON Web Token)
![Example](/images/jwt.png)

- JWT is a special form of Token-Based Authentication.
- JWT is an open standard for auth tokens across various platforms (web, phones, etc...).
- They are verified with a digital signature for added security.
- JWT's have 3 important components:
	- Header - Defines token type and signing algorithm
	- Payload - Defines the token issuer, expiration, etc...
	- Signature - Verifies that message hasn't changed in transit
- These 3 components are base64 encoded and split by fullstops. (See the example above)
- This final token is then typically used in the `Authorization` header as a `Bearer` token, e.g. `Authorization: Bearer <token>`


## OAuth



## SAML



## OpenID



## Terms

- `nonce` - Number Used Once - Unique and time-sensitive randomly generated value, used to prevent replay attacks
- `opaque` - 
