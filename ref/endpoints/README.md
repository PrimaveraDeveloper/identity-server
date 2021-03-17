# Reference - Endpoints

Identity Server, as it supports OAuth and OIDC, provides a set of **core endpoints** to allow client applications and resource servers to integrate.

These are the endpoints available:

| Endpoint | Purpose |
| - | - |
| [Discovery](discovery-endpoint.md) | Used to retrieve meta data about the authority server |
| [Token](token-endpoint.md) | Used to request tokens |
| [Authorize](authorize-endpoint.md) | Used to request tokens or authorization codes via the browser, involving user authentication |
| [User Info](userinfo-endpoint.md) | Used to retrieve information about an user |
| [Device Authorization](deviceauthorization-endpoint.md) | Used to request device and user codes |
| [Introspection](introspection-endpoint.md) | Used to validate reference tokens |
| [Revocation](revocation-endpoint.md) | Used to revoke access tokens (reference tokens only) and refresh tokens |
| [End Session](endsession-endpoint.md) | Used to trigger single sign-out |