# Reference - Authorize Endpoint

The Authorize Endpoint can be used by client applications to request tokens or authorization codes via the browser. This typically involves authentication of the end-user and (optionally) the end-user consent.

## Route

`GET /connect/authorize`

## Parameters

This endpoint accepts a combination of the following parameters, depending on the grant type.

| Parameter | Description |
| - | - |
| `client_id` | Client identifier (required) |
| `request` | The parameters (a subset or all) provided as JWT instead of query string parameters (optional) |
| `request_uri` | The URI of the JWT containing the request parameters (optional) |
| `scope` | Scopes requested (required) |
| `redirect_uri` | The redirect URI (matching exactly one of the allowed redirect URIs for the client) |
| `response_type` | The response type (required) (see bellow) |
| `response_mode` | The response mode (optional) (if `form_post` the token response is sent as a form post instead of a fragment encoded redirect URI) |
| `nonce` | The state that will be echoed in the identity token to preven CSRF/replay (required) |
| `prompt` | A value indicating whether the login UI should be shown (if `none`, no UI will be shown and an error will be returned if the user has to sign-in or consent; if `prompt`, the UI will always be shown even if the user is already signed-in) |
| `code_challenge` | The code challenge for PKCE (required for authorization code with PKCE) |
| `code_challenge_method` | The method used for the code challenge (`plain` or `S256`) (required for authorization code with PKCE) |
| `login_hint` | A hint to suggest the user name field in the login UI (optional) |
| `ui_locales` | A hint for the display language to use in the login UI (optional) |
| `max_age` | The maximum age of the user session (in seconds) (if the session is older, the login UI will be shown) (optional) |
| `acr_values` | Allows passing additional information for the request (optional) |

## Response Types

Identity Server supports the following response types:

| Response type | Description |
| - | - |
| id_token | Requests an identity token |
| token | Requests an access token |
| id_token token | Requests and identity token and an access token |
| code | Requests an authorization code |
| code id_token | Request an authorization code and an identity token |
| code id_token token | Requests an authorization code, an identity token and an access token |

> The response type is related with the grant type used.

## Example Request

Here is an example request:

```
GET /connect/authorize?
    client_id=client1&
    scope=openid email api1&
    response_type=id_token token&
    redirect_uri=https://myapp/callback&
    state=abc&
    nonce=xyz
```

## Remarks

You can invoke the endpoint directly as any other HTTP call.