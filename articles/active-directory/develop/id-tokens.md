---
title: Jetons d’ID de la plateforme d’identités Microsoft | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment utiliser les id_tokens émis par Azure AD 1.0 et les points de terminaison de la plateforme d’identités Microsoft (v2.0).
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 06/25/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: fb20ed3a3a09c7bef7495d439ad2856f646e8a51
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131067189"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Jetons d’ID de la plateforme d’identités Microsoft

Le jeton d'ID est l'extension principale qu'[OpenID Connect](v2-protocols-oidc.md) apporte à OAuth 2.0. Les jetons d'ID sont émis par le serveur d'autorisation et contiennent des revendications incluant des informations sur l'utilisateur. Ils peuvent être envoyés en même temps qu’un jeton d’accès ou à la place de celui-ci. Les informations contenues dans les jetons d'ID permettent au client de vérifier qu'un utilisateur est bien celui qu'il prétend être. Les jetons d'ID sont destinés à être compris par des applications tierces. Les jetons d'ID ne doivent pas être utilisés à des fins d'autorisation. Les [jetons d'accès](access-tokens.md) sont utilisés pour l'autorisation. Les revendications fournies par les jetons d'ID peuvent être utilisées pour l'expérience utilisateur au sein de votre application, en tant que [clés au sein d'une base de données](#using-claims-to-reliably-identify-a-user-subject-and-object-id), et pour fournir un accès à l'application cliente.

## <a name="prerequisites"></a>Prérequis

L'article suivant vous sera très utile avant de poursuivre le présent article :

* [Protocoles OAuth 2.0 et OpenID Connect](active-directory-v2-protocols.md) sur la Plateforme d'identités Microsoft


## <a name="claims-in-an-id-token"></a>Revendications dans un jeton d'ID

Les jetons d'ID sont des [jetons web JSON (JWT)](https://wikipedia.org/wiki/JSON_Web_Token). Ces jetons d'ID se composent d'un en-tête, d'une charge utile et d'une signature. L'en-tête et la signature permettent de vérifier l'authenticité du jeton, tandis que la charge utile contient les informations relatives à l'utilisateur qui sont demandées par votre client. Les informations contenues dans les jetons d'ID v1.0 et v2.0 présentent des différences. La version est basée sur le point de terminaison à partir duquel le jeton a été demandé. Alors que les applications existantes utilisent probablement le point de terminaison Azure AD (v1.0), les nouvelles applications doivent utiliser le point de terminaison « Plateforme d'identités Microsoft » (v2.0).

* v1.0 : Point de terminaison Azure AD : `https://login.microsoftonline.com/common/oauth2/authorize`
* v2.0 : Point de terminaison Plateforme d'identités Microsoft : `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

### <a name="sample-v10-id-token"></a>Exemple de jeton d'ID v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Afficher cet exemple de jeton v1.0 dans [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="sample-v20-id-token"></a>Exemple de jeton d'ID v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Afficher cet exemple de jeton v2.0 dans [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw).

Sauf indication contraire, toutes les revendications JWT répertoriées ci-dessous apparaissent à la fois dans les jetons v1.0 et v2.0.

### <a name="header-claims"></a>Revendications de l’en-tête

Le tableau ci-dessous indique les revendications d'en-tête présentes dans les jetons d'ID.

|Revendication | Format | Description |
|-----|--------|-------------|
|`typ` | Chaîne : toujours « JWT » | Indique que le jeton est un jeton JWT.|
|`alg` | String | Indique l’algorithme utilisé pour signer le jeton. Exemple : "RS256" |
| `kid` | String | Spécifie l’empreinte numérique de la clé publique qui peut être utilisée pour valider la signature de ce jeton. Émis dans les jetons v1.0 et v2.0. |
| `x5t` | String | Fonctions identiques (en utilisation et en valeur) à `kid`. `x5t` est une revendication héritée émise uniquement dans les jetons v1.0 à des fins de compatibilité. |

### <a name="payload-claims"></a>Revendications de la charge utile

Le tableau ci-dessous indique les revendications présentes par défaut dans la plupart des jetons d'ID (sauf indication contraire).  Toutefois, votre application peut utiliser des [revendications facultatives](active-directory-optional-claims.md) pour demander des revendications supplémentaires dans le jeton d'ID. Les revendications facultatives peuvent aller de la revendication `groups` à des informations telles que le nom de l'utilisateur.

|Revendication | Format | Description |
|-----|--------|-------------|
|`aud` |  Chaîne, GUID de l'ID d'application | Identifie le destinataire du jeton. Dans les jetons `id_tokens`, l'audience est l'ID attribué à votre application dans le portail Azure. Cette valeur doit être validée. Le jeton doit être rejeté s'il ne correspond pas à l'ID de votre application. |
|`iss` |  Chaîne, URI d'un émetteur | Identifie l'émetteur, ou « serveur d'autorisation », qui construit et renvoie le jeton. Il identifie également le locataire Azure AD pour lequel l'utilisateur a été authentifié. Si le jeton a été émis par le terminal v2.0, l’URI se termine par `/v2.0`.  La partie GUID qui indique que l’utilisateur est un utilisateur consommateur d’un compte Microsoft est `9188040d-6c67-4c5b-b112-36a304b66dad`. Votre application doit utiliser la partie GUID de la revendication pour restreindre l’ensemble des clients qui peuvent se connecter à l’application, le cas échéant. |
|`iat` |  int, timestamp UNIX | « Issued At » (Délivré le) indique quand l'authentification de ce jeton a eu lieu.  |
|`idp`|Chaîne, généralement un URI STS | Enregistre le fournisseur d’identité qui a authentifié le sujet du jeton. Cette valeur est identique à la valeur de la revendication de l’émetteur sauf si le compte d’utilisateur n’est pas dans le même locataire que l’émetteur (invités par exemple). Si la revendication n’est pas présente, cela signifie que la valeur `iss` peut être utilisée à la place.  Pour les comptes personnels utilisés dans un contexte organisationnel (par exemple, un compte personnel invité dans un locataire Azure AD), la revendication `idp` peut être « live.com » ou un URI STS contenant le locataire de compte Microsoft `9188040d-6c67-4c5b-b112-36a304b66dad`. |
|`nbf` |  int, timestamp UNIX | La revendication « nbf » (pas avant) indique le délai avant lequel le JWT ne doit PAS être accepté pour être traité.|
|`exp` |  int, timestamp UNIX | La revendication « exp » (délai d’expiration) indique le délai d’expiration après lequel le JWT **ne doit pas** être accepté pour traitement.  Il est convient de noter que dans certaines circonstances, une ressource peut rejeter le jeton avant l'expiration de ce délai. Par exemple, si une modification de l’authentification est nécessaire ou si une révocation de jeton a été détectée. |
| `c_hash`| String |Le hachage de code n’est inclus dans un jeton d’ID que si ce dernier est émis avec un code d’autorisation OAuth 2.0. Il peut servir à valider l’authenticité d’un code d’autorisation. Pour plus d'informations sur l'exécution de cette validation, consultez la [spécification OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html#HybridIDToken). |
|`at_hash`| String |Le hachage de jeton d’accès n’est inclus dans un jeton d’ID que si ce dernier est émis du point de terminaison `/authorize` avec un jeton d’accès OAuth 2.0. Il peut servir à valider l’authenticité d’un jeton d’accès. Pour plus d'informations sur l'exécution de cette validation, consultez la [spécification OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html#HybridIDToken). Cela n’est pas retourné sur les jetons d’ID du point de terminaison `/token`. |
|`aio` | Chaîne opaque | Revendication interne utilisée par Azure AD pour enregistrer des données afin de réutiliser les jetons. Cette valeur doit être ignorée.|
|`preferred_username` | String | Nom d’utilisateur principal qui représente l’utilisateur. Il peut s’agir d’une adresse e-mail, d’un numéro de téléphone ou d’un nom d’utilisateur générique sans format spécifié. Sa valeur est mutable et peut changer au fil du temps. Dans la mesure où cette valeur peut être modifiée, elle ne doit pas utilisée pour prendre des décisions d'autorisation. L’étendue `profile` est requise afin de recevoir cette revendication.|
|`email` | String | La revendication `email` est présente par défaut pour les comptes invités qui disposent d’une adresse de messagerie.  Votre application peut demander la revendication de courrier électronique pour les utilisateurs gérés (provenant du même locataire que la ressource) à l’aide de la `email` [revendication facultative](active-directory-optional-claims.md).  Sur le point de terminaison v2.0, votre application peut également demander l’étendue OpenID Connect `email` : vous n’avez pas besoin de demander la revendication facultative et l’étendue pour obtenir la revendication.  La revendication de courrier électronique prend uniquement en charge la messagerie adressable provenant des informations du profil de l’utilisateur. |
|`name` | String | La revendication `name` fournit une valeur explicite qui identifie le sujet du jeton. Il n'est pas certain que cette valeur soit unique. Elle peut être modifiée et elle est conçue pour être utilisée uniquement à des fins d'affichage. L’étendue `profile` est requise afin de recevoir cette revendication. |
|`nonce`| String | La valeur à usage unique correspond au paramètre inclus dans la requête /authorize d’origine au point de distribution d’émission. Si ce n’est pas le cas, votre application doit rejeter le jeton. |
|`oid` | Chaîne, GUID | Identificateur immuable pour un objet dans le système d’identité Microsoft, dans cet exemple, un compte d’utilisateur. Cet ID identifie de manière unique l’utilisateur entre les applications ; deux applications différentes se connectant au même utilisateur auront la même valeur dans la revendication `oid`. Microsoft Graph renverra cet ID en tant que propriété `id` pour un compte d’utilisateur donné. `oid` permettant à plusieurs applications de faire correspondre des utilisateurs, l’étendue `profile` est requise afin de recevoir cette revendication. Notez que si un utilisateur existe dans plusieurs locataires, l’utilisateur contient un ID d’objet différent dans chaque locataire. Ils sont considérés comme des comptes différents, même si l’utilisateur se connecte à chaque compte avec les mêmes informations d’identification. La revendication `oid` est un GUID qui ne peut pas être réutilisé. |
|`roles`| Tableau de chaînes | Ensemble des rôles attribués à l’utilisateur qui se connecte. |
|`rh` | Chaîne opaque |Revendication interne utilisée par Azure pour revalider des jetons. Cette valeur doit être ignorée. |
|`sub` | String | Principal sur lequel portent les assertions d’informations du jeton, comme l’utilisateur d’une application. Cette valeur est immuable et ne peut pas être réattribuée ou réutilisée. L’objet est un identificateur par paire ; il est spécifique à un ID d’application donné. Si un utilisateur se connecte à deux applications différentes à l’aide de deux ID clients différents, ces applications reçoivent deux valeurs différentes pour la revendication de l’objet. Ceci peut être souhaitable ou non en fonction de vos exigences en matière d’architecture et de confidentialité. |
|`tid` | Chaîne, GUID | Représente le locataire auquel l’utilisateur se connecte. Pour les comptes professionnels et scolaires, le GUID correspond à l’ID de locataire immuable de l’organisation à laquelle l’utilisateur se connecte. Pour les connexions au locataire de compte Microsoft personnel (services tels que Xbox, Teams à usage personnel ou Outlook), la valeur est `9188040d-6c67-4c5b-b112-36a304b66dad`. Pour recevoir cette revendication, votre application doit demander l’étendue `profile`. |
|`unique_name` | String | Fournit une valeur contrôlable de visu qui identifie le sujet du jeton. Cette valeur est unique à un moment donné mais, comme les e-mails et autres identificateurs peuvent être réutilisés, elle peut réapparaître sur d'autres comptes. Par conséquent, elle ne doit être utilisée qu'à des fins d'affichage. Émise uniquement dans les jetons `id_tokens` v1.0. |
|`uti` | Chaîne opaque | Revendication interne utilisée par Azure pour revalider des jetons. Cette valeur doit être ignorée. |
|`ver` | Chaîne, 1.0 ou 2.0 | Indique la version du jeton id_token. |
|`hasgroups`|Boolean|Le cas échéant, toujours true, ce qui indique que l’utilisateur appartient à au moins un groupe. Utilisé à la place de la revendication des groupes pour les JWT dans les flux d’octroi implicites si la revendication des groupes complets étend le fragment URI au-delà des limites de longueur d’URL (actuellement, 6 groupes ou plus). Indique que le client doit utiliser l’API Microsoft Graph pour déterminer les groupes de l’utilisateur (`https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects`).|
|`groups:src1`|Objet JSON | Pour les demandes de jetons dont la longueur n'est pas limitée (voir `hasgroups` ci-dessus) mais qui restent trop volumineuses pour le jeton, un lien vers la liste complète des groupes de l'utilisateur sera inclus. Pour les jetons JWT en tant que revendication distribuée, pour SAML en tant que nouvelle revendication à la place de la revendication `groups`. <br><br>**Exemple de valeur JWT** : <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }`<br><br> Pour plus d’informations, consultez [Revendication de dépassement des groupes](#groups-overage-claim).|

### <a name="using-claims-to-reliably-identify-a-user-subject-and-object-id"></a>Utilisation de revendications pour identifier de manière fiable un utilisateur (Objet et ID d’objet)

Lorsque vous identifiez un utilisateur (par exemple, en le recherchant dans une base de données ou en déterminant les autorisations qui lui sont attribuées), il est essentiel d’utiliser des informations qui resteront constantes et uniques au fil du temps. Les applications héritées utilisent parfois des champs tels que l’adresse e-mail, un numéro de téléphone ou l’UPN.  Tous ces éléments peuvent évoluer et être réutilisés au fil du temps. Par exemple, lorsqu'un employé modifie son nom ou reçoit une adresse e-mail correspondant à celle d'un employé précédent qui n'est plus présent. Par conséquent, il est **essentiel** que votre application n'utilise pas de données lisibles par l'homme pour identifier un utilisateur ; « lisible par l'homme » signifie généralement que quelqu'un le lira et voudra le modifier. Utilisez plutôt les revendications fournies par la norme OIDC, ou les revendications d’extension fournies par Microsoft (revendications `sub` et `oid`).

Pour stocker correctement les informations par utilisateur, utilisez `sub` ou `oid` seul (qui, comme des GUID sont uniques), avec `tid` utilisé pour le routage ou partitionnement si nécessaire.  Si vous avez besoin de partager des données entre les services, `oid`+`tid` est préférable, car toutes les applications reçoivent les mêmes revendications `oid` et `tid` pour un utilisateur donné agissant dans un locataire donné.  La revendication `sub` dans la plateforme d’identité Microsoft est « par paire ». Elle est unique en fonction d’une combinaison de destinataire, de locataire et d’utilisateur du jeton.  Ainsi, deux applications qui demandent des jetons d'ID pour un utilisateur donné recevront des revendications `sub` différentes, mais les mêmes revendications `oid` pour cet utilisateur.

>[!NOTE]
> N’utilisez pas la revendication `idp` pour stocker des informations sur un utilisateur dans une tentative de mettre en corrélation des utilisateurs parmi les locataires.  Elle ne fonctionnera pas, car, par conception, les revendications `oid` et `sub` pour un utilisateur changent entre locataires pour s’assurer que des applications ne puissent pas suivre des utilisateurs parmi les locataires.  
>
> Les scénarios d’invité, où un utilisateur est hébergé dans un locataire et s’authentifie dans un autre, doivent traiter l’utilisateur comme s’il s’agissait d’un tout nouvel utilisateur du service.  Vos documents et privilèges dans le locataire Contoso ne doivent pas s’appliquer dans le locataire Fabrikam. Cela est important pour éviter les fuites accidentelles de données entre les locataires et l’application des cycles de vie des données.  Évincer un invité d’un locataire doit également supprimer son accès aux données qu’il a créées dans ce locataire. 

### <a name="groups-overage-claim"></a>Revendication de dépassement des groupes
Pour s’assurer que la taille du jeton ne dépasse pas les limites de taille d’en-tête HTTP, Azure AD limite le nombre d’ID d’objets inclus dans la revendication `groups`. Si un utilisateur est membre d’un nombre de groupes supérieur à la limite de dépassement (150 pour les jetons SAML, 200 pour les jetons JWT), Azure AD n’émet pas la revendication des groupes dans le jeton. Au lieu de cela, il inclut une revendication de dépassement dans le jeton qui indique à l’application d’interroger l’API Microsoft Graph pour récupérer l’appartenance de groupe de l’utilisateur.

```json
{
  ...
  "_claim_names": {
   "groups": "src1"
    },
    {
  "_claim_sources": {
    "src1": {
        "endpoint":"[Url to get this user's group membership from]"
        }
       }
     }
  ...
}
```

## <a name="id-token-lifetime"></a>Durée de vie du jeton d’identification

Par défaut, un jeton d'ID est valable pendant une heure. Au bout d'une heure, le client doit en acquérir un nouveau.

Vous pouvez ajuster la durée de vie d'un jeton d'ID pour contrôler la fréquence à laquelle l'application cliente met fin à la session de l'application, ainsi que la fréquence à laquelle elle demande à l'utilisateur de se réauthentifier de manière silencieuse ou interactive. Pour plus d’informations, consultez [Durées de vie des jetons configurables](active-directory-configurable-token-lifetimes.md).

## <a name="validating-an-id-token"></a>Valider un jeton d'ID

La validation d'un jeton d'ID est semblable à la première étape de [validation d'un jeton d'accès](access-tokens.md#validating-tokens). Votre client peut vérifier si le jeton a été falsifié. Il peut également vérifier l'émetteur pour s'assurer que celui qui a renvoyé le jeton est le bon. Étant donné que les jetons d'ID sont toujours des jetons JWT, de nombreuses bibliothèques permettent de les valider. Nous vous recommandons d'utiliser l'une d'entre elles plutôt que de le faire vous-même.  Notez que seuls des clients confidentiels (avec un secret) doivent valider des jetons d’ID.  Les applications publiques (dont le code s'exécute entièrement sur un appareil ou un réseau que vous ne contrôlez pas, comme le navigateur ou le réseau domestique d'un utilisateur) ne bénéficient pas de la validation du jeton d'ID. Cela est dû au fait qu'un utilisateur malveillant peut intercepter et modifier les clés utilisées pour la validation du jeton.

Pour valider manuellement le jeton, consultez les étapes détaillées dans la section [Validation d’un jeton d’accès](access-tokens.md#validating-tokens). Les revendications JWT suivantes doivent être validées dans le jeton d'ID après avoir validé la signature du jeton. Ces revendications peuvent également être validées par votre bibliothèque de validation de jetons :

* Horodatages : les horodatages `iat`, `nbf` et `exp` doivent tous correspondre à un moment avant ou après l’heure actuelle, selon ce qui convient.
* Audience : la revendication `aud` doit correspondre à l’ID de votre application.
* Valeur à usage unique : la revendication `nonce` dans la charge utile doit correspondre au paramètre de valeur à usage unique transmis au point de terminaison /authorize lors de la demande initiale.

## <a name="next-steps"></a>Étapes suivantes

* Examinez le flux [OpenID Connect](v2-protocols-oidc.md), qui définit les protocoles émettant un jeton d’ID. 
* En savoir plus sur les [jetons d’accès](access-tokens.md)
* Personnalisez les revendications JWT de votre jeton d'ID à l'aide de [revendications facultatives](active-directory-optional-claims.md).
