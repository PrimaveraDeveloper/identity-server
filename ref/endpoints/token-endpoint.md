# Reference - Token Endpoint

The Token Endpoint can be used by client applications to retrieve access tokens programmatically, using a specific grant type (from the standard grants defined by the specs or extension grant types supported by IDS).

## Route

`POST /connect/token`

## Grant Types

Identity Server supports the following grant types:

- Authorization Code (`authorization_code`)
- Client Credentials (`client_credentials`)
- Delegation (`delegation`)
- Device Code (`urn:ietf:params:oauth:grant-type:device_code`)
- Password (`password`)
- Refresh Token (`refresh_token`)

> Delegation is an extension grant.

> Password and Implicit (not listed) are legacy (as per OAuth 2.0).

## Parameters

This endpoint accepts a combination of the following parameters, depending on the grant type.

| Parameter | Description |
| - | - |
| `client_id` | Client identifier (required) |
| `client_secret` | Client secret (optional) |
| `grant_type` | Grant type (required) |
| `scope` | Scopes requested (optional) |
| `redirect_uri` | The redirect URI (required for `authorization_code`) |
| `code` | The authorization code (required for `authorization_code`) |
| `code_verifier` | PKCE proof key (required for `authorization_code` with PKCE) |
| `username` | The user name (required for `password`) |
| `password` | Client identifier (required for `password`) |
| `acr_values` | Additional authentication information (optional for `password`) |
| `refresh_token` | Refresh token (required for `refresh_token`) |
| `device_code` | Device code (required for `urn:ietf:params:oauth:grant-type:device_code`) |

## Example Request

Here is an example request to retrieve a token with the client credentails grant type:

```
POST /connect/token
CONTENT-TYPE application/x-www-form-urlencoded

    client_id=client1&
    client_secret=secret&
    grant_type=client_credentials&
    scope=scope1
```

## Remarks

You can invoke the endpoint directly as any other HTTP call.

However, it is easier to use `Primavera.Hydrogen.IdentityModel.Client.TokenClient` as it provides methods tailored for each grant type and it deals with encoding the parameters and parsing the response correctly.