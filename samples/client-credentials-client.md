# Sample - Client Credentials Client

This sample shows how to create a client application that uses the client credentials flow to obtain an access token to invoke an API protected by OAuth 2.0.

The projects required for this sample are:

- `ClientCredentialsClient.csproj`

## Concept

The client credentials grant type allows client applications to invoke APIs protected by OAuth 2.0 scopes using an access token previously obtained from an authority server.

This grant type is the the most addequate for server-to-server (machine-to-machine) scenarios, where no user is involved.

When a client application wants to perform the API call, it first contacts the authority server to obtain an access token with the scope(s) required by the API endpoint it wants to call (thus it requires prior knowledge of the API's OAuth requirements).

If authorized, it will receive the access token and it should pass it along to the resource server in the `Authorization` header.

The resource server will respond successfully only if that access token is valid.

> These access tokens have a lifetime associated, allowing the client to make multiple API calls with the same token.

## Resources Configuration

This sample requires the following resources to be configured in the back-office:

> You can use the file `ClientCredentialsClient.authzspec.json` to import these resources in the back-office.

### Clients

| Configuration | Value |
| - | - |
| Client Id. | `identityserver-sample-clientcredentials` |
| Require client secret | `true` |
| Client secret | `secret` |
| Require consent | `false` |
| Allowed Grant types | `client_credentials` |
| Allowed Scopes | `identityserver4` |

## Behavior

When the console application is run it will:

1. Obtain an access token from the authority server by invoking the Token Endpoint directly.

2. Obtain an access token from the authority server using Hydrogen's `TokenClient`.

> This illustrates an alternative (more convenient) way of retrieving access tokens.

3. Invoke IDS Web API to get a page of all the clients available.

> The API being invoked by this client is IDS's own API, only for the purpose of the simplicity of the sample. It could be any API protected by Identity Server.

![Console Output](_assets/client-credentials-client-1.png "Console Output")

## Worth Noting

The only aspects worth noting is how the access token is obtained from Identity Server and that access token is sent in the requests to the API.

### Obtaining the access token with `HttpClient`

This is implemented in `Application.RetrieveAcessTokenWithHttpClientAsync()`.

### Obtaining the access token with `TokenClient`

This is implemented in `Application.RetrieveAcessTokenWithTokenClientAsync()`.

Notice that the code is simpler as you do not have to deal with how the request should be encoded, deserialized, etc.

### Setting the access token in the API requests

See `Application.RetrieveClientsAsync()`. The access token is sent in the `Authorization` header:

```csharp
httpRequest.Headers.TryAddWithoutValidation("Authorization", $"Bearer {accessToken}");
```

> `Bearer` is the default authentication scheme used for OAuth and JWT bearer tokens.