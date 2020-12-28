# Feature - User Invitations

User invitations is a feature of Identity Server that allows any registered client application (with the appropriate scope) to invite a person (the invitation receiver) to create a new account on Identity Server so he can access that client application. These persons are invited on behalf of another existing user (the invitation sender).

The invitation sender can be any user registered on Identity Server and is identified by his user id.

The invitation receiver can be a person that does not have an account on Identity Server or an already existing user. The receiver is identified by a user id and an email address.

> When the user does not exist on Identity Server, the user identifier is a proposal given by the client application of the actual user identifier that should be used when the user creates his account. If the user already exists, the proposed user id is meaningless as Identity Server will determine and use the actual user id (derived from the user email, which is used to find the user account).

## Flow of the invitation

The usual flow for an invitation is the following:

1. The client application invokes the endpoint of the Web API that allows inviting an user. This endpoint requires the following information:
    - The sender identifier, which is a GUID, and should match an existing user.
    - The receiver identifier (see below) which is a GUID.
    - The receiver email address.
    - The receiver name.
    - The receiver culture (e.g. `en-US`), which is used to set the language used to communicate with that user during the invitation.
    - An optional redirect URI that can point to the client application and that will be used to redirect the user after he accepts the invitation.
    - A set of parameters that allow customizing the notifications sent to the receiver.

2. IDS will then determine if the receiver already has an account or not.
    - If that user already exists, he will be notified of the invitation via email with a link to access the client application (determined by the redirect URI or, if not specified, from the client configuration). No invitation will actually be created.

3. When the user does not exist, IDS will create a new invitation, generate a unique token for it, and send an email notification to the receiver, containing a link that he can use to accept it.
    - All invitations have an expiration time set. After that time elapses, they automatically expire and cannot be accepted any longer.
    - A user can be invited multiple times - from the same or from different client applications. When a new invitation is created, any previously existing (and pending) ones will be automatically canceled.

4. The client application receives a response that indicates whether the notification was created or not (including, when applicable, the invitation identifier, which can be used to obtain its state later).

4. The user accepts the invitation by following the link provided in the email notification and by completing his account registration in IDS's front-office.
    - At the end of the registration, IDS will show a link to redirect the user to the client application that created the invitation (see above).

## Receiver Identifier

As mentioned above, the receiver identifier is suggested by the client application. This is important because it allows the client application to store information about the potential user, before he actually exists.

IDS will always create the new user account - when the invitation is accepted - using this identifier to ensure that both "accounts" match (in IDS and in the client application).

## Customizations

The following customizations can be applied to the invitations:

### Redirect after Registration

See above.

### Redirect for Existing Users

See above.

### Branding of the Email Notifications

See [Client Overrides](client-overrides.md).

### Customization of the Email Notifications

Presently, the email notifications sent to the invitation receivers accept the following variables (sent as parameters in the payload of the request to invite an user):

- `IDSSubscriptionAlias` - the alias (identifier) of the subscription in the client application that the new user will have access to.
- `IDSSubscriptionDescription` - the description of the subscription.

> Both parameters are required.

## Limitations

The current implementation has the following limitations:

- The receivers, when creating their accounts, are bound to use email and password and cannot create their accounts using external login providers.
- There is no mechanism to notify client applications when invitations are accepted or expire. Webhooks for these events will be available soon (in version 5.0).