---
title: 'Tutoriel : Configurer Peripass pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Apprenez à approvisionner et désapprovisionner automatiquement des comptes d’utilisateur entre Azure AD et Peripass.
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 1d036aa3-4e07-4f48-a6ae-40fc6c066e42
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/27/2021
ms.author: thwimmer
ms.openlocfilehash: 5372364d5fa7574dcc2005df37d399e451b439c2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012697"
---
# <a name="tutorial-configure-peripass-for-automatic-user-provisioning"></a>Tutoriel : Configurer Peripass pour l’approvisionnement automatique d’utilisateurs

Ce tutoriel décrit les étapes à suivre dans Peripass et Azure Active Directory (Azure AD) pour configurer l’approvisionnement automatique d’utilisateurs. Une fois configuré, Azure AD approvisionne et désapprovisionne automatiquement les utilisateurs et les groupes pour [Peripass](https://www.peripass.com/) à l’aide du service d’approvisionnement Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md).


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans Peripass
> * Supprimer les utilisateurs dans Peripass quand ils n’ont plus besoin d’accès
> * Maintenir la synchronisation des attributs utilisateur entre Azure AD et Peripass
> * [Authentification unique](../manage-apps/add-application-portal-setup-oidc-sso.md) auprès de Peripass (recommandée)

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](../roles/permissions-reference.md) de configurer l’approvisionnement (par exemple, administrateur d’application, administrateur d’application Cloud, propriétaire d’application ou administrateur général). 
* Un locataire Peripass - contactez [Peripass](https://www.peripass.com/) pour configurer votre locataire.
* Un utilisateur Peripass avec des autorisations sur la configuration du locataire.

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
2. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Déterminez les données à [mapper entre Azure AD et Peripass](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-peripass-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer Peripass pour prendre en charge l’approvisionnement avec Azure AD

1. Connectez-vous à Peripass avec l’URL de connexion de votre locataire.

2. Accédez à la **configuration** de votre locataire.

   ![Capture d’écran du menu principal de Peripass](media/peripass-provisioning-tutorial/peripass-main-menu.png)

3. Ouvrez les paramètres **Identity providers & provisioning**.

   ![Capture d’écran de la configuration du locataire Peripass](media/peripass-provisioning-tutorial/peripass-configuration.png)

4. Donnez un **Nom de fournisseur** au fournisseur d’identité que vous configurez.

5.   Choisissez le **Rôle utilisateur** qui doit être attribué aux utilisateurs approvisionnés.

6. Notez le **point de terminaison SCIM** et le **jeton SCIM** de votre locataire (vous en aurez besoin plus tard lors de la configuration de l’approvisionnement d’utilisateurs dans les Applications d’entreprise Azure AD et utilisez-les comme **URL de locataire Peripass** et **Jeton de secret**).

   ![Capture d’écran des paramètres du fournisseur d’identité Peripass](media/peripass-provisioning-tutorial/peripass-id-provider-settings.png)

7. **Enregistrez vos changements** dans la configuration.

   ![Capture d’écran de l’enregistrement du fournisseur](media/peripass-provisioning-tutorial/peripass-save-changes.png)

## <a name="step-3-add-peripass-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter Peripass à partir de la galerie d’applications Azure AD

Ajoutez Peripass à partir de la galerie d’applications Azure AD pour commencer à gérer l’approvisionnement dans Peripass. Si vous avez déjà configuré Peripass pour l’authentification SSO, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Lorsque vous attribuez des utilisateurs et des groupes à Peripass, vous devez sélectionner un autre rôle que le rôle **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible sur l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste d’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter plus de rôles.

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


## <a name="step-5-configure-automatic-user-provisioning-to-peripass"></a>Étape 5. Configurer l’approvisionnement automatique d’utilisateurs pour Peripass

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans TestApp en fonction des assignations d’utilisateurs et/ou de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-peripass-in-azure-ad"></a>Pour configurer l’approvisionnement automatique d’utilisateurs pour Peripass dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

   ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Peripass**.

   ![Lien Peripass dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

   ![Onglet Approvisionner](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

   ![Onglet Approvisionnement](common/provisioning-automatic.png)

5. Sous la section **Informations d’identification de l’administrateur**, entrez l’URL de locataire et le Jeton de secret de Peripass. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Peripass. Si la connexion échoue, vérifiez que votre compte Peripass dispose d’autorisations d’administrateur, puis réessayez.

   ![par jeton](common/provisioning-testconnection-tenanturltoken.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

   ![E-mail de notification](common/provisioning-notification-email.png)

7. Sélectionnez **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Peripass**.

9. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Peripass. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Peripass pour les opérations de mise à jour. Si vous choisissez de modifier l’[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vous devez vérifier que l’API Peripass prend en charge le filtrage des utilisateurs en fonction de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

   |Attribut|Type|Pris en charge pour le filtrage|
   |---|---|---|
   |userName|String|&check;
   |active|Boolean|
   |displayName|String|
   |externalId|String|
   |preferredLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |emails[type eq "work"].value|String|
   
10. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pour activer le service d’approvisionnement Azure AD pour Peripass, définissez le paramètre **État d’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

12. Définissez les utilisateurs et/ou les groupes que vous souhaitez approvisionner dans Peripass en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

13. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs et groupes définis dans **Étendue** dans la section **Paramètres**. Le cycle de synchronisation initiale prend plus de temps que les cycles de synchronisation suivants, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution.

## <a name="step-6-monitor-your-deployment"></a>Étape 6. Surveiller votre déploiement
Une fois que vous avez configuré l’approvisionnement, utilisez les ressources suivantes pour surveiller votre déploiement :

1. Utilisez les [journaux d’approvisionnement](../reports-monitoring/concept-provisioning-logs.md) pour déterminer quels utilisateurs ont été configurés avec succès ou échoué.
2. Consultez la [barre de progression](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) pour afficher l’état du cycle d’approvisionnement et quand il se termine
3. Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour en savoir plus sur les états de quarantaine, cliquez [ici](../app-provisioning/application-provisioning-quarantine-status.md).



## <a name="more-resources"></a>Plus de ressources

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)
