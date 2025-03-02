---
title: 'Tutoriel : Configurer Comeet Recruiting Software pour le provisionnement automatique des utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et retirer automatiquement des comptes d’utilisateur dans Comeet Recruiting Software.
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 04f4d1c25fcd7a0072e8d72eb28c1cfac106166f
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131988783"
---
# <a name="tutorial-configure-comeet-recruiting-software-for-automatic-user-provisioning"></a>Tutoriel : Configurer Comeet Recruiting Software pour le provisionnement automatique des utilisateurs

L’objectif de ce didacticiel est de présenter les étapes à effectuer dans Comeet Recruiting Software et Azure Active Directory (Azure AD) afin de configurer Azure AD pour l’approvisionnement et le retrait automatiques d’utilisateurs et/ou de groupes sur Comeet Recruiting Software.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ce connecteur est actuellement en préversion publique. Pour plus d’informations sur les conditions d’utilisation Microsoft Azure générales relatives aux fonctionnalités d’évaluation, consultez [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* un locataire Azure AD ;
* [Un locataire Comeet Recruiting Software](https://www.comeet.co/)
* Un compte d’utilisateur dans Comeet Recruiting Software avec des autorisations d’administrateur.

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.
## <a name="add-comeet-recruiting-software-from-the-gallery"></a>Ajouter Comeet Recruiting Software depuis la galerie

Avant de configurer Comeet Recruiting Software pour l’approvisionnement automatique d’utilisateurs avec Azure AD, vous devez ajouter Comeet Recruiting Software à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS managées.

**Pour ajouter Comeet Recruiting Software à partir de la galerie d’applications Azure AD, effectuez les étapes suivantes :**

1. Dans le panneau de navigation gauche du **[portail Azure](https://portal.azure.com)** , sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut du volet.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Comeet Recruiting Software**, sélectionnez **Comeet Recruiting Software** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Comeet Recruiting Software dans la liste des résultats](common/search-new-app.png)

## <a name="assigning-users-to-comeet-recruiting-software"></a>Affectation d’utilisateurs à Comeet Recruiting Software

Azure Active Directory utilise un concept appelé *affectations* pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre d’une attribution automatique d’utilisateurs, seuls les utilisateurs ou les groupes auxquels une application a été attribuée dans Azure AD sont synchronisés.

Avant de configurer et d’activer l’approvisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à Comeet Recruiting Software. Une fois que vous avez choisi, vous pouvez affecter ces utilisateurs et/ou groupes à Comeet Recruiting Software en suivant les instructions fournies ici :

* [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-comeet-recruiting-software"></a>Conseils importants pour l’affectation d’utilisateurs à Comeet Recruiting Software

* Il est recommandé de n’affecter qu’un seul utilisateur Azure AD à Comeet Recruiting Software afin de tester la configuration de l’approvisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Lorsque vous affectez un utilisateur à Comeet Recruiting Software, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’affectation. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configuring-automatic-user-provisioning-to-comeet-recruiting-software"></a>Configuration de l’approvisionnement automatique d’utilisateurs sur Comeet Recruiting Software 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans Comeet Recruiting Software en fonction des affectations d’utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour Comeet Recruiting Software en suivant les instructions fournies dans le [tutoriel sur l’authentification unique Comeet Recruiting Software](comeetrecruitingsoftware-tutorial.md). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

### <a name="to-configure-automatic-user-provisioning-for-comeet-recruiting-software-in-azure-ad"></a>Pour configurer l’approvisionnement automatique d’utilisateurs pour Comeet Recruiting Software dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Comeet Recruiting Software**.

    ![Lien Comeet Recruiting Software dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Capture d’écran des options Gérer avec l’option Provisionnement en évidence.](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Capture d’écran de la liste déroulante Mode de provisionnement avec l’option Automatique en évidence.](common/provisioning-automatic.png)

5. Dans la section **Informations d’identification de l’administrateur**, entrez les valeurs **URL de locataire** et **Jeton secret** de votre compte Comeet Recruiting Software, comme cela est décrit dans l’étape 6.

6. Dans la [console d’administration de Comeet Recruiting Software](https://app.comeet.co/), accédez à **Comeet > Paramètres > Authentification > Microsoft Azure** et copiez la valeur **Secret Token for your company** (Jeton secret pour votre entreprise) dans le champ **Jeton secret** dans Azure AD.

    ![Approvisionnement Comeet Recruiting Software](./media/comeet-recruiting-software-provisioning-tutorial/secret-token-1.png)

7. Après avoir renseigné les champs indiqués à l’étape 5, cliquez sur **Tester la connexion** pour vous assurer qu’Azure AD peut se connecter à Comeet Recruiting Software. Si la connexion échoue, vérifiez que votre compte Comeet Recruiting Software dispose des autorisations d’administrateur et réessayez.

    ![par jeton](common/provisioning-testconnection-token.png)

8. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

9. Cliquez sur **Enregistrer**.

10. Dans la section **Mappages**, sélectionnez **Synchronize Azure Active Directory Users to Comeet** (Synchroniser les utilisateurs Azure Active Directory avec Comeet).

    ![Mappages d’utilisateur Comeet Recruiting Software](media/comeet-recruiting-software-provisioning-tutorial/user-mappings.png)

11. Passez en revue les attributs utilisateur qui sont synchronisés d’Azure AD vers Comeet Recruiting Software dans la section **Mappages des attributs**. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Comeet Recruiting Software pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Attributs de groupe Comeet Recruiting Software](media/comeet-recruiting-software-provisioning-tutorial/user-mapping-attributes.png)

12. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pour activer le service d’approvisionnement Azure AD pour Comeet Recruiting Software, affectez la valeur **Activé** au paramètre **Statut d’approvisionnement** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

14. Définissez les utilisateurs et/ou groupes que vous aimeriez approvisionner sur Comeet Recruiting Software en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

15. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour surveiller la progression et les liens vers les rapports d’activité d’approvisionnement, qui décrivent toutes les actions effectuées par le service d’approvisionnement Azure AD sur Comeet Recruiting Software.

Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitations du connecteur

* Comeet Recruiting Software ne prend actuellement pas en charge les groupes.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)

