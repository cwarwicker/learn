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

### JWT (JSON Web Token)
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


### OAuth (2.0)
![Example](/images/oauth.jpg)
- OAuth is an open standard for authorization which works over HTTPS and authorizes clients with access tokens, created for SSO. It is not a protocol. It is a framework you can follow.
- Prior to OAuth, authenticating via different services meant the server actually logging into your account (e.g. Google) with the user/pass you provided.
- In OAuth, the client talks to their identity provider, which generates a token, which it hands off to the application to authenticate the user. The application trusts the identify server, as it is signed.
- OAuth allows applications to obtain access to specific parts (scopes) of a user's data, without giving away the password or all their data.
- Tokens and refresh tokens are provided (refresh token allows you to get a new token)
  	- These tokens can be in any format. E.g. JWTs. The implementation is up to you.
- Process:
  	- Application requests authorization from user
  	- User authorizes the application to access their data
  	- Application presents proof of authorization to server to get a token
  	- Token is restricted to only access what the user authorized for that application
- Key concepts:
  	- Scopes - Bundles of permissions asked for when requesting the token. E.g. "This application will be able to - read your posts, see who you follow, post for you, etc..."
  	- Actors:
  	  	- Resource Owner - Owns the data in the resource server, e.g. you are the Resource Owner of your Google Photos account
  	  	- Resource Server - The API which stores the data the application wants access to, e.g. Google Photos API
  	  	- Client - The application what wants access to the data, e.g. Photo editing software requesting access to photos
  	  	- Authorization Server - The server responsible for authernticating and issuing tokens, e.g. Google OAuth Server


## OpenID Connect (OIDC)
- OpenID Connect was built on top of OAuth 2.0 but with an authentication focus, for SSO.
- It includes an `id_token` and a `UserInfo` endpoint to receive user attributes, using standardised scopes and claims
- Scopes:
	- Pre-defined permissions that speciofy what info is being requested, e.g. `openid`, `profile`, `email`, `address`, `phone`, as well as custom scopes.
- Claims:
	- Individual pieces of info (attributes) returned as part of the `id_token` or from `UserInfo` endpoint, e.g. from the `profile` scope: `"name": "Conn Warwicker", "gender": "M"`


### Difference between OAuth and OIDC
- Where OAuth focuses more on authorization for specific resources, e.g. API/Application permissions, OIDC is more for authentication and SSO, including user data to identify the user.
- OAuth handles user's consent and access delegation. OIDC uses the OAuth flow to authenciate the user and retrieve identify information.


## SAML (Security Assertion Markup Language)
- SAML is similiar in purpose to OIDC, allowing you to access multiple applications (SPs - Service Providers) using 1 set of credentials, managed by an idP (Identity Provider).
- Process:
	- User tries to authenticate with application.
 	- Application makes a SAML request to the idP.
  	- User is redirected to the SSO login page for the configured idP, based on the URI returned in the request.
  	- idP authenticates the user and returns a SAML response to client.
  	- Client sends the SAML response to the application for verification.

 ![Workflow](/images/saml.png)

- Typically, when configuring the SAML integration, the SP and idP will exchange metadata, which are XML files containing information about the system, such as endpoints.



## Terms

- `nonce` - Number Used Once - Unique and time-sensitive randomly generated value, used to prevent replay attacks
- `opaque` - Randomly generated string to be returned by the client, so we know it's valid response


## Useful Links
- JWT - https://www.youtube.com/watch?v=7Q17ubqLfaM
- 


## Notes

- In PHP, when you use the auth option `CURLAUTH_ANY`, the initial request will be sent to the server without any auth method, and it will check the response headers for the available authentication methods. From there, it will choose the most secure one. So, for example if the end server responds that you can use Basic or Digest, it will pick Digest.
