# Samples

This chapter includes samples that illustrate IAM concepts, common integration scenarios, customization, etc.

The samples are organized by theme.

> The samples source code is available along with Identity Server's source code.

## Client Applications

These samples illustrate the configuration of client applications to integrate with Identity Server.

| Sample | Grant Type | Description |
| - | - | - |
| [`HybridClientWithConsent`](hybrid-client-with-consent.md) | Hybrid | Illustrates an ASP.NET client application that uses the Hybrid flow to authenticate users and require explicit consent |
| [`Delegation`](delegation-grant.md) | Delegation | Illustrates an ASP.NET client application that uses the Delegation flow to call a back-end API on behalf of an user |

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