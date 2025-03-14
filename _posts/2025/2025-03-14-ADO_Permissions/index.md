---
layout: post
title: "Azure DevOps Permission Analysis"
categories: Supply_Chain_Security
---

# Investigating ADO Permissions
For querying permissions in ADO we are going to be using the following two APIs:
- [Security Namespaces - Query](https://learn.microsoft.com/en-us/rest/api/azure/devops/security/security-namespaces/query?view=azure-devops-rest-7.1&tabs=HTTP)
- [Access Control Lists - Query](https://learn.microsoft.com/en-us/rest/api/azure/devops/security/access-control-lists/query?view=azure-devops-rest-7.1&tabs=HTTP)

## Security Namespaces
1. If you do not know the Security Namespace (id) you need, you must first query the `securitynamespaces` endpoint to find it: **`https://dev.azure.com/{{organization}}/_apis/securitynamespaces?api-version=7.1`**
    1. This is a long list, but here you can search for the specific Security Namespace name (if you know it).
    2. You can also  search for the specific permission name like "Edit task group". The task will have the **namespaceId**.
2. If you have the Security Namespace Id, you can query it directly. This will save time and give you just what you need: **`https://dev.azure.com/{{organization}}/_apis/securitynamespaces/{{securityNamespaceId}}?api-version=7.2-preview.1`**
3. The actions array within the JSON results, will have the permissions:
    ```json
      "actions": [
        {
          "bit": 1,
          "name": "Administer",
          "displayName": "Administer task group permissions",
          "namespaceId": "f6a4de49-dbe2-4704-86dc-f8ec1a294436"
        },
        {
          "bit": 2,
          "name": "Edit",
          "displayName": "Edit task group",
          "namespaceId": "f6a4de49-dbe2-4704-86dc-f8ec1a294436"
        },
        {
          "bit": 4,
          "name": "Delete",
          "displayName": "Delete task group",
          "namespaceId": "f6a4de49-dbe2-4704-86dc-f8ec1a294436"
        }
      ]
    ```
    - The bit is the value for that permission
    - The name is the short name for that operation within the namespace
    - The displayName is the name actually seen in the Web UI
    - The namespaceId, is the Security Namespace Id you use to search for this specific permissions

You can also leverage the documentation to get the Security Namespace Id, but sometimes it is just not as clear due to the naming structure, what exactly you are looking for. The documentation here also doesn't have the values: [Security namespace and permission reference for Azure DevOps](https://learn.microsoft.com/en-us/azure/devops/organizations/security/namespace-reference?view=azure-devops)

#### az devops cli
We could also use the **az devops cli**:
List all available security namespaces within an organization:
`az devops security permission namespace list --org https://dev.azure.com/{organization}`

Show a certain security namespace permissions:
`az devops security permission namespace show --id "f6a4de49-dbe2-4704-86dc-f8ec1a294436"`

## Access Control List (ACL)
Now given a token for which you want to check the permissions for, you will use a combination of the ACL API response results and the response of security namespace to calculate the permissions. It sounds a bit more complicated then it is, so let's just get to it as the example will help clear things up!

1. If you don't have a token and you would like to retrieve all of them within a security namespace run the accesscontrollists API with just the security namespace id: **`https://dev.azure.com/{{organization}}/_apis/accesscontrollists/{{securityNamespaceId}}?api-version=7.1`**
    1. This will be a long list, so it would be better if you know what you are looking for and use the next API below.
2. Given a token you acquired through your recon (somehow 😉 you crafty you), you will pass that token to the accesscontrollists API as a parameter (along with a couple of other params to give your more context): **`https://dev.azure.com/{{organization}}/_apis/accesscontrollists/{{securityNamespaceId}}?token={token}&includeExtendedInfo=true&recurse=true&api-version=7.1`**
3. Now we are going to calculate the permission given in the response means:
    ```json
          "acesDictionary": {
        "Microsoft.IdentityModel.Claims.ClaimsIdentity;cf7b0e4b-7577-4cf7-b42b-4f3130bb52d6\\john@example.com": {
          "descriptor": "Microsoft.IdentityModel.Claims.ClaimsIdentity;cf7b0e4b-7577-4cf7-b42b-4f3130bb52d6\\john@example.com",
          "allow": 7,
          "deny": 0,
          "extendedInfo": {
            "effectiveAllow": 
          }
    ```

### Permission breakdown
In the response we see:
```json
"allow": 7
```
It is bit based and we have to use the permissions given to us from the security namespace above to figure out what the permission is. To do this we simply add the bits together and match to what we have. In this case, is pretty simple, if we add all the bits (`1`+`2`+`4`) they equal `7`. Meaning this user has full permissions in that namespace and they can Administer, Edit, and Delete a task group within our example.
