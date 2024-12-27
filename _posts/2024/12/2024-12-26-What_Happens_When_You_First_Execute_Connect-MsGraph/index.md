---
layout: post
title: What happens when you first execute Connect-MsGraph⁉
categories: Azure Cloud
---


## What happens when you first execute Connect-MsGraph⁉
If you go to the Azure Portal -> Entra ID and check your Audit logs, you will see about 4 entries (in order of execution per timestamp):
0. "Service","Category","Activity"
1. "Core Directory","ApplicationManagement","Add service principal"
2. "Core Directory","ApplicationManagement","Add delegated permission grant"
3. "Core Directory","UserManagement","Add app role assignment grant to user"
4. "Core Directory","ApplicationManagement","Consent to application"

Let's break these down individually to understand what is happening...
### 1. "Core Directory","ApplicationManagement","Add service principal"
Here we add the Application to our tenant and we get prompted to consent the application with it's required scopes.

Here is some metadata from the log entry:
```json
        ---snipped---
        "category": "ApplicationManagement",
        "activityDisplayName": "Add service principal",
        "loggedByService": "Core Directory",
        "operationType": "Add",
        ---snipped---
        "targetResources": [
            {
                "id": "This will be unique OID in your tenant for that Service Principal",
                "displayName": "Microsoft Graph Command Line Tools",
                "type": "ServicePrincipal",
                "userPrincipalName": null,
                "groupType": null,
                "modifiedProperties": [
                    {
                        "displayName": "AccountEnabled",
                        "oldValue": "[]",
                        "newValue": "[true]"
                    },
                    {
                        "displayName": "AppAddress",
                        "oldValue": "[]",
                        "newValue": "[{\"AddressType\":0,\"Address\":\"https://login.microsoftonline.com/common/oauth2/nativeclient\",\"ReplyAddressClientType\":2,\"ReplyAddressIndex\":null,\"IsReplyAddressDefault\":false},{\"AddressType\":0,\"Address\":\"http://localhost\",\"ReplyAddressClientType\":2,\"ReplyAddressIndex\":null,\"IsReplyAddressDefault\":false},{\"AddressType\":0,\"Address\":\"ms-appx-web://microsoft.aad.brokerplugin/14d82eec-204b-4c2f-b7e8-296a70dab67e\",\"ReplyAddressClientType\":2,\"ReplyAddressIndex\":null,\"IsReplyAddressDefaul\\t\":false}]"
                    },
                    {
                        "displayName": "AppPrincipalId",
                        "oldValue": "[]",
                        "newValue": "[\"14d82eec-204b-4c2f-b7e8-296a70dab67e\"]"
                    },
                    {
                        "displayName": "DisplayName",
                        "oldValue": "[]",
                        "newValue": "[\"Microsoft Graph Command Line Tools\"]"
                    },
                    {
                        "displayName": "ServicePrincipalName",
                        "oldValue": "[]",
                        "newValue": "[\"14d82eec-204b-4c2f-b7e8-296a70dab67e\"]"
                    },
                    {
                        "displayName": "Credential",
                        "oldValue": "[]",
                        "newValue": "[{\"CredentialType\":2,\"KeyStoreId\":\Some GUID"\",\"KeyGroupId\":\"Same GUID as KeyStoreId\"}]"
                    },
                    {
                        "displayName": "Included Updated Properties",
                        "oldValue": null,
                        "newValue": "\"AccountEnabled, AppAddress, AppPrincipalId, DisplayName, ServicePrincipalName, Credential\""
                    },
                    {
                        "displayName": "TargetId.ServicePrincipalNames",
                        "oldValue": null,
                        "newValue": "\"14d82eec-204b-4c2f-b7e8-296a70dab67e\""
                    }
                ]
            }
        ---snipped---
```

In the backend, from the logs, we can see that the activity Targets an **`Id`** with a GUID and a **`Display Name`** of "Microsoft Graph Command Line Tools" with an **`"operationType": "Add"`**. In other words, here we created a Service Principal with a unique Object ID (OID) for the Microsoft Graph Command Line Tools application in our tenant.

### 2. "Core Directory","ApplicationManagement","Add delegated permission grant"
This next one is adding the permissions to use the application we just added to actually use Microsoft Graph (**`GraphAggregatorService`**) _first Party enterprise application_.

Log entry:
```json
        ---snipped---
        "category": "ApplicationManagement",
        "activityDisplayName": "Add delegated permission grant",
        "loggedByService": "Core Directory",
        "operationType": "Assign",
        ---snipped---
        "targetResources": [
            {
                "id": "OID of the Microsoft Graph (GraphAggregatorService) First Party App in your tenant",
                "displayName": "Microsoft Graph",
                "type": "ServicePrincipal",
                "userPrincipalName": null,
                "groupType": null,
                "modifiedProperties": [
                    {
                        "displayName": "DelegatedPermissionGrant.Scope",
                        "oldValue": null,
                        "newValue": "\" User.Read openid profile offline_access\""
                    },
                    {
                        "displayName": "DelegatedPermissionGrant.ConsentType",
                        "oldValue": null,
                        "newValue": "\"Principal\""
                    },
                    {
                        "displayName": "ServicePrincipal.ObjectID",
                        "oldValue": null,
                        "newValue": "\"05e6c6b7-a1cf-47f3-a2ae-37368ec1e883\""
                    },
                    {
                        "displayName": "ServicePrincipal.DisplayName",
                        "oldValue": null,
                        "newValue": null
                    },
                    {
                        "displayName": "ServicePrincipal.AppId",
                        "oldValue": null,
                        "newValue": null
                    },
                    {
                        "displayName": "ServicePrincipal.Name",
                        "oldValue": null,
                        "newValue": null
                    },
                    {
                        "displayName": "TargetId.ServicePrincipalNames",
                        "oldValue": null,
                        "newValue": "\"00000003-0000-0000-c000-000000000000/ags.windows.net;00000003-0000-0000-c000-000000000000;https://canary.graph.microsoft.com;https://graph.microsoft.com;https://ags.windows.net;https://graph.microsoft.us;https://graph.microsoft.com/;https://dod-graph.microsoft.us;https://canary.graph.microsoft.com/;https://graph.microsoft.us/;https://dod-graph.microsoft.us/\""
                    }
        ---snipped---
```

Here we are assigning permission to the actual **Microsoft Graph** Enterprise Application to allow us to use it with the "**Microsoft Graph Command Line Tools**" with the scopes of  "`User.Read openid profile offline_access`".
#### Permissions
These permissions can be reviewed through the Azure Portal under Microsoft Entra ID -> Enterprise Application -> Microsoft Graph Command Line Tools -> Security-> Permissions

### 3. "Core Directory","UserManagement","Add app role assignment grant to user"
Here Entra ID is adding the user to the "Microsoft Graph Command Line Tools" so the user can use the application.

Log:
```json
        ---snipped---
        "category": "UserManagement",
        "activityDisplayName": "Add app role assignment grant to user",
        "loggedByService": "Core Directory",
        "operationType": "Assign",
        ---snipped---
        "targetResources": [
            {
                "id": "This will be unique OID in your tenant for that Service Principal",
                "displayName": "Microsoft Graph Command Line Tools",
                "type": "ServicePrincipal",
                "userPrincipalName": null,
                "groupType": null,
                "modifiedProperties": [
                    {
                        "displayName": "AppRole.Id",
                        "oldValue": null,
                        "newValue": "\"00000000-0000-0000-0000-000000000000\""
                    },
                    {
                        "displayName": "AppRole.Value",
                        "oldValue": null,
                        "newValue": "\"\""
                    },
                    {
                        "displayName": "AppRole.DisplayName",
                        "oldValue": null,
                        "newValue": "\"\""
                    },
                    {
                        "displayName": "AppRoleAssignment.CreatedDateTime",
                        "oldValue": null,
                        "newValue": "\"2024-12-24T24:24:24.5865527Z\""
                    },
                    {
                        "displayName": "AppRoleAssignment.LastModifiedDateTime",
                        "oldValue": null,
                        "newValue": "\"2024-12-24T24:24:24.5865527Z\""
                    },
                    {
                        "displayName": "User.ObjectID",
                        "oldValue": null,
                        "newValue": "\"Your user OID GUID\""
                    },
                    {
                        "displayName": "User.UPN",
                        "oldValue": null,
                        "newValue": "\"YourUser@YourTenant.onmicrosoft.com\""
                    },
                    {
                        "displayName": "User.PUID",
                        "oldValue": null,
                        "newValue": "\"Your USER PUIDF\""
                    },
                    {
                        "displayName": "TargetId.ServicePrincipalNames",
                        "oldValue": null,
                        "newValue": "\"14d82eec-204b-4c2f-b7e8-296a70dab67e\""
                    }
                ]
            },
        ---snipped---
```

#### Permissions
The permissions can be reviewed by going to the that user in Entra ID -> Applications -> Microsoft Graph Command Line Tools -> View granted permissions

### 4. "Core Directory","ApplicationManagement","Consent to application"
Here Entra ID is adding the consented permissions to the user for the "Microsoft Graph Command Line Tools".

Log entry:
```json
        ---snipped---
        "category": "ApplicationManagement",
        "activityDisplayName": "Consent to application",
        "loggedByService": "Core Directory",
        "operationType": "Assign",
        ---snipped---
        "targetResources": [
            {
                "id": "05e6c6b7-a1cf-47f3-a2ae-37368ec1e883",
                "displayName": "Microsoft Graph Command Line Tools",
                "type": "ServicePrincipal",
                "userPrincipalName": null,
                "groupType": null,
                "modifiedProperties": [
                    {
                        "displayName": "ConsentContext.IsAdminConsent",
                        "oldValue": null,
                        "newValue": "\"True\""
                    },
                    {
                        "displayName": "ConsentContext.IsAppOnly",
                        "oldValue": null,
                        "newValue": "\"False\""
                    },
                    {
                        "displayName": "ConsentContext.OnBehalfOfAll",
                        "oldValue": null,
                        "newValue": "\"False\""
                    },
                    {
                        "displayName": "ConsentContext.Tags",
                        "oldValue": null,
                        "newValue": "\"WindowsAzureActiveDirectoryIntegratedApp\""
                    },
                    {
                        "displayName": "ConsentAction.Permissions",
                        "oldValue": null,
                        "newValue": "\"[] => [[Id: --------snipped---------, ClientId: 00000000-0000-0000-0000-000000000000, PrincipalId: {OID of your user}, ResourceId: {OID of Microsoft Graph (GraphAggregatorService) in your tenant}, ConsentType: Principal, Scope:  User.Read openid profile offline_access, CreatedDateTime: , LastModifiedDateTime ]]; \""
                    },
                    {
                        "displayName": "TargetId.ServicePrincipalNames",
                        "oldValue": null,
                        "newValue": "\"14d82eec-204b-4c2f-b7e8-296a70dab67e\""
                    }
                ]
            }
        ---snipped---
```

### End
In our Tenant we can now go to Microsoft Entra ID and see the relationships of these different identities.

It looks a bit like this:
Microsoft Graph Command Line Tools ---uses the User's permissions to query---> Microsoft Graph
Microsoft Graph Command Line Tools ---has permissions to use--->  Microsoft Graph
User ---consented permissions to--> Microsoft Graph Command Line Tools