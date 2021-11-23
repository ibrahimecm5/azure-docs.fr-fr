---
title: Identity Protection et accès conditionnel dans Azure AD B2C
description: Découvrez comment Identity Protection offre une visibilité quant aux connexions risquées et aux détections des risques. Découvrez comment l’accès conditionnel vous permet d’appliquer des stratégies organisationnelles basées sur des événements à risque dans vos locataires Azure AD B2C.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 05/28/2021
ms.author: kengaderdus
author: kengaderdus
manager: CelesteDG
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c5c7b8ed515fc3148f42b06c3c81f6060816e79
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132281378"
---
# <a name="identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Identity Protection et accès conditionnel pour Azure AD B2C

Améliorez la sécurité d’Azure Active Directory B2C (Azure AD B2C) avec Azure AD Identity Protection et l’accès conditionnel. Les fonctionnalités de détection des risques Identity Protection, y compris les utilisateurs à risque et les connexions risquées, sont automatiquement détectées et affichées dans votre locataire Azure AD B2C. Vous pouvez créer des stratégies d’accès conditionnel qui utilisent ces détections des risques afin de déterminer les actions à effectuer et d’appliquer des stratégies organisationnelles. Ensemble, ces fonctionnalités permettent aux propriétaires d’applications Azure AD B2C de mieux contrôler les authentifications risquées et les stratégies d’accès.
  
Si vous connaissez déjà [Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) et l’[accès conditionnel](../active-directory/conditional-access/overview.md) dans Azure AD, l’utilisation de ces fonctionnalités avec Azure AD B2C sera une expérience familière, avec les différences mineures abordées dans cet article.

![Accès conditionnel dans un locataire B2C](media/conditional-access-identity-protection-overview/conditional-access-b2c.png)

> [!NOTE]
> Azure AD B2C **Premium P2** est nécessaire pour créer des stratégies de connexion risquée. Les locataires **Premium P1** peuvent créer une stratégie basée sur l’emplacement, l’application, des stratégies basées sur l’utilisateur ou le groupe. Pour plus d’informations, consultez [Modifier votre niveau tarifaire Azure AD B2C](billing.md#change-your-azure-ad-pricing-tier).

## <a name="benefits-of-identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Avantages d’Identity Protection et de l’accès conditionnel pour Azure AD B2C

En associant des stratégies d’accès conditionnel à la détection des risques Identity Protection, vous pouvez répondre aux authentifications risquées avec l’action de stratégie appropriée.

- **Bénéficiez d’un nouveau niveau de visibilité quant aux risques d’authentification pour vos applications et votre base de clients**. Avec les signaux de milliards d’authentifications mensuelles auprès d’Azure AD et des comptes Microsoft, les algorithmes de détection des risques marquent désormais les authentifications comme présentant un risque faible, moyen ou élevé pour vos authentifications de citoyen ou de consommateur local.
- **Gérez automatiquement les risques en configurant votre propre authentification adaptative**. Pour les applications spécifiées, vous pouvez imposer à un ensemble spécifique d’utilisateurs de fournir un second facteur d’authentification, comme dans MFA (Multi-Factor Authentication). Vous pouvez également bloquer l’accès en fonction du niveau de risque détecté. Comme avec d’autres expériences Azure AD B2C, vous pouvez personnaliser l’expérience utilisateur final résultante avec la voix, le style et la personnalisation de votre organisation. Vous pouvez également afficher des solutions alternatives d’atténuation si l’utilisateur ne peut pas obtenir l’accès.
- **Contrôlez l’accès en fonction du lieu, des groupes et des applications**.  L’accès conditionnel peut également être utilisé pour contrôler des situations qui ne sont pas basées sur les risques. Par exemple, vous pouvez exiger MFA pour les clients qui accèdent à une application spécifique, ou bloquer l’accès à partir de zones géographiques spécifiées.
- **Tirez parti de l’intégration des flux d’utilisateurs Azure AD B2C et des stratégies personnalisées Identity Experience Framework**. Utilisez vos expériences personnalisées existantes et ajoutez les contrôles dont vous avez besoin pour interagir avec l’accès conditionnel. Vous pouvez également implémenter des scénarios avancés pour accorder l’accès, tels que l’accès basé sur les connaissances ou le fournisseur MFA de votre choix.

## <a name="feature-differences-and-limitations"></a>Différences et limitations des fonctionnalités

Identity Protection et l’accès conditionnel dans Azure AD B2C fonctionnent généralement de la même façon que dans Azure AD, avec les exceptions suivantes :

- Microsoft defender pour le cloud n’est pas disponible dans Azure AD B2C.

- Identity Protection et l’accès conditionnel ne sont pas pris en charge pour les flux de serveur à serveur ROPC dans les locataires Azure AD B2C.

- Dans les locataires Azure AD B2C, les détections des risques Identity Protection sont disponibles uniquement pour les comptes B2C locaux, et non pour les identités sociales telles que Google ou Facebook.

- Dans les locataires Azure AD B2C, un sous-ensemble des détections des risques Identity Protection est disponible. Consultez [Examiner les risques avec Identity Protection](identity-protection-investigate-risk.md)et [Ajouter un accès conditionnel à des flux d’utilisateurs](conditional-access-user-flow.md).

- La fonctionnalité de conformité de l’appareil de l’accès conditionnel n’est pas disponible dans les locataires Azure AD B2C.

## <a name="integrate-conditional-access-with-user-flows-and-custom-policies"></a>Intégrer l’accès conditionnel avec des flux d’utilisateurs et des stratégies personnalisées

Dans Azure AD B2C, vous pouvez déclencher des conditions d’accès conditionnel à partir de flux d’utilisateurs intégrés. Vous pouvez également incorporer l’accès conditionnel dans des stratégies personnalisées. Comme avec d’autres aspects du flux d’utilisateur B2C, la messagerie de l’expérience de l’utilisateur final peut être personnalisée en fonction des alternatives de voix, de marque et d’atténuation de votre organisation. Consultez [Ajouter un accès conditionnel à des flux d’utilisateurs](conditional-access-user-flow.md).

## <a name="microsoft-graph-api"></a>API Microsoft Graph

Vous pouvez également gérer les stratégies d’accès conditionnel dans Azure AD B2C avec l’API Microsoft Graph. Pour plus d’informations, consultez la [documentation relative à l’accès conditionnel](../active-directory/conditional-access/overview.md) et les [opérations Microsoft Graph](microsoft-graph-operations.md#conditional-access).

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter un accès conditionnel à des flux d’utilisateurs](conditional-access-user-flow.md)
- [En savoir plus sur Identity Protection dans Azure AD](../active-directory/identity-protection/overview-identity-protection.md)
- [En savoir plus sur l’accès conditionnel](../active-directory/conditional-access/overview.md)
