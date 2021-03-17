# Samples

This chapter includes samples, organized by theme, that illustrate IAM concepts, common integration scenarios, customization, etc.

> The samples source code is available along with Identity Server's source code.

## Client Applications

These samples illustrate the configuration of client applications to integrate with Identity Server using different grant types and some not-so-obvious features.

| Sample | Grant Type | Description |
| - | - | - |
| [`ClientCredentialsClient`](client-credentials-client.md) | Client Credentials | Illustrates a client application (Console) that uses the Client Credentials flow to call an API |
| [`Delegation`](delegation-grant.md) | Delegation | Illustrates a client application (ASP.NET Core) that uses the Delegation flow to call a back-end API on behalf of an user |
| [`HybridClientWithConsent`](hybrid-client-with-consent.md) | Hybrid | Illustrates a client application (ASP.NET Core) that uses the Hybrid flow to authenticate users and require explicit consent |

## Important

You can manipulate the base addresses of the sample applications and the base address of the Identity Server instance used in the `appsettings.json` file. Here's an example:

```json
{
    "SAMPLE_AUTHORITYSERVER_BASEADDRESS": "https://localhost:5001",
    "SAMPLE_MIDDLETIER_BASEADDRESS": "https://localhost:20002",
    "Logging": {
        "LogLevel": {
            "Default": "Information"
        }
    },
    "AllowedHosts": "*"
}
```