---
title: 'Tutoriel : Configurer Cloud Academy - SSO pour le provisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment approvisionner et déprovisionner automatiquement des comptes d’utilisateur entre Azure AD et Cloud Academy - SSO.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 224777cb-fc03-4e4a-8c8d-5befe1174233
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/02/2021
ms.author: thwimmer
ms.openlocfilehash: 1158bbb0e7f2bfd7e0503d2de3e4fb44b8e3a4f3
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326875"
---
# <a name="tutorial-configure-cloud-academy---sso-for-automatic-user-provisioning"></a>Tutoriel : configurer Cloud Academy - SSO pour l’approvisionnement automatique d’utilisateurs

Ce tutoriel décrit la procédure à suivre dans Cloud Academy - SSO et Azure Active Directory (Azure AD) pour configurer l’approvisionnement automatique d’utilisateurs. Une fois configuré, Azure AD approvisionne et déprovisionne automatiquement des utilisateurs et des groupes vers [Cloud Academy - SSO](https://cloudacademy.com) à l’aide du service d’approvisionnement d’Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans Cloud Academy - SSO
> * Supprimer les utilisateurs dans Cloud Academy - SSO quand ils n’ont plus besoin d’accès
> * Conservez les attributs utilisateur synchronisés entre Azure AD et Cloud Academy - SSO
> * [Authentification unique](./cloud-academy-sso-tutorial.md) à Cloud Academy - SSO (recommandé)

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](../roles/permissions-reference.md) de configurer l’approvisionnement (par exemple, administrateur d’application, administrateur d’application Cloud, propriétaire d’application ou administrateur général). 
* Un compte d’utilisateur dans Cloud Academy avec un rôle d’administrateur dans votre entreprise pour activer l’intégration Active Directory et générer la clé API.

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
2. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Déterminez les données à [mapper entre Azure AD et Cloud Academy - SSO](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-cloud-academy---sso-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer Cloud Academy - SSO pour prendre en charge l’approvisionnement avec Azure AD

1. Connectez-vous au portail d’administration de [Sigma Computing](https://cloudacademy.com/).

2. Cliquez sur **Tableau de bord** dans la page d’accueil en regard de l’icône de profil.

    ![Accueil](media/cloud-academy-sso-provisioning-tutorial/dashboard.png)

3. accédez à **Votre profil** > **Paramètres et intégrations**.

    ![Intégrations](media/cloud-academy-sso-provisioning-tutorial/settings.png)

4. Cliquez sur l’onglet **Intégrations**, puis sur **Afficher l’intégration** dans Azure AD.

    ![Répertoire](media/cloud-academy-sso-provisioning-tutorial/active.png)

5. Cliquez sur **Générer une nouvelle clé API**.

    ![Generate](media/cloud-academy-sso-provisioning-tutorial/key.png)

6. Copiez l’intégralité de la clé d’API. Vous devrez entrer cette valeur dans le champ **Jeton secret** dans l’onglet Approvisionnement de votre application Cloud Academy - SSO dans le portail Azure.

   >[!Note]
   >Vous pouvez générer une nouvelle clé d’API si nécessaire. L’ancienne clé API sera marquée comme expirée dans les prochaines **8 heures** afin de laisser le temps nécessaire pour mettre à jour la configuration dans le portail AD.

7. L’URL du locataire est `https://cloudacademy.com/webhooks/ad/v1/scim` ou `https://app.qa.com/webhooks/ad/v1/scim` en fonction de l’emplacement d’enregistrement de votre société. Vous devrez entrer cette valeur dans le champ **URL de locataire** situé sous l’onglet Provisionnement de votre application Cloud Academy - SSO sur le portail Azure.

## <a name="step-3-add-cloud-academy---sso-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter Cloud Academy - SSO à partir de la galerie d’applications Azure AD

Ajoutez Cloud Academy - SSO à partir de la galerie d’applications Azure AD pour commencer à gérer l’approvisionnement dans Cloud Academy - SSO. Si vous avez déjà configuré Cloud Academy - SSO pour l’authentification unique (SSO), vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Lors de l’attribution d’utilisateurs et de groupes dans Cloud Academy - SSO, vous devez choisir un autre rôle que le rôle **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible dans l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste de l’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter des rôles supplémentaires. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-cloud-academy---sso"></a>Étape 5. Configurer l’approvisionnement automatique d’utilisateurs dans Cloud Academy - SSO 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans TestApp en fonction des assignations d’utilisateurs et/ou de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-cloud-academy---sso-in-azure-ad"></a>Pour configurer le provisionnement automatique d’utilisateurs pour Cloud Academy - SSO dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Cloud Academy - SSO**.

    ![Lien Cloud Academy - SSO dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Provisionnement automatique](common/provisioning-automatic.png)

5. Sous la section **Informations d’identification de l’administrateur**, entrez l’URL du locataire et le Jeton secret de votre compte Cloud Academy - SSO. Cliquez sur **Tester la connexion** pour vous assurer qu’Azure AD peut se connecter à Cloud Academy - SSO. Si la connexion échoue, vérifiez que votre compte Cloud Academy - SSO dispose d’autorisations d’administrateur et réessayez.

    ![par jeton](common/provisioning-testconnection-tenanturltoken.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Sélectionnez **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Cloud Academy - SSO**.

9. Passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Cloud Academy - SSO dans la section **Mappages d’attributs**. Les attributs sélectionnés comme propriétés de **Correspondance** sont utilisés pour la mise en correspondance des comptes d’utilisateur dans Cloud Academy - SSO dans le cadre des opérations de mise à jour. Si vous décidez de modifier l’[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vous devez vérifier que l’API Cloud Academy - SSO prend en charge le filtrage des utilisateurs en fonction de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

   |Attribut|Type|Pris en charge pour le filtrage|
   |---|---|---|
   |userName|String|&check;|
   |externalId|String|
   |active|Boolean|
   |name.givenName|String|
   |name.familyName|String|

10. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pour activer le service d’approvisionnement Azure AD pour Cloud Academy - SSO, définissez l’**État de l’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

12. Définissez les utilisateurs et/ou groupes que vous aimeriez approvisionner dans Cloud Academy - SSO en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

13. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs et groupes définis dans **Étendue** dans la section **Paramètres**. Le cycle de synchronisation initiale prend plus de temps que les cycles de synchronisation suivants, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. 

## <a name="step-6-monitor-your-deployment"></a>Étape 6. Surveiller votre déploiement
Une fois que vous avez configuré l’approvisionnement, utilisez les ressources suivantes pour surveiller votre déploiement :

1. Utilisez les [journaux d’approvisionnement](../reports-monitoring/concept-provisioning-logs.md) pour déterminer quels utilisateurs ont été configurés avec succès ou échoué.
2. Consultez la [barre de progression](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) pour afficher l’état du cycle d’approvisionnement et quand il se termine
3. Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour en savoir plus sur les états de quarantaine, cliquez [ici](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)