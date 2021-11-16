---
title: Authentification et autorisation dans votre application
description: Vue d’ensemble de l’utilisation de l’authentification et de l’autorisation avec un service Relais Azure Fluid.
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/build/auth/
ms.openlocfilehash: e2f24c5455548980318c4536b5c65f84ea6a7dfb
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893820"
---
# <a name="authentication-and-authorization-in-your-app"></a>Authentification et autorisation dans votre application

> [!NOTE]
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.

La sécurité est essentielle pour les applications web modernes. L’infrastructure Fluid, dans le cadre de l’architecture de votre application web, est un élément important de l’infrastructure à sécuriser. L’infrastructure Fluid est une architecture multicouches. Les concepts liés à l’authentification sont implémentés en fonction du service Fluid auquel elle se connecte. Cela signifie que les détails et les spécificités diffèrent pour chaque service bien qu’il existe des thèmes d’authentification communs à l’ensemble des services Fluid.

## <a name="azure-fluid-relay-service"></a>Service Relais Azure Fluid

Chaque locataire du service Relais Azure Fluid que vous créez se voit attribuer un **ID de locataire** et sa propre **clé secrète de locataire** unique.

La clé secrète est un **secret partagé**. Votre application/service la connaît, et le service Relais Azure Fluid la connaît également. Comme la clé secrète du locataire est liée de manière unique à votre locataire, son utilisation garantit au service Relais Azure Fluid que les requêtes proviennent d’un utilisateur autorisé du locataire.

La clé secrète permet au service Relais Azure Fluid de savoir que les requêtes proviennent de votre application ou de votre service. Ce point est essentiel, car une fois que le service Relais Azure Fluid a la garantie que c’est *votre application* qui effectue les requêtes, il peut faire confiance aux données que vous envoyez. C’est également pour cette raison qu’il est important que le secret soit géré en toute sécurité.

> [!CAUTION]
> Toute personne ayant accès au secret peut emprunter l’identité de votre application lors de la communication avec le service Relais Azure Fluid.

## <a name="json-web-tokens-and-azure-fluid-relay-service"></a>Jetons web JSON et service Relais Azure Fluid

Le service Relais Azure Fluid utilise des [jetons web JSON (JWT)](https://jwt.io/) pour encoder et vérifier les données signées avec votre clé secrète. Les jetons web JSON correspondent à un élément JSON signé qui peut inclure des informations supplémentaires sur les droits et les autorisations.

> [!NOTE]
> Les spécificités des jetons JWT ne sont pas traitées dans cet article. Pour plus d’informations sur les jetons JWT, consultez <https://jwt.io/introduction>.

Bien que les détails de l’authentification diffèrent entre les services Fluid, plusieurs valeurs doivent toujours être présentes.

- **containerId** : le service Fluid a besoin de l’ID de conteneur pour identifier le service qui correspond au conteneur appelant. *Remarque* : JWT appelle ce champ documentId, mais le service Fluid attend un ID de conteneur dans ce champ.
- **tenantId** : le service Relais Azure Fluid utilise l’ID de locataire pour récupérer le secret partagé qu’il utilise pour authentifier votre requête. 
- **Étendues** : les étendues définissent les autorisations du conteneur appelant. Le contenu du champ Étendues est flexible, ce qui vous permet de créer vos propres autorisations personnalisées.

```json {linenos=inline,hl_lines=["5-6",13]}
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "documentId": "azureFluidDocumentId",
  "scopes": [ "doc:read", "doc:write", "summary:write" ],
  "user": {
    "name": "TestUser",
    "id": "Test-Id-123"
  },
  "iat": 1599098963,
  "exp": 1599098963,
  "tenantId": "AzureFluidTenantId",
  "ver": "1.0"
}.[Signature]
```

Le mode de l’utilisateur indique si la connexion est en mode lecture ou lecture/écriture. Vous pouvez voir cela dans le champ `connections` dans `AzureAudience`. Les autorisations d’étendue de jeton peuvent être mises à jour dans votre fonction Azure sans serveur sous la fonction `generateToken`.

```ts
const token = generateToken(
  tenantId,
  documentId,
  key,
  scopes ?? [ "Token Scope" ],
  user
);
```

Les étendues de jeton, ainsi que le comportement et les modes de conteneur sont les suivants :

| Étendue de jeton | Comportement de mon document | Comportement du document d’audience | 
|-------------|----------------------|----------------------------|
| DocRead     | Lecture et écriture du document. Les modifications apportées au document ne sont reflétées dans aucun autre document d’audience. <br /> Mode : lecture | Lecture et écriture de document. Les modifications ne sont reflétées dans aucun autre document d’audience. <br /> Mode : écriture | 
| DocWrite    | Lecture et écriture du document. Les modifications apportées sont reflétées dans tous les autres document d’audience. <br />Mode : écriture | Lecture et écriture du document. Les modifications apportées sont reflétées dans tous les autres document d’audience. <br />Mode : écriture |
| DocRead, DocWrite | Lecture et écriture du document. Les modifications apportées sont reflétées dans tous les autres document d’audience. <br />Mode : écriture | Lecture et écriture du document. Les modifications apportées sont reflétées dans tous les autres document d’audience. <br />Mode : écriture |

> [!NOTE]
> Notez que le jeton comprend également des informations sur l’utilisateur (voir les lignes 7 à 9 ci-dessus). Vous pouvez l’utiliser pour augmenter les informations utilisateur qui sont automatiquement disponibles pour le code Fluid à l’aide de la fonctionnalité [audience](../how-tos/connect-fluid-azure-service.md#getting-audience-details). Pour plus d’informations, consultez [Ajout de données personnalisées à des jetons](../how-tos/connect-fluid-azure-service.md#adding-custom-data-to-tokens).

## <a name="the-token-provider"></a>Fournisseur de jetons

Chaque requête adressée à Relais Azure Fluid doit être signée avec un jeton JWT valide. Fluid délègue la responsabilité de la création et de la signature de ces jetons aux fournisseurs de jetons.

Un fournisseur de jetons est chargé de créer et de signer des jetons utilisés par `@fluidframework/azure-client` pour effectuer des requêtes auprès du service Relais Azure Fluid. Vous devez fournir votre propre implémentation de fournisseur de jetons sécurisés. Toutefois, Fluid fournit un `InsecureTokenProvider` qui accepte votre secret de locataire, puis génère et retourne localement un jeton signé. Ce fournisseur de jetons est utile pour les tests, mais il ne peut pas être utilisé dans les scénarios de production.

### <a name="a-secure-serverless-token-provider"></a>Fournisseur de jetons serverless sécurisé

Pour créer un fournisseur de jetons sécurisés, une option consiste à créer une fonction Azure serverless et à l’exposer en tant que fournisseur de jetons. Cela vous permet de stocker la *clé secrète du locataire* sur un serveur sécurisé. Votre application appelle ensuite la fonction Azure pour générer des jetons au lieu de les signer localement, comme le fait `InsecureTokenProvider`. Pour plus d’informations, consultez [Guide pratique : Écrire un TokenProvider avec une fonction Azure](../how-tos/azure-function-token-provider.md).

## <a name="connecting-user-auth-to-fluid-service-auth"></a>Connexion de l’authentification utilisateur à l’authentification du service Fluid

Les services Fluid authentifient les appels entrants à l’aide d’une clé secrète cliente partagée, qui n’est pas liée à un utilisateur spécifique. L’authentification utilisateur peut être ajoutée en fonction des détails de votre service Fluid. 

Une option facile pour l’authentification de l’utilisateur consiste à utiliser simplement une [fonction Azure](../../azure-functions/index.yml) comme fournisseur de jetons et à appliquer l’authentification de l’utilisateur comme condition d’obtention d’un jeton. Si une application tente d’appeler la fonction, elle échoue, sauf si elle est authentifiée auprès de votre système d’authentification. Si vous utilisez Azure Active Directory (Azure AD) par exemple, vous pouvez créer une application Azure AD pour votre fonction Azure et l’associer au système d’authentification de votre organisation.

Dans ce cas, l’utilisateur se connecte à votre application à l’aide d’Azure AD, ce qui vous permet d’obtenir un jeton à utiliser pour appeler votre fonction Azure. La fonction Azure elle-même se comporte de la même façon, mais elle est désormais accessible uniquement aux personnes qui se sont également authentifiées auprès d’Azure AD.

Étant donné que la fonction Azure est désormais votre point d’entrée pour obtenir un jeton valide, seuls les utilisateurs s’étant correctement authentifiés auprès de la fonction sont en mesure de fournir ce jeton au service Relais Azure Fluid à partir de leur application cliente. Cette approche en deux étapes vous permet d’utiliser votre propre processus d’authentification personnalisé conjointement avec le service Relais Azure Fluid.

## <a name="see-also"></a>Voir aussi

- [Introduction aux jetons JWT](https://jwt.io/introduction)
- [Comment créer un jeton JSON Web Token](../how-tos/fluid-json-web-token.md)
- [Revendications de charge utile dans Relais Azure Fluid](../how-tos/fluid-json-web-token.md#payload-claims)
