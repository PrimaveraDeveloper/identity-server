# Implementation - Web Site - Caching

Identity Server has two main caching services in place:

- `ICacheService` - a global distributed cache that uses REDIS (`IDistributedCache`).
- `ISessionCacheService` - a distributed cache (stored in the database) that is scoped to the user session.

## Global Cache

`ICacheService` is global to the Web Site (and distributed) and is used to store slow-changing values for performance reasons.

This cache may have effect on changes made in the back-office like:

- Client configurations.
- Client overrides.

## Session Cache

`ISessionCacheService` is associated with the user session. Because of that it only stores data specific and meaningful to a specific user. Underneath it relies on ASP.NET Session.

Data is stored in the database in table `[IDSv4-Custom].[SessionCache]`.

## Other Caching Scenarios

- The core endpoints (e.g. connect and authorize) cache data about clients and resources for a few minutes. This means that changes in the clients and/or resources in the back-office may take a few minutes to become effective.