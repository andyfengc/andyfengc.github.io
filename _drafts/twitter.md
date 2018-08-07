Twitter uses OAuth 1.0 to provide authorized access to its API.

# Introduction
there are two types of authentication 

## Application-only authentication ##
It is a form of authentication where an application makes API requests on its own behalf, without the user context (user doesn't have to sign). Application-only authentication is for developers that just need to access public information.

Example: If a developer is trying to view tweets or lists that are publically available, then they just need to use application-only authentication. 

Our app will be able to, i.e.:

- Pull user timelines;
- Access friends and followers of any account;
- Access lists resources;
- Search in Tweets;
- Retrieve any user information;

And it will not be able to:

- Post Tweets or other resources;
- Connect to Streaming endpoints;
- Search for users;
- Use any geo endpoint;
- Access Direct Messages or account credentials;

Please note Application-only authentication doesn't require user sign a request, but can only work within the application. There is no concept of a "current user". Therefore, endpoints such as `POST statuses/update` will not function with application-only auth.

Application-only authentication requires: 
- consumer key and secret from twitter developer account

## User authentication ##
It allows an app to act on behalf of the user to manager facebook requests. 

For example: if a developer wanted to build a feature that would allow a user to post Tweets through their platform using the statuses/update endpoint, the developer would have to use user authentication to get permission from the user to post a tweet on their behalf. 

Please note that each request need to be `signed` by user in order to identify an application’s identity, also, the identity should be able to carry granted permissions of the end-user regarding the desired API call. This `sign` is represented by the user’s access token.

User authentication requires: 
- consumer key and secret from our Twitter developer account
- access token and access token secret from the user we are on the behalf of

# workflow of using Twitter API via application-only authentication
Here is the basic workflow: 

![](/images/posts/20180721-twitter-3.png)

## Steps ##

1. apply twitter developer account (the same as twitter account)

	we just need standard API
	![](/images/posts/20180721-twitter-1.png)

1. create application at [https://apps.twitter.com](https://apps.twitter.com)
	![](/images/posts/20180721-twitter-2.png)

1. Encode consumer key and secret
	
	Concatenate the encoded consumer key, a colon character ”:”, and the encoded consumer secret into a single string. 

	![](/images/posts/20180721-twitter-4.png)
	
	then, base64 encode the string

1. Obtain a bearer token

	POST oauth2/token:

	- the request must include an Authorization header with the value of Basic `<base64 encoded value from consumer key + secret>`
	
	![](/images/posts/20180721-twitter-5.png)
	
	- the request must include a Content-Type header with the value of `application/x-www-form-urlencoded;charset=UTF-8`
	- The body of the request must be `grant_type=client_credentials`.

	![](/images/posts/20180721-twitter-6.png)

	then, we will get the bearer token
	
1. Authenticate API requests with the bearer token

	- This bearer token may be used to issue requests to API endpoints which support application-only auth. 
	- here, we construct a normal HTTPS request and include an Authorization header with the value of `Bearer <base64 bearer token value from step 2>`
	
	![](/images/posts/20180721-twitter-7.png)

	more examples:

		POST oauth2 / token 
		POST oauth2 / invalidate_token

# Workflow of using Twitter API via user authentication
It follows Oauth 1.0 workflow:

![](/images/posts/20180721-twitter-8.png)

In this workflow, each request finally will include the user access token to access user's account. Therefore, each access token should identify:

- Which application is making the request
- Which user the request is posting on behalf of
- Whether the user has granted the application authorization to post on the user’s behalf
- Whether the request has been tampered by a third party while in transit

## Steps ##
1. get request_token, `POST https://api.twitter.com/oauth/request_token`

	![](/images/posts/20180721-twitter-9.png)
	
	![](/images/posts/20180721-twitter-10.png)

	[https://developer.twitter.com/en/docs/basics/authentication/api-reference/request_token](https://developer.twitter.com/en/docs/basics/authentication/api-reference/request_token)

	here we got response like 

		oauth_token=_HnDAgAAAAAA7wSzAAABCCDDEEF&oauth_token_secret=tWSaEvHULL3bWd5hzOlocYapIVVw6GTm&oauth_callback_confirmed=true

	In the response, `oauth_token` is actually the `request_token` in oauth 1.0. `oauth_token_secret` will be saved to get access token later on

1. Open browser > navigate to authenticate or authorize url > go to user's account to get permission

	e.g. navigate to `https://api.twitter.com/oauth/authenticate?oauth_token=oauth-token-returned-by-request-token&force_login=true`

	Here, we neeed to login

	![](/images/posts/20180721-twitter-11.png)

	if user grant successfully, twitter will rediret back to callback url in twitter developer account

	![](/images/posts/20180721-twitter-12.png)

	![](/images/posts/20180721-twitter-13.png)

	here, we was redirected back and the response url like
`http://localhost/?oauth_token=mtrbwwAAAAAA7wSzAAABZLXP9wA&oauth_verifier=RPZ2z5rTNgTZTS0FqNz8NGyq2d2pLYN7`

	[https://developer.twitter.com/en/docs/basics/authentication/api-reference/authenticate](https://developer.twitter.com/en/docs/basics/authentication/api-reference/authenticate)
	[https://developer.twitter.com/en/docs/basics/authentication/api-reference/authorize](https://developer.twitter.com/en/docs/basics/authentication/api-reference/authorize)

	In the response, `oauth_token` and `oauth_verifier` will be used to get access token later on

1. Get user access token via `POST https://api.twitter.com/oauth/access_token`

	Here, we will include previous credentials in the header:

		```
			oauth_token=HcfJIAAAAAAA7wSzAAABZLXiVuA
			oauth_token_secret=tWSaEvHULL3bWd5hzOlocYapIVVw6GTm
			oauth_verifier=9YowSseh3HCHNOBweHV2I9VtElpJTIr1
		```

	![](/images/posts/20180721-twitter-14.png)

	get response like 

		oauth_token=1020154357860487169-rGPwYrLNXgzMju0frr69qE7mhyET67&oauth_token_secret=yNdgCjBPKhBcQKVxwXy79vWFNAIWbazAvZ6He7fNneaQL&user_id=1020154357860487169&screen_name=andyinbox3

	Here, oauth_token, oauth_token_secret are actually the user access token

1. use APIs with the access token

	e.g. post a tweet `POST statuses/update`

	![](/images/posts/20180721-twitter-15.png)

	![](/images/posts/20180721-twitter-16.png)

# 3rd library
[Tweetinvi](https://github.com/linvi/tweetinvi)

# FAQ #
1. API reports `Timestamp out of bounds.`

	pls update `oauth_timestamp` parameter in the header. It should be the number of whole seconds since 1 Jan 1970 00:00:00 UTC.

	e.g. `oauth_timestamp=1502553412`


# References
[twitter oauth overview](https://developer.twitter.com/en/docs/basics/authentication/overview/oauth)

[application-only authentication](https://developer.twitter.com/en/docs/basics/authentication/overview/application-only)

[user authentication](https://developer.twitter.com/en/docs/basics/authentication/guides/authorizing-a-request.html)

[OAuth FAQ](https://developer.twitter.com/en/docs/basics/authentication/guides/oauth-faq.html)