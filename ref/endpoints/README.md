# Reference - Endpoints

Identity Server, as it supports OAuth and OIDC, provides a set of **core endpoints** to allow client applications and resource servers to integrate.

These are the endpoints available:

| Endpoint | Purpose |
| - | - |
| Discovery | Used to retrieve meta data about the authority server |
| [Token](token-endpoint.md) | Use to request tokens |
| Authorize | Used to request tokens or authorization codes via the browser, involving user authentication |
| User Info | Used to retrieve information about an user |
| Device Authorization | Used to request device and user codes |
| Introspection | Used to validate reference tokens |
| Revocation | Used to revoke access tokens (reference tokens only) and refresh tokens |
| End Session | Used to trigger single sign-out |