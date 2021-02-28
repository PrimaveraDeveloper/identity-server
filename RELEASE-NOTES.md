# Identity Server Release Notes

## Version 5.0.0.XXX (Not released yet)

**Web API**

- The Web API no longer includes operations to modify users (except adding or removing roles and claims).
- Webhooks (see the respective documentation).
- The `identityserver4-privileged` scope was removed.
- New scope `identityserver4-wh`, required to subscribe webhooks.

**Accounts**

- Users with external logins only can no longer set a password in their account from the profile.

**Front-office**

- Sign-in and sign-up with email are no longer available in the first sign-in/sign-up view. The user is required to select that sign-in/sign-up option like it does for external login providers. This is to promote the usage of external login providers instead of email/password for login.

**Back-office**

- New operation to import resource (API scopes, API resources and clients) using Authorization spec files generated for Lithium microservices.

**Invitations**

- User invitations now allow registration using external login providers.