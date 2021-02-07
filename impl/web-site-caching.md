# Implementation - Web Site - Caching

Identity Server has 3 main caching services in place:

- `ICacheService` - a global distributed cache that uses REDIS (`IDistributedCache`).
- `ISessionCacheService` - a distributed cache (stored in the database) that is scoped to the user session.
- `ISessionOneTimeCacheService` - a distributed cache (stored in the database) that is scoped to the user session and whose items can only be read once.

## Global Cache

`ICacheService` is global to the Web Site (and distributed) and is used to store slow-changing values for performance reasons.

This cache may have effect on changes made in the back-office like:

- Client configurations.
- Client overrides.

## Session Cache

`ISessionCacheService` is associated with the user session. Because of that it only stores data specific and meaningful to a specific user. Underneath it relies on ASP.NET Session.

`ISessionOneTimeCacheService` is a particular case of `ISessionCacheService`. Cache items stored in this cache can only be read once, as they are automatically removed after being read (which is useful for example to store temporary error messages and validation codes).

Data is stored in the database in table `[IDSv4-Custom].[SessionCache]`.

## Other Caching Scenarios

- The core endpoints (e.g. connect and authorize) cache data about clients and resources for a few minutes. This means that changes in the clients and/or resources in the back-office may take a few minutes to become effective.