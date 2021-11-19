---
title: configuration de l’identité Azure Active Directory pour le service FHIR des api Healthcare
description: Découvrez les principes de l’identité, de l’authentification et de l’autorisation pour le service FHIR
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: cavoeg
ms.openlocfilehash: 43d206c30bd4a3ee043dab3d96247c010f4cdc92
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814578"
---
# <a name="azure-active-directory-identity-configuration-for-fhir-service"></a>configuration de l’identité Azure Active Directory pour le service FHIR

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Lorsque vous utilisez des données de santé, il est important de s’assurer que les données sont sécurisées et qu’elles ne sont pas accessibles par des utilisateurs ou des applications non autorisés. Les serveurs FHIR utilisent [OAuth 2.0](https://oauth.net/2/) pour garantir la sécurité des données. le service FHIR dans les api de santé Azure (par exemple, le service FHIR) est sécurisé à l’aide de [Azure Active Directory](../../active-directory/index.yml), qui est un exemple de fournisseur d’identité OAuth 2,0. Cet article fournit une vue d’ensemble du principe d’autorisation relatif à un serveur FHIR ainsi que les étapes nécessaires à l’obtention d’un jeton d’accès à un serveur FHIR. bien que ces étapes s’appliquent à n’importe quel serveur FHIR et n’importe quel fournisseur d’identité, nous allons passer en revue le service FHIR et Azure Active Directory (Azure AD) en tant que fournisseur d’identité dans cet article.

## <a name="access-control-overview"></a>Présentation du contrôle d’accès

Pour qu’une application cliente puisse accéder au service FHIR, elle doit présenter un jeton d’accès. Le jeton d’accès est une collection de propriétés (revendications) encodée au format [Base64](https://en.wikipedia.org/wiki/Base64) et signée, qui transmet des informations sur l’identité du client ainsi que les rôles et privilèges accordés au client.

Il existe de nombreuses façons d’obtenir un jeton, mais le service FHIR ne se soucie pas de la façon dont le jeton est obtenu tant qu’il s’agit d’un jeton correctement signé avec les revendications appropriées. 

Si nous prenons l’exemple du [flux du code d’autorisation](../../active-directory/azuread-dev/v1-protocols-oauth-code.md), l’accès à un serveur FHIR passe par les quatre étapes ci-dessous :

![Autorisation FHIR](media/azure-active-directory-fhir-service/fhir-authorization.png)

1. Le client envoie une requête au point de terminaison `/authorize` d’Azure AD. Azure AD redirige le client vers une page de connexion où l’utilisateur s’authentifie à l’aide des informations d’identification appropriées (par exemple un nom d’utilisateur et un mot de passe, ou une authentification à 2 facteurs). Pour plus d’informations, consultez les détails relatifs à l’[obtention d’un code d’autorisation](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code). Une fois l’authentification réussie, un *code d’autorisation* est retourné au client. Azure AD autorise uniquement le retour de ce code d’autorisation à une URL de réponse inscrite, configurée au moment de l’inscription de l’application cliente (voir ci-dessous).
1. L’application cliente échange le code d’autorisation contre un *jeton d’accès* au niveau du point de terminaison `/token` d’Azure AD. Au moment où elle demande un jeton, l’application cliente peut être amenée à fournir un secret client (mot de passe de l’application). Pour plus d’informations, consultez les détails relatifs à l’[obtention d’un jeton d’accès](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).
1. Le client envoie une requête au service FHIR, par exemple `GET /Patient` pour rechercher tous les patients. Au moment où il effectue la requête, il inclut le jeton d’accès dans un en-tête de requête HTTP, par exemple `Authorization: Bearer eyJ0e...`, où `eyJ0e...` représente le jeton d’accès encodé au format Base64.
1. Le service FHIR vérifie que le jeton contient des revendications appropriées (propriétés dans le jeton). Si tout est correct, la requête est soumise et un bundle FHIR est retourné avec les résultats appropriés au client.

Il est important de noter que le service FHIR n’est pas impliqué dans la validation des informations d’identification de l’utilisateur et qu’il n’émet pas le jeton. L’authentification et la création de jeton sont effectuées par Azure AD. Le service FHIR valide simplement que le jeton est signé correctement (il est authentique) et qu’il possède les revendications appropriées.

## <a name="structure-of-an-access-token"></a>Structure d’un jeton d’accès

Le développement d’applications FHIR implique souvent le débogage des problèmes d’accès. Si un client se voit refuser l’accès au service FHIR, il est utile de comprendre la structure du jeton d’accès et la façon dont il peut être décodé pour inspecter le contenu (les revendications) du jeton. 

Les serveurs FHIR attendent généralement un [JSON Web Token](https://en.wikipedia.org/wiki/JSON_Web_Token) (JWT, parfois prononcé « jot »). Il se compose de trois parties :

**Partie 1**: en-tête, qui peut se présenter comme suit :
```json
    {
      "alg": "HS256",
      "typ": "JWT"
    }
```

**Partie 2**: la charge utile (les revendications), par exemple :
```json
    {
     "oid": "123",
     "iss": "https://issuerurl",
     "iat": 1422779638,
     "roles": [
        "admin"
      ]
    }
```

**Partie 3**: signature, calculée en concaténant le contenu encodé en base64 de l’en-tête et la charge utile et en calculant un hachage de chiffrement de ces contenus en fonction de l’algorithme ( `alg` ) spécifié dans l’en-tête. Un serveur peut obtenir des clés publiques auprès du fournisseur d’identité et vérifier que le jeton a été émis par un fournisseur d’identité spécifique, et qu’il n’a pas été falsifié.

Le jeton complet se compose des versions encodées au format Base64 (en fait encodées en tant qu’URL au format Base64) de ces trois segments. Les trois segments sont concaténés et séparés par un `.` (point).

Voici un exemple de jeton ci-dessous :

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOiIxMjMiLCAiaXNzIjoiaHR0cHM6Ly9pc3N1ZXJ1cmwiLCJpYXQiOjE0MjI3Nzk2MzgsInJvbGVzIjpbImFkbWluIl19.gzSraSYS8EXBxLN_oWnFSRgCzcmJmMjLiuyu5CSpyHI
```

Le jeton peut être décodé et inspecté à l’aide d’outils tels que [https://jwt.ms](https://jwt.ms). Le résultat du décodage du jeton est le suivant :

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "oid": "123",
  "iss": "https://issuerurl",
  "iat": 1422779638,
  "roles": [
    "admin"
  ]
}.[Signature]
```

## <a name="obtaining-an-access-token"></a>Obtention d’un jeton d’accès

Comme indiqué ci-dessus, il existe plusieurs façons d’obtenir un jeton auprès d’Azure AD. Elles sont décrites en détail dans la [documentation destinée aux développeurs Azure AD](../../active-directory/develop/index.yml).

Azure AD comporte deux versions distinctes des points de terminaison OAuth 2.0, appelés `v1.0` et `v2.0`. Ces deux versions sont des points de terminaison OAuth 2.0. Les désignations `v1.0` et `v2.0` font référence aux différentes façons dont Azure AD implémente cette norme. 

Quand vous utilisez un serveur FHIR, vous pouvez vous servir des points de terminaison `v1.0` ou `v2.0`. Le choix peut dépendre des bibliothèques d’authentification que vous utilisez dans votre application cliente.

Les sections pertinentes de la documentation Azure AD sont les suivantes :

* Point de terminaison `v1.0` :
    * [Flux du code d’autorisation](../../active-directory/azuread-dev/v1-protocols-oauth-code.md).
    * [Flux des informations d’identification du client](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md).
* Point de terminaison `v2.0` :
    * [Flux du code d’autorisation](../../active-directory/develop/v2-oauth2-auth-code-flow.md).
    * [Flux des informations d’identification du client](../../active-directory/develop/v2-oauth2-client-creds-grant-flow.md).

Il existe d’autres variantes (par exemple le flux On-Behalf-Of) pour obtenir un jeton. Pour plus d’informations, consultez la documentation Azure AD. Lorsque vous utilisez le service FHIR, il existe également des raccourcis pour l’obtention d’un jeton d’accès (à des fins de débogage) [à l’aide de l’Azure CLI](get-healthcare-apis-access-token-cli.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris certains des concepts de base liés à la sécurisation de l’accès au service FHIR à l’aide de Azure AD. Pour plus d’informations sur le déploiement du service FHIR, consultez.

>[!div class="nextstepaction"]
>[Déployer le service FHIR](fhir-portal-quickstart.md)
