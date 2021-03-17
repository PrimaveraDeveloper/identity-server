# Reference - Revocation Endpoint

The Revocation Endpoint allows revoking access tokens (reference tokens only) and refresh tokens.

## Route

`POST /connect/revocation`

## Parameters

This endpoint accepts a combination of the following parameters.

| Parameter | Description |
| - | - |
| `token` | The token to revoke (required) |
| `token_type_hint` | The token type (`access_token` or `refresh_token`) (optional) |

## Example Request

Here is an example request:

```
POST /connect/revocation
CONTENT-TYPE application/x-www-form-urlencoded
Authorization: Basic <basic_auth>

    token=<refresh_token>&
    token_type_hint=refresh_token
```

## Remarks

You can invoke the endpoint directly as any other HTTP call.