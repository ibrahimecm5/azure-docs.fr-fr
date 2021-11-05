---
title: Fédération des identités de charge de travail
titleSuffix: Microsoft identity platform
description: Utilisez la fédération des identités de charge de travail pour accorder des charges de travail qui s’exécutent en dehors d’Azure l’accès à des ressources protégées Azure AD sans utiliser de secrets ou de certificats. Ceci élimine la nécessité pour les développeurs de stocker et de gérer des secrets ou certificats à long terme en dehors d’Azure.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/20/2021
ms.author: ryanwi
ms.reviewer: keyam, udayh, vakarand
ms.custom: aaddev
ms.openlocfilehash: ff1161e41965c92b469372038c4ba0cd602ee074
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096947"
---
# <a name="workload-identity-federation-preview"></a>Fédération des identités de charge de travail (préversion)
Cet article fournit une vue d’ensemble de la fédération des identités pour les charges de travail logicielles. L’utilisation de la fédération des identités de charge de travail vous permet d’accéder à des ressources protégées par Azure Active Directory (Azure AD) sans devoir gérer des secrets (pour les scénarios pris en charge).

Vous pouvez utiliser la fédération des identités de charge de travail dans les scénarios comme GitHub Actions.

## <a name="why-use-workload-identity-federation"></a>Pourquoi utiliser la fédération des identités de charge de travail ?

En général, une charge de travail logicielle (comme une application, un service, un script ou une application basée sur des conteneurs) a besoin d’une identité pour s’authentifier, et pour accéder à des ressources ou communiquer avec d’autres services.  Quand ces charges de travail s’exécutent sur Azure, vous pouvez utiliser des identités managées, et la plateforme Azure gère les informations d’identification pour vous.  Pour une charge de travail logicielle exécutée en dehors d’Azure, vous devez utiliser les informations d’identification de l’application (un secret ou un certificat) pour accéder à des ressources protégées par Azure AD (par exemple Azure, Microsoft Graph, Microsoft 365 ou des ressources de tiers).  Ces informations d’identification présentent un risque de sécurité, et doivent être stockées de façon sécurisée et faire régulièrement l’objet de rotations. Vous courez également le risque d’un arrêt du service si les informations d’identification expirent.

Vous utilisez la fédération des identités de charge de travail pour configurer une inscription d’application Azure AD afin d’approuver des jetons provenant d’un fournisseur d’identité externe, comme GitHub.  Une fois cette relation de confiance créée, votre charge de travail logicielle peut échanger des jetons approuvés provenant du fournisseur d’identité externe pour des jetons d’accès de la plateforme d’identités Microsoft.  Votre charge de travail logicielle utilise ensuite ce jeton d’accès pour accéder aux ressources protégées par Azure AD auxquelles l’accès a été accordé à la charge de travail. Ceci élimine la charge en maintenance pour gérer manuellement les informations d’identification, et élimine aussi le risque de fuite de secrets ou d’expiration des certificats.

## <a name="supported-scenarios"></a>Scénarios pris en charge

Les scénarios suivants sont pris en charge pour l’accès aux ressources protégées par Azure AD en utilisant la fédération des identités de charge de travail :

- GitHub Actions. Tout d’abord, [configurez une relation d’approbation](workload-identity-federation-create-trust-github.md) entre votre application dans Azure AD et un dépôt GitHub dans le portail Azure ou en utilisant Microsoft Graph. Ensuite, [configurez un workflow GitHub Actions](/azure/developer/github/connect-from-azure) pour obtenir un jeton d’accès du fournisseur d’identité Microsoft et accéder aux ressources Azure.
- Charges de travail exécutées dans des plateformes de calcul en dehors d’Azure. [Configurez une relation d’approbation](workload-identity-federation-create-trust.md) entre l’inscription de votre application Azure AD et le fournisseur d’identité externe pour votre plateforme de calcul. Vous pouvez utiliser des jetons émis par cette plateforme pour vous authentifier auprès de la plateforme d’identités Microsoft et appeler des API dans l’écosystème Microsoft. Utilisez le [flux des informations d’identification du client](v2-oauth2-client-creds-grant-flow.md#third-case-access-token-request-with-a-federated-credential) pour obtenir un jeton d’accès auprès de la plateforme d’identités Microsoft, en passant le jeton JWT du fournisseur d’identité au lieu d’en créer un vous-même en utilisant un certificat stocké.

## <a name="how-it-works"></a>Fonctionnement
Créez une relation d’approbation entre le fournisseur d’identité externe et une application dans Azure AD en configurant des [informations d’identification d’identité fédérée](/graph/api/resources/federatedidentitycredentials-overview?view=graph-rest-beta&preserve-view=true). Les informations d’identification d’identité fédérée sont utilisées pour indiquer quel jeton du fournisseur d’identité externe doit être approuvé par votre application. Vous configurez les informations d’identification d’identité fédérée sur l’inscription de votre application dans le portail Azure ou via Microsoft Graph.  Les étapes de configuration de la relation d’approbation diffèrent selon le scénario et le fournisseur d’identité externe.

Le workflow pour l’échange d’un jeton externe contre un jeton d’accès est cependant identique pour tous les scénarios. Le diagramme suivant montre le flux de travail général d’une charge de travail qui échange un jeton externe contre un jeton d’accès, puis accède à des ressources protégées par Azure AD.

:::image type="content" source="media/workload-identity-federation/workflow.svg" alt-text="Montre un jeton externe échangé contre un jeton d’accès et l’accès à Azure" border="false":::

1. La charge de travail externe (par exemple un workflow GitHub Actions) demande un jeton auprès du fournisseur d’identité externe (par exemple GitHub).
1. Le fournisseur d’identité externe envoie un jeton à la charge de travail externe.
1. La charge de travail externe (par exemple l’action de connexion dans un workflow GitHub) [envoie le jeton à la plateforme d’identités Microsoft](v2-oauth2-client-creds-grant-flow.md#third-case-access-token-request-with-a-federated-credential) et demande un jeton d’accès.
1. La plateforme d’identités Microsoft vérifie la [relation d’approbation](workload-identity-federation-create-trust.md) sur l’inscription d’application et valide le jeton externe par rapport à l’URL de l’émetteur OIDC (OpenID Connect) sur le fournisseur d’identité externe.
1. Quand les vérifications sont satisfaites, la plateforme d’identités Microsoft émet un jeton d’accès à destination de la charge de travail externe.
1. La charge de travail externe accède aux ressources protégées par Azure AD en utilisant le jeton d’accès provenant de la plateforme d’identités Microsoft. Par exemple, un workflow GitHub Actions utilise le jeton d’accès pour publier une application web sur Azure App Service.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur le fonctionnement de la fédération des identités de charge de travail :
- Comment Azure AD utilise l’[octroi d’informations d’identification du client OAuth 2.0](v2-oauth2-client-creds-grant-flow.md#third-case-access-token-request-with-a-federated-credential) et une assertion du client émise par un autre fournisseur d’identité pour obtenir un jeton.
- Comment créer, supprimer, obtenir ou mettre à jour les [informations d’identification d’identité fédérée](/graph/api/resources/federatedidentitycredentials-overview?view=graph-rest-beta&preserve-view=true) sur une inscription d’application en utilisant Microsoft Graph.
- Lisez la [documentation de GitHub Actions](https://docs.github.com/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-azure) pour en savoir plus sur la configuration de votre workflow GitHub Actions afin d’obtenir un jeton d’accès auprès du fournisseur d’identité Microsoft et d’accéder à des ressources Azure.
