---
title: Identifier un groupe à gérer dans Privileged Identity Management – Azure AD | Microsoft Docs
description: Découvrez comment intégrer des groupes assignables à un rôle pour les gérer en tant que groupes d’accès privilégié dans Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/09/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72f848dae3eda447edee40b0da18f09fed50462c
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132157046"
---
# <a name="bring-privileged-access-groups-preview-into-privileged-identity-management"></a>Introduire un groupe d’accès privilégié (préversion) dans Privileged Identity Management

Dans Azure Active Directory (Azure AD), vous pouvez attribuer des rôles intégrés Azure AD à des groupes cloud pour simplifier la gestion des attributions de rôles. Pour protéger les rôles Azure AD et sécuriser l’accès, vous pouvez désormais utiliser Privileged Identity Management (PIM) pour gérer l’accès juste-à-temps pour les membres ou les propriétaires de ces groupes. Pour gérer un groupe assignable à un rôle Azure AD en tant que groupe d’accès privilégié dans Privileged Identity Management, vous devez l’amener sous la gestion de PIM.

## <a name="identify-groups-to-manage"></a>Identifier les groupes à gérer

Vous pouvez créer un groupe assignable à un rôle dans Azure AD comme décrit dans [Créer un groupe assignable à un rôle dans Azure Active Directory](../roles/groups-create-eligible.md). Vous devez être propriétaire du groupe pour l’amener sous la gestion de Privileged Identity Management.

1. [Connectez-vous à Azure AD](https://aad.portal.azure.com) avec les autorisations du rôle Administrateur de rôle privilégié.

1. Sélectionnez **Groupes**, puis sélectionnez le groupe assignable à un rôle que vous souhaitez gérer dans PIM. Vous pouvez rechercher dans la liste et la filtrer.

    ![Rechercher un groupe assignable à un rôle à gérer dans PIM](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. Ouvrez le groupe et sélectionnez **Accès privilégié (préversion)** .

    ![Ouvrir l’expérience Privileged Identity Management](./media/groups-discover-groups/groups-discover-groups.png)

1. Commencez à gérer les affectations dans PIM.

    ![Gérer les affectations dans Privileged Identity Management](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> Une fois qu’un groupe d’accès privilégié est géré, il ne peut pas être supprimé de la gestion. Cela empêche tout autre administrateur de ressources de supprimer des paramètres de Privileged Identity Management (PIM).

> [!IMPORTANT]
> Si un groupe d’accès privilégié est supprimé d’Azure Active Directory, la suppression du groupe dans le panneau Groupes d’accès privilégiés (préversion) peut prendre jusqu’à 24 heures.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les affectations de groupe d’accès privilégié dans Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Affecter des groupes d’accès privilégié dans Privileged Identity Management](pim-resource-roles-assign-roles.md)
