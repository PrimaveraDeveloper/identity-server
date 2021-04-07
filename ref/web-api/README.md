# Reference - Web API

Identity Server provides an extensive Web API to manage resources (API scopes, API resources, and Clients) and users (to some extent).

## Documentation

The complete Web API documentation is available [here](https://dv-ids.lithium.primaverabss.com/.doc/webapi).

## Scopes

This Web API is protected by the following scopes:

| Scope | Description |
| - | - |
| `identityserver4` | Allows access to all the endpoints that allow reading data |
| `identityserver4-write` | Allows access to all the endpoints that allow writing data |

> User invitations can be created with the default `identityserver4` scope.

## Remarks

For privacy reasons, the Web API does not include operations on users, except the following:

- Retrieve a list of users.
- Retrieve a specific user (by id or email).
- Retrieve, add, and remove user claims.