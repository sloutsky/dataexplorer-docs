---
title: Kusto Access Control Overview - Azure Data Explorer
description: This article describes Kusto Access Control Overview in Azure Data Explorer.
ms.reviewer: orspodek
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 11/25/2019
---
# Kusto Access Control Overview

Access Control in Azure Data Explorer is based on two key factors.
* [Authentication](#authentication): Validates the identity of the security principal making a request
* [Authorization](#authorization): Validates that the security principal making a request is permitted to make that request on the target resource

A query or a control command on an Azure Data Explorer cluster, database, or table, must pass both authentication and authorization checks.

## Authentication

**Azure Active Directory (Azure AD)** is Azure's preferred multi-tenant cloud directory service. It can authenticate security principals or federate with other identity providers.

Azure AD is the preferred method for authenticating to Azure Data Explorer in Microsoft. It supports a number of authentication scenarios.
* **User authentication** (interactive sign-in): Used to authenticate human principals.
* **Application authentication** (non-interactive sign-in): Used to authenticate services and applications that have to run and authenticate with no human user present.

### User authentication

User authentication is done when the user presents credentials to:
* Azure AD 
* an identity provider that works with Azure AD

If successful, the user receives a security token that can be presented to the Azure Data Explorer service. The Azure Data Explorer service doesn't care how the security token was obtained. It cares about whether the token is valid and what information is put there by Azure AD (or the federated IdP).

On the client side, Azure Data Explorer supports interactive authentication, where the Microsoft Authentication Library or similar code, requests the user to enter credentials. It also supports token-based authentication, where the application using Azure Data Explorer obtains a valid user token. 
The application that uses Azure Data Explorer can also obtain a valid user token for another service. The user token is obtainable only if a trust relationship between that resource and Azure Data Explorer exists.

For more information, see [Kusto connection strings](../api/connection-strings/kusto.md) for details on how to use the Kusto client libraries and authenticate by using Azure AD to Azure Data Explorer.

### Application authentication

Use the Azure AD application authentication flow when requests aren't associated with a specific user or there's no user available to enter credentials. In the flow, the application authenticates to Azure AD (or the federated IdP) by presenting some secret information. The following scenarios are supported by the various Azure Data Explorer clients.

* Application authentication using an X.509v2 certificate installed locally
* Application authentication using an X.509v2 certificate given to the client library as a byte stream
* Application authentication using an Azure AD application ID and an Azure AD application key.

    > [!NOTE] 
    > The ID and key are the equivalent of a username and password

* Application authentication using a previously obtained valid Azure AD token, issued to Azure Data Explorer.
* Application authentication using a previously obtained valid Azure AD token, issued to some other resource. This method will work if there's a trust relationship between that resource and Azure Data Explorer.

### Microsoft Accounts (MSAs)

Microsoft Account (MSA) is the term used for all the Microsoft-managed non-organizational user accounts, such as `hotmail.com`, `live.com`, `outlook.com`.
Kusto supports user authentication for MSAs (there's no security groups concept) that are identified by their User Principal Name (UPN).

When an MSA principal is configured on an Azure Data Explorer resource, Azure Data Explorer **won't** attempt to resolve the UPN provided.

### Authenticated SDK or REST calls

* When using the REST API, authentication is done with the standard HTTP `Authorization` header
* When using any of the Azure Data Explorer .NET libraries, authentication is controlled by specifying the authentication method and parameters in the [connection string](../api/connection-strings/kusto.md). Another method is to set the properties on the [client request properties](../api/netfx/request-properties.md) object.

### Azure Data Explorer client SDK as an Azure AD client application

When the Kusto client libraries invoke the Microsoft Authentication Library to acquire a token for communicating with Kusto, it provides the following information:

* The Resource (Cluster URI, such as, `https://Cluster-and-region.kusto.windows.net`)
* The Azure AD Client Application ID
* The Azure AD Client Application Redirect URI
* The Azure AD Tenant, that affects the Azure AD endpoint used for authentication. For example, for Azure AD tenant `microsoft.com`, the Azure AD endpoint is `https://login.microsoftonline.com/microsoft.com`)

The token returned by the Microsoft Authentication Library to the Azure Data Explorer Client Library has the appropriate Azure Data Explorer cluster URL as the audience, and the "Access Azure Data Explorer" permission as the scope.

**Example: Obtain an Azure AD User token for an Azure Data Explorer cluster**

```csharp
var appId = "<appId>";
var appTenant = "<appTenant>";
var redirectUri = "<appRedirectUri>";
// Create a public authentication client for Azure AD
var authClient = PublicClientApplicationBuilder.Create(appId)
    .WithAuthority($"https://login.microsoftonline.com/{appTenant}")
    .WithRedirectUri(redirectUri)
    .Build();
// acquireToken will receive the bearer token for the authenticated user
var result = authClient.AcquireTokenInteractive(
    new[] { $"https://<clusterName>.<region>.kusto.windows.net/.default" }
).ExecuteAsync().Result;
var acquireToken = result.AccessToken;
```

## Authorization

All authenticated principals undergo an authorization check before they may carry out an action on an Azure Data Explorer resource.
Azure Data Explorer uses a [role-based access control model](role-based-access-control.md), where principals are ascribed to one or more security roles. Authorization succeeds as long as one of the principal's roles is authorized.

For example, the database user role grants security principals, users, or services, the right to:
* read the data of a particular database
* create tables in the database
* create functions in the database

The association of security principals to security roles can be defined individually,
or by using security groups that are defined in Azure AD. The commands are defined in [Security roles management](../management/security-roles.md).
