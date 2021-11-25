---
title: Autorisations dans Microsoft Sentinel | Microsoft Docs
description: Cet article explique la manière dont Microsoft Sentinel utilise le contrôle d’accès en fonction du rôle Azure pour attribuer des autorisations à des utilisateurs et identifie les actions autorisées pour chaque rôle.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 77d9af26c175de162576fc8948dc3b8d5eedcada
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132516985"
---
# <a name="permissions-in-microsoft-sentinel"></a>Autorisations dans Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Sentinel utilise le [contrôle d’accès en fonction du rôle Azure (RBAC Azure)](../role-based-access-control/role-assignments-portal.md) pour fournir des [rôles intégrés](../role-based-access-control/built-in-roles.md) susceptibles d’être attribués à des utilisateurs, des groupes et des services dans Azure.

Utilisez RBAC Azure pour créer et attribuer des rôles au sein de votre équipe en charge des opérations de sécurité afin d’accorder l’accès approprié à Microsoft Sentinel. Les différents rôles vous donnent un contrôle précis sur ce que les utilisateurs de Microsoft Sentinel peuvent voir et faire. Les rôles Azure peuvent être attribués directement dans l’espace de travail Microsoft Sentinel (voir la remarque ci-dessous) ou dans un abonnement ou un groupe de ressources auquel appartient l’espace de travail, dont Microsoft Sentinel héritera.

## <a name="roles-for-working-in-microsoft-sentinel"></a>Rôles pour travailler dans Microsoft Sentinel

### <a name="microsoft-sentinel-specific-roles"></a>Rôles spécifiques à Microsoft Sentinel

**Tous les rôles intégrés de Microsoft Sentinel accordent un accès en lecture aux données de votre espace de travail Microsoft Sentinel.**

- [Lecteur Microsoft Sentinel](../role-based-access-control/built-in-roles.md#microsoft-sentinel-reader) : il peut visualiser les données, les incidents, les classeurs et d’autres ressources Microsoft Sentinel.

- [Répondeur Microsoft Sentinel](../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder) : il peut, en plus des éléments ci-dessus, gérer les incidents (affecter, ignorer, etc.)

- [Contributeur Microsoft Sentinel](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor) : il peut, en plus des éléments ci-dessus, créer et modifier des classeurs, des règles d’analytique et d’autres ressources Microsoft Sentinel.

- [Contributeur Microsoft Sentinel Automation](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor) : permet à Microsoft Sentinel d’ajouter des playbooks aux règles d’automatisation. Il n’est pas destiné aux comptes d’utilisateur.

> [!NOTE]
>
> - Pour de meilleurs résultats, ces rôles doivent être attribués sur le **groupe de ressources** contenant l’espace de travail Microsoft Sentinel. De cette façon, les rôles s’appliqueront à toutes les ressources qui sont déployées pour prendre en charge Microsoft Sentinel, car ces ressources doivent également être placées dans ce même groupe de ressources.
>
> - Une autre option consiste à attribuer les rôles directement sur l’**espace de travail** Microsoft Sentinel lui-même. Dans ce cas, vous devez également attribuer les mêmes rôles sur la **ressource de solution** SecurityInsights dans cet espace de travail. Il peut être nécessaire de les attribuer également sur d’autres ressources, et vous devrez constamment gérer les attributions de rôles sur les ressources.

### <a name="additional-roles-and-permissions"></a>Rôles et autorisations supplémentaires

Il peut être nécessaire d’affecter des rôles supplémentaires ou des autorisations spécifiques aux utilisateurs ayant des exigences particulières pour accomplir leurs tâches.

- **Utilisation de playbooks pour automatiser les réponses aux menaces**

    Microsoft Sentinel utilise des **playbooks** pour automatiser la réponse aux menaces. Les playbooks sont basés sur **Azure Logic Apps** et constituent une ressource Azure distincte. Vous pouvez attribuer à des membres spécifiques de votre équipe en charge des opérations de sécurité la possibilité d’utiliser Logic Apps pour les opérations SOAR (Security Orchestration, Automation, and Response). Vous pouvez utiliser le rôle [Contributeur d’application logique](../role-based-access-control/built-in-roles.md#logic-app-contributor) pour attribuer une autorisation explicite pour l’utilisation des playbooks.

- **Connexion de sources de données à Microsoft Sentinel**

    Pour qu’un utilisateur ajoute des **connecteurs de données**, vous devez attribuer les autorisations d’accès en écriture à l’utilisateur sur l’espace de travail Microsoft Sentinel. Notez également les autorisations supplémentaires nécessaires pour chaque connecteur, comme indiqué dans la page du connecteur approprié.

- **Utilisateurs invités attribuant des incidents**

    Si un utilisateur invité doit être en mesure d’attribuer des incidents, il doit se voir attribuer le rôle [Lecteur de répertoire](../active-directory/roles/permissions-reference.md#directory-readers) en plus du rôle Répondeur Microsoft Sentinel. Notez que ce rôle *n'est pas* un rôle Azure, mais un rôle **Azure Active Directory**, et qu'il est attribué par défaut aux utilisateurs ordinaires (non invités).

- **Création et suppression de classeurs**

    Pour qu’un utilisateur crée et supprime un classeur Microsoft Sentinel, il doit également se voir attribuer le rôle Azure Monitor intitulé [Contributeur de surveillance](../role-based-access-control/built-in-roles.md#monitoring-contributor). Ce rôle n’est pas nécessaire pour *l’utilisation* de classeurs, mais uniquement pour leur création et leur suppression.

### <a name="other-roles-you-might-see-assigned"></a>Autres rôles que vous pouvez voir attribués

En attribuant des rôles Azure spécifiques à Microsoft Sentinel, vous pouvez rencontrer d’autres rôles Azure et Log Analytics qui peuvent avoir été attribués aux utilisateurs à d’autres fins. Notez que ces rôles accordent un ensemble plus important d’autorisations qui incluent l’accès à votre espace de travail Microsoft Sentinel et à d’autres ressources :

- **Rôles Azure :** [Propriétaire](../role-based-access-control/built-in-roles.md#owner), [Contributeur](../role-based-access-control/built-in-roles.md#contributor) et [Lecteur](../role-based-access-control/built-in-roles.md#reader). Les rôles Azure accordent l’accès à l’ensemble de vos ressources Azure, notamment aux espaces de travail Log Analytics et aux ressources Microsoft Sentinel.

- **Rôles Log Analytics :** [Contributeur Log Analytics](../role-based-access-control/built-in-roles.md#log-analytics-contributor) et [Lecteur Log Analytics](../role-based-access-control/built-in-roles.md#log-analytics-reader). Les rôles Log Analytics accordent l’accès à vos espaces de travail Log Analytics.

Par exemple, un utilisateur auquel est attribué le rôle **Lecteur Microsoft Sentinel**, mais pas le rôle **Contributeur Microsoft Sentinel**, pourra quand même modifier des éléments dans Microsoft Sentinel si le rôle **Contributeur** au niveau d’Azure lui est attribué. Par conséquent, si vous voulez accorder des autorisations à un utilisateur seulement dans Microsoft Sentinel, vous devez supprimer soigneusement les autorisations antérieures de cet utilisateur, en veillant à ne pas supprimer un accès nécessaire à une autre ressource.

## <a name="microsoft-sentinel-roles-and-allowed-actions"></a>Rôles Microsoft Sentinel et actions autorisées

Le tableau suivant récapitule les rôles Microsoft Sentinel et leurs actions autorisées dans Microsoft Sentinel.

| Role | Créer et exécuter des playbooks| Créer et modifier des règles d’analyse et d’autres ressources Microsoft Sentinel[*](#workbooks) | Gérer les incidents (ignorer, attribuer, etc.) | Visualiser des données, des incidents, des classeurs et d’autres ressources Microsoft Sentinel |
|---|---|---|---|---|
| Lecteur Microsoft Sentinel | -- | -- | -- | &#10003; |
| Répondeur Microsoft Sentinel | -- | -- | &#10003; | &#10003; |
| Contributeur Microsoft Sentinel | -- | &#10003; | &#10003; | &#10003; |
| Contributeur Microsoft Sentinel + Contributeur d’application logique | &#10003; | &#10003; | &#10003; | &#10003; |
| | | | | |

<a name=workbooks></a>* La création et la suppression de classeurs requièrent le rôle supplémentaire de [Contributeur de surveillance](../role-based-access-control/built-in-roles.md#monitoring-contributor). Pour plus d’informations, consultez [Rôles et autorisations supplémentaires](#additional-roles-and-permissions).
## <a name="custom-roles-and-advanced-azure-rbac"></a>Rôles personnalisés et RBAC Azure avancé

- **Rôles personnalisés**. En plus d’utiliser des rôles intégrés Azure, ou au lieu d’en utiliser, vous pouvez créer des rôles personnalisés Azure pour Microsoft Sentinel. Les rôles personnalisés Azure pour Microsoft Sentinel sont créés de la même façon que les autres [rôles personnalisés Azure](../role-based-access-control/custom-roles-rest.md#create-a-custom-role), c’est-à-dire en fonction des [autorisations propres à Microsoft Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) et des [ressources Azure Log Analytics](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights).

- **RBAC Log Analytics**. Vous pouvez utiliser le contrôle d’accès en fonction du rôle Azure avancé de Log Analytics sur les données de votre espace de travail Microsoft Sentinel. Cela comprend à la fois le RBAC Azure basé sur le type de données et le RBAC Azure dans le contexte de la ressource. Pour plus d'informations, consultez les pages suivantes :

    - [Gérer les données du journal et les espaces de travail dans Azure Monitor](../azure-monitor/logs/manage-access.md#manage-access-using-workspace-permissions)

    - [RBAC de contexte de ressource pour Microsoft Sentinel](resource-context-rbac.md)
    - [Table-level RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043)

    Le RBAC de contexte de ressource et le RBAC au niveau table sont deux méthodes d’octroi d’accès à des données spécifiques dans votre espace de travail Microsoft Sentinel sans autoriser l’accès à toute l’expérience Microsoft Sentinel.

## <a name="role-recommendations"></a>Recommandations relatives aux rôles

Après avoir compris le fonctionnement des rôles et des autorisations dans Microsoft Sentinel, vous pouvez utiliser l’aide suivante relative aux meilleures pratiques pour appliquer des rôles à vos utilisateurs :

|Type d’utilisateur  |Rôle |Resource group  |Description  |
|---------|---------|---------|---------|
|**Analystes de sécurité**     | [Répondeur Microsoft Sentinel](../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder)        | Groupe de ressources de Microsoft Sentinel        | Visualiser des données, des incidents, des classeurs et d’autres ressources Microsoft Sentinel. <br><br>Gérer les incidents, par exemple les attribuer ou les ignorer.        |
|     | [Contributeur Logic Apps](../role-based-access-control/built-in-roles.md#logic-app-contributor)        | Groupe de ressources de Microsoft Sentinel ou groupe de ressources dans lequel vos playbooks sont stockés        | Attacher des playbooks aux règles analytiques et d’automatisation, et exécuter des playbooks. <br><br>**Remarque** : Ce rôle permet également aux utilisateurs de modifier des playbooks.         |
|**Ingénieurs de la sécurité**     | [Contributeur Microsoft Sentinel](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor)       |Groupe de ressources de Microsoft Sentinel         |   Visualiser des données, des incidents, des classeurs et d’autres ressources Microsoft Sentinel. <br><br>Gérer les incidents, par exemple les attribuer ou les ignorer. <br><br>Créer et modifier des classeurs, des règles d’analyse et d’autres ressources Microsoft Sentinel.      |
|     | [Contributeur Logic Apps](../role-based-access-control/built-in-roles.md#logic-app-contributor)        | Groupe de ressources de Microsoft Sentinel ou groupe de ressources dans lequel vos playbooks sont stockés        | Attacher des playbooks aux règles analytiques et d’automatisation, et exécuter des playbooks. <br><br>**Remarque** : Ce rôle permet également aux utilisateurs de modifier des playbooks.         |
|  **Principal du service**   | [Contributeur Microsoft Sentinel](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor)      |  Groupe de ressources de Microsoft Sentinel       | Configuration automatisée des tâches de gestion |
|     |         |        | |

> [!TIP]
> Des rôles supplémentaires peuvent être nécessaires en fonction des données que vous ingérez ou supervisez. Par exemple, des rôles Azure AD peuvent être nécessaires, tels que les rôles d’administrateur général ou d’administrateur de la sécurité, afin de configurer des connecteurs de données pour des services dans d’autres portails Microsoft.
>

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à utiliser des rôles pour les utilisateurs de Microsoft Sentinel et découvert ce que chaque rôle permet aux utilisateurs de faire.

Découvrez des billets de blog sur la sécurité et la conformité Azure dans le [blog Microsoft Sentinel](https://aka.ms/azuresentinelblog).
