---
title: Gérer les groupes d’accès privilégiés dans Privileged Identity Management (PIM) | Microsoft Docs
description: Comment gérer les membres et propriétaires de groupes d’accès privilégié dans Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/07/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8a36c1837273fc3fa173994e2ec3b3465ed4cb6
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520253"
---
# <a name="management-capabilities-for-privileged-access-groups-preview"></a>Fonctionnalités de gestion pour les groupes d’accès privilégié (préversion)

Dans Privileged Identity Management (PIM), vous pouvez désormais attribuer l’éligibilité à l’appartenance ou à la propriété de groupes d’accès privilégié. À partir de cette préversion, vous pouvez attribuer des rôles intégrés Azure AD (Azure Active Directory) à des groupes cloud et utiliser PIM pour gérer l’éligibilité à l’appartenance ou à la propriété des groupes et les activer. Pour plus d’informations sur les groupes avec attribution de rôle dans Azure AD, consultez [Utiliser des groupes Azure AD pour gérer les attributions de rôles](../roles/groups-concept.md).

>[!Important]
> Pour attribuer un groupe d’accès privilégié à un rôle afin d’obtenir un accès administratif à Exchange, au Centre de sécurité et de conformité ou à SharePoint, utilisez l’expérience **Rôles et administrateurs** du portail Azure AD, et non l’expérience Groupes d’accès privilégié pour que l’utilisateur ou le groupe puisse être activé dans le groupe.

> [!NOTE]
> Pour les groupes d’accès privilégiés qui sont utilisés pour obtenir des rôles Azure AD, nous recommandons de demander un processus d’approbation pour les attributions de membres éligibles. Les attributions qui sont activables sans approbation peuvent créer un risque de sécurité pour les administrateurs qui ont un niveau d’autorisations inférieur. Par exemple, l’administrateur du support technique a l’autorisation de réinitialiser le mot de passe d’un utilisateur éligible.

## <a name="require-different-policies-for-each-role-assignable-group"></a>Imposer des stratégies différentes à chaque groupe avec attribution de rôle

Certaines organisations utilisent des outils comme la collaboration B2B (business-to-business) Azure AD pour inviter leurs partenaires en tant qu’invités à leur organisation Azure AD. Au lieu d’utiliser une seule stratégie juste-à-temps pour toutes les attributions à un rôle privilégié, vous pouvez créer deux groupes d’accès privilégié différents avec leurs propres stratégies. Vous pouvez appliquer des exigences moins strictes à vos employés approuvés et des exigences plus strictes comme un workflow d’approbation à vos partenaires quand ils demandent une activation dans le groupe qui leur est attribué.

## <a name="activate-multiple-role-assignments-in-a-single-request"></a>Activer plusieurs attributions de rôle avec une requête unique

Avec la préversion des groupes d’accès privilégié, vous pouvez accorder aux administrateurs spécifiques à une charge de travail un accès rapide à plusieurs rôles avec une seule requête juste-à-temps. Par exemple, les administrateurs Office de niveau 0 peuvent avoir besoin d’un accès juste-à-temps aux rôles Administrateur Exchange, Administrateur des applications Office, Administrateur Teams et Administrateur de recherche pour examiner minutieusement les incidents au quotidien. Jusqu’à présent, quatre requêtes consécutives étaient nécessaires, ce qui représentait un processus particulièrement long. Vous pouvez maintenant créer un groupe avec attribution de rôle nommé « Administrateurs Office de niveau 0 », lui attribuer chacun des quatre rôles mentionnés précédemment (ou n’importe quel rôle intégré Azure AD) et activer l’accès privilégié dans la section Activité du groupe. Après avoir activé le groupe pour l’accès privilégié, vous pouvez configurer les paramètres juste-à-temps pour les membres du groupe et définir vos administrateurs et propriétaires comme éligibles. Quand l’accès privilégié est accordé aux administrateurs dans le groupe, ces derniers deviennent membres des quatre rôles Azure AD.

## <a name="extend-and-renew-group-assignments"></a>Étendre et renouveler les attributions de groupe

Après avoir configuré vos attributions de propriétaire ou de membres limitées dans le temps, vous vous demanderez peut-être ce qui se passe si une attribution expire. Dans cette nouvelle version, nous proposons deux options pour ce scénario :

- Étendre : quand une attribution de rôle arrive bientôt à expiration, l’utilisateur peut utiliser Privileged Identity Management pour demander son extension.
- Renouveler : quand une attribution de rôle a expiré, l’utilisateur peut utiliser Privileged Identity Management pour demander son renouvellement.

Ces deux actions utilisateur exigent l’approbation d’un Administrateur général ou d’un Administrateur de rôle privilégié. Les administrateurs n’ont plus besoin d’être spécialisés dans la gestion de ces expirations. Ils peuvent simplement attendre les demandes d’extension ou de renouvellement et les approuver si elles sont valides.

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer l’éligibilité à l’appartenance ou à la propriété d’un groupe d’accès privilégié](groups-assign-member-owner.md)
- [Approuver ou refuser des demandes d’activation pour des membres et propriétaires de groupe d’accès privilégié](groups-approval-workflow.md)
