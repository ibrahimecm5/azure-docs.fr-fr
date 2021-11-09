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
ms.openlocfilehash: ff7c9d0e24eefb1c615f45f0aa33f5a1fe0af760
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097499"
---
# <a name="self-review-of-access-packages-and-resources-in-azure-ad-entitlement-management"></a>Auto-révision des packages d’accès et des ressources dans la gestion des droits d’utilisation d’Azure AD

La gestion des droits d'utilisation d’Azure AD simplifie la gestion de l’accès aux groupes, aux applications et aux sites SharePoint pour les entreprises. Cet article décrit comment un utilisateur effectue une auto-révision de ses packages d’accès attribués.

## <a name="review-your-own-access-using-my-access"></a>Passer en revue votre propre accès en utilisant Mon Accès

Vous pouvez passer en revue votre propre accès à un groupe, une application ou un package d’accès de deux façons, comme indiqué ci-dessous :

### <a name="email"></a>E-mail

>[!IMPORTANT]
> Il peut y avoir des retards dans la réception de l’e-mail et, dans certains cas, cela peut prendre jusqu’à 24 heures. Ajoutez azure-noreply@microsoft.com à votre liste de destinataires approuvés pour vous assurer de recevoir tous les e-mails.

1. Recherchez un e-mail de Microsoft vous invitant à réviser les accès. Vous pouvez voir un exemple d’e-mail ci-dessous :

 ![Exemple d’e-mail de Microsoft pour la révision d’accès d’un groupe](./media/self-access-review/access-review-email-preview.png)

2. Cliquez sur le lien **Réviser l’accès** pour ouvrir la révision d’accès.

3. Continuer dans la section **Effectuer la révision d’accès**

### <a name="directly-at-my-access"></a>Directement à Mon Accès

Vous pouvez également afficher vos révisions d’accès en attente en ouvrant Mon Accès dans votre navigateur.

1. Connectez-vous à Mon Accès à l’adresse https://myaccess.microsoft.com/.

2. Sélectionnez **Révisions d’accès** dans le menu de la barre latérale gauche pour afficher la liste des révisions d’accès en attente qui vous sont affectées.

   ![révisions d’accès dans le menu](./media/self-access-review/access-review-menu.png)

## <a name="perform-the-access-review"></a>Effectuer la révision d’accès

1. Sous Groupes et applications, vous pouvez voir :
    
    - **Nom** Nom de la révision d’accès.
    - **Échéance** Date d’échéance de la révision. Après cette date, des utilisateurs dont l’accès a été refusé pourraient être supprimés du groupe ou de l’application en cours de révision.
    - **Ressource** Nom de la ressource objet de la révision.
    - **Progression** Nombre d’utilisateurs révisés sur le nombre total d’utilisateurs entrant dans le cadre de la révision d’accès.
    
2. Cliquez sur le nom d’une révision d’accès pour commencer.

   ![Liste de révisions d’accès en attente pour les applications et les groupes](./media/self-access-review/access-reviews-list-preview.png)

3. Révisez votre accès et déterminez si vous en avez toujours besoin.

    Si la requête vise à réviser l’accès d’autres utilisateurs, la page aura un aspect différent. Pour plus d’informations, consultez [Réviser l’accès à des groupes ou à des applications](perform-access-review.md).

    ![Ouvrir la révision d’accès en vous demandant si vous avez toujours besoin de l’accès à un groupe](./media/self-access-review/review-access-preview.png)

1. Sélectionnez **Oui** pour conserver votre accès ou **Non** pour le supprimer.

1. Si vous cliquez sur **Oui**, vous devrez peut-être spécifier une justification dans la zone **Raison**.

    ![Révision d’accès déterminant si vous avez toujours besoin de l’accès à un groupe terminée](./media/self-access-review/review-access-yes-preview.png)

1. Cliquez sur **Envoyer**.

    Votre sélection est soumise et vous revenez au portail Mon Accès.

    Si vous souhaitez modifier votre réponse, rouvrez la page de révisions d’accès et mettez à jour votre réponse. Vous pouvez modifier votre réponse à tout moment jusqu'à ce que la révision d’accès soit terminée.

    > [!NOTE]
    > Si vous avez indiqué que vous n’avez plus besoin de l’accès, votre accès n’est pas supprimé immédiatement. Vous êtes supprimé une fois la révision terminée ou lorsqu’un administrateur interrompt la révision.

## <a name="next-steps"></a>Étapes suivantes

- [Effectuer une révision d’accès de groupes ou révisions d’accès des applications dans Azure AD](complete-access-review.md)


