# How to obtain an access token from Identity Server

This guide describes the steps required to obtain an access token from Identity Server.

## Access Tokens

An access token contains the authorization information provided by the authority server required to invoke an endpoint on a resource server.

There are several kinds of access tokens and the way you obtain them varies on the grant type used by the client application.

## Client Credentials Access Token

The simplest and most common form is retrieving a client credentials access token. These can be explicitly retrieved from the [Token Endpoint](../ref/endpoints/token-endpoint.md) with a simple HTTP call.

Here's an example:

```csharp
using HttpRequestMessage httpRequest = new HttpRequestMessage(
    HttpMethod.Post, 
    $"{authorityServer}/connect/token");

httpRequest.Headers.Accept.Add(
    new MediaTypeWithQualityHeaderValue(
        "application/json"));

httpRequest.Content = new FormUrlEncodedContent(
    new Dictionary<string, string>()
    {
        ["client_id"] = "identityserver-sample-clientcredentials",
        ["client_secret"] = "secret",
        ["grant_type"] = "client_credentials",
        ["scope"] = "identityserver4",
    });

using HttpClient httpClient = new HttpClient();

using HttpResponseMessage httpResponse = 
    await httpClient.SendAsync(
        httpRequest)
        .ConfigureAwait(false);

if (!httpResponse.IsSuccessStatusCode)
{
    throw new InvalidOperationException(
        $"Token request failed with error: {httpResponse.ReasonPhrase}.");
}

string json = await httpResponse.Content
    .ReadAsStringAsync().ConfigureAwait(false);

JsonDocument jsonDocument = JsonDocument.Parse(
    json);

if (!jsonDocument.RootElement.TryGetProperty("access_token", out JsonElement accessToken))
{
    throw new InvalidOperationException(
        $"Token request returned invalid content.");
}

return accessToken.GetString();
```

- The call is made as a `POST` to the token endpoint (`connect/token`).
- All parameters are sent in the form (URL encoded).
- The `client_id`, `client_secret`, and `grant_type` parameters are required.
- The `scope` parameter is optional and it may reference multiple scopes (e.g.: `scope1 scope2 scope3`).
- A successful response returns `OK` (200) and a JSON body that describes the response and contains the actual access token in one of the properties (`access_token`).

## Using `TokenClient`

`Primavera.Hydrogen.IdentityModel.Client.TokenClient` allows performing these requests with less ceremony.

```csharp
using (ITokenClient client = new TokenClient())
{
    TokenResponse response = await client
        .GetClientCredentialsTokenAsync(
            new ClientCredentialsTokenRequest(
                "https://server.io")
            {
                ClientId = "myclient",
                ClientSecret = "mysecret",
                Scope = "myscope"
            }).ConfigureAwait(false);

    if (!response.IsError)
     {
         string accessToken = response.AccessToken;
     }
 }
```

> `TokenClient` provides two interesting features: caching of the access tokens, and it may use the discovery endpoint to find the token endpoint location (as in the previous example we are sending the authority server base address in the request)