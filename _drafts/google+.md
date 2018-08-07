Google uses the OAuth 2.0 protocol to allow authorized applications to access user data. 

# Introduction

# Create API key
1. Go to the [Google API Console](https://console.developers.google.com/apis/library)
2. From the project drop-down, select a project, or create a new one.
4. In the list of Google APIs, search for the Google+ API service.

	![](/images/posts/20180731-google-1.png)

5. Enable the Google+ API service
	
	![](/images/posts/20180731-google-2.png)

8. Click "create credentials"

	![](/images/posts/20180731-google-3.png)

9. In the Credentials tab, select the New credentials drop-down list, and choose API key.
From the Create a new key pop-up, choose the appropriate kind of key for your project: Server key, Browser key, Android key, or iOS key.

	![](/images/posts/20180731-google-4.png)

1. Enter a key Name, fill in any other fields as instructed, then select Create.

	![](/images/posts/20180731-google-5.png)

# Steps
1. install nuget package `Google.Apis.plus`
	
	![](/images/posts/20180731-google-6.png)
# References

[https://developers.google.com/+/web/api/rest/oauth.html](https://developers.google.com/+/web/api/rest/oauth.html)

[https://developers.google.com/identity/sign-in/web/sign-in](https://developers.google.com/identity/sign-in/web/sign-in)