---
title: Meilleures pratiques pour Azure RBAC
description: Meilleures pratiques d'utilisation du contrôle d’accès en fonction du rôle Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 11/15/2021
ms.author: rolyon
ms.openlocfilehash: 947645848fd60a6d2864a1715ddc32424a683ce8
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524832"
---
# <a name="best-practices-for-azure-rbac"></a>Meilleures pratiques pour Azure RBAC

Cet article décrit quelques-unes des meilleures pratiques d'utilisation du contrôle d’accès en fonction du rôle Azure (Azure RBAC). Ces meilleures pratiques sont issues de notre expérience d’Azure RBAC, mais également de celle des clients, comme vous.

## <a name="only-grant-the-access-users-need"></a>Accorder uniquement l’accès nécessaire aux utilisateurs

Avec Azure RBAC, vous pouvez séparer les tâches au sein de votre équipe et accorder aux utilisateurs uniquement les accès nécessaires pour accomplir leur travail. Plutôt que de donner à tous des autorisations illimitées au sein de votre abonnement ou de vos ressources Azure, vous pouvez autoriser uniquement certaines actions sur une étendue donnée.

Lorsque vous planifiez votre stratégie de contrôle d’accès, vous pouvez accorder aux utilisateurs les privilèges minimaux pour effectuer leur travail. Évitez d’attribuer des rôles plus larges à des étendues plus importantes, même s’ils semblent plus pratiques dans un premier temps. Lorsque vous créez des rôles personnalisés, incluez uniquement les autorisations dont les utilisateurs ont besoin. En limitant les rôles et les étendues, vous limitez les ressources menacées en cas de compromission du principal de sécurité.

Le diagramme suivant illustre un modèle suggéré pour l’utilisation d’Azure RBAC.

![Azure RBAC et privilège minimum](./media/best-practices/rbac-least-privilege.png)

Pour obtenir des informations sur la procédure d’attribution de rôles, consultez [Attribuer des rôles Azure à l’aide du portail Azure](role-assignments-portal.md).

## <a name="limit-the-number-of-subscription-owners"></a>Limiter le nombre de propriétaires d’abonnements

Vous devez disposer d'un maximum de trois propriétaires d’abonnement afin de réduire le risque de violation par un propriétaire compromis. Cette recommandation peut être surveillée dans Microsoft Defender pour le Cloud. Pour obtenir d’autres recommandations relatives aux identités et aux accès dans Security Center, consultez [Recommandations de sécurité - Guide de référence](../security-center/recommendations-reference.md).

## <a name="use-azure-ad-privileged-identity-management"></a>Utiliser Azure AD Privileged Identity Management

Pour protéger des comptes privilégiés contre les cyberattaques malveillantes, vous pouvez utiliser Azure Active Directory Privileged Identity Management (PIM) afin de réduire le temps d’exposition des privilèges et d’augmenter la visibilité quant à leur utilisation via des rapports et des alertes. PIM permet de protéger les comptes privilégiés en fournissant un accès privilégié de type juste-à-temps aux ressources Azure AD et Azure. L’accès peut être lié au laps de temps après lequel les privilèges sont automatiquement révoqués. 

Pour plus d’informations, consultez [Qu’est-ce qu’Azure AD Privileged Identity Management ?](../active-directory/privileged-identity-management/pim-configure.md).

## <a name="assign-roles-to-groups-not-users"></a>Attribuer des rôles à des groupes et non à des utilisateurs

Pour faciliter la gestion des attributions de rôles, évitez d’attribuer des rôles directement aux utilisateurs. Attribuez plutôt des rôles à des groupes. L'attribution de rôles à des groupes plutôt qu'à des utilisateurs permet également de minimiser le nombre d'attributions de rôles, qui est [limité à un nombre d'attributions de rôles par abonnement](troubleshooting.md#azure-role-assignments-limit).

## <a name="assign-roles-using-the-unique-role-id-instead-of-the-role-name"></a>Affecter des rôles à l’aide de l’ID de rôle unique à la place du nom de rôle

Un nom de rôle peut changer dans certaines circonstances, par exemple :

- Vous utilisez vos propres rôles personnalisés et vous décidez d’en modifier le nom.
- Vous utilisez un rôle en préversion dont le nom contient **(préversion)** . Lorsque le rôle est publié, le rôle est renommé.

Même si un rôle est renommé, l’ID de rôle ne change pas. Si vous utilisez des scripts ou une automatisation pour créer vos attributions de rôles, il est recommandé d’utiliser l’ID de rôle unique au lieu du nom de rôle. Par conséquent, si un rôle est renommé, vos scripts sont plus susceptibles de fonctionner.

Pour plus d’informations, consultez [affecter un rôle à l’aide de l’ID de rôle unique et Azure PowerShell](role-assignments-powershell.md#assign-a-role-for-a-user-using-the-unique-role-id-at-a-resource-group-scope) et [affecter un rôle à l’aide de l’ID de rôle unique et Azure CLI](role-assignments-cli.md#assign-a-role-for-a-user-using-the-unique-role-id-at-a-resource-group-scope).

## <a name="next-steps"></a>Étapes suivantes

- [Résoudre les problèmes liés à Azure RBAC](troubleshooting.md)
