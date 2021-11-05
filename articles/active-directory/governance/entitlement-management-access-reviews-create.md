---
title: Créer une révision d’accès d’un package d’accès dans la gestion des droits d’utilisation Azure AD
description: Découvrez comment créer une stratégie de révision d’accès pour les packages d’accès de gestion des droits d’utilisation dans les révisions d’accès Azure Active Directory (préversion).
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 10/26/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8e4b08f407417136462ff066d0f0e81312662ae
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131051696"
---
# <a name="create-an-access-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Créer une révision d’accès d’un package d’accès dans la gestion des droits d’utilisation Azure AD

Pour réduire le risque d’un accès obsolète, vous devez activer les révisions périodiques des utilisateurs qui ont des attributions actives à un package d’accès dans la gestion des droits d'utilisation Azure AD. Vous pouvez activer les révisions lorsque vous créez un nouveau package d’accès ou modifiez un package d’accès existant. Cet article explique comment activer des révisions d’accès des packages d'accès.

## <a name="prerequisites"></a>Prérequis

Pour activer des révisions des packages d’accès, vous devez remplir les conditions préalables à la création d’un package d’accès :
- Azure AD Premium P2
- Administrateur général, administrateur de la gouvernance des identités, administrateur d’utilisateurs, propriétaire de catalogue ou gestionnaire de package d’accès

Pour plus d’informations, consultez [Exigences des licences](entitlement-management-overview.md#license-requirements).


## <a name="create-an-access-review-of-an-access-package"></a>Créer une révision d’accès pour un package d'accès

Vous pouvez activer des révisions d’accès lorsque [vous créez un nouveau package d’accès](entitlement-management-access-package-create.md) ou [modifiez une stratégie de package d’accès existant](entitlement-management-access-package-lifecycle-policy.md). Pour activer les révisions d’accès d’un package d’accès, procédez comme suit :

1. Ouvrez l’onglet **Cycle de vie** pour un package d'accès afin de spécifier l’expiration de l’attribution du package d’accès à un utilisateur. Vous pouvez également spécifier si les utilisateurs peuvent étendre leurs attributions.

1. Dans la section **Expiration**, définissez L’affectation de package d’accès expire sur **Date**, **Nombre de jours**, **Nombre d’heures** ou **Jamais**.

    Pour **Date**, sélectionnez une date d’expiration.

    Pour **Nombre de jours**, spécifiez un nombre de jours compris entre 0 et 3 660.

    Dans **Nombre d’heures**, indiquez un nombre d’heures.

    Selon votre sélection, l’attribution du package d’accès à un utilisateur expire à une date définie, un nombre de jours définis après approbation ou n’expire jamais.
    
    ![Package d’accès - Paramètres d’expiration du cycle de vie](./media/entitlement-management-access-reviews/expiration.png)

1. Cliquez sur Afficher les paramètres avancés d’expiration pour afficher plus de paramètres.

1. Pour autoriser un utilisateur à étendre son attribution, définissez **Autoriser un utilisateur à étendre son accès** sur **Oui**.

    Si les extensions sont autorisées dans la stratégie, l’utilisateur recevra un e-mail 14 jours ainsi qu’un jour avant l’expiration de son attribution l’invitant à étendre l’attribution. L’utilisateur doit toujours se trouver dans l’étendue de la stratégie au moment où il demande une prolongation. En outre, si la stratégie a une date de fin explicite pour les attributions et qu’un utilisateur envoie une demande de prolongation de l’accès, la date de prolongation de la demande doit être au plus tard la date d’expiration des attributions, comme défini dans la stratégie utilisée pour accorder à l’utilisateur l’accès au package d’accès. Par exemple, si la stratégie indique que les affectations sont configurées pour expirer le 30 juin, l’extension maximale qu’un utilisateur peut demander est le 30 juin.

    Si l’accès d’un utilisateur est étendu, il ne pourra pas demander le package d’accès après la date d’extension spécifiée (date définie dans le fuseau horaire de l’utilisateur qui a créé la stratégie).

1. Pour demander une approbation pour accorder une extension, définissez **Exiger une approbation pour accorder l'extension** sur **Oui**.

    Les mêmes paramètres d’approbation que ceux spécifiés sous l’onglet Requêtes seront utilisés.

1. Ensuite, déplacez la bascule **Demander des révisions d’accès** vers **Oui**.

    ![Ajouter la révision d’accès](./media/entitlement-management-access-reviews/access-reviews-pane.png)

1. Spécifiez la date à laquelle les révisions démarreront en regard de **Date de début**.

1. Ensuite, définissez **Fréquence de vérification** sur **Annuelle**, **Bi-annuelle**, **Trimestrielle** ou **Mensuelle**.
Ce paramètre détermine la fréquence à laquelle les révisions d’accès auront lieu.

1. Configurez la **Durée** pour définir le nombre de jours pendant lequel chaque révision de la série périodique sera ouverte pourra être modifiée par les réviseurs. Par exemple, vous pouvez planifier une révision annuelle qui commence le 1er janvier et qui est ouverte pour une période de 30 jours, de sorte que les réviseurs ont jusqu’à la fin du mois pour répondre.

1. En regard de **Relecteurs**, sélectionnez **Révision automatique** si vous souhaitez que les utilisateurs effectuent leur propre révision d’accès ou sélectionnez **Réviseur(s) spécifique(s)** si vous souhaitez désigner un réviseur. Vous pouvez également sélectionner **Manager** si vous souhaitez désigner comme réviseur le manager de l’utilisateur qui fait l’objet de la révision. Si vous sélectionnez cette option, vous devez ajouter un **réviseur de secours** à qui confier la révision dans le cas où le manager soit introuvable dans le système.

1. Si vous avez sélectionné **Réviseur(s) spécifique(s)** , spécifiez les utilisateurs qui feront la révision d’accès : ![Sélectionnez Ajouter des réviseurs](./media/entitlement-management-access-reviews/access-reviews-add-reviewer.png)

    1. Sélectionnez **Ajouter des réviseurs**.
    1. Dans le volet **Sélectionner des réviseurs**, recherchez et sélectionnez le ou les utilisateurs que vous souhaitez utiliser comme réviseurs.
    1. Une fois que vous avez sélectionné le ou les réviseurs, cliquez sur le bouton **Sélectionner**.

    ![Spécifier les réviseurs](./media/entitlement-management-access-reviews/access-reviews-select-reviewer.png)

1. Si vous avez sélectionné  **Manager**, spécifiez le réviseur de secours : 
    1. Sélectionnez  **Ajouter des réviseurs de secours**.
    1. Dans le volet Sélectionner les réviseurs de secours, recherchez puis sélectionnez le ou les utilisateurs que vous souhaitez désigner comme réviseurs de secours pour le manager.
    1. Une fois que vous avez sélectionné le ou les réviseurs de secours, cliquez sur le bouton  **Sélectionner**. 

    ![Ajouter les réviseurs de secours](./media/entitlement-management-access-reviews/access-reviews-select-manager.png)

1. Cliquez sur **Évaluer + Créer** si vous créez un nouveau package d’accès ou **Mettre à jour** si vous modifiez un package d’accès, en bas de la page.

## <a name="view-the-status-of-the-access-review"></a>Afficher l’état de la révision d’accès

Après la date de début, une révision d’accès est indiquée dans la section **Révisions de l’accès**. Pour afficher l’état d’une révision d’accès, procédez comme suit :

1. Dans **Gouvernance des identités**, cliquez sur **Packages d’accès** puis sélectionnez le package d’accès avec l’état de révision d’accès que vous souhaitez vérifier.   

1. Une fois dans l’aperçu du package d’accès, cliquez sur **Révisions d’accès** dans le menu de gauche.
    
    ![Sélectionner des révisions d’accès](./media/entitlement-management-access-reviews/access-review-status-access-package-overview.png)

1. Une liste contenant toutes les stratégies auxquelles des révisions d’accès sont associées s’affiche. Cliquez sur la révision pour afficher son rapport.

    ![Liste des révisions d’accès](./media/entitlement-management-access-reviews/access-review-status-select-access-reviews.png)
   
1. Lorsque vous affichez le rapport, il affiche le nombre d’utilisateurs revus et les actions effectuées par le réviseur.

    ![Afficher l’état de révision](./media/entitlement-management-access-reviews/access-review-status.png)
 

## <a name="access-reviews-email-notifications"></a>Notifications par e-mail de révisions d’accès
Vous pouvez désigner des réviseurs ou les utilisateurs peuvent examiner leur accès eux-mêmes. Par défaut, Azure AD envoie un e-mail aux réviseurs ou aux réviseurs indépendants peu de temps après le démarrage de la révision.

L’e-mail inclut des instructions sur la façon d’examiner l’accès aux packages d’accès. Si la révision est destinée aux utilisateurs pour évaluer leur accès, indiquez-leur les instructions sur la façon d’effectuer une auto-analyse de leurs packages d’accès.
  
Si vous avez attribué des utilisateurs invités en tant que réviseurs et qu’ils n’ont pas accepté leur invitation invité Azure AD, ils ne recevront pas d’e-mails des révisions d’accès Azure AD. Ils doivent d’abord accepter l’invitation et créer un compte avec Azure AD avant de pouvoir recevoir les e-mails. 

## <a name="next-steps"></a>Étapes suivantes

- [Évaluation de l’accès aux packages d’accès](entitlement-management-access-reviews-review-access.md)
- [Auto-évaluation des packages d’accès](entitlement-management-access-reviews-self-review.md)
