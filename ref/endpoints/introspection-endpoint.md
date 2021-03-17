# Reference - Introspection Endpoint

The Introspection Endpoint can be used to validate reference tokens.

> This endpoint requires authentication, configured in the API resource.

## Route

`POST /connect/introspect`

## Example Request

Here is an example request:

```
POST /connect/introspect
Authorization: Basic <basic_auth>

token=<token>
```

The response will return status code OK (200) and a body describing the status of the token.

Valid tokens:

```json
{
    "active": true,
    "sub": "123"
}
```

Unknown or expired tokens:

```json
{
    "active": false,
}
```

## Remarks

You can invoke the endpoint directly as any other HTTP call.