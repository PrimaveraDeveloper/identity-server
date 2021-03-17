# Reference - End Session Endpoint

The End Session Endpoint allows client applications to trigger single-sign-out.

The client application will redirect the user's browser to this endpoint. Then all applications where the user signed-in during the user's session are allowed to participate in the sign-out.

## Route

`GET /connect/endsession`

## Parameters

This endpoint accepts a combination of the following parameters.

| Parameter | Description |
| - | - |
| `id_token_hint` | A hint of the user's identity token (optional) |
| `post_logout_redirect_uri` | The redirect URI that will be used to redirect the user back to the client (only if a valid `id_token_hint` is sent) (optional) |
| `state` | The state that will be echoed back to the client as query string parameter (only if a valid `post_logout_redirect_uri` is sent) (optional) |

> By default, when an end-user is redirected to this endpoint, they will be prompted to confirm that they really want to sign-out. This prompt can be bypassed by the client sending the user's original identity token in the `id_token_hint` parameter.

## Example Request

Here is an example request:

```
GET /connect/endsession?
    id_token_hint=<id_token>
    post_logout_redirect_uri=<redirect_uri>
```

## Remarks

You can invoke the endpoint directly as any other HTTP call.