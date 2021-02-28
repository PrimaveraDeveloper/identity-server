# Reference

This chapter includes reference documentation including technologies, libraries, features, and specific use cases.

| Topic | Description |
| - | - |
| [Protocols](protocols/README.md) | Identity Server implements and supports several IAM protocols, frameworks, and technologies |
| [Front-office](back-office/README.md) | The Web application that serves the users login and profile management experiences |
| [Back-office](back-office/README.md) | The Web application that allows managers and contributors to configure Identity Server |
| [Endpoints](endpoints/README.md) | The OAuth and OIDC endpoints supported by Identity Server |
| [Web API](web-api/README.md) | The Web API that allows client applications to perform operations like creating clients, configuring resources, etc. |
| [Client Libraries](client-libraries/README.md) | Client libraries that provide recommended models to consume the Web API |
| [Specific-purpose features](features/README.md) | Custom and advanced features of Identity Server, useful for specific use cases |

## Terminology

The documentation uses certain terminology that may require habituation and is very important to recall.

| Term | Description |
| - | - |
| Access Token | Allows access to an API resource |
| API | Application Programming Interface, here refering typically to a RESTful API, a set of endpoints accessible via HTTP |
| Authority Server | An OAuth 2.0 and/or OIDC provider such as IDS |
| Authorization Server | Same as Authority Server |
| Claim | A name/value pair that asserts something about an user or a client |
| Client | A piece of software that requests tokens from IDS, either for authenticating an user or for accessing a resource |
| Grant Type (Grant Flow) | Describe how clients interact with the authority server, as defined by the OAuth and OIDC specifications |
| Identity Provider (IdP) | Same as Authority Server |
| Identity Server (IDS) | PRIMAVERA Identity Server, an OAuth 2.0 and OIDC provider |
| Identity Token | Represents the outcome of an authentication process |
| JWT | JSON Web Token, a proposed standard to represent tokens in JSON || OAuth | Open framework for authorization (see RFC 6749) |
| OpenID Connect (OIDC) | An identity layer built on top of OAuth to support end-user authentication |
| User | A human that is using a registered client to access resources |
| Resource | Something that needs to be protected with IDS, either the identity of an user or an API |
| Resource Server | The server running the resources protected by IDS |
| Scope | A special kind of claim that is used by OAuth to limit access to a resource |
| Security Token Service (STS) | Same as Authority Server |
| Token | May refer to an Access Token or an Identity Token |
