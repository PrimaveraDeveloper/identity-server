# Reference - User Info Endpoint

The User Info endpoint allows retrieving information (the mapped claims) about a particular user.

The client application is required to send a access token representing the user whose information is to be retrieved.

## Route

`GET /connect/userinfo`

## Example Request

Here is an example request:

```
GET /connect/userinfo
Authorization: Bearer <access_token>
```

```
HTTP/1.1 200 OK
Content-Type: application/json
{
    "sub": "248289761001",
    "name": "John Ded",
    "given_name": "John",
    "family_name": "Doe",
    "role": [
        "user",
        "admin"
    ]
}
```

## Remarks

You can invoke the endpoint directly as any other HTTP call.