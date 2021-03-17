# Reference - Discovery Endpoint

The Discovery Endpoint returns metadata about the authorization server, like the issuer name, supported scopes, etc.

This is a public endpoint that returns the metadata as JSON.

## Route

`GET /.well-known/openid-configuration`

## Remarks

You can use `Primavera.Hydrogen.IdentityModel.Client.DiscoveryClient` to invoke the discovery endpoint.