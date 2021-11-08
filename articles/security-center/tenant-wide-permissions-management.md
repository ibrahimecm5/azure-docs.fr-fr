---
title: Accorder et demander des autorisations à l’ensemble du locataire dans Microsoft Defender pour le cloud
description: Découvrez comment accorder et demander des autorisations à l’ensemble du locataire dans Microsoft Defender pour le cloud
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2021
ms.openlocfilehash: 23bb5771923b3a15ed58ed3e3189b3edf46b4664
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131424985"
---
# <a name="grant-and-request-tenant-wide-visibility"></a>Accorder et demander une visibilité à l’échelle du locataire

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Si un utilisateur ayant le rôle Azure Active Directory (AD) d’**administrateur général** peut avoir des responsabilités à l’échelle du locataire, il peut ne pas disposer des autorisations Azure lui permettant de consulter les informations à l’échelle de l’organisation dans Microsoft Defender pour le cloud. L’élévation d’autorisations est requise, car les attributions de rôles Azure AD n’accordent pas l’accès aux ressources Azure. 

## <a name="grant-tenant-wide-permissions-to-yourself"></a>Accorder à vous-même des autorisations à l’échelle du locataire

Pour vous attribuer des autorisations au niveau du locataire :

1. Si votre organisation gère l’accès aux ressources avec [Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md) ou tout autre outil PIM, le rôle d’administrateur général doit être actif pour l’utilisateur qui effectue la procédure ci-dessous.

1. En tant qu’utilisateur Administrateur général sans affectation au groupe d’administration racine du locataire, ouvrez la page **Vue d’ensemble** de Defender pour le cloud, puis sélectionnez le lien **Visibilité à l’échelle du locataire** dans la bannière. 

    :::image type="content" source="media/management-groups-roles/enable-tenant-level-permissions-banner.png" alt-text="Activez les autorisations au niveau du locataire dans Microsoft Defender pour le cloud.":::

1. Sélectionnez le nouveau rôle Azure à attribuer. 

    :::image type="content" source="media/management-groups-roles/enable-tenant-level-permissions-form.png" alt-text="Formulaire de définition des autorisations au niveau du locataire à attribuer à votre utilisateur.":::

    > [!TIP]
    > En règle générale, le rôle Administrateur de sécurité est nécessaire pour appliquer des stratégies au niveau racine, tandis que Lecteur de Sécurité est suffisant pour fournir une visibilité au niveau locataire. Pour plus d’informations sur les autorisations accordées par ces rôles, consultez la [description du rôle intégré Administrateur de sécurité](../role-based-access-control/built-in-roles.md#security-admin) ou la [description du rôle intégré de Lecteur Sécurité](../role-based-access-control/built-in-roles.md#security-reader).
    >
    > Pour connaître les différences entre ces rôles spécifiques à Defender pour le cloud, consultez le tableau présenté dans [Rôles et actions autorisées](permissions.md#roles-and-allowed-actions).

    La vue à l’échelle de l’organisation est obtenue en accordant des rôles au niveau du groupe d’administration racine du locataire.  

1. Déconnectez-vous du portail Azure, puis reconnectez-vous.

1. Une fois que vous disposez d’un accès élevé, ouvrez ou actualisez Microsoft Defender pour le cloud pour vérifier que vous voyez tous les abonnements sous votre locataire Azure AD. 

Le processus simple ci-dessus effectue automatiquement un certain nombre d’opérations :

1. Les autorisations de l’utilisateur sont temporairement élevées.
1. Grâce aux nouvelles autorisations, l’utilisateur est affecté au rôle Azure RBAC souhaité sur le groupe d’administration racine.
1. Les autorisations élevées sont supprimées.

Pour plus d’informations sur le processus d’élévation d’Azure AD, consultez [Élever l’accès pour gérer tous les abonnements et groupes d’administration Azure](../role-based-access-control/elevate-access-global-admin.md).


## <a name="request-tenant-wide-permissions-when-yours-are-insufficient"></a>Demander des autorisations à l’échelle du locataire quand les vôtres sont insuffisantes

Si vous vous connectez à Defender pour le cloud et que vous voyez une bannière indiquant que votre affichage est limité, vous pouvez cliquer dessus pour envoyer une demande à l’administrateur général de votre organisation. Dans la demande, vous pouvez inclure le rôle que vous souhaitez obtenir, et l’administrateur général prendra une décision sur le rôle à accorder. 

Il appartient à l’administrateur général d’accepter ou de rejeter ces demandes. 

> [!IMPORTANT]
> Vous ne pouvez envoyer qu’une demande tous les sept jours.

Pour demander des autorisations à privilège élevé à votre administrateur général :

1. Dans le portail Azure, ouvrez Microsoft Defender pour le cloud.

1. Si la bannière « Vous voyez des informations limitées. » s’affiche, Sélectionnez-le.

    :::image type="content" source="media/management-groups-roles/request-tenant-permissions.png" alt-text="Bannière informant l’utilisateur qu’il peut demander des autorisations à l’échelle du locataire.":::

1. Dans le formulaire détaillé de la demande, sélectionnez le rôle souhaité et la raison pour laquelle vous avez besoin de ces autorisations.

    :::image type="content" source="media/management-groups-roles/request-tenant-permissions-details.png" alt-text="Page de détails pour la demande d’autorisations à l’échelle du locataire auprès de votre administrateur général Azure.":::

1. Sélectionnez **Demander l’accès**.

    Un e-mail est envoyé à l’administrateur général. L’e-mail contient un lien vers Defender pour le cloud où il peut approuver ou rejeter la demande.

    :::image type="content" source="media/management-groups-roles/request-tenant-permissions-email.png" alt-text="E-mail envoyé à l’administrateur général pour de nouvelles autorisations.":::

    Une fois que l’administrateur général a sélectionné **Examiner la demande** et a terminé le processus, la décision est envoyée par e-mail à l’utilisateur qui effectue la demande. 

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les autorisations de Defender pour le cloud dans la page associée suivante :

- [Autorisations dans Microsoft Defender pour le cloud](permissions.md)