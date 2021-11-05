---
title: Effectuer une révision d’accès des groupes et applications - Azure AD
description: Découvrez comment effectuer une révision d’accès pour des membres de groupes ou pour l’accès aux applications dans les révisions d’accès Azure Active Directory.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 08/20/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29dc8e93af91f81a3d9b9426138e8f301c9528f7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052245"
---
# <a name="complete-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Effectuer une révision d’accès des groupes et applications dans les révisions d’accès Azure AD
 
En tant qu’administrateur, vous [créez une révision d’accès de groupes ou d’applications](create-access-review.md) tandis que les réviseurs [effectuent la révision d’accès](perform-access-review.md). Cet article explique comment visualiser les résultats de la révision d’accès et les appliquer.
 
[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]
 
## <a name="prerequisites"></a>Prérequis
 
- Azure AD Premium P2
- Administrateur général, administrateur d’utilisateurs ou administrateur de gouvernance des identités pour gérer l’accès aux révisions sur les groupes et les applications. Les administrateurs généraux et les administrateurs de rôle privilégié peuvent gérer les revues des groupes auxquels des rôles peuvent être attribués. Consultez [Utiliser des groupes Azure AD pour gérer les attributions de rôles](../roles/groups-concept.md).
- Les lecteurs Sécurité ont un accès en lecture.
 
Pour plus d’informations, consultez [Exigences des licences](access-reviews-overview.md#license-requirements).

 
## <a name="view-the-status-of-an-access-review"></a>Visualiser l’état d’une révision d’accès
 
Vous pouvez suivre la progression des révisions d’accès à mesure qu’elles sont effectuées.
 
1. Connectez-vous au portail Azure et ouvrez la page [Identity Governance](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).
 
1. Dans le menu de gauche, cliquez sur **Révisions d’accès**.
 
1. Dans la liste, cliquez sur une révision d’accès.
 
 
    Dans la page **Vue d’ensemble**, vous pouvez voir la progression de l’instance **Actuelle** de la revue. Si aucune instance active n’est ouverte à ce moment-là, vous voyez des informations sur l’instance précédente. Aucun droit d’accès n’est modifié dans le répertoire avant que la révision ne soit terminée.
 
     ![Révision du groupe All Company](./media/complete-access-review/all-company-group.png)
 
    Tous les volets sous **Actuelle** sont visibles seulement pendant la durée de chaque instance de révision. 
    > [!NOTE]
    > La révision d’accès **Actuelle** affiche seulement des informations sur l’instance de révision active, mais vous pouvez obtenir des informations sur les révisions qui doivent encore avoir lieu dans **Série** sous la section **Révision prévue** .
 
    La page Résultats fournit des informations supplémentaires sur chaque utilisateur révisé dans l’instance, notamment la possibilité d’arrêter, de réinitialiser et de télécharger les résultats.
 
    ![Réviser l’accès invité dans les groupes Microsoft 365](./media/complete-access-review/all-company-group-results.png)
 
    Si vous affichez une révision d’accès qui révise l’accès invité dans les groupes Microsoft 365, le panneau Vue d’ensemble répertorie chaque groupe dans la révision. 
   
    ![Réviser l’accès invité dans les groupes Microsoft 365](./media/complete-access-review/review-guest-access-across-365-groups.png)
 
    Cliquez sur un groupe pour voir la progression de la révision sur ce groupe, et pour arrêter, réinitialiser, appliquer et supprimer la révision.
 
   ![Réviser l’accès invité dans les groupes Microsoft 365 en détail](./media/complete-access-review/progress-group-review.png)
 
1. Si vous souhaitez arrêter une révision d’accès avant qu’elle ait atteint la date de fin planifiée, cliquez sur le bouton **Arrêter**.
 
    Lorsque vous arrêtez une révision, les réviseurs ne peuvent plus donner de réponses. Vous ne pouvez pas redémarrer une révision ayant été arrêtée.
 
1. Si vous n’êtes plus intéressé par la révision d’accès, vous pouvez la supprimer en cliquant sur le bouton **Supprimer**.
 
## <a name="retrieve-the-results"></a>Récupérer les résultats
 
Pour voir les résultats d’une révision, cliquez sur la page **Résultats**. Pour voir uniquement l’accès d’un utilisateur, dans la zone de recherche, tapez le nom d’affichage ou le nom d’utilisateur principal d’un utilisateur dont l’accès a été refusé.
 
![Récupérer les résultats d’une révision d’accès](./media/complete-access-review/retrieve-results.png) 
 
 
Pour visualiser les résultats d’une instance terminée d’une révision d’accès récurrente, cliquez sur **Historique des révisions**, puis choisissez l’instance concernée dans la liste des instances de révision d’accès terminées, en fonction des dates de début et de fin de l’instance. Vous pouvez obtenir les résultats de cette instance à partir de la page **Résultats**. Les révisions d’accès récurrentes vous permettent d’avoir une image constante de l’accès aux ressources qui peuvent nécessiter des mises à jour plus souvent que des révisions d’accès ponctuelles.
 
Pour récupérer les résultats d’une révision d’accès, en cours ou terminée, cliquez sur le bouton **Télécharger**. Le fichier CSV généré est consultable dans Excel ou dans d’autres programmes qui permettent d’ouvrir des fichiers CSV encodés UTF-8.


 

## <a name="apply-the-changes"></a>Appliquer les modifications
 
Si l’option **Appliquer automatiquement les résultats à la ressource** a été activée sur la base de vos sélections dans **Paramètres de saisie semi-automatique**, l’application automatique est exécutée une fois que l’instance de révision est terminée ou si vous arrêtez celle-ci manuellement.
 
Si l’option **Appliquer automatiquement les résultats à la ressource** n’a pas été activée pour la révision, accédez **Historique des révisions** sous **Série** une fois la révision terminée ou arrêtée, puis cliquez sur l’instance de la révision que vous souhaitez appliquer.
 
![Appliquer des modifications à une révision d’accès](./media/complete-access-review/apply-changes.png)
 
Cliquez sur **Appliquer** pour appliquer les modifications. Si l’accès d’un utilisateur a été refusé dans la révision, lorsque vous cliquez sur **Appliquer**, Azure AD supprime l’appartenance de cet utilisateur ou son attribution à l’application.
 
![Bouton Appliquer les modifications de révision d’accès](./media/complete-access-review/apply-changes-button.png)
 
La révision passe alors de l’état **Terminé** à divers états intermédiaires, comme **Application en cours**, pour arriver enfin à l’état **Résultat appliqué**. Les utilisateurs dont l’accès est refusé doivent normalement perdre leur appartenance au groupe ou leur affectation d’applications au bout de quelques minutes.
 
L’application manuelle ou automatique des résultats est sans effet sur un groupe provenant d’un annuaire local. Si vous souhaitez modifier un groupe qui provient d’un répertoire local, téléchargez les résultats et appliquez ces modifications à la représentation du groupe dans ce répertoire.

> [!NOTE]
> Les résultats ne peuvent pas être appliqués à certains utilisateurs refusés. Les scénarios où cela peut se produire sont les suivants :
> - Révision des membres d’un groupe AD Windows local synchronisé : si le groupe est synchronisé à partir d’un annuaire AD Windows local, le groupe ne peut pas être géré dans Azure AD et l’appartenance ne peut donc pas être modifiée.
> - Révision d’une ressource (rôle, groupe, application) avec des groupes imbriqués affectés : pour les utilisateurs qui sont membres via un groupe imbriqué, nous ne supprimons pas leur appartenance au groupe imbriqué et ils conservent donc l’accès à la ressource révisée.
> - Un utilisateur introuvable ou d’autres erreurs peuvent aussi entraîner la non-prise en charge de l’application du résultat.
 

## <a name="actions-taken-on-denied-guest-users-in-an-access-review"></a>Actions effectuées sur des utilisateurs invités refusés dans une révision d’accès
 
À la création de la révision, le créateur peut choisir entre deux options pour les utilisateurs invités refusés dans une révision d’accès. 
 - Les utilisateurs invités refusés peuvent avoir leur accès à la ressource supprimé. Il s’agit de la valeur par défaut.
 - La connexion de l’utilisateur invité refusé peut être bloquée pendant 30 jours, puis supprimée du locataire. Pendant la période de 30 jours, l’accès de l’utilisateur invité au locataire peut être restauré par un administrateur. Une fois la période de 30 jours terminée, si l’accès de l’utilisateur invité à la ressource ne lui a pas été accordé, il sera supprimé définitivement du locataire. En outre, à l’aide du portail Azure Active Directory, un Administrateur général peut explicitement [supprimer définitivement un utilisateur récemment supprimé](../fundamentals/active-directory-users-restore.md) avant que cette période arrive à son terme. Une fois qu’un utilisateur a été définitivement supprimé, les données sur cet utilisateur invité seront supprimées des révisions d’accès actives. Les informations d’audit relatives aux utilisateurs supprimés sont conservées dans le journal d’audit.


## <a name="next-steps"></a>Étapes suivantes
 
- [Gérer les révisions d’accès](manage-access-review.md) 
- [Créer une révision d’accès de groupes ou d’applications](create-access-review.md)
- [Create an access review of users in an Azure AD administrative role](../privileged-identity-management/pim-create-azure-ad-roles-and-resource-roles-review.md) (Créer une révision d’accès pour les utilisateurs dans un rôle administratif d’Azure AD)

