---
title: Rechercher et filtrer des membres et propriétaires de groupes (préversion) – Azure Active Directory | Microsoft Docs
description: Rechercher et filtrer des membres et propriétaires de groupes dans le portail Azure.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 10/22/2021
ms.author: curtand
ms.reviewer: jodah
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8774bef1dd939daea4384a25bc35d22f3475f57a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049595"
---
# <a name="search-groups-and-members-in-azure-active-directory"></a>Rechercher des groupes et des membres dans Azure Active Directory

Cet article explique comment rechercher les membres et les propriétaires d’un groupe, et comment utiliser des filtres de recherche dans le dans le portail Azure Active Directory (Azure AD). Les fonctions de recherche pour les groupes sont les suivantes :

- Fonctionnalités de recherche dans les groupes, comme la recherche d’une sous-chaîne dans les noms des groupes
- Options de filtrage et de tri sur les listes de membres et de propriétaires
- Fonctionnalités de recherche pour les listes de membres et de propriétaires

## <a name="group-search-and-sort"></a>Recherche et tri des groupes

Dans la page **Tous les groupes**, quand vous entrez une chaîne de recherche, vous pouvez maintenant basculer entre les recherches « contient » et « commence par » seulement sur la page **Tous les groupes**. La recherche de sous-chaînes est effectuée seulement sur des mots entiers, et les caractères spéciaux sont recherchés aussi comme recherche liée par un ET. Par exemple, la recherche de -Nom lance une recherche de la sous-chaîne « Nom » et une recherche de « - ». La recherche substring respecte la casse. La recherche se fait également dans les propriétés ID d’objet ou mailNickname.

![Nouvelles recherches de sous-chaînes dans la page Tous les groupes](./media/groups-members-owners-search/members-list.png)

Par exemple, une recherche portant sur le mot « stratégie » retourne les chaînes « stratégie MDM – Ouest » et « groupe de stratégies ». Un groupe nommé « Nouvelle_stratégie » n’est pas retourné. Vous pouvez trier la liste **Tous les groupes** par nom dans l’ordre croissant ou décroissant.

## <a name="group-member-search-and-filter"></a>Recherche et filtrage de membres de groupe

### <a name="search-group-member-and-owner-lists"></a>Rechercher dans les listes de membres et de propriétaires de groupes

Vous pouvez rechercher les membres ou propriétaires d’un groupe spécifique par nom, et quand vous entrez une chaîne de recherche, une recherche `contains` est effectuée automatiquement. Par exemple, une recherche de « Scott » retourne à la fois « Scott Wilkinson » et « Maya Scott ».

![nouvelles recherches de sous-chaînes sur les listes de membres et propriétaires de groupes](./media/groups-members-owners-search/groups-search-preview.png)

### <a name="filter-member-and-owner-lists"></a>Filtrer des listes de membres et de propriétaires

Vous pouvez également filtrer les listes de membres et de propriétaires des groupes par type d’utilisateur. Ces informations se trouvent dans la colonne **Type d’utilisateur** de la liste de membres ou de propriétaires. Vous pouvez filtrer la liste pour voir seulement les membres ou les invités.

La page **Membres** inclut tous les membres uniques du groupe, y compris les personnes qui héritent de leur appartenance à un groupe d’un autre groupe.

Vous pouvez également rechercher et filtrer les listes individuellement. Le filtrage de la liste tous les membres n’affecte pas les filtres appliqués à la liste des membres directs.

## <a name="group-memberships"></a>Appartenances aux groupes

Vous pouvez également visualiser les appartenances au groupe pour un groupe dans la page **Appartenances aux groupes**. La page **Appartenances aux groupes** prend en charge les opérations de recherche, de tri et de filtrage de façon similaire aux autres pages Groupes.

## <a name="group-member-counts"></a>Nombre de membres par groupe

La page **Vue d’ensemble** indique des comptages de membres pour les groupes. Vous pouvez voir le nombre total de membres directs d’un groupe et le nombre total d’appartenances (tous les membres uniques du groupe, y compris les appartenances héritées) dans la page **Vue d’ensemble**.

![Plus grande précision des comptages d’appartenances aux groupes](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>Étapes suivantes

Ces articles fournissent des informations supplémentaires sur l’utilisation des groupes dans Azure AD.

- [Afficher vos groupes et vos membres](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Gérer l’appartenance au groupe](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gérer les règles dynamiques pour les utilisateurs dans un groupe](groups-create-rule.md)
- [Modifier vos paramètres de groupe](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gérer l’accès aux ressources à l’aide des groupes](../fundamentals/active-directory-manage-groups.md)
- [Gérer l’accès aux applications SaaS en utilisant des groupes](groups-saasapps.md)
- [Gérer des groupes au moyen de commandes PowerShell](../enterprise-users/groups-settings-v2-cmdlets.md)
- [Ajouter un abonnement Azure à Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
