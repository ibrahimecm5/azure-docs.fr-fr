---
title: Créer une révision d’accès aux groupes et applications - Azure AD
description: Découvrez comment créer une révision d’accès pour les membres de groupes ou pour l’accès aux applications dans Azure Active Directory.
services: active-directory
author: ajburnle
manager: karenhoran
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
ms.openlocfilehash: 224c81d1f5e827bb53fce1cfdeae22028ec1b598
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517669"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad"></a>Créer une révision d’accès aux groupes et applications dans Azure AD

L’accès aux groupes et aux applications pour les employés et les invités change au fil du temps. Afin de réduire les risques associés aux affectations d’accès obsolètes, les administrateurs peuvent utiliser Azure Active Directory (Azure AD) pour créer des révision des accès pour les membres de groupes ou pour l’accès aux applications.

Microsoft 365 et les propriétaires du groupe de sécurité peuvent également utiliser Azure AD pour créer des révisions d’accès pour les membres du groupe tant que l’administrateur général ou utilisateur active le paramètre via le volet **Paramètres de révision d’accès** (préversion). Pour plus d’informations sur ces scénarios, consultez [Gérer les révisions d’accès](manage-access-review.md).

Regardez une brève vidéo à propos de l’activation des révisions d’accès.

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

Cet article explique comment créer une ou plusieurs révisions d’accès pour les membres de groupes ou pour l’accès aux applications.

## <a name="prerequisites"></a>Prérequis

- Azure AD Premium P2.
- Administrateur général, administrateur utilisateur ou administrateur de gouvernance des identités pour créer des révisions sur les groupes ou les applications.
- Les administrateurs généraux et les administrateurs de rôle privilégié peuvent créer des révisions sur des groupes assignables à un rôle. Pour plus d’informations, consultez [Utiliser des groupes cloud pour gérer les attributions de rôles dans Azure AD](../roles/groups-concept.md).
- (Préversion) Microsoft 365 et Propriétaire du groupe de sécurité.

Pour plus d’informations, consultez [Exigences des licences](access-reviews-overview.md#license-requirements).

## <a name="create-one-or-more-access-reviews"></a>Créer une ou plusieurs révisions d’accès

1. Connectez-vous au portail Azure et ouvrez la page [Gouvernance des identités](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Dans le menu de gauche, sélectionnez **Révisions d’accès**.

1. Sélectionnez **Nouvelle révision d’accès** pour créer une révision d’accès.

    ![Capture d’écran montrant le volet Révisions d’accès dans Gouvernance des identités.](./media/create-access-review/access-reviews.png)

1. Dans la boîte de dialogue **Éléments à réviser**, sélectionnez la ressource que vous souhaitez réviser.

    ![Capture d’écran montrant la création d’une révision d’accès.](./media/create-access-review/select-what-review.png)

1. Si vous avez sélectionné **Équipes + groupes** à l’étape 1, vous avez deux options :

   - **Tous les groupes Microsoft 365 avec des utilisateurs invités** : Sélectionnez cette option si vous souhaitez créer des révisions périodiques sur tous vos utilisateurs invités dans l’ensemble des équipes Microsoft et des groupes Microsoft 365 de votre organisation. Groupes dynamiques et groupes attribuables à un rôle non inclus. Vous pouvez également choisir d’exclure des groupes individuels en cliquant sur **Sélectionner le(s) groupe(s) à exclure**.
   - **Sélectionnez les équipes + groupes** : sélectionnez cette option si vous souhaitez spécifier un ensemble limité d’équipes et/ou de groupes à réviser. Une liste de groupes parmi lesquels choisir s’affiche à droite.

     ![Capture d’écran montrant la sélection des équipes et des groupes.](./media/create-access-review/teams-groups.png)

1. Si vous avez sélectionné **Applications**, vous pouvez sélectionner une ou plusieurs applications.

   ![Capture d’écran qui montre l’interface qui s’affiche si vous avez sélectionné Applications au lieu de Groupes.](./media/create-access-review/select-application-detailed.png)

    > [!NOTE]
    > La sélection de plusieurs groupes ou applications entraîne la création de plusieurs révisions d’accès. Par exemple, si vous sélectionnez cinq groupes à réviser, cela donnera lieu à cinq révisions d’accès distinctes.

1. Désormais, vous pouvez sélectionner l’étendue de la révision. Les options disponibles sont :

    - **Utilisateurs invités uniquement** : cette option limite la révision d’accès aux seuls utilisateurs invités d’Azure AD B2B dans votre répertoire.
    - **Tout le monde** : cette option permet d’étendre la révision d’accès à tous les objets utilisateur associés à la ressource.

    > [!NOTE]  
    > Si vous avez sélectionné **Tous les groupes Microsoft 365 avec des utilisateurs invités**, votre seule option est la révision sur les **Utilisateurs invités uniquement**.

1. Sélectionnez **Suivant : Révision**.

1. Dans la section **Sélectionner les réviseurs**, dans la boîte de dialogue **Sélectionner les réviseurs** sélectionnez une ou plusieurs personnes pour effectuer les révisions d’accès. Vous pouvez choisir :

    - **Propriétaires de groupe** : cette option est disponible uniquement lors de la révision d’une équipe ou d’un groupe.
    - **Utilisateur(s) ou groupe(s) sélectionné(s)** .
    - **Les utilisateurs révisent leur propre accès**
    - **Managers des utilisateurs**

   Si vous choisissez **Managers des utilisateurs** ou **Propriétaires de groupes**, vous avez également la possibilité de spécifier un réviseur de secours. Les réviseurs de secours sont invités à effectuer une révision lorsque l’utilisateur n’a pas de manager spécifié dans le répertoire ou que le groupe n’a pas de propriétaire.

      ![Capture d’écran montrant la nouvelle révision d’accès.](./media/create-access-review/new-access-review.png)

1. Dans la section **Spécifier la récurrence de la révision**, précisez les éléments suivants :

   - **Durée (en jours)** : pendant combien de temps une révision sera ouverte à la contribution des réviseurs.
   - **Date de début** : date de début de la série de révisions.
   - **Date de fin** : date de fin de la série de révisions. Vous pouvez spécifier qu’elle ne se termine **jamais**. Ou alors, vous pouvez choisir qu’elle se termine à une **date spécifique** ou qu’elle se termine **après le nombre d’occurrences**.

     ![Capture d’écran qui montre le choix de la fréquence à laquelle la révision doit se produire.](./media/create-access-review/frequency.png)

1. Sélectionnez **Suivant : Paramètres**.

1. Dans la section **Paramètres de saisie semi-automatique**, vous pouvez spécifier ce qui se produit une fois la révision terminée.

    ![Capture d’écran qui illustre la section Paramètres de saisie semi-automatique.](./media/create-access-review/upon-completion-settings-new.png)

    - **Appliquer automatiquement les résultats à la ressource** : cochez cette case si vous souhaitez que l’accès des utilisateurs refusés soit supprimé automatiquement une fois la durée de la révision terminée. Si l’option est désactivée, vous devrez appliquer manuellement les résultats une fois la révision terminée. Pour en savoir plus sur l’application des résultats de la révision, consultez [Gérer les révisions d’accès](manage-access-review.md).

    - **Si les réviseurs ne répondent pas** : utilisez cette option pour spécifier ce qui se passe pour les utilisateurs dont l’accès n’a pas été vérifié par un réviseur quelconque au cours de la révision. Ce paramètre n’affecte pas les utilisateurs qui ont fait l’objet d’un examen par un réviseur. La liste déroulante affiche les options suivantes :

       - **Aucune modification** : laisse l’accès de l’utilisateur inchangé.
       - **Supprimer l’accès** : supprime l’accès de l’utilisateur.
       - **Approuver l’accès** : approuve l’accès de l’utilisateur.
       - **Accepter les recommandations** : accepte la recommandation du système sur le refus ou l’approbation de la prolongation de l’accès de l’utilisateur.

    - **Action à appliquer sur les utilisateurs invités refusés** : cette option est disponible uniquement si la révision d’accès est limitée à inclure uniquement les utilisateurs invités, pour spécifier ce qui arrive aux utilisateurs invités s’ils sont refusés par un réviseur ou par le paramètre **Si les réviseurs ne répondent pas**.

       - **Supprimer l’appartenance de l’utilisateur à la ressource** : cette option supprime l’accès de l’utilisateur invité refusé au groupe ou à l’application en cours de révision. Ils peuvent toujours se connecter au locataire et ne perdent aucun autre accès.
       - **Empêcher l’utilisateur de se connecter pendant 30 jours, puis supprimer l’utilisateur du locataire** : cette option empêche un utilisateur invité refusé de se connecter au locataire, même s’il a accès à d’autres ressources. Si cette action a été effectuée par erreur, les administrateurs peuvent réactiver l’accès de l’utilisateur invité dans les 30 jours suivant la désactivation de l’utilisateur invité. Si aucune action n’est effectuée sur l’utilisateur invité désactivé après 30 jours, il est supprimé du locataire.

    Pour en savoir plus sur les meilleures pratiques pour la suppression des utilisateurs invités qui n’ont plus accès aux ressources de votre organisation, consultez [Utiliser Azure AD Identity Governance pour examiner et supprimer les utilisateurs externes qui n’ont plus accès aux ressources](access-reviews-external-users.md).

    > [!NOTE]
    > L’**Action à appliquer sur les utilisateurs invités refusés** n’est pas configurable sur les révisions dont l’étendue est plus large que celle des utilisateurs invités. Elle n’est pas non plus configurable pour les révisions de **Tous les groupes Microsoft 365 avec des utilisateurs invités.** Lorsqu’elle n’est pas configurable, l’option par défaut de suppression de l’appartenance de l’utilisateur à la ressource est utilisée sur les utilisateurs refusés.

1. Utilisez l’option **À la fin de la révision, envoyer une notification à** pour envoyer des notifications à d’autres utilisateurs ou groupes contenant les mises à jour d’achèvement de la révision. Cette fonctionnalité permet d’informer les parties prenantes autres que le créateur de la révision de la progression de la révision. Pour utiliser cette fonctionnalité, choisissez **Sélectionner un ou plusieurs utilisateurs ou groupes** et ajoutez un autre utilisateur ou groupe pour lequel vous souhaitez recevoir l’état d’achèvement.

1. Dans la section **Activer l’assistance aux décisions de révision**, indiquez si vous souhaitez que votre réviseur reçoive des recommandations au cours du processus de révision. Lorsque cette option est activée, les utilisateurs qui se sont connectés au cours de la période précédente de 30 jours sont recommandés pour être approuvés. Les utilisateurs qui ne se sont pas connectés au cours des 30 derniers jours sont recommandés pour être refusés.

   > [!NOTE]
   > Si vous créez une révision d’accès basée sur les applications, vos recommandations seront basées sur la période d'intervalle de 30 jours en fonction de la date de la dernière connexion de l’utilisateur à l’application et non au locataire.

   ![Capture d’écran qui montre l’option Activer l’assistance aux décisions de révision.](./media/create-access-review/helpers.png)

1. Dans la section **Paramètres avancés**, vous pouvez choisir les options suivantes :

    - **Justification obligatoire** : activez cette case afin d’exiger que le réviseur indique un motif d’approbation ou de refus.
    - **Notifications par e-mail** : activez cette case pour qu’Azure AD envoie des notifications par e-mail aux réviseurs quand une révision d’accès commence et aux administrateurs quand une révision est terminée.
    - **Rappels** : activez cette case pour qu’Azure AD envoie des rappels concernant les révisions d’accès en cours à tous les réviseurs. Les réviseurs reçoivent les rappels à mi-parcours de la révision, qu’ils aient déjà terminé leur révision ou non.
    - **Contenu supplémentaire pour l’e-mail du réviseur** : le contenu de l’e-mail envoyé aux réviseurs est généré automatiquement en fonction des détails de la révision, comme le nom de la révision, le nom de la ressource, la date d’échéance, etc. Si vous avez besoin de communiquer plus d’informations, vous pouvez spécifier des informations comme des instructions ou des informations de contact dans cette zone. Les informations que vous entrez sont incluses dans les e-mails d’invitation et de rappel envoyés aux réviseurs désignés. La section mise en surbrillance dans l’image suivante montre où s’affichent ces informations.

      ![Capture d’écran montrant le contenu supplémentaire pour les réviseurs.](./media/create-access-review/additional-content-reviewer.png)

1. Sélectionnez **Suivant : Vérifier + créer**.

   ![Capture d’écran montrant l’onglet Vérifier + créer.](./media/create-access-review/create-review.png)

1. Nommez la révision d’accès. Si vous le souhaitez, vous pouvez fournir une description de cette révision. Les réviseurs ont accès au nom et à la description de la révision.

1. Vérifiez les informations, puis sélectionnez **Créer**.

## <a name="allow-group-owners-to-create-and-manage-access-reviews-of-their-groups-preview"></a>Autoriser les propriétaires de groupes à créer et à gérer des révisions d’accès de leurs groupes (préversion)

Le rôle prérequis est celui d’administrateur général ou utilisateur.

1. Connectez-vous au portail Azure et ouvrez la page [Identity Governance](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Dans le menu de gauche, sous **Révisions d’accès**, sélectionnez **Paramètres**.

1. Dans la page **Déléguer qui peut créer et gérer des révisions d’accès**, définissez **(Préversion) Les propriétaires du groupe peuvent créer et gérer les révisions d’accès des groupes dont ils disposent**, sur **Oui**.

    ![Capture d’écran illustrant comment autoriser les propriétaires de groupes à procéder à des examens.](./media/create-access-review/group-owners-review-access.png)

    > [!NOTE]
    > Par défaut, ce paramètre est défini sur **Non**. Pour autoriser les propriétaires de groupes à créer et à gérer des révisions d’accès, modifier la valeur du paramètre sur **Oui**.

## <a name="start-the-access-review"></a>Démarrer la révision d’accès

Une fois que vous avez spécifié les paramètres pour une révision d’accès, sélectionnez **Démarrer**. La révision d’accès s’affiche dans votre liste, avec un indicateur de son état.

![Capture d’écran montrant la liste des révisions d’accès et leur état.](./media/create-access-review/access-reviews-list.png)

Par défaut, Azure AD envoie un e-mail aux réviseurs peu de temps après le démarrage de la révision. Si vous ne souhaitez pas qu’Azure AD envoie cet e-mail, veillez à informer les réviseurs qu’une révision d’accès leur a été assignée. Vous pouvez leur montrer les instructions relatives à la [révision d’accès aux groupes ou aux applications](perform-access-review.md). Si votre révision s’adresse à des invités qui doivent réviser leur propre accès, donnez-leur des instructions sur la méthode à suivre pour [réviser leur accès à des groupes ou à des applications](review-your-access.md).

Si vous avez attribué des invités en tant que réviseurs et qu’ils n’ont pas accepté leur invitation au locataire, ils ne recevront pas d’e-mail concernant les révisions d’accès. Ils doivent d’abord accepter l’invitation avant de pouvoir commencer les révisions.

## <a name="update-the-access-review"></a>Mettre à jour la révision d’accès

Après le lancement d’une ou de plusieurs révisions d’accès, vous souhaiterez peut-être modifier ou mettre à jour les paramètres de vos révisions d’accès existantes. Voici quelques scénarios courants à prendre en compte :

- **Mise à jour des paramètres ou des réviseurs** : si une révision d’accès est récurrente, il existe des paramètres distincts sous **Actuel** et sous **Série**. La mise à jour des paramètres ou des réviseurs sous le paramètre **Actuel** applique uniquement les modifications apportées à la révision d’accès actuelle. La mise à jour des paramètres sous **Série** met à jour les paramètres pour toutes les futures récurrences.

   ![Capture d’écran montrant la mise à jour des paramètres de révision d’accès.](./media/create-access-review/current-v-series-setting.png)

- **Ajout et suppression de réviseurs** : lors de la mise à jour des révisions d’accès, vous pouvez choisir d’ajouter un réviseur de secours en plus du réviseur principal. Les réviseurs principaux peuvent être supprimés lorsque vous mettez à jour une révision d’accès. Les réviseurs de secours ne peuvent, par définition, pas être supprimés.

    > [!Note]
    > Les réviseurs de secours ne peuvent être ajoutés que lorsque le type de réviseur est « manager » ou « propriétaire de groupe ». Des réviseurs principaux peuvent être ajoutés lorsque le type de réviseur est « utilisateur sélectionné ».

- **Rappel aux réviseurs** : lors de la mise à jour des révisions d’accès, vous pouvez choisir d’activer l’option de **rappel** sous **Paramètres avancés**. Les utilisateurs reçoivent ensuite une notification par e-mail à la moitié de la période de révision, qu’ils aient terminé la révision ou non.

   ![Capture d’écran montrant le rappel aux réviseurs.](./media/create-access-review/reminder-setting.png)

## <a name="next-steps"></a>Étapes suivantes

- [Réviser l’accès à des groupes ou à des applications](perform-access-review.md)
- [Réviser son accès à des groupes ou à des applications](review-your-access.md)
- [Effectuer une révision d’accès de groupes ou révisions d’accès des applications dans Azure AD](complete-access-review.md)


