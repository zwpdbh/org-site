+++
title = "OAuth Introduction"
date = 2020-08-04T18:11:00+08:00
lastmod = 2020-08-04T18:23:13+08:00
tags = ["Software-Engineering"]
draft = false
weight = 10
autoCollapseToc = true
mathjax = true
contentCopyright = "MIT"
author = "Z wei"
+++

<div class="ox-hugo-toc toc">
<div></div>

<div class="heading">Table of Contents</div>

- [What is OAuth2.0](#what-is-oauth2-dot-0)
- [How OAuth solve the problem?](#how-oauth-solve-the-problem)
- [OAuth's components](#oauth-s-components)
    - [Refresh tokens](#refresh-tokens)
- [Authorization code grant](#authorization-code-grant)

</div>
<!--endtoc-->


## What is OAuth2.0 {#what-is-oauth2-dot-0}

-   A delegation protocol.
-   The **definition**, enable a third-party application to obtain limited access to an HTTP service, either:
    -   on behalf of a resource owner by orchestrating an approval interaction between the resource owner and the HTTP service
    -   allow the third-party application to obtain access on its own behalf
-   Components
    -   resource ower, usually a person has access to an API and can delegate access to that API
    -   protected resources, the component that the resource owner has access to, such as some Web API
    -   client , is the piece of software that access the protected resources on behalf of the resource owner. In OAuth, the client is whatever software consumes the API that makes up the protected resource.

-   An example
    -   You have a cloud phote-storage service and a phote printing service.
    -   Want to print the photes stored in storage service.
    -   Those two services are running by different companies.
    -   Use OAuth to solve this problem by letting you delegate access to your photos across different services, all without giving your password away to phote printer.
    -   **resource owner** (you/browser)
    -   **protected resources** (photos in storage service)
    -   **client** (printing service that want to access photo storage service)
-   What we want ?
    -   a kind of limited credential, issued **seperately** for each client and each user combination to be used at a protected resources.
    -   then tie limited rights to each of these limited credentials.
    -   a network based protocol that allowed the generation and secure distribution of these limitied credentials across security boundaries


## How OAuth solve the problem? {#how-oauth-solve-the-problem}

-   The end user delegates some part of their authority to access the protected resource to client application to act on their behalf.
-   OAuth introduces another component into the system: **authorization server(AS)**. Now, there are four components:
    -   AS
    -   resource ower, user(you/browser)
    -   protected resources
    -   client, app/api
-   AS is trusted by the protected resource to issue special purpose security credentials -- called **OAuth access tokens** to clients.
-   Steps to acquire a token:
    1.  the client first sends the resource owner to the authorization server in order to request that the resource owner authorize this client.
    2.  the resource owner authenticates to the authorization server and is generally presented with a choice of whether to authorize the client making the request.
    3.  the client is able to ask for a subset of functionality, or scopes
    4.  Once the authorization grant has been made, the client can then request an access token from the authorization server.
        -   An **authorziation grant** is the means by which an OAuth client is given access to a protected resource using the OAuth Protocol, and if successful it ultimately results in the client getting a token.
        -   Don't misunderstand authorization code as it, the **entire OAuth process** is the authorization grant.
    5.  This access token can be used at the protected resource to access the API, as granted by the resource owner.


## OAuth's components {#oauth-s-components}


### Refresh tokens {#refresh-tokens}

-   Similar in concept to the access token
-   Different, though, is that the token is never sent to the protected resources. Intead, the client uses the refresh token to request new access tokens **without involving the resource owner**.
-   diagram

    {{< figure src="/ox-hugo/refreshToken.png" >}}


## Authorization code grant {#authorization-code-grant}

```plantuml
ResourceOwner -> Client: 1.user visits service
Client --> ResourceOwner: 2.client redirect user(ResourceOwner) to Authorization endpoint
ResourceOwner -> AuthorizationServer: 3. ResourceOwner do the authentication
AuthorizationServer --> ResourceOwner: 4. AuthorizationServer send user a redirect response
ResourceOwner -> Client: 5. Redirect reponse causes user to send get request to client.
Client -> AuthorizationServer: 6.POST request to AuthorizationServer on its token endpoint
Client <- AuthorizationServer: 7. AuthorizationServer issues an OAuth access token to the client
Client -> ProtectedResource: 8. Client sends access token to protected resource
Client <- ProtectedResource: 9. Protected resource returns resource to Client
```

{{< figure src="/ox-hugo/authorizationCodeGrant.png" >}}

-   step2's response is an HTTP redirect, it causes step 3: cause the browser to send an HTTP GET to the autorization server. step2 have the following query parameters:
    -   `response_type`
    -   `scope`
    -   `client_id`
    -   `redirect_uri`
    -   `state`
-   step5, the client could receive
    -   `code`, known as **authorization code**.
    -   `state`, matches the value that it sent in the step 2.
-   step6, POST request with passing its `client_id` and `client_secret` as an HTTP Basic authorization header.

    ```text
    Authorization: Basic b2F1dGgtY2xpZW50LTE6b2F1dGgtY2xpZW50LXNlY3JldC0x
    ```

    -   This separation between different HTTP connections ensures that the client can   authenticate itself directly without other components being able to see or manipulate  the token request.
-   step7, authorization server need to ensure the request from step6 is legitimate, issue accessToken if valid.
    -   check the client's credentials (passed in the authentication header) to see which client is requesting acess.
    -   check code (authorization code) from the body. It incldues
        1.  which client made the initial authorization request
        2.  which user authorized it
        3.  what it is authorized for.
    -   if the code has not been used previously and the client making this request is the same as the client that made the original request, then the authorization server generates and returns a new access token for the client

        ```text
        HTTP 200 OK
        Date: Fri, 31 Jul 2015 21:19:03 GMT
        Content-type: application/json

        {
          “access_token”: “987tghjkiu6trfghjuytrghj”,
          “token_type”: “Bearer”
        }
        ```

        -   The response could also include a **refresh token** (used to get new access tokens without asking for authorization again)
        -   additional info could be **scopes and expiration time**.
-   step8, the client get the access token from step7 response and present it to the protect resource
    -   present the access token as **OAuth Bearer token**

        ```text
        GET /resource HTTP/1.1
        Host: localhost:9002
        Accept: application/json
        Connection: keep-alive
        Authorization: Bearer 987tghjkiu6trfghjuytrghj
        ```
    -   can **store this access token in a secure place** for as long as it wants to use the token, even after the user has left.
-   step9, protected resource use the access token to determine whether it is still valid
    -   **HOW to validate?** A protected resource has a number of options for doing this token lookup. The simplest option is for the resource server and the authorization server to share a database that contains the token information. The authorization server writes new tokens into the database when they are generated, and the resource server reads tokens from the store when they are presented.