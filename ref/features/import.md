# Feature - Import

The Lithium Framework generates a file for each microservice that is called an Authorization Spec. These files include the specification of the API scopes, the API resource, and the Clients required in Identity Server to make that service work correctly.

The IDS back-office includes an operation - from menu option "Import" - that allows importing such files in bulk, making it easier to create the resources.

This file uses a format which is a serialization of the type `AuthorizationSpec` of Hydrogen. It is also possible, off course, to create the files by hand to facilitate the creation of resources in the back-office.

> This operation is NOT available in the Identity Server Web API.