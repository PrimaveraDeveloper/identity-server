# Sample - Back-channel Logout

This sample shows how to implement Single Sign-out using back-channel logout notifications.

The projects required for this sample are:

- `BackChannelLogoutClient1.csproj`
- `BackChannelLogoutClient2.csproj`

## Concept

Single sign-out (or Single Logout) refers to the capability of signing-out all the sessions for a particular user when he signs-out explicitly from one of these sessions.

Given the nature of OIDC - as it relies on cookies to mantain the user sessions state - implementing single sign-out (federated or not) is not trivial and is not "automagically" ensured by the protocol itself or the Identity Provider:

As user sessions are stored in cookies (and cookies are per browser and per origin):

- If user A signs-in to application B using browser X, a new session is created.
- While that session is active, if user A uses application B in the same browser, the same session will be used.
- If user A signs-in to application C using browser X again, a new session will also created.
- If user A signs-in to application C using browser Y, yet again a new session will be created.

Implementing single sign-out starts with 2 decisions:

- If the user signs-out from one application, should all sessions for that application (on all browsers) be automatically signed-out?
- If the user signs-out from one application, should all sessions for any other application be automatically signed-out?

Out the box, when a client application uses OIDC with any of the available grant types, neither of the previous requirements will be guaranteed. Each application participating in single sign-out will need to implement some sort of mechanism to achieve these requirements.

**Back-channel logout notifications** is one of the mechanisms available in OIDC that allow client applications to implement single sign-out:

- The client application registers a callback address - where the notifications should be received.
- Everytime any user signs-out, the IDP will send a notification to that address containing a logout token with information about the user and the user session that just signed-out.
- It is up to the client application to implement some sort of custom session management to determine which other sessions need to sign-out and trigger the sign-out for those sessions.

> Back-channel logout is adequate for server-based client applications.

## Resources Configuration

This sample requires the following resources to be configured in the back-office:

> Use `BackChannelLogout.authzspec.json` to import these resources in the back-office.

### Clients

| Configuration | Value |
| - | - |
| Client Id. | `sample-backchannellogout-client1` |
| Allow access tokens via browser | `false` |
| Allowed Grant types | `authorization_code` |
| Allowed Scopes | `openid email profile identityserver4` |
| Back-channel Logout Session Required | `true` |
| Back-channel Logout URI | `https://localhost:20001/logout` |
| Post-logout redirect URIs | `https://localhost:[PORT]/signout-callback-oidc` |
| Redirect URIs | `https://localhost:[PORT]/signin-oidc` |
| Require client secret | `false` |
| Require consent | `false` |
| Require PKCE | `true` |

| Configuration | Value |
| - | - |
| Client Id. | `sample-backchannellogout-client2` |
| Allow access tokens via browser | `false` |
| Allowed Grant types | `authorization_code` |
| Allowed Scopes | `openid email profile identityserver4` |
| Back-channel Logout Session Required | `true` |
| Back-channel Logout URI | `https://localhost:200011/logout` |
| Post-logout redirect URIs | `https://localhost:[PORT]/signout-callback-oidc` |
| Redirect URIs | `https://localhost:[PORT]/signin-oidc` |
| Require client secret | `false` |
| Require consent | `false` |
| Require PKCE | `true` |

## Behavior

This sample includes two clients - `BackChannelLogoutClient1` and `BackChannelLogoutClient2` - which are fundamentally implemented the same way. They serve the purpose of demonstrating single sign-out with multiple applications involved.

After starting both applications, each will show an home page in the browser. These page are not authenticated, thus do not require to the user to enter credentials.

![Home Page (BackChannelLogoutClient1)](_assets/back-channel-logout-1.png "Home Page (BackChannelLogoutClient1)")

![Home Page (BackChannelLogoutClient2)](_assets/back-channel-logout-2.png "Home Page (BackChannelLogoutClient2)")

After the user signs-in on each of the clients, each will present a new page will with information about the claims, headers and tokens, but also with the response obtained from a call to the IDS Web API (notice that both clients request the `identityserver4` scope for that purpose):

![Signed-in Page (BackChannelLogoutClient1)](_assets/back-channel-logout-3.png "Signed-in Page (BackChannelLogoutClient1)")

![Signed-in Page (BackChannelLogoutClient2)](_assets/back-channel-logout-4.png "Signed-in Page (BackChannelLogoutClient2)")

Now for single sign-out...

If the user signs-out from `BackChannelLogoutClient1`, the browser will be redirected to the home page (see above).

Apparently nothing happened on `BackChannelLogoutClient2`, but the truth is that the user session was already marked for sign-out using the back-channel notification received. It's just that the browser has no knowledge of that because it happened in the back-end.

The next time the user tries to access any protected resource on that client, as the session is no longer valid, the authentication process will be triggered again, and the user login UI will be shown so that the user is required to enter his credentials again:

> Just hit F5 to refresh the current view in the browser to trigger this behavior.

![Login (BackChannelLogoutClient2)](_assets/back-channel-logout-5.png "Login (BackChannelLogoutClient2)")

## Worth Noting

As stated before, both client applications are implemented the same way (although it didn't need to be that way exactly). As such, these remarks apply to both applications.

### Back-channel Logout Configuration

To receive back-channel logout notifications, the client application needs to provide a callback route and have that address configured in the IDS back-office (see Clients above).

This route is implemented in `LogoutController`:

```csharp
[HttpPost("/logout")]
public async Task<IActionResult> IndexAsync(string logout_token)
```

### Authorization Grant

The clients are configured to use the Authorization Code grant. There is nothing particular here concerning the back-channel notification.

> The Hybrid grant type would also be fine.

`Startup.cs`:

```csharp
services
    .AddAuthentication(
        (options) =>
        {
            options.DefaultScheme = "Cookies";
            options.DefaultChallengeScheme = "oidc";
        })
    .AddCookie(
        (options) =>
        {
            options.Cookie.Name = "sample-backchannellogout-client1";
            options.ExpireTimeSpan = TimeSpan.FromMinutes(60);

            options.AccessDeniedPath = new PathString("/home/accessdenied");
            options.EventsType = typeof(CookieAuthenticationEventsHandler);
        })
    .AddOpenIdConnect(
        "oidc",
        (options) =>
        {
            options.SignInScheme = "Cookies";
            options.Authority = this.Configuration.GetValue<string>("SAMPLE_AUTHORITYSERVER_BASEADDRESS");
            options.RequireHttpsMetadata = false;
            options.MaxAge = TimeSpan.FromMinutes(60);

            options.ClientId = "sample-backchannellogout-client1";

            options.ResponseType = "code";
            options.UsePkce = true;

            options.SaveTokens = true;
            options.GetClaimsFromUserInfoEndpoint = false;

            options.Scope.Add("profile");
            options.Scope.Add("email");
            options.Scope.Add("identityserver4");

            options.Events = new OpenIdConnectEvents()
            {
                OnAuthorizationCodeReceived = OnAuthorizationCodeReceivedAsync,
                OnRedirectToIdentityProvider = OnRedirectToIdentityProviderAsync,
                OnRedirectToIdentityProviderForSignOut = OnRedirectToIdentityProviderForSignOutAsync,
                OnTokenResponseReceived = OnTokenResponseReceivedAsync,
                OnUserInformationReceived = OnUserInformationReceivedAsync
            };
        })
    .AddJwtBearer();
```

### Session Management

The OIDC general session management services are not sufficient to implement single sign-out because these rely solely on cookies.

Applications need to keep track somehow of the active sessions for all users so they can determine which sessions need to be signed-out at any moment.

For the purpose of this sample, the session management service is implemented by `IUserSessionManager` and `SampleUserSessionManager`.

```csharp
/// <summary>
/// Defines the interface of the user session manager.
/// This manager is responsible for tracking user sessions in the application.
/// </summary>
public partial interface IUserSessionManager
{
    #region Methods

    /// <summary>
    /// Records a new user session.
    /// </summary>
    /// <param name="sessionId">The session identifier.</param>
    /// <param name="subjectId">The subject identifier.</param>
    /// <param name="cancellationToken">The cancellation token.</param>
    /// <returns>
    /// The <see cref="Task"/> that represents the asynchronous operation.
    /// </returns>
    Task AddSessionAsync(string sessionId, string subjectId, CancellationToken cancellationToken = default);

    /// <summary>
    /// Removes an existing user session.
    /// </summary>
    /// <param name="sessionId">The session identifier.</param>
    /// <param name="subjectId">The subject identifier.</param>
    /// <param name="cancellationToken">The cancellation token.</param>
    /// <returns>
    /// The <see cref="Task"/> that represents the asynchronous operation.
    /// </returns>
    Task RemoveSessionAsync(string sessionId, string subjectId, CancellationToken cancellationToken = default);

    /// <summary>
    /// Marks the specified user session has signed-out and handle any other active
    /// session for the same user accordingly.
    /// </summary>
    /// <param name="sessionId">The session identifier.</param>
    /// <param name="subjectId">The subject identifier.</param>
    /// <param name="cancellationToken">The cancellation token.</param>
    /// <returns>
    /// The <see cref="Task"/> that represents the asynchronous operation.
    /// </returns>
    Task SessionSignedOutAsync(string sessionId, string subjectId, CancellationToken cancellationToken = default);

    /// <summary>
    /// Determines whether the specified user session is required to sign-out.
    /// </summary>
    /// <param name="sessionId">The session identifier.</param>
    /// <param name="subjectId">The subject identifier.</param>
    /// <param name="cancellationToken">The cancellation token.</param>
    /// <returns>
    /// The <see cref="Task{TResult}"/> that represents the asynchronous operation.
    /// A value indicating whether the specified user session is required to sign-out.
    /// </returns>
    Task<bool> SignoutSessionAsync(string sessionId, string subjectId, CancellationToken cancellationToken = default);

    #endregion
}
```

> Be aware that for real-world scenarios, this implementation would need to be improved with things like storage, thread-safety, etc.

When a user starts a new session, `AddSessionAsync()` is called from the `SignedIn` cookie event (in `CookieAuthenticationEventsHandler`):

```csharp
public override async Task SignedIn(CookieSignedInContext context)
{
    SmartGuard.NotNull(() => context, context);

    // This is called immediately after the user signs-in

    // NOTE:
    // Must always use context.Principal, not context.HttpContext.User

    if (context.Principal.Identity.IsAuthenticated)
    {
        string sid = context.Principal.FindFirst("sid")?.Value;
        string sub = context.Principal.FindFirst("sub")?.Value;

        await this.UserSessionManager.AddSessionAsync(
            sid,
            sub)
            .ConfigureAwait(false);
    }

    // Default behavior

    await base.SignedIn(context).ConfigureAwait(false);
}
```

When a user session ends, `RemoveSessionAsync()` is called from the `SigningOut` cookie event:

```csharp
/// <inheritdoc />
public override async Task SigningOut(CookieSigningOutContext context)
{
    SmartGuard.NotNull(() => context, context);

    // This is called immediately before the user signs-out

    if (context.HttpContext.User.Identity.IsAuthenticated)
    {
        string sid = context.HttpContext.User.FindFirst("sid")?.Value;
        string sub = context.HttpContext.User.FindFirst("sub")?.Value;

        await this.UserSessionManager.RemoveSessionAsync(
            sid,
            sub)
            .ConfigureAwait(false);
    }

    // Default behavior

    await base.SigningOut(context).ConfigureAwait(false);
}
```

> `SessionSignedOutAsync()` and `SignoutSessionAsync()` are related with the back-channel logout notification, explained below.

### Receiving Back-channel Logout Notifications

Back-channel logout notifications are received in the `LogoutController`.

When a new notification is received, the application is responsible for:

1. Validate the logout token according to back-channel logout specification.
2. Record the session signed-out.
3. Trigger the single sign-out.

```csharp
public async Task<IActionResult> IndexAsync(string logout_token)
{
    this.Response.Headers.Add("Cache-Control", "no-cache, no-store");
    this.Response.Headers.Add("Pragma", "no-cache");

    try
    {
        // Validate the logout token and
        // extract the principal referenced in the logout token
        // to identify the session that caused the notification

        string authorizationServer = this.Configuration.GetValue<string>("SAMPLE_AUTHORITYSERVER_BASEADDRESS");

        ClaimsPrincipal user = await this.Validator.ValidateAsync(
            authorizationServer,
            "sample-backchannellogout-client1",
            logout_token)
            .ConfigureAwait(false);

        // Record this session to reflect the sign-out on any other
        // active session

        string sid = user.FindFirst("sid")?.Value;
        string sub = user.FindFirst("sub")?.Value;

        await this.UserSessionManager.SessionSignedOutAsync(
            sid,
            sub)
            .ConfigureAwait(false);

        // Indicate that the notification was processed correctly

        return this.Ok();
    }
    catch
    {
    }

    // Indicate that there is something wrong with the notification

    return this.BadRequest();
}
```

The token validation is implemented by the `IBackChannelLogoutTokenValidator` service. This process requires:

- Obtaining the issuer name and signing keys from the authorization server discovery endpoint.
- Validating the issuer, the audience, and the signing keys present in the token.
- Ensuring that the token includes the `sub` and/or the `sid` claims, it does not include a `nonce`, and it includes the `http://schemas.openid.net/event/backchannel-logout` event reference.

This validation allows the application to obtain a principal from the logout token, including the session id and the subject id (the user id) of the signed-out session.

Then the controller simply records this session with `IUserSessionManager`, which, in turn, marks all the other sessions for the same user to be signed-out as soon as they are used again.

```
public Task SessionSignedOutAsync(string sessionId, string subjectId, CancellationToken cancellationToken = default)
{
    SmartGuard.NotNullOrWhiteSpace(() => sessionId, sessionId);
    SmartGuard.NotNullOrWhiteSpace(() => subjectId, subjectId);

    // Find all the sessions for this same user
    // mark all to sign-out, so that the next
    // time they are checked, sign-out is forced

    IEnumerable<UserSession> sessions = this.fieldSessions
        .Where(i => i.SubjectId.EqualsNoCase(subjectId));

    sessions.ForEach(i => i.Signout = true);

    return Task.CompletedTask;
}
```

### Invalidating Sessions (Sign-out)

The only way to sign-out a session is when the browser tries to use it again. This happens when the user tries to access any protected resource.

The `ValidatePrincipal` event in `CookieAuthenticationEventsHandler` can be used for that purpose:

```csharp
public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
{
    SmartGuard.NotNull(() => context, context);

    // This is called on every request
    // It allows checking if the current user session needs to be signed-out
    // as result of back-channel logout notification

    // NOTE:
    // Must always use context.Principal, not context.HttpContext.User

    if (context.Principal.Identity.IsAuthenticated)
    {
        string sid = context.Principal.FindFirst("sid")?.Value;
        string sub = context.Principal.FindFirst("sub")?.Value;

        bool signout = await this.UserSessionManager.SignoutSessionAsync(
            sid,
            sub)
            .ConfigureAwait(false);

        if (signout)
        {
            // Reject the principal

            context.RejectPrincipal();

            // Sign-out

            context.HttpContext.Items["ForceLogin"] = true;

            await context.HttpContext.SignOutAsync()
                .ConfigureAwait(false);
        }
    }
}
```

There is a gotcha here. By default, when a cookie session is signed-out it will force the client application to communicate with the IDP the next time a protected resource is accessed.

Wheather this communication actually triggers a new login depends on other factors, mainly the duration of the OIDC session and when the user signed-in for the last time.

To force the login UI always after the back-channel logout, the application does two other things:

1. It sets a variable in the `HttpContext` called `ForceLogin` (above).
2. It uses the presence of this variable in the context to "inform" the OIDC that the login should be presented regardless of other conditions. This is implemented in the `OnRedirectToIdentityProvider` event (in `Startup`):

```csharp
private static Task OnRedirectToIdentityProviderAsync(RedirectContext context)
{
    if (context != null)
    {
        ILogger logger = context.HttpContext.RequestServices.GetRequiredService<ILogger<Startup>>();

        logger.LogDebug($"Authentication: Redirect to identity provider.");

        // NOTE:
        // Prompt here forces the OIDC server to always show the login UI
        // to complete the authorize request
        // This is necessary because when the user session is signed-out in
        // CookieAuthenticationEventsHandler.ValidatePrincipal, the authorize
        // endpoint is called but if the user had signed-in just a few minutes
        // ago, he would be automatically signed-in again without having to
        // enter his credentials

        if (context.HttpContext.Items.ContainsKey("ForceLogin"))
        {
            context.ProtocolMessage.Prompt = "login";
        }
    }

    return Task.CompletedTask;
}
```

### Services

Notice how the various services are registered in `Startup`:

```csharp
services
    .AddTransient<CookieAuthenticationEventsHandler>();
            
services
    .AddSingleton<IUserSessionManager, SampleUserSessionManager>();

services
    .AddSingleton<IBackChannelLogoutTokenValidator, DefaultBackChannelLogoutTokenValidator>();
```