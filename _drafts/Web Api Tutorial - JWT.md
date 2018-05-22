# Introduction #
JSON Web Tokens are an open specification that defines a compact and self-contained way for securely transmitting information between parties as a JSON object. The information contained can be verified and trusted because it is digitally signed. 

Two major usage:

- **Authentication**: Once the user is logged in, each subsequent request will include the JWT, allowing the user to access routes, services, and resources that are permitted with that token. Single Sign On is a feature that widely uses JWT nowadays, because of its small overhead and its ability to be easily used across different domains.

- **Information Exchange**: JSON Web Tokens are a good way of securely transmitting information between parties. Because JWTs can be signed—for example, using public/private key pairs—we can be sure the senders and receivers. Additionally, as the signature is calculated using the header and the payload, we can also verify that the content hasn't been tampered with.

# JWT structure #

JSON Web Tokens consist of three parts separated by dots (.), which are:

- Header
- Payload
- Signature

Therefore, a JWT typically looks like the following.

`xxxxx.yyyyy.zzzzz`

1. Header

	The header typically consists of two parts: the type of the token, which is JWT, and the hashing algorithm being used, such as HMAC SHA256 or RSA. e.g.

		{
		  "alg": "HS256",
		  "typ": "JWT"
		}

	Then, this JSON is `Base64Url` encoded to form the first part of the JWT.

1. Payload

	Payload contains the claims which are statements about an entity (typically, the user) and additional metadata. There are three types of claims: registered, public, and private claims. The payload is then Base64Url encoded to form the second part of the JSON Web Token.

1. Signature

	The signature is used to verify that the sender of the JWT is who it says it is and to ensure that the message wasn't changed along the way.

	In order to To create the signature part we have to take the encoded header, the encoded payload, a secret, the algorithm specified in the header, and sign that with a secret.

	e.g.

		HMACSHA256(
		  base64UrlEncode(header) + "." +
		  base64UrlEncode(payload),
		  secret)
	
Finally, the output is three Base64 strings separated by dots that can be easily passed in HTML and HTTP environments, while being more compact when compared to XML-based standards such as SAML.

Here is a JWT that has the header encoded, the payload encoded, and the signature signed with a secret. 

![](/images/posts/20180303-jwt-2.png)

we got

![](/images/posts/20180303-jwt-1.png)

There are multiple libraries allow us to decode, verify and generate JWT.

# How JWT works? #

1. In authentication, when the user successfully logs in using their credentials, a JSON Web Token(JWT) will be returned

1. User agent must save the JWT locally (typically in local storage, but cookies can be also used), instead of the traditional approach of creating a session in the server and returning a cookie.

1. Whenever the user wants to access a protected route or resource, the user agent should send the JWT, typically in the `Authorization` header using the Bearer schema. The content of the header should look like:

	`Authorization: Bearer <token>`

	This is a stateless authentication mechanism as the user state is never saved in server memory. 

1. The server's protected routes will check for a valid JWT in the Authorization header, and if it's present, the user will be allowed to access protected resources. As JWTs are self-contained, all the necessary information is there, reducing the need to query the database multiple times.

	![](/images/posts/20180303-jwt-3.png)

# References #
[https://jwt.io/introduction/](https://jwt.io/introduction/)

[http://www.c-sharpcorner.com/article/handle-refresh-token-using-asp-net-core-2-0-and-json-web-token/](http://www.c-sharpcorner.com/article/handle-refresh-token-using-asp-net-core-2-0-and-json-web-token/)

[https://www.codeproject.com/Articles/1217608/ASP-NET-Core-Identity-Supporting-JWT-Token-for-Use](https://www.codeproject.com/Articles/1217608/ASP-NET-Core-Identity-Supporting-JWT-Token-for-Use)

refresh token
[https://auth0.com/docs/tokens/refresh-token/current](https://auth0.com/docs/tokens/refresh-token/current)