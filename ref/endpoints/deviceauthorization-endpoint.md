# Reference - Device Authorization Endpoint

The Device Authorization Endpoint can be used to initiate the device flow authorization process by requesting device and user codes.

## Route

`POST /connect/deviceauthorization`

## Parameters

This endpoint accepts a combination of the following parameters.

| Parameter | Description |
| - | - |
| `client_id` | Client identifier (required) |
| `client_secret` | Client secret (optional) |
| `scope` | Scopes requested (optional) |

## Example Request

Here is an example request:

```
POST /connect/deviceauthorization

    client_id=client1&
    client_secret=secret&
    scope=openid api1
```

## Remarks

You can invoke the endpoint directly as any other HTTP call.