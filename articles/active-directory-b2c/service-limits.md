---
title: Restrictions et limites du service Azure AD B2C
titleSuffix: Azure AD B2C
description: Référence pour les restrictions et limites de service pour le service Azure AD B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/02/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 8e3ceab429f92340a080a6a42afd095375ce51b1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130266084"
---
# <a name="azure-active-directory-b2c-service-limits-and-restrictions"></a>Restrictions et limites du service Azure Active Directory B2C

Cet article présente les contraintes d’utilisation et autres limites de service pour le service Azure Active Directory B2C (Azure AD B2C).

## <a name="end-userconsumption-related-limits"></a>Limites relatives à l’utilisateur final/à la consommation

Les limites de service suivantes relatives à l’utilisateur final s’appliquent à tous les protocoles d’authentification et d’autorisation pris en charge par Azure AD B2C, notamment SAML, OpenID Connect, OAuth2 et ROPC.

|Category |Limite    |
|---------|---------|
|Nombre de requêtes par adresse IP par locataire Azure AD B2C       |6 000/5 min          |
|Nombre total de requêtes par locataire Azure AD B2C     |12 000/min          |

Le nombre de requêtes peut varier en fonction du nombre de lectures et d’écritures de répertoires qui se produisent pendant le parcours utilisateur Azure AD B2C. Par exemple, un parcours de connexion simple qui lit à partir du répertoire comprend une requête. Si le parcours de connexion doit également mettre à jour le répertoire, cette opération est comptabilisée comme une requête supplémentaire.

## <a name="azure-ad-b2c-configuration-limits"></a>Limites de configuration d’Azure AD B2C

Le tableau suivant répertorie les limites de configuration administrative dans le service Azure AD B2C.

|Category  |Limite  |
|---------|---------|
|Nombre d’étendues par application        |1 000          |
|Nombre d’[attributs personnalisés](user-profile-attributes.md#extension-attributes) par utilisateur<sup>1</sup>       |100         |
|Nombre d’URL de redirection par application       |100         |
|Nombre d’URL de déconnexion par application        |1          |
|Limite de chaîne par attribut      |250 caractères          |
|Nombre de locataires B2C par abonnement      |20         |
|Niveaux d’[héritage](custom-policy-overview.md#inheritance-model) dans les stratégies personnalisées     |10         |
|Nombre de stratégies par locataire Azure AD B2C (flux utilisateur + stratégies personnalisées)     |200          |
|Taille maximale du fichier de stratégie      |1 024 Ko          |

<sup>1</sup> Voir également [Restrictions et limites du service Azure AD](../active-directory/enterprise-users/directory-service-limits-restrictions.md).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur l’[aide à la limitation de Microsoft Graph](/graph/throttling) 
- En savoir plus sur les [différences de validation pour les applications Azure AD B2C](../active-directory/develop/supported-accounts-validation.md)
