# Implementation - Core - Database Upgrades

IDS stores its data in a SQL Server database. This database is versioned and the application includes an automatic mechanism to upgrade that database, when needed, at startup.

## Database Version

The database is versioned and the current version is stored in table `[IDSv4-Custom].[DatabaseVersions]`.

## Upgrade Scripts

For each database version there is a script that upgrades the database from version N-1 to version N.

These scripts are named `IDS-Upgrade-V{version}.sql` and stored in the Domain project.

> All the scripts should be idempotent to ensure they can be executed multiple times on the same database without producing errors or leaving the database in an inconsistent state.

## Database Migrations Service

The `DatabaseMigrationsService` is responsible for determining the current database version when the application starts and applying any upgrade script to migrate the database automatically to the its latest version.

This background service starts earlier that all the other services (which apply a concept of delayed execution to ensure the database is up-to-date when they run).