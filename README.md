#OAuth Sample

OAuth Sample demonstrating Login with OAuth 2.0 with both Google and Github as identity providers.


`spring-boot-starter-oauth2-client` handles the entire OAuth2.0 Authorization flow in this application.
I have registered the app with all three OAuth Providers, providing the URL of the application (http://localhost:8080) and the re-direct URL (http://localhost:8080/login/oauth2/code/gitub, for example).
```
The default redirect URI template is {baseUrl}/login/oauth2/code/{registrationId}. The registrationId is a unique identifier for the ClientRegistration, that can be found in application.properties
```



## OAuth 2.0 Roles

### Client -  3rd Party application trying to get access to user account
### Resource Server - Service holding user information
### Authorization Server - Server that presents the interface where the user approves / denies an authorization request
### Resource Owner - The user


## Authorization Process

### User is presented with a UI on your app with a login Link to the Authorization Server
```
https://authorization-server.com/auth?response_type=code&client_id=CLIENT_ID&redirect_uri=REDIRECT_URI&scope=photos&state=1234zyx`
```

1. response_type=code - Indicates that your server expects to receive an authorization code
2. client_id - The client ID you received when you first created the application
3. redirect_uri - Indicates the URI to return the user to after authorization is complete
4. scope - One or more scope values indicating which parts of the user's account you wish to access
5. state - A random string generated by your application, which you'll verify later

### The Authorization server logs the user in (if needed) and generates an authorization code, and re-directs to user's application, returning the same state value that was passed in.

```
https://example-app.com/cb?code=AUTH_CODE_HERE&state=1234zyx
```


### The user application then compares this state value to ensure it matches the one it has created. You can typically store the state value in a cookie or session, and compare it when the user comes back. This helps ensure your redirection endpoint isn't able to be tricked into attempting to exchange arbitrary authorization codes.

### The client application server exchanges the authorization code for an access token by making a POST request to the authorization server's token endpoint.

```
POST 
https://api.authorization-server.com/token?grant_type=authorization_code&code=AUTH_CODE_HERE&redirect_uri=REDIRECT_URI&client_id=CLIENT_ID&client_secret=CLIENT_SECRET
```
1. grant_type=authorization_code - The grant type for this flow is authorization_code
2. code=AUTH_CODE_HERE - This is the code you received in the query string
3. redirect_uri=REDIRECT_URI - Must be identical to the redirect URI provided in the original link
4. client_id=CLIENT_ID - The client ID you received when you first created the application
5. client_secret=CLIENT_SECRET - Since this request is made from server-side code, the secret is included


### Authorization server replies with an access token and expiration time

```
{
  "access_token":"RsT5OjbzRn430zqMLgV3Ia",
  "expires_in":3600
}
```
OR
```
{
  "error":"invalid_request"
}
```

## To Summarize

1. The client application submits an authorization request to the Auth Provider, which validates that the client is a legitimate client of its service.
2. The server redirects the client to the content provider to request access to its resources.
3. The content provider validates the user's identity, and often requests their permission to access the resources.
4. The content provider redirects the client back to the server, notifying it of success or failure. This request includes an authorization code on success.
5. The server makes an out-of-band request to the content provider and exchanges the authorization code for an access token.
6. The server can now make requests to the content provider on behalf of the user by passing the access token.

![Alt text](./images/Oauth2.png?raw=true "Oauth Flow")

![Alt text](./images/Oauth.jpg?raw=true "Oauth Flow")

## Further Reading

1. https://spin.atomicobject.com/2016/05/30/openid-oauth-saml/
2. https://developer.okta.com/books/api-security/authn/federated/
3. https://medium.com/@robert.broeckelmann/authentication-vs-federation-vs-sso-9586b06b1380
