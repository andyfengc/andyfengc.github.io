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

# WebApi Demo
## Demo1 ##
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

1. Now, we are ready to validate the JWT when the request comes. We have multiple ways to do so. e.g.create a filter implements `IAuthenticationFilter`, create middleware in .net core, or create interceptor of `DelegatingHandler`

	**Way1, we create a `AuthenticationFilter` to implement `IAuthenticationFilter`**

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
	
## Demo 2 ##
Way 2, create JwtAuthHandler inherits `DelegatingHandler` class which handles the processing of HTTP response messages to another handler, called the inner handler.

1. create `JwtAuthHandler`
	
		using System;
		using System.Collections;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Security.Claims;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Web;
		using System.Web.Configuration;
		using System.Web.Script.Serialization;
		using JWT;
		
		namespace MyApp.JWT
		
		{
		    public class JwtAuthHandler:DelegatingHandler
		    {
		        protected override Task<HttpResponseMessage> SendAsync(HttpRequestMessage request,
		                    CancellationToken cancellationToken)
		        {
		            HttpResponseMessage errorResponse = null;
		
		            try
		            {
		                IEnumerable<string> authHeaderValues;
		                request.Headers.TryGetValues("Authorization", out authHeaderValues);
		
		
		                if (authHeaderValues == null)
		                    return base.SendAsync(request, cancellationToken); // cross fingers
		
		                var bearerToken = authHeaderValues.ElementAt(0);
		                var token = bearerToken.StartsWith("Bearer ") ? bearerToken.Substring(7) : bearerToken;
		                var secret = WebConfigurationManager.AppSettings.Get("jwtKey");
		                Thread.CurrentPrincipal = ValidateToken(
		                    token,
		                    secret,
		                    true
		                    );
		                
		                if (HttpContext.Current != null)
		                {
		                    HttpContext.Current.User = Thread.CurrentPrincipal;
		                }
		            }
		            catch (SignatureVerificationException ex)
		            {
		                errorResponse = request.CreateErrorResponse(HttpStatusCode.Unauthorized, ex.Message);
		            }
		            catch (Exception ex)
		            {
		                errorResponse = request.CreateErrorResponse(HttpStatusCode.InternalServerError, ex.Message);
		            }
		
		
		            return errorResponse != null
		                ? Task.FromResult(errorResponse)
		                : base.SendAsync(request, cancellationToken);
		        }
		        private static ClaimsPrincipal ValidateToken(string token, string secret, bool checkExpiration)
		        {
		            var jsonSerializer = new JavaScriptSerializer();
		            var payloadJson = JsonWebToken.Decode(token, secret);
		            var payloadData = jsonSerializer.Deserialize<Dictionary<string, object>>(payloadJson);
		
		
		            object exp;
		            if (payloadData != null && (checkExpiration && payloadData.TryGetValue("exp", out exp)))
		            {
		                var validTo = FromUnixTime(long.Parse(exp.ToString()));
		                if (DateTime.Compare(validTo, DateTime.UtcNow) <= 0)
		                {
		                    throw new Exception(
		                        string.Format("Token is expired. Expiration: '{0}'. Current: '{1}'", validTo, DateTime.UtcNow));
		                }
		            }
		
		            var subject = new ClaimsIdentity("Federation", ClaimTypes.Name, ClaimTypes.Role);
		
		            var claims = new List<Claim>();
		
		            if (payloadData != null)
		                foreach (var pair in payloadData)
		                {
		                    var claimType = pair.Key;
		
		                    var source = pair.Value as ArrayList;
		
		                    if (source != null)
		                    {
		                        claims.AddRange(from object item in source
		                                        select new Claim(claimType, item.ToString(), ClaimValueTypes.String));
		
		                        continue;
		                    }
		
		                    switch (pair.Key)
		                    {
		                        case "name":
		                            claims.Add(new Claim(ClaimTypes.Name, pair.Value.ToString(), ClaimValueTypes.String));
		                            break;
		                        case "surname":
		                            claims.Add(new Claim(ClaimTypes.Surname, pair.Value.ToString(), ClaimValueTypes.String));
		                            break;
		                        case "email":
		                            claims.Add(new Claim(ClaimTypes.Email, pair.Value.ToString(), ClaimValueTypes.String));
		                            break;
		                        case "role":
		                            claims.Add(new Claim(ClaimTypes.Role, pair.Value.ToString(), ClaimValueTypes.String));
		                            break;
		                        case "userId":
		                            claims.Add(new Claim(ClaimTypes.UserData, pair.Value.ToString(), ClaimValueTypes.Integer));
		                            break;
		                        default:
		                            claims.Add(new Claim(claimType, pair.Value.ToString(), ClaimValueTypes.String));
		                            break;
		                    }
		                }
		
		            subject.AddClaims(claims);
		            return new ClaimsPrincipal(subject);
		        }
		
		        private static DateTime FromUnixTime(long unixTime)
		        {
		            var epoch = new DateTime(1970, 1, 1, 0, 0, 0, DateTimeKind.Utc);
		            return epoch.AddSeconds(unixTime);
		        }
		    }
		}

1. In the `WebApiConfig` class, you have to register the message handler to use the `JWTAuthHandler` class.

	config.MessageHandlers.Add(new JwtAuthHandler());

1. Create `JWTManager`. After creating the JwtAuthHandler class, add the JWTManager class that just creates the token when ever a user is signing in and returns the token as the response.
	
		using System;
		using System.Collections.Generic;
		using System.Web.Configuration;
		
		namespace MyApp.JWT
		{
		    public class JwtManager
		    {
		        /// <summary>
		        /// Create a Jwt with user information
		        /// </summary>
		        /// <param name="user"></param>
		        /// <param name="dbUser"></param>
		        /// <returns></returns>
		        public static string CreateToken(User user, out object dbUser)
		        {
		
		            var unixEpoch = new DateTime(1970, 1, 1, 0, 0, 0, DateTimeKind.Utc);
		            var expiry = Math.Round((DateTime.UtcNow.AddMinutes(45) - unixEpoch).TotalSeconds);
		            var issuedAt = Math.Round((DateTime.UtcNow - unixEpoch).TotalSeconds);
		            var notBefore = Math.Round((DateTime.UtcNow.AddMonths(6) - unixEpoch).TotalSeconds);
		            
		
		            var payload = new Dictionary<string, object>
		            {
		                {"email", user.Email},
		                {"userId", user.Id},
		                {"role", user.UserRoles},
		                {"sub", user.Id},
		                {"nbf", notBefore},
		                {"iat", issuedAt},
		                {"exp", expiry}
		            };
		
		            var secret = WebConfigurationManager.AppSettings.Get("jwtKey"); //secret key
		            dbUser = new { user.Email, user.Id };
		            var token = JsonWebToken.Encode(payload, secret, JwtHashAlgorithm.HS256);
		            return token;
		        }
		    }
		}
	
		public class User
		{
		   
		 public int Id { get; set; }
		
		 [DataType(DataType.EmailAddress)]
		 [RegularExpression(@"\w+([-+.']\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*", ErrorMessage = "Invalid email address")]
		 public string Email { get; set; }
		
		 public string UserRoles { get; set; }
		}

3. Decode token. The next thing is creating a class that decodes the token from the request header and read the information like the user id and the name and uses the information to get data from the database.

		using System.Collections.Generic;
		using System.Web.Configuration;
		using System.Web.Script.Serialization;
		public class JwtDecoder
	    {
	        /// <summary>
	        /// Get the userid from the token if the token is not expired
	        /// </summary>
	        /// <param name="token"></param>
	        /// <returns></returns>
	        public static int? GetUserIdFromToken(string token)
	        {
	            string key = WebConfigurationManager.AppSettings.Get("jwtKey");
	            var jsonSerializer = new JavaScriptSerializer();
	            var decodedToken = JsonWebToken.Decode(token, key);
	            var data = jsonSerializer.Deserialize<Dictionary<string, object>>(decodedToken);
	            object userId,exp;
	            data.TryGetValue("userId", out userId);
	            data.TryGetValue("exp", out exp);
	            var validTo = FromUnixTime(long.Parse(exp.ToString()));
	            if (DateTime.Compare(validTo, DateTime.UtcNow) <= 0)
	            {
	                return null;
	            }
	            return (int) userId;
	        }
	
	        private static DateTime FromUnixTime(long unixTime)
	        {
	            var epoch = new DateTime(1970, 1, 1, 0, 0, 0, DateTimeKind.Utc);
	            return epoch.AddSeconds(unixTime);
	        }
	    }

1. We can use now authorize a web API controller class and methods. For example, any request made to the user profile has to be authorized

        [HttpGet]
        [Route("account/profile")]
        [Authorize]
        public async Task<IHttpActionResult> Profile()
        {
            var userId = JwtDecoder.GetUserIdFromToken(Request.Headers.Authorization.Scheme); //decode the token

            var user = await _uow.Repository<User>().GetAsync(u => u.Id == userId); //Get the user by id
            
            return Ok(user);
        }

# MVC demo #
Here, we build a custom class the extends the authorization filter class for authentication in Asp.Net MVC. We use `HttpClient`

1. Creating the JWTAuthorize custom attribute class

	using System;
	using System.Collections.Generic;
	using System.Web;
	using System.Web.Mvc;
	using System.Web.Script.Serialization;
	using CommonLibs;

	public class JwtAuthorizeAttribute:AuthorizeAttribute
	{
	
	    private readonly IApiConsumer _apiConsumer;
	
	    public JwtAuthorizeAttribute()
	    {
	        _apiConsumer = new ApiConsumer();
	    }
	
	    protected override bool AuthorizeCore(HttpContextBase httpContext)
	    {
	        if (base.AuthorizeCore(httpContext))
	        {
	            return false;
	        }
	        return IsTokenValid();
	        //return base.AuthorizeCore(httpContext);
	    }
	
	    /// <summary>
	    /// If a token is present on the client side, the the request is authenticated
	    /// </summary>
	    /// <returns></returns>
	    private bool IsAuthenticated()
	    {
	        try
	        {
	            var token =  HttpContext.Current.Request.Cookies.Get("lc_token").Value;
	            return token != null;
	        }
	        catch (NullReferenceException)
	        {
	            return false;
	        }
	    }
	
	    /// <summary>
	    /// Checks if the token is valid
	    /// </summary>
	    /// <returns></returns>
	    private bool IsTokenValid()
	    {
	        try
	        {
	            if (IsAuthenticated()) //If token is found in cookie
	            {
	                //check expiry date
	                var jsonSerializer = new JavaScriptSerializer();
	                var payloadJson = JsonWebToken.Decode(Utils.GetCookie("lc_token"),
	                    "token");
	
	                var payloadData = jsonSerializer.Deserialize<Dictionary<string, object>>(payloadJson);
	                object expiration;
	                payloadData.TryGetValue("exp", out expiration);
	                var validTo = FromUnixTime(long.Parse(expiration.ToString()));
	                if (DateTime.Compare(validTo, DateTime.UtcNow) <= 0)
	                {
	                    return false;
	                }
	                return true;
	            }
	            return false;
	        }
	        catch (NullReferenceException)
	        {
	            return false;
	        }
	    }
	
	    private static DateTime FromUnixTime(long unixTime)
	    {
	        var epoch = new DateTime(1970, 1, 1, 0, 0, 0, DateTimeKind.Utc);
	        return epoch.AddSeconds(unixTime);
	    }  
	}

	Please note the IsAuthenticated() method checks if the token is found in the cookie. The IsTokenValid() method decodes the token and checks if the token has expired. These are just the right steps for validating a token on the client side.

1. Adding the filter on the Action Method

	The “api/v1/profile” is a protected resource. That means you will need to provide the token for authorization. With the custom attribute class, you don’t need to start writing tedious and boring codes, all you need to do is this 

		public class DashboardController : Controller
		{
		    private static IApiConsumer _apiConsumer;
		    private const string BaseUri = "http://localhost:45432/";
		    //private Identity _identity;
		
		    public DashboardController(IApiConsumer apiConsumer)
		    {
		        _apiConsumer = apiConsumer;
		        _apiConsumer.BaseUri = new Uri(BaseUri);
		       // _identity = new Identity();
		    }
		
		    [JwtAuthorize]
		    public async Task<ActionResult> Index()
		    {
		
		        var token = Session["lc_token"].ToString();
		        var usid = int.Parse(Session["lc_usid"].ToString());
		
		        _apiConsumer.ContentType = ApiConsumer.ApplicationJson;
		        _apiConsumer.ResourcePath = "v1/api/user/" + usid;
		        var headers = new List<KeyValuePair<string, string>>
		        {
		            new KeyValuePair<string, string>("Authorization", token)
		        };
		        var response = await _apiConsumer.GetAsync(headers);
		        if (response.IsSuccessStatusCode)
		        {
		            var user = await response.Content.ReadAsAsync<dynamic>();
		            return View(user);
		        }
		        return RedirectToAction("SignOut", "Account");
		
		    }
		}

	Note: The ApiConsumer class is a custom class I created that wraps the HttpClient class. You can use the default class if you want to. The code for the class is listed below:

1. Create apiconsumer
		
		using System;
		using System.Collections.Generic;
		using System.Net.Http;
		using System.Net.Http.Headers;
		using System.Threading.Tasks;
		
		namespace CommonLibs
		{
		    public interface IApiConsumer
		    {
		        /// <summary>
		        /// The base uri of the resource
		        /// </summary>
		        Uri BaseUri { get; set; }
		
		        /// <summary>
		        /// The request uri of the resource
		        /// </summary>
		        string ResourcePath { get; set; }
		
		        /// <summary>
		        /// The resource content type
		        /// </summary>
		        string ContentType { get; set; }
		
		        /// <summary>
		        /// Send a Get request. Pass header information in the parameter if any
		        /// </summary>
		        /// <returns></returns>
		        Task<HttpResponseMessage> GetAsync(IEnumerable<KeyValuePair<string, string>> headers = null);
		
		        /// <summary>
		        /// Send a post request
		        /// </summary>
		        /// <param name="content"></param>
		        /// <returns></returns>
		        Task<HttpResponseMessage> PostAsync(HttpContent content);
		    }
		
		    /// <summary>
		    /// A class for consuming web api from a.net client
		    /// </summary>
		    public class ApiConsumer : IApiConsumer
		    {
		        /// <summary>
		        /// The base uri of the resource
		        /// </summary>
		        public Uri BaseUri { get; set; }
		
		        /// <summary>
		        /// The request uri of the resource
		        /// </summary>
		        public string ResourcePath { get; set; }
		
		        /// <summary>
		        /// The resource content type
		        /// </summary>
		        public string ContentType { get; set; }
		
		        public static string ApplicationXml = "application/xml";
		        public static string TextXml = "text/xml";
		        public static string ApplicationJson = "application/json";
		        public static string TextJson = "text/json";
		
		        /// <summary>
		        /// Configure the client to consume the Api
		        /// </summary>
		        /// <returns></returns>
		        private HttpClient ConfigureClient(IEnumerable<KeyValuePair<string, string>> headers=null)
		        {
		            var client = new HttpClient
		            {
		                BaseAddress = new Uri(BaseUri.AbsoluteUri)
		
		            };
		            if (headers != null)
		                foreach (var items in headers)
		                {
		                    client.DefaultRequestHeaders.Add(items.Key, items.Value);
		
		                }
		            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue(ContentType));
		            return client;
		        }
		
		        /// <summary>
		        /// Send a Get request. Pass header information in the parameter if any
		        /// </summary>
		        /// <returns></returns>
		        public async Task<HttpResponseMessage> GetAsync(IEnumerable<KeyValuePair<string, string>> headers = null)
		        {
		            var client = ConfigureClient(headers);
		            var response = await client.GetAsync(ResourcePath);
		            return response;
		        }
		
		        /// <summary>
		        /// Send a post request
		        /// </summary>
		        /// <param name="content"></param>
		        /// <returns></returns>
		        public async Task<HttpResponseMessage> PostAsync(HttpContent content)
		        {
		            var client = ConfigureClient();
		            var response = await client.PostAsync(ResourcePath, content);
		            return response;
		        }
		
		    }
		}

1. Using OWIN For Authentication

	The website will work properly because we are using IIS. But in a scenario where we want to use OWIN, which is not dependent on the System.Web, we have to modify the JwtAuthHandler class and comment or remove the SendAsyn overridden method and create the owin Startup class. If you don't, you would get the "Authorization has been denied for this request" error message. So, the modification is displayed below:

		public static void OnAuthenticateRequest(IOwinContext context)
		{
		    var requestHeader = context.Request.Headers.Get("Authorization");
			if (requestHeader != null)
			
			        {
			    int userId = Convert.ToInt32(JwtDecoder.GetUserIdFromToken(requestHeader).ToString());
			    var identity = new GenericIdentity(userId.ToString(), "StakersClubOwinAuthentication");
			    //context.Authentication.User = new ClaimsPrincipal(identity);
			
			    var token = requestHeader.StartsWith("Bearer ") ? requestHeader.Substring(7) : requestHeader;
			    var secret = WebConfigurationManager.AppSettings.Get("jwtKey");
			    Thread.CurrentPrincipal = ValidateToken(
			        token,
			        secret,
			        true
			        );
			    context.Authentication.User = (ClaimsPrincipal) Thread.CurrentPrincipal;
			    //if (HttpContext.Current != null)
			    //{
			    //    HttpContext.Current.User = Thread.CurrentPrincipal;
			    //}
			}
		}

	The OWIN Startup class

		public class Startup
		{
		    public void Configuration(IAppBuilder app)
		    {
		        var config = new HttpConfiguration();
		
		        app.Use((context, next) =>
		        {
		            JwtAuthHandler.OnAuthenticateRequest(context); //the new method
		            return next.Invoke();
		        });
		        app.UseStageMarker(PipelineStage.Authenticate);            
		        WebApiConfig.Register(config);//Remove or comment the config.MessageHandlers.Add(new JwtAuthHandler()) section it would not be triggered on execution.
		
		       
		        app.UseWebApi(config);
		    }
		}

Please note that there are some difference between MVC and Web Api. For MVC, besides issue token, we can also use a login form and create a session using cookies. For Web Api, there is no session and we have to use the token technique to login user in.

# References #
[https://jwt.io/introduction/](https://jwt.io/introduction/)

[http://www.c-sharpcorner.com/article/handle-refresh-token-using-asp-net-core-2-0-and-json-web-token/](http://www.c-sharpcorner.com/article/handle-refresh-token-using-asp-net-core-2-0-and-json-web-token/)

[https://www.codeproject.com/Articles/1217608/ASP-NET-Core-Identity-Supporting-JWT-Token-for-Use](https://www.codeproject.com/Articles/1217608/ASP-NET-Core-Identity-Supporting-JWT-Token-for-Use)

refresh token
[https://auth0.com/docs/tokens/refresh-token/current](https://auth0.com/docs/tokens/refresh-token/current)

[5 Easy Steps to Understanding JSON Web Tokens (JWT)](https://medium.com/vandium-software/5-easy-steps-to-understanding-json-web-tokens-jwt-1164c0adfcec)

[https://stackoverflow.com/questions/40281050/jwt-authentication-for-asp-net-web-api](https://stackoverflow.com/questions/40281050/jwt-authentication-for-asp-net-web-api)

[https://stackoverflow.com/questions/38661090/token-based-authentication-in-web-api-without-any-user-interface](https://stackoverflow.com/questions/38661090/token-based-authentication-in-web-api-without-any-user-interface)