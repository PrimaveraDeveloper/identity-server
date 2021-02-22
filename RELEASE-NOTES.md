# Identity Server Release Notes

## Version 5.0.0.XXX (Not released yet)

**Web API**

- The Web API no longer includes operations to modify users (except adding or removing roles and claims).
- Webhooks (see the respective documentation).
- The `identityserver4-privileged` scope was removed.
- New scope `identityserver4-wh`, required to subscribe webhooks.

**Accounts**

- Users with external logins only can no longer set a password in their account.

**Back-office**

- New operation to import resource (API scopes, API resources and clients) using Authorization spec files generated for Lithium microservices.

**Invitations**

- User invitations now allow registration using external login providers.