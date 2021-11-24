---
title: Réviser votre accès aux ressources dans les révisions d’accès – Azure AD
description: Découvrez comment réviser votre propre accès à des ressources dans les révisions d’accès Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 08/27/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: a997e4f92e1336d75f1c9c690b4c84ae0bddcf6f
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524148"
---
# <a name="self-review-of-access-packages-and-resources-in-azure-ad-entitlement-management"></a>Auto-révision des packages d’accès et des ressources dans la gestion des droits d’utilisation d’Azure AD

La gestion des droits d'utilisation d’Azure Active Directory (Azure AD) simplifie la gestion de l’accès aux groupes, aux applications et aux sites SharePoint pour les entreprises. Cet article explique comment vous pouvez effectuer un auto-examen de vos packages d’accès attribués.

## <a name="review-your-own-access-by-using-my-access"></a>Passer en revue votre propre accès en utilisant Mon Accès

Vous pouvez passer en revue votre propre accès à un groupe, une application ou un package d’accès de deux façons.

### <a name="use-email"></a>Utiliser l’e-mail

>[!IMPORTANT]
> Il peut y avoir des retards dans la réception de l’e-mail et, dans certains cas, cela peut prendre jusqu’à 24 heures. Ajoutez azure-noreply@microsoft.com à votre liste de destinataires approuvés pour vous assurer de recevoir tous les e-mails.

1. Recherchez un e-mail de Microsoft vous invitant à réviser les accès. Voici un exemple d’e-mail.

   ![Capture d’écran représentant un exemple d’e-mail de Microsoft vous demandant de réviser l’accès à un groupe.](./media/self-access-review/access-review-email-preview.png)

1. Sélectionnez **Réviser l’accès** pour ouvrir la révision d’accès.

1. Continuez dans la section **Effectuer la révision d’accès**.

### <a name="use-my-access"></a>Utiliser mon accès

Vous pouvez également afficher vos révisions d’accès en attente en ouvrant **Mon Accès** dans votre navigateur.

1. Connectez-vous à [Mon Accès](https://myaccess.microsoft.com/).

1. Dans le menu à gauche, sélectionnez **Révisions d’accès** pour afficher la liste des révisions d’accès en attente qui vous sont affectées.

   ![Capture d’écran montrant les révisions d’accès dans le menu.](./media/self-access-review/access-review-menu.png)

## <a name="do-the-access-review"></a>Faire la révision d’accès

1. Sous **Groupes et applications**, vous pouvez voir :

    - **Nom** : Nom de la révision d’accès.
    - **Échéance** : Date d’échéance de la révision. Après cette date, des utilisateurs dont l’accès a été refusé pourraient être supprimés du groupe ou de l’application en cours de révision.
    - **Ressource** : Nom de la ressource objet de la révision.
    - **Progression** : Nombre d’utilisateurs révisés sur le nombre total d’utilisateurs entrant dans le cadre de la révision d’accès.

1. Sélectionnez le nom d’une révision d’accès pour commencer.

   ![Capture d’écran représentant une liste de révisions d’accès en attente pour les applications et les groupes.](./media/self-access-review/access-reviews-list-preview.png)

1. Révisez votre accès et déterminez si vous en avez toujours besoin.

    Si la requête vise à réviser l’accès d’autres utilisateurs, la page aura un aspect différent. Pour plus d’informations, consultez [Réviser l’accès à des groupes ou à des applications](perform-access-review.md).

    ![Capture d’écran montrant une révision d’accès ouverte qui vous demande si vous avez toujours besoin d’accéder à un groupe.](./media/self-access-review/review-access-preview.png)

1. Sélectionnez **Oui** pour conserver votre accès, ou **Non** pour le supprimer.

1. Si vous sélectionnez **Oui**, vous devrez peut-être spécifier une justification dans la zone **Raison**.

    ![Capture d’écran qui montre comment sélectionner Oui pour conserver l’accès à un groupe.](./media/self-access-review/review-access-yes-preview.png)

1. Sélectionnez **Envoyer**.

    Votre sélection est soumise et vous revenez au portail **Mon Accès**.

    Si vous souhaitez modifier votre réponse, rouvrez la page de **révisions d’accès** et mettez à jour votre réponse. Vous pouvez modifier votre réponse à tout moment jusqu'à ce que la révision d’accès soit terminée.

    > [!NOTE]
    > Si vous avez indiqué que vous n’avez plus besoin de l’accès, votre accès n’est pas supprimé immédiatement. Vous êtes supprimé une fois la révision terminée ou lorsqu’un administrateur interrompt la révision.

## <a name="next-steps"></a>Étapes suivantes

[Effectuer une révision d’accès de groupes ou révisions d’accès des applications dans Azure AD](complete-access-review.md)
