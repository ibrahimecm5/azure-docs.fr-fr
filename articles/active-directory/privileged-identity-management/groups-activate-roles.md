---
title: Activer les rôles de groupe d'accès privilégié dans PIM - Azure AD | Microsoft Docs
description: Découvrez comment activer vos rôles de groupe d'accès privilégié dans Azure AD Privileged Identity Management (PIM).
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
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 365f480af81289a9f48dd6ce5a1102be7a7ed6a1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444152"
---
# <a name="activate-my-privileged-access-group-roles-in-privileged-identity-management"></a>Activer mes rôles de groupe d’accès privilégié dans Privileged Identity Management

Utilisez Privileged Identity Management (PIM) pour permettre aux membres de rôle éligibles pour les groupes d’accès privilégiés de planifier l’activation de rôle pour une date et une heure spécifiques. Ils peuvent également sélectionner une durée d’activation jusqu’à la durée maximale configurée par les administrateurs.

Cet article est destiné aux membres éligibles qui souhaitent activer leur rôle de groupe d’accès privilégié dans Privileged Identity Management.

## <a name="activate-a-role"></a>Activer un rôle

Lorsque vous avez besoin d’endosser un rôle de groupe d’accès privilégié, vous pouvez demander une activation à l’aide de l’option de la navigation **Mes rôles** dans Privileged Identity Management.

1. [Connectez-vous au portail Azure AD](https://aad.portal.azure.com) avec les autorisations d'administrateur général ou de propriétaire de groupe.

1. Ouvrez [Privileged Identity Management](https://portal.azure.com/#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart).

1. Sélectionnez **Groupes d'accès privilégiés (préversion)** , puis sélectionnez **Activer le rôle** pour ouvrir la page **Mes rôles** pour les groupes d'accès privilégiés.

    ![Page rôles d’accès privilégié dans PIM](./media/groups-activate-roles/groups-select-group.png)

1. Sur la page **Mes rôles**, sélectionnez **Activer** sur la ligne de l'affectation éligible que vous souhaitez activer.

    ![Activer le lien sur la ligne d’attribution de rôle éligible](./media/groups-activate-roles/groups-activate-link.png)

1. Si votre rôle exige une authentification multifacteur, sélectionnez **Vérifier votre identité avant de continuer**. Vous ne devez vous authentifier qu’une seule fois par session.

    ![Vérifier mon identité à l’aide de l’authentification multifacteur avant activation du rôle](./media/groups-activate-roles/groups-my-roles-mfa.png)

1. Sélectionnez **Vérifier mon identité** et suivez les instructions pour effectuer une vérification de sécurité supplémentaire.

    ![Écran de vérification de sécurité permettant la saisie d’un code secret, par exemple](./media/groups-activate-roles/groups-mfa-enter-code.png)

1. Si nécessaire, spécifiez une heure de début personnalisée pour l’activation. Le membre ou le propriétaire ne doit être activé qu’après l’heure sélectionnée.

1. Dans la zone de texte **Raison**, entrez le motif de la demande d’activation.

    ![Activer la page avec une durée et une justification](./media/groups-activate-roles/groups-activate-page.png)

1. Sélectionnez **Activer**.

Si [l’activation du rôle nécessite une approbation](pim-resource-roles-approval-workflow.md), une notification Azure s’affiche dans le coin supérieur droit de votre navigateur pour vous informer que la demande est en attente d’approbation.

## <a name="view-the-status-of-your-requests"></a>Afficher l’état de vos demandes

Vous pouvez afficher l’état de vos demandes d’activation en attente.

1. Ouvrez Azure AD Privileged Identity Management.

1. Sélectionnez **Mes demandes** pour afficher la liste des demandes de vos rôles Azure AD et des requêtes de rôles de groupe d’accès privilégié.

1. Si nécessaire, faites défiler vers la droite pour afficher la colonne **État de la demande**.

## <a name="cancel-a-pending-request"></a>Annuler une demande en attente

Si vous n’avez pas besoin de l’activation d’un rôle nécessitant une approbation, vous pouvez annuler une demande en attente à tout moment.

1. Ouvrez Azure AD Privileged Identity Management.

1. Sélectionnez **Mes demandes**.

1. Pour le rôle que vous souhaitez annuler, sélectionnez le lien **Annuler**.

    Lorsque vous sélectionnez **Annuler**, la requête est annulée. Pour réactiver le rôle, vous devez envoyer une nouvelle demande d’activation.

## <a name="troubleshoot"></a>Dépanner

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Les autorisations ne sont pas accordées après l’activation d’un rôle

Quand vous activez un rôle dans Privileged Identity Management, il est possible que l’activation ne se propage pas instantanément à tous les portails qui nécessitent le rôle privilégié. Parfois, même si la modification est propagée, son application immédiate peut être empêchée par la mise en cache web dans un portail. Si l’activation est retardée, voici les étapes à suivre.

1. Déconnectez-vous du portail Microsoft Azure, puis reconnectez-vous.
1. Dans Privileged Identity Management, vérifiez que vous êtes répertorié en tant que membre du rôle.

## <a name="next-steps"></a>Étapes suivantes

- [Étendre ou renouveler des rôles de groupe d’accès privilégié dans Privileged Identity Management](groups-renew-extend.md)
- [Attribuer mes rôles de groupe d’accès privilégié dans Privileged Identity Management](groups-assign-member-owner.md)
