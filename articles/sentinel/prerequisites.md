---
title: Prérequis pour le déploiement de Microsoft Sentinel
description: Découvrez les activités de prédéploiement et les conditions préalables au déploiement de Microsoft Sentinel.
services: sentinel
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: bae2e792ab85ec87f1187c632f9582e9d38e87ed
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521127"
---
# <a name="pre-deployment-activities-and-prerequisites-for-deploying-microsoft-sentinel"></a>Activités de prédéploiement et prérequis pour le déploiement de Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cet article présente les activités de prédéploiement et les conditions préalables au déploiement de Microsoft Sentinel.

## <a name="pre-deployment-activities"></a>Activités de prédéploiement

Avant de déployer Microsoft Sentinel, nous vous recommandons de suivre les étapes ci-dessous pour vous aider à tirer le meilleur parti de votre déploiement, dès que possible.

1. Déterminez les [sources de données](connect-data-sources.md) dont vous avez besoin et les critères de taille des données pour vous aider à projeter avec précision le budget et la chronologie de votre déploiement.

    Vous pouvez déterminer ces informations lors de la révision de vos cas d’usage métier ou en évaluant une solution SIEM que vous avez déjà en place. Si vous disposez déjà d’une solution SIEM, analysez vos données pour comprendre quelles sources de données ont le plus de valeur et doivent être ingérées dans Microsoft Sentinel.

1. Concevez votre espace de travail Microsoft Sentinel. Tenez compte de paramètres tels que les suivants :

    - Utilisation d’un seul locataire ou de plusieurs locataires
    - Toutes les exigences de conformité que vous avez pour la collecte et le stockage des données
    - Mode de contrôle de l’accès aux données Microsoft Sentinel

    Pour plus d’informations, consultez [Bonnes pratiques pour l’architecture de l’espace de travail](best-practices-workspace-architecture.md) et [Exemples de conceptions d’espaces de travail](sample-workspace-designs.md).

1. Une fois que les cas d’usage métier, les sources de données et les critères de taille de données ont été identifiés, [commencez à planifier votre budget](azure-sentinel-billing.md), en tenant compte des implications sur le coût de chaque scénario planifié.

    Assurez-vous que votre budget couvre le coût de l’ingestion des données pour Microsoft Sentinel et Azure Log Analytics, tous les playbooks à déployer, etc.

    Pour plus d'informations, consultez les pages suivantes :

    - [Coûts et facturation de Microsoft Sentinel](azure-sentinel-billing.md)
    - [Tarification Microsoft Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/)
    - [Tarifs de Log Analytics](https://azure.microsoft.com/pricing/details/monitor/)
    - [Tarification Logic Apps (playbooks)](https://azure.microsoft.com/pricing/details/logic-apps/)
    - [Intégration d’Azure Data Explorer pour la conservation des journaux à long terme](store-logs-in-azure-data-explorer.md)

1. Nommez un ingénieur ou un architecte chargé du déploiement, en fonction des besoins et des chronologies. Cette personne doit mener le déploiement et être le point de contact principal de votre équipe.

## <a name="azure-tenant-requirements"></a>Éléments requis pour le locataire Azure

Avant de déployer Microsoft Sentinel, assurez-vous que votre locataire Azure dispose des éléments requis suivants :

- Une [licence et un locataire Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md), ou un [compte individuel avec un mode de paiement valide](https://azure.microsoft.com/free/), sont requis pour accéder à Azure et déployer des ressources.

- Une fois que vous avez un locataire, vous devez disposer d’un [abonnement Azure](../cost-management-billing/manage/create-subscription.md) pour effectuer le suivi de la création et de la facturation des ressources.

- Une fois que vous avez un abonnement, vous devez disposer des [autorisations appropriées](../role-based-access-control/index.yml) pour commencer à l’utiliser. Si vous utilisez un nouvel abonnement, un administrateur ou une personne plus haut placée par rapport au locataire AAD doit être désigné comme [propriétaire/contributeur](../role-based-access-control/rbac-and-directory-admin-roles.md) de l’abonnement.

    - Pour conserver l’accès le moins privilégié possible, affectez des rôles au niveau du groupe de ressources.
    - Pour plus de contrôle sur les autorisations et l’accès, configurez des rôles personnalisés. Pour plus d’informations, consultez [Contrôle d’accès en fonction du rôle](../role-based-access-control/custom-roles.md).
    - Pour séparer davantage les utilisateurs des utilisateurs de sécurité, vous souhaiterez peut-être utiliser le [contrôle d’accès en fonction du rôle dans le contexte de la ressource](resource-context-rbac.md) ou [au niveau de la table](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043).

    Pour plus d’informations sur les autres rôles et autorisations pris en charge pour Microsoft Sentinel, consultez [Autorisations dans Microsoft Sentinel](roles.md).

- Un [espace de travail Log Analytics](../azure-monitor/logs/quick-create-workspace.md) est nécessaire afin d’héberger toutes les données que Microsoft Sentinel doit ingérer et utiliser pour ses détections, analyses et autres fonctionnalités. Pour plus d’informations, consultez [Meilleures pratiques pour l’architecture de l’espace de travail Microsoft Sentinel](best-practices-workspace-architecture.md).

> [!TIP]
> Lors de la configuration de votre espace de travail Microsoft Sentinel, [créez un groupe de ressources](../azure-resource-manager/management/manage-resource-groups-portal.md) dédié à Microsoft Sentinel et aux ressources que Microsoft Sentinel utilise, notamment l’espace de travail Log Analytics, les playbooks, les classeurs, etc.
>
> Un groupe de ressources dédié permet d’attribuer des autorisations en une seule opération, au niveau du groupe de ressources, avec application automatique des autorisations aux ressources pertinentes. La gestion de l’accès par le biais d’un groupe de ressources vous aide à vous assurer que vous utilisez Microsoft Sentinel efficacement sans risque d’émettre des autorisations incorrectes. S’il n’y a pas de groupe de ressources pour Microsoft Sentinel, les ressources sont disséminées entre plusieurs groupes de ressources, pouvant empêcher un utilisateur ou un principal de service d’effectuer une action requise ou de visualiser des données en raison d’autorisations insuffisantes.
>
> Pour renforcer le contrôle d’accès sur les ressources par niveaux, utilisez des groupes de ressources supplémentaires destinés à héberger les ressources accessibles uniquement par ces groupes. L’utilisation de plusieurs niveaux de groupes de ressources vous permet de séparer l’accès entre ces niveaux.
>

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
>[Intégrer Microsoft Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Obtenir une visibilité des alertes](get-visibility.md)
