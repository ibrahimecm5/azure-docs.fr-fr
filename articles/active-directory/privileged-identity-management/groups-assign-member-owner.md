---
title: Attribuer l’éligibilité à l’appartenance ou la propriété de groupes d’accès privilégié - Azure Active Directory
description: Découvrez comment attribuer l’éligibilité à l’appartenance ou la propriété d’un groupe avec attribution de rôle dans Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/09/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62012b910ad1e08291760a2aea0747bf5d4c024d
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132157578"
---
# <a name="assign-eligibility-for-a-privileged-access-group-preview-in-privileged-identity-management"></a>Attribuer l’éligibilité à l’appartenance ou la propriété d’un groupe d’accès privilégié (préversion) dans Privileged Identity Management

Azure AD PIM (Azure Active Directory Privileged Identity Management) vous permet de gérer l’éligibilité à l’appartenance ou la propriété de groupes d’accès privilégié et l’activation des attributions correspondantes dans Azure AD. Vous pouvez attribuer l’éligibilité à des membres ou propriétaires du groupe.

>[!NOTE]
>Chaque utilisateur éligible à l’appartenance ou à la propriété d’un groupe d’accès privilégié doit avoir une licence Azure AD Premium P2. Pour plus d’informations, consultez [Exigences relatives aux licences pour l’utilisation de Privileged Identity Management](subscription-requirements.md).

## <a name="assign-an-owner-or-member-of-a-group"></a>Attribuer l’éligibilité à l’appartenance ou à la propriété d’un groupe

Effectuez les étapes suivantes pour rendre un utilisateur éligible à l’appartenance ou à la propriété d’un groupe d’accès privilégié.

1. Connectez-vous au [Centre d’administration Azure AD](https://aad.portal.azure.com/) avec un utilisateur ayant le rôle d’[administrateur général](../roles/permissions-reference.md#global-administrator), d’administrateur de rôle privilégié ou de propriétaire de groupe.

1. Sélectionnez **Groupes**, puis sélectionnez le groupe assignable à un rôle que vous souhaitez gérer. Vous pouvez effectuer une recherche dans la liste et filtrer celle-ci.

    ![Rechercher un groupe assignable à un rôle à gérer dans PIM](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. Ouvrez le groupe et sélectionnez **Accès privilégié (préversion)** .

    ![Ouvrir l’expérience Privileged Identity Management](./media/groups-assign-member-owner/groups-discover-groups.png)

1. Sélectionnez **Ajouter des affectations**.

    ![Volet Nouvelle affectation](./media/groups-assign-member-owner/groups-add-assignment.png)

1. Sélectionnez les membres ou propriétaires que vous souhaitez rendre éligibles vis-à-vis du groupe d’accès privilégié.

    ![Capture d’écran représentant la page « Add assignments » (« Ajouter des affectations ») dans laquelle le volet « Select a member or group » (« Sélectionner un membre ou un groupe ») est ouvert et le bouton « Select » (« Sélectionner ») est mis en surbrillance.](./media/groups-assign-member-owner/add-assignments.png)

1. Sélectionnez **Suivant** pour définir la durée de l’appartenance ou de la propriété.

    ![Volet Sélectionner un membre ou un groupe](./media/groups-assign-member-owner/assignment-duration.png)

1. Dans la liste **Type d'attribution**, sélectionnez **Éligible** ou **Actif**. Les groupes d’accès privilégié offrent deux types d’attribution distincts :

    - Les attributions **éligibles** exigent des membres qu’ils effectuent une action pour utiliser ce rôle. Il peut s’agir de procéder à une vérification de l’authentification multifacteur (MFA), de fournir une justification professionnelle ou de demander une approbation aux approbateurs désignés. 

      > [!Important]
      > Pour les groupes d’accès privilégiés utilisés pour l’élévation des rôles Azure AD, Microsoft recommande de demander un processus d’approbation pour les attributions de membres éligibles. Les attributions qui peuvent être activées sans approbation peuvent vous exposer à un risque de sécurité, dans lequel un autre administrateur est autorisé à réinitialiser les mots de passe d’un utilisateur éligible.

    - Les attributions **actives** n’exigent pas des membres qu’ils effectuent une action pour utiliser ce rôle. Les membres attribués comme étant actifs détiennent à tout moment les privilèges affectés au rôle.

1. Si l’attribution doit être permanente (éligible de façon permanente ou attribuée définitivement), cochez la case **Définitivement**. Selon les paramètres de votre organisation, il se peut que cette case à cocher ne soit pas affichée ou ne soit pas modifiable.

1. Lorsque vous avez terminé, sélectionnez **Attribuer**.

1. Pour créer la nouvelle attribution de rôle, sélectionnez **Ajouter**. Une notification de l’état s’affiche.

    ![Nouvelle affectation - Notification](./media/groups-assign-member-owner/groups-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Mettre à jour ou supprimer une attribution de rôle existante

Suivez ces étapes pour mettre à jour ou supprimer une attribution de rôle existante.

1. [Connectez-vous à Azure AD](https://aad.portal.azure.com) avec les autorisations d'administrateur général ou de propriétaire de groupe.
1. Sélectionnez **Groupes**, puis sélectionnez le groupe assignable à un rôle que vous souhaitez gérer. Vous pouvez effectuer une recherche dans la liste et filtrer celle-ci.

    ![Rechercher un groupe assignable à un rôle à gérer dans PIM](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. Ouvrez le groupe et sélectionnez **Accès privilégié (préversion)** .

    ![Ouvrir l’expérience Privileged Identity Management](./media/groups-assign-member-owner/groups-discover-groups.png)

1. Sélectionnez le rôle que vous souhaitez mettre à jour ou supprimer.

1. Recherchez l’attribution de rôle sous les onglets **Rôles éligibles** et **Rôles actifs**.

    ![Mettre à jour ou supprimer une attribution de rôle](./media/groups-assign-member-owner/groups-bring-under-management.png)

1. Sélectionnez **Mettre à jour** ou **Supprimer** pour mettre à jour ou supprimer l’attribution de rôle.

    Pour obtenir des informations sur l’extension d’une attribution de rôle, consultez [Étendre ou renouveler des rôles de ressources Azure dans Privileged Identity Management](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Étapes suivantes

- [Étendre ou renouveler des rôles de ressources Azure dans Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Configurer les paramètres des rôles de ressources Azure dans Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Attribuer des rôles dans Azure AD dans Privileged Identity Management](pim-how-to-add-role-to-user.md)
