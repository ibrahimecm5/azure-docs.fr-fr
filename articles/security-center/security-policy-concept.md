---
title: Comprendre les stratégies de sécurité, les initiatives et les recommandations dans Microsoft Defender pour le cloud
description: Apprenez-en davantage sur les stratégies de sécurité, les initiatives et les recommandations de Microsoft Defender pour le cloud.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 00d73987acd0e5137913872138d6f78ac18e9bdf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131083970"
---
# <a name="what-are-security-policies-initiatives-and-recommendations"></a>Que sont les stratégies, les initiatives et les recommandations de sécurité ?

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender pour le cloud applique des initiatives de sécurité à vos abonnements. Ces initiatives contiennent une ou plusieurs stratégies de sécurité. Chacune de ces stratégies entraîne une recommandation de sécurité pour améliorer votre position de sécurité. Cette page explique en détail chacun de ces concepts.


## <a name="what-is-a-security-policy"></a>Qu’est-ce qu’une stratégie de sécurité ?

Une définition Azure Policy, créée dans Azure Policy, est une règle relative à des conditions de sécurité spécifiques que vous souhaitez contrôler. Les définitions intégrées incluent des éléments tels que le contrôle du type de ressources qui peut être déployé ou le respect des balises sur toutes les ressources. Vous pouvez également créer vos propres définitions de stratégies personnalisées.

Pour implémenter ces définitions de stratégie (définitions intégrées et personnalisées), vous devez les attribuer. Vous pouvez affecter ces stratégies via le portail Azure, PowerShell ou Azure CLI. Les stratégies peuvent être désactivées ou activées à partir d’Azure Policy.

Il existe différents types de stratégies dans Azure Policy. Defender pour le cloud utilise principalement des stratégies « d’audit » qui vérifient des conditions et configurations spécifiques, puis rendent compte de la conformité. Il existe également des stratégies de « respect » qui peuvent être utilisées pour appliquer des paramètres sécurisés.

## <a name="what-is-a-security-initiative"></a>Qu’est-ce qu’une initiative de sécurité ?

Une initiative Azure Policy est une collection de définitions ou de règles Azure Policy regroupées pour atteindre un objectif spécifique. Les initiatives Azure simplifient la gestion de vos stratégies en regroupant un ensemble de stratégies, de manière logique, en tant qu’élément unique.

Une initiative de sécurité définit la configuration souhaitée de vos charges de travail, et vous permet de vous assurer que vous êtes en conformité avec les exigences de sécurité de votre organisation ou des régulateurs.

À l’instar des stratégies de sécurité, les initiatives de Defender pour le cloud sont également créées dans Azure Policy. Vous pouvez utiliser [Azure Policy](../governance/policy/overview.md) pour gérer vos stratégies, créer des initiatives et attribuer des initiatives à plusieurs abonnements ou à des groupes d’administration complets.

L’initiative par défaut attribuée automatiquement à chaque abonnement dans Microsoft Defender pour le cloud est le Benchmark de sécurité Azure. Ce benchmark de sécurité Azure constitue l’ensemble des directives propres à Azure et créées par Microsoft, qui contient les bonnes pratiques de sécurité et de conformité s’inscrivant dans les cadres de conformité courants. Ce point de référence, largement respecté et centré sur le cloud, est basé sur les contrôles du [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) et du [National Institute of Standards and Technology (NIST)](https://www.nist.gov/). Découvrez le [Benchmark de sécurité Azure](/security/benchmark/azure/introduction).

Defender pour le cloud offre les options suivantes pour l’utilisation des initiatives de sécurité :

- **Afficher et modifier l’initiative par défaut intégrée** : lorsque vous activez Defender pour le cloud, l’initiative nommée « Benchmark de sécurité Azure » est automatiquement attribuée à tous les abonnements inscrits auprès de Defender pour le cloud. Pour personnaliser cette initiative, vous pouvez activer ou désactiver des stratégies individuelles au sein des paramètres de stratégies. Pour comprendre les options disponibles prêtes à l’emploi, consultez la liste des [stratégies de sécurité intégrées](./policy-reference.md).

- **Ajouter vos propres initiatives personnalisées** : si vous souhaitez personnaliser les initiatives de sécurité appliquées à votre abonnement, vous pouvez le faire dans Defender pour le cloud. Vous recevez ensuite des recommandations si vos machines ne suivent pas les stratégies que vous créez. Pour obtenir des instructions sur la création et l’attribution de stratégies personnalisées, consultez [Utilisation de stratégies et d’initiatives de sécurité personnalisées](custom-security-policies.md).

- **Ajouter des initiatives de conformité réglementaire** : le tableau de bord de conformité réglementaire de Defender pour le cloud affiche l’état de toutes les évaluations au sein de votre environnement dans le contexte d’une norme ou d’une réglementation particulière (par exemple, Azure CIS, NIST SP 800-53 R4, CSP CSCF-v2020). Pour plus d’informations, voir [Améliorer votre conformité aux normes](regulatory-compliance-dashboard.md).

## <a name="what-is-a-security-recommendation"></a>Qu’est-ce qu’une recommandation de sécurité ?

À l’aide des stratégies, Defender pour le cloud analyse régulièrement l’état de conformité de vos ressources pour identifier les problèmes de configuration de sécurité potentiels et les faiblesses. Il fournit ensuite des recommandations sur la façon de résoudre ces problèmes. Les recommandations sont le résultat de l’évaluation de vos ressources par rapport aux stratégies pertinentes et de l’identification des ressources qui ne répondent pas à vos exigences définies.

Defender pour le cloud émet ses recommandations de sécurité en fonction des initiatives que vous avez choisies. Lorsqu’une stratégie de votre initiative est comparée à vos ressources et en trouve une ou plusieurs non conformes, elle est présentée comme recommandation dans Defender pour le cloud.

Les recommandations sont des actions à effectuer pour sécuriser et renforcer vos ressources. Chaque recommandation vous fournit les informations suivantes :

- Courte description du problème
- Étapes de correction à effectuer pour implémenter la recommandation
- Ressources concernées

Dans la pratique, cela fonctionne comme suit :

1. Le Benchmark de sécurité Azure est une ***initiative*** qui contient des exigences.

    Par exemple, les comptes Stockage Azure doivent limiter l’accès au réseau pour réduire leur surface d’attaque.

1. L’initiative comprend plusieurs ***stratégies***, chacune avec une exigence d’un type de ressource spécifique. Ces stratégies appliquent les exigences de l’initiative. 

    Pour continuer l’exemple, l’exigence de stockage est appliquée avec la stratégie « Les comptes de stockage doivent restreindre l’accès au réseau à l’aide de règles de réseau virtuel ».

1. Microsoft Defender pour le cloud évalue continuellement vos abonnements connectés. S’il trouve une ressource qui ne respecte pas une stratégie, il affiche une ***recommandation*** pour corriger cette situation et renforcer la sécurité des ressources qui ne répondent pas à vos besoins en matière de sécurité.

    Par exemple, si un compte Stockage Azure sur l’un de vos abonnements protégés n’est pas protégé par des règles de réseau virtuel, vous verrez la recommandation portant sur le durcissement de ces ressources. 

Par conséquent, (1) une initiative comprend (2) des stratégies qui génèrent (3) des recommandations propres à l’environnement.

## <a name="viewing-the-relationship-between-a-recommendation-and-a-policy"></a>Affichage de la relation entre une recommandation et une stratégie

Comme indiqué ci-dessus, les recommandations intégrées de Defender pour le cloud sont basées sur le Benchmark de sécurité Azure. Presque toutes les recommandations ont une stratégie sous-jacente qui est dérivée d’une exigence dans le point de référence.

Lorsque vous examinez les détails d’une recommandation, il est souvent utile de pouvoir consulter la stratégie sous-jacente. Pour chaque recommandation soutenue par une stratégie, utilisez le lien **Afficher la définition de stratégie** à partir de la page des détails de la recommandation pour accéder directement à l’entrée d’Azure Policy pour la stratégie correspondante :

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Lien vers la page Azure Policy de la stratégie spécifique prenant en charge une recommandation.":::

Utilisez ce lien pour afficher la définition de stratégie et passer en revue la logique d’évaluation. 

Si vous examinez la liste des recommandations de notre [Guide de référence des recommandations de sécurité](recommendations-reference.md), vous remarquerez également des liens vers les pages de définition de stratégie :

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Accès à la page Azure Policy d’une stratégie particulière, directement à partir de la page de référence des recommandations de Microsoft Defender pour le cloud.":::


## <a name="next-steps"></a>Étapes suivantes

Cette page a expliqué, à un niveau élevé, les concepts de base et les relations entre les stratégies, les initiatives et les recommandations. Pour plus d’informations, consultez :

- [Créer des initiatives personnalisées](custom-security-policies.md)
- [Désactiver des stratégies de sécurité pour désactiver des recommandations](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)
- [Découvrez comment modifier une stratégie de sécurité dans Azure Policy](../governance/policy/tutorials/create-and-manage.md)
