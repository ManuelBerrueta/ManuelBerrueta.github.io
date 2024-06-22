---
title: "JWT Tokens..."
date: "2021-12-30"
tags: 
  - "accesselevation"
  - "accesstokens"
  - "apisecurity"
  - "auth"
  - "authbypass"
  - "authentication"
  - "authenticationbypass"
  - "azure"
  - "cloudsecurity"
  - "cybersecurity"
  - "infosec"
  - "jsonwebtoken"
  - "jsonwebtokens"
  - "jwt"
  - "jwttoken"
  - "oauth"
  - "oauth2"
  - "oauth2-0"
  - "oidc"
  - "openid"
  - "openidconnect"
  - "security"
  - "webappsecurity"
  - "websecurity"
---

JWT or JSON Web Tokens are used extensively for authorization following authentication. For most of my use cases, I am mostly working with Microsoft related stack (Azure, AAD, ADO) shenanigans. As I continue to dive into auth and identity related topics, I figured I would write some quick basic notes on JWTs since they are very common.

#### What is JWT?

It is a compact, standardized and URL safe token which is signed and to be exchanged between two parties with authorization to access resources. For us this is mostly through a client/server relationship to get access to resources.

#### How do we get a JWT?

Usually, we obtain a JWT after authenticating (AuthN) to an application/service using some credentials and then use the JWT as part of subsequent request to server/service as a means of authorization (AuthZ) of the request.

### Anatomy of a JWT

A JWT consists of **3** parts: `Header`, `Payload`, and `Signature` which are all **Base64** encoded and separated by a dot.

- The `Header` contains the type of token and the algorithm used for signing the token, thus providing information as to how to validate the token.

- The `Payload` contains the "`Claims`", stating crucial data about the user or application trying to access the target service/application.

- The `Signature` is used to make sure the token has not been modified and validate the token.

#### Example Token

```JSON
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiAiaHR0cHM6Ly9zb21lLmRvbWFpbi5uZXQvIiwiaWF0IjogMTY0MDgwMzc2MDIsIm5iZiI6IDE2NDg5NzY2MTAsImV4cCI6IDE2NDg5ODY2MTAsIm5hbWUiOiAiTm90IE1lIiwic3ViIjogIjFBMmIzQzRkNUU2ZjdHOGg5STBqIn0.SflKx1p5SleKKF2aT4fwpMeJn36POk7yJV_adQssw5c
```

#### Example Decoded Token:

![](images/image.png)

## Tooling

As part of my [BST Tools](https://github.com/ManuelBerrueta/BST) that I have been working on, I have some code to help you automate decoding JWTs offline as well as parsing and decoding access tokens from the `accessTokens.json`:

https://github.com/ManuelBerrueta/BST/blob/main/Azure/BST-JWTTool.py

The tool also allows you to change/replace the `Payload` of an existing JWT and reuse the rest of the body of the token for testing purposes as one of the attack vectors in JWTs is modifying the Payload, if the logic for the token is not properly checking the signature, then we can take advantage of this vulnerability to perform actions/elevating our access with this modified JWT.

## Some reference documents/sites:

- [https://tools.ietf.org/html/rfc6749](https://tools.ietf.org/html/rfc6749)

- https://openid.net/specs/draft-jones-json-web-token-07.html

- https://developer.okta.com/blog/2020/12/21/beginners-guide-to-jwt

- https://jwt.io/introduction

- https://medium.com/@robert.broeckelmann/saml2-vs-jwt-understanding-json-web-token-jwt-67d56462feaf

- https://jwt.ms

- https://systemweakness.com/hacking-jwt-d29f39e202d5

## Vulnerabilities

- [Disclosing a New Vulnerability in JWT Secret Poisoning (CVE-2022-23529)](https://unit42.paloaltonetworks.com/jsonwebtoken-vulnerability-cve-2022-23529/)

.
