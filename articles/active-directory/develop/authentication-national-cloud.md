---
title: Authentification et clouds nationaux Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Découvrez l’inscription d’application et les points de terminaison d’authentification pour les clouds nationaux.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/03/2021
ms.author: negoe
ms.reviewer: marsma, negoe,celested
ms.custom: aaddev,references_regions
ms.openlocfilehash: bc62e3a4ae32cf4b2d0a63dd15bdab24ac490d1e
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564534"
---
# <a name="national-clouds"></a>Clouds nationaux

Les clouds nationaux sont des instances Azure physiquement isolées. Ces régions d’Azure sont conçues pour garantir que les conditions de résidence, de souveraineté et de conformité des données sont respectées au sein de limites géographiques.

En plus du cloud global Azure, le Répertoire actif Azure (Azure AD) est déployé dans les clouds nationaux suivants :

- Azure Government
- Azure China 21Vianet
- Azure Allemagne ([fermé le 29 octobre 2021](https://www.microsoft.com/cloud-platform/germany-cloud-regions)). En savoir plus sur la [migration d’Azure Allemagne](#azure-germany-microsoft-cloud-deutschland).

Les différents clouds nationaux et le cloud Azure global constituent des _instances_ cloud. Chaque instance cloud est distincte des autres. Elle possède son propre environnement et ses propres _points de terminaison_. Les points de terminaison spécifiques au Cloud incluent le jeton d’accès OAuth 2.0 et les points de terminaison de demande de jeton d’ID OpenID Connect, ainsi que les url de gestion et de déploiement d’applications, comme le portail Azure.

Lorsque vous développez vos applications, utilisez les points de terminaison de l’instance Cloud dans laquelle vous déploierez l’application.

## <a name="app-registration-endpoints"></a>Points de terminaison d’inscription d’application

Il existe un Portail Azure distinct pour chacun des clouds nationaux. Pour intégrer des applications avec la plateforme d’identités Microsoft dans un cloud national, vous devez inscrire votre application séparément dans chaque Portail Azure propre à l’environnement.

Le tableau suivant répertorie les URL de base des points de terminaison Azure AD utilisés pour inscrire une application pour chaque cloud national.

| Cloud national                          | Point de terminaison du portail Azure      |
| --------------------------------------- | -------------------------- |
| Portail Azure pour le gouvernement des États-Unis          | `https://portal.azure.us`  |
| Portail Azure Chine géré par 21Vianet | `https://portal.azure.cn`  |
| Portail Azure (service global)           | `https://portal.azure.com` |

## <a name="application-endpoints"></a>Points de terminaison d’application

Vous pouvez trouver les points de terminaison d’authentification pour votre application dans le portail Azure.

1. Connectez-vous au <a href="https://portal.azure.com/" target="_blank">portail Azure</a>.
1. Sélectionnez **Azure Active Directory**.
1. Sous **Gérer**, sélectionnez **Inscriptions d’applications**, puis sélectionnez **Points de terminaison** dans le menu supérieur.

   La page **Points de terminaison** qui s’affiche présente les points de terminaison d’authentification de l’application inscrite dans votre locataire Azure AD.

   Utilisez le point de terminaison qui correspond au protocole d’authentification que vous utilisez en combinaison avec l’**ID d’application (client)** pour créer la demande d’authentification spécifique à votre application.

## <a name="azure-ad-authentication-endpoints"></a>Points de terminaison d’authentification Azure AD

Tous les clouds nationaux authentifient les utilisateurs séparément dans chaque environnement et ont des points de terminaison d’authentification distincts.

Le tableau suivant répertorie les URL de base des points de terminaison Azure AD utilisés pour obtenir des jetons pour chaque cloud national.

| Cloud national                      | Point de terminaison d’authentification Azure AD           |
| ----------------------------------- | ------------------------------------------ |
| Azure AD pour le gouvernement des États-Unis          | `https://login.microsoftonline.us`         |
| Azure AD Chine géré par 21Vianet | `https://login.partner.microsoftonline.cn` |
| Azure AD (service mondial)           | `https://login.microsoftonline.com`        |

Vous pouvez former des requêtes pour les points de terminaison de jeton ou d’autorisation Azure AD à l’aide de l’URL de base spécifique de la région appropriée. Par exemple, pour Azure global :

- Le point de terminaison d’autorisation commun est `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`.
- Le point de terminaison de jeton commun est `https://login.microsoftonline.com/common/oauth2/v2.0/token`.

Pour les applications à client unique, remplacez « common » dans les URL ci-dessus par l’ID ou le nom de votre locataire. par exemple `https://login.microsoftonline.com/contoso.com`.

## <a name="azure-germany-microsoft-cloud-deutschland"></a>Azure Allemagne (Microsoft Cloud Deutschland)

Si vous n’avez pas migré votre application à partir d’Azure Allemagne, suivez [les informations du Répertoire actif Azure relatives à la migration à partir d’Azure Allemagne](/microsoft-365/enterprise/ms-cloud-germany-transition-azure-ad) pour commencer.

## <a name="microsoft-graph-api"></a>API Microsoft Graph

Pour savoir comment appeler les API Microsoft Graph dans un environnement de cloud national, accédez à [Microsoft Graph in national cloud deployments (Microsoft Graph dans les déploiements sur les clouds nationaux)](/graph/deployments).

Certains services et fonctionnalités du cloud Azure global peuvent ne pas être disponibles dans d’autres instances cloud comme les clouds nationaux.

Pour connaître les services et les fonctionnalités disponibles dans une instance cloud donnée, consultez [Produits disponibles région par région](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Pour savoir comment créer une application à l’aide de la Plateforme d’identités Microsoft, suivez l'[application à page unique (SPA) à l’aide du didacticiel de flux de code auth](tutorial-v2-angular-auth-code.md). Plus précisément, cette application doit connecter un utilisateur et obtenir un jeton d’accès pour appeler l’API Microsoft Graph.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment utiliser la [Bibliothèque d’authentification Microsoft (MSAL) dans un environnement cloud national](msal-national-cloud.md).

Documentation sur le cloud national :

- [Azure Government](../../azure-government/index.yml)
- [Azure China 21Vianet](/azure/china/)
- [Azure Allemagne (fermé le 29 octobre 2021)](../../germany/index.yml)
