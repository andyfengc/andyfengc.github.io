---
layout: post
title: JWT
author: Andy Feng
---

# Introduction #
`JSON Web Tokens(JWT)` is an open specification that defines a compact and self-contained way for securely transmitting information between parties as a JSON object. The information contained can be verified and trusted because it is digitally signed. 

Two major usage:

- **Authentication**: Once the user is logged in, each subsequent request will include the JWT, allowing the user to access routes, services, and resources that are permitted with that token. Single Sign On is a feature that widely uses JWT nowadays, because of its small overhead and its ability to be easily used across different domains.

- **Information Exchange**: JSON Web Tokens are a good way of securely transmitting information between parties. Because JWTs can be signed—for example, using public/private key pairs—we can be sure the senders and receivers. Additionally, as the signature is calculated using the header and the payload, we can also verify that the content hasn't been tampered with.

# JWT structure #

JSON Web Tokens consist of three parts separated by dots (.), which are:

- Header
- Payload
- Signature

Therefore, a JWT typically looks like the following.

`<base64-encoded header>.<base64-encoded claims>.<base64-encoded signature>`

e.g

> eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1bmlxdWVfbmFtZSI6ImN1b25nIiwibmJmIjoxNDc3NTY1NzI0LCJleHAiOjE0Nzc1NjY5MjQsImlhdCI6MTQ3NzU2NTcyNH0.6MzD1VwA5AcOcajkFyKhLYybr3h13iZjDyHm9zysDFQ


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

	Technically, JWT takes the encoded header, the encoded payload, a secret, the algorithm specified in the header(example: HmacSha256), and sign that with a secret to get the signature.

	e.g.

		HMACSHA256(
		  base64UrlEncode(header) + "." +
		  base64UrlEncode(payload),
		  secret)
	
Finally, the output of JWT is three Base64 strings separated by dots that can be easily passed in HTML and HTTP environments, while being more compact when compared to XML-based standards such as SAML. The JWT is required to be transferred over HTTPs if we store any sensitive information in claims.

For example, here is a JWT that has the header encoded, the payload encoded, and the signature signed with a secret. 

![](/images/posts/20180303-jwt-2.png)

There are multiple libraries across different technologies allow us to decode, verify and generate JWT.

# How JWT works? #

1. In authentication, when the user successfully logs in using their credentials, a JSON Web Token(JWT) will be returned

1. User agent must save the JWT locally (typically in local storage, but cookies can be also used), instead of the traditional approach of creating a session in the server and returning a cookie.

1. Whenever the user wants to access a protected route or resource, the user agent should send the JWT, typically in the `Authorization` header using the Bearer schema. The content of the header should look like:

	`Authorization: Bearer <token>`

	This is a stateless authentication mechanism as the user state is never saved in server memory. 

1. The server's protected routes will check for a valid JWT in the Authorization header, and if it's present, the user will be allowed to access protected resources. As JWTs are self-contained, all the necessary information is there, reducing the need to query the database multiple times.

	![](/images/posts/20180303-jwt-3.png)

# Demo
1. Create an endpoint to issue JWT token when user enter username/password

		public class TokenController : ApiController
		{
		    // This is naive endpoint for demo, it should use Basic authentication to provide token or POST request
		    [AllowAnonymous]
		    public string Get(string username, string password)
		    {
		        if (CheckUser(username, password))
		        {
		            return JwtManager.GenerateToken(username);
		        }
		
		        throw new HttpResponseException(HttpStatusCode.Unauthorized);
		    }
		
		    public bool CheckUser(string username, string password)
		    {
		        // check user credentails in the database
				// ...
		        return true;
		    }
		}

1. Create JwtManager to issue token.

	We can use the NuGet package `icrosoft.IdentityModel.Tokens`(5.x), `System.IdentityModel.Tokens.Jwt`(5.x) from MS to generate the token, or even another package if you like. In the demo, I use `HMACSHA256` with SymmetricKey

		using Microsoft.IdentityModel.Tokens;
		using System;
		using System.Collections.Generic;
		using System.IdentityModel.Tokens.Jwt;
		using System.Security.Claims;
		public class JwtManager{
		    /// <summary>
		    /// Use the below code to generate symmetric Secret Key
		    ///     var hmac = new HMACSHA256();
		    ///     var key = Convert.ToBase64String(hmac.Key);
		    /// </summary>
		    private const string SecretBase64 = "db3OIsj+BXE9NZDy0t8W3TcNekrF+2d/1sFnWG4HnV8TZY30iTOdtVWJG8abWvB1GlOgJuQZdcF2Luqm/hccMw==";
		
		    public static string GenerateToken(string username, int expireMinutes = 20)
		    {
		        var symmetricKey = Convert.FromBase64String(SecretBase64);
		        var tokenHandler = new JwtSecurityTokenHandler();
		
		        var now = DateTime.UtcNow;
		        var tokenDescriptor = new SecurityTokenDescriptor
		        {
		            Subject = new ClaimsIdentity(new[]
		                    {
		                        new Claim(ClaimTypes.Name, username)
		                    }),
		
		            Expires = now.AddMinutes(Convert.ToInt32(expireMinutes)),
		
		            SigningCredentials = new SigningCredentials(new SymmetricSecurityKey(symmetricKey), SecurityAlgorithms.HmacSha256Signature)
		        };
		
		        var stoken = tokenHandler.CreateToken(tokenDescriptor);
		        var token = tokenHandler.WriteToken(stoken);
		
		        return token;
		    }
		}

1. Now, we are ready to validate the JWT when the request comes. We have multiple ways to do so. e.g.create a filter implements `IAuthenticationFilter`, create middleware in .net core, create interceptor of `DelegatingHandler`

	Here, we create a `AuthenticationFilter` to implement `IAuthenticationFilter`

	**How to create an authentication filter?**

	The IAuthenticationFilter interface has two methods:

	- `AuthenticateAsync` authenticates the request by validating credentials in the request, if present.
	- `ChallengeAsync` adds an authentication challenge to the HTTP response, if needed. The challenge will be returned to user to tell user which authentication scheme will be accepted if the filter failed to authenticate user's credentials. The challenge will add a `Www-Authentication` header in the response.

	We can create multiple authentication filters and they handle authentication in a pipeline. 

	1. Before invoking an action, Web API creates a list of the authentication filters for that action. This includes filters with action scope, controller scope, and global scope.
	1. Web API calls AuthenticateAsync on every filter in the list. Each filter can validate credentials in the request. If any filter successfully validates credentials, the filter creates an IPrincipal and attaches it to the request. A filter can also trigger an error at this point. If so, the rest of the pipeline does not run.
	1. Assuming there is no error, the request flows through the rest of the pipeline.
	1. Finally, Web API calls every authentication filter's ChallengeAsync method. Filters use this method to add a challenge to the response, if needed. Typically (but not always) that would happen in response to a 401 error.

	We can add them to action/class level. e.g.

		[IdentityBasicAuthentication] // Enable Basic authentication for this controller.
		[Authorize] // Require authenticated requests.
		public class HomeController : ApiController
		{
		    public IHttpActionResult Get() { . . . }
		    public IHttpActionResult Post() { . . . }
		}

	Or add them to global level

		public static class WebApiConfig
		{
		    public static void Register(HttpConfiguration config)
		    {
		        config.Filters.Add(new IdentityBasicAuthenticationAttribute());
		
		        // Other configuration code not shown...
		    }
		}

	More details at [Authentication Filters in ASP.NET Web API 2](https://docs.microsoft.com/en-us/aspnet/web-api/overview/security/authentication-filters)

	**create the authentication filter**

		public class JwtAuthenticationAttribute : Attribute, IAuthenticationFilter
		{
			public string Realm { get; set; }
			public bool AllowMultiple => false;
		
			public async Task AuthenticateAsync(HttpAuthenticationContext context, CancellationToken cancellationToken)
			{
				var request = context.Request;
				var authorization = request.Headers.Authorization;
		
				if (authorization == null || authorization.Scheme != "Bearer")
				{
					context.ErrorResult = new AuthenticationFailureResult("Missing Jwt Token", request);
					return;
				}
				if (string.IsNullOrEmpty(authorization.Parameter))
				{
					context.ErrorResult = new AuthenticationFailureResult("Missing Jwt Token", request);
					return;
				}
		
				var token = authorization.Parameter;
				var principal = await AuthenticateJwtToken(token);
		
				if (principal == null)
					context.ErrorResult = new AuthenticationFailureResult("Invalid token", request);
		
				else
					context.Principal = principal;
			}
		
			private static bool ValidateToken(string token, out string username)
			{
				username = null;
		
				var simplePrinciple = JwtManager.GetPrincipal(token);
				var identity = simplePrinciple.Identity as ClaimsIdentity;
		
				if (identity == null)
					return false;
		
				if (!identity.IsAuthenticated)
					return false;
		
				var usernameClaim = identity.FindFirst(ClaimTypes.Name);
				username = usernameClaim?.Value;
		
				if (string.IsNullOrEmpty(username))
					return false;
		
				// More validate to check whether username exists in system
		
				return true;
			}
		
			protected Task<IPrincipal> AuthenticateJwtToken(string token)
			{
				string username;
		
				if (ValidateToken(token, out username))
				{
					// based on username to get more information from database in order to build local identity
					var claims = new List<Claim>
					{
						new Claim(ClaimTypes.Name, username)
						// Add more claims if needed: Roles, ...
					};
		
					var identity = new ClaimsIdentity(claims, "Jwt");
					IPrincipal user = new ClaimsPrincipal(identity);
		
					return Task.FromResult(user);
				}
		
				return Task.FromResult<IPrincipal>(null);
			}
		
			public Task ChallengeAsync(HttpAuthenticationChallengeContext context, CancellationToken cancellationToken)
			{
				Challenge(context);
				return Task.FromResult(0);
			}
		
			private void Challenge(HttpAuthenticationChallengeContext context)
			{
				string parameter = null;
		
				if (!string.IsNullOrEmpty(Realm))
					parameter = "realm=\"" + Realm + "\"";
		
				context.ChallengeWith("Bearer", parameter);
			}
		}
	
# References #
[https://jwt.io/introduction/](https://jwt.io/introduction/)

[http://www.c-sharpcorner.com/article/handle-refresh-token-using-asp-net-core-2-0-and-json-web-token/](http://www.c-sharpcorner.com/article/handle-refresh-token-using-asp-net-core-2-0-and-json-web-token/)

[https://www.codeproject.com/Articles/1217608/ASP-NET-Core-Identity-Supporting-JWT-Token-for-Use](https://www.codeproject.com/Articles/1217608/ASP-NET-Core-Identity-Supporting-JWT-Token-for-Use)

refresh token
[https://auth0.com/docs/tokens/refresh-token/current](https://auth0.com/docs/tokens/refresh-token/current)