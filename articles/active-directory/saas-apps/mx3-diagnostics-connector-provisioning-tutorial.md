---
title: 'Didacticiel : Configurer le Connecteur de diagnostics MX3 pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment approvisionner et déprovisionner automatiquement des comptes d’utilisateur d’Azure AD dans le Connecteur de diagnostics MX3.
services: active-directory
documentationcenter: ''
author: twimmers
writer: Thwimmer
manager: beatrizd
ms.assetid: 6d54ea28-0208-45bc-8e29-c6cf9a912f00
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2021
ms.author: Thwimmer
ms.openlocfilehash: 2021b7a7af7aee23e118de0b555e86cb7ee20272
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270940"
---
# <a name="tutorial-configure-mx3-diagnostics-connector-for-automatic-user-provisioning"></a>Tutoriel : Configurer le Connecteur de diagnostics MX3 pour l’approvisionnement automatique d’utilisateurs

Ce tutoriel décrit les étapes à suivre dans le Connecteur de diagnostics MX3 et Azure Active Directory (Azure AD) pour configurer l’approvisionnement automatique d’utilisateurs. Une fois configuré, Azure AD approvisionne et déprovisionne automatiquement les utilisateurs et les groupes dans le [Connecteur de diagnostics MX3](https://www.mx3diagnostics.com/) à l’aide du service d’approvisionnement Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créez des utilisateurs dans le Connecteur de diagnostics MX3.
> * Supprimez des utilisateurs du Connecteur de diagnostics MX3 quand ils ne nécessitent plus d’accès.
> * Maintenez les attributs utilisateur synchronisés entre Azure AD et le Connecteur de diagnostics MX3.
> * Approvisionnez des groupes et des appartenances de groupe dans le Connecteur de diagnostics MX3.
> * Authentification unique auprès du Connecteur de diagnostics MX3.

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md). 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](../roles/permissions-reference.md) de configurer l’approvisionnement (par exemple, administrateur d’application, administrateur d’application Cloud, propriétaire d’application ou administrateur général). 
* Un compte MX3 avec fonctionnalité d’organisation.
* Un compte dans le portail MX3 avec SSO.

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
1. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Déterminez les données à [mapper entre Azure AD et le Connecteur de diagnostics MX3](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-mx3-diagnostics-connector-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer le Connecteur de diagnostics MX3 pour prendre en charge l’approvisionnement avec Azure AD

1. Si votre compte MX3 n’a pas de fonctionnalité d’organisation activée, demandez-la en procédant de la manière décrite dans cette [documentation](https://www.mx3diagnostics.com/files/files/MX3_PortalGuide_0321.pdf).

1. Si votre compte MX3 n’a pas de fonctionnalité d’authentification unique, configurez l’authentification unique Azure AD comme décrit dans cette documentation.

1. Connectez-vous au [portail MX3](https://portal.mx3.app). Accédez à la page des paramètres SSO en cliquant sur paramètres, puis sur **Authentification unique**.

    ![Capture d’écran des paramètres d’authentification unique du Connecteur de diagnostics MX3.](media/mx3-provisioning/sso-settings.png)


1. Faites défiler l’écran pour afficher le jeton. Copiez et enregistrez le jeton. Vous en aurez besoin à l’**Étape 5**.

    ![Capture d’écran du jeton secret du Connecteur de diagnostics MX3 pour Azure AD.](media/mx3-provisioning/sso-settings-token.png)

## <a name="step-3-add-mx3-diagnostics-connector-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter le Connecteur de diagnostics MX3 à partir de la galerie d’applications Azure AD

Ajoutez le Connecteur de diagnostics MX3 à partir de la galerie d’applications Azure AD pour commencer à gérer l’approvisionnement du Connecteur de diagnostics MX3. Si vous avez précédemment configuré le Connecteur de diagnostics MX3 pour l’authentification unique, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Lorsque vous attribuez des utilisateurs et des groupes au Connecteur de diagnostics MX3, vous devez sélectionner rôle autre que le rôle **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible sur l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste d’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter plus de rôles. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-mx3-diagnostics-connector"></a>Étape 5. Configurer l’approvisionnement automatique d’utilisateurs pour le Connecteur de diagnostics MX3 

Cette section vous guide tout au long des étapes de configuration du service de provisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans le Connecteur de diagnostics MX3 en fonction des attributions d’utilisateurs et/ou de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-mx3-diagnostics-connector-in-azure-ad"></a>Pour configurer l’approvisionnement automatique d’utilisateurs pour le Connecteur de diagnostics MX3 dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Capture d’écran montrant le panneau Applications d’entreprise.](common/enterprise-applications.png)

1. Dans la liste d’applications, sélectionnez **Connecteur de diagnostics MX3**.

    ![Lien du Connecteur de diagnostics MX3 dans la liste d’applications](common/all-applications.png)

1. Sélectionnez l’onglet **Approvisionnement**.

    ![Capture d’écran de l’onglet Approvisionnement montrant où le trouver.](common/provisioning.png)

1. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Capture d’écran de l’onglet Approvisionnement montrant comment sélectionner Automatique.](common/provisioning-automatic.png)

1. Dans la section **Informations d’identification d’administration**, entrez l’URL (https://scim.mx3.app) ) et le jeton secret du locataire du Connecteur de diagnostics MX3. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter au Connecteur de diagnostics MX3. Si la connexion échoue, vérifiez que votre compte de Connecteur de diagnostics MX3 dispose des autorisations d’administrateur, puis réessayez.

    ![Capture d’écran montrant un champ de texte pour entrer le jeton et l’URL SCIM.](common/provisioning-testconnection-tenanturltoken.png)

1. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

    ![Capture d’écran d’un champ de texte dans lequel vous pouvez entrer un e-mail de notification.](common/provisioning-notification-email.png)

1. Sélectionnez **Enregistrer**.

1. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec le Connecteur de diagnostics MX3**.

1. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et le Connecteur de diagnostics MX3. Les attributs sélectionnés en tant que propriétés **Correspondantes** sont utilisés pour faire correspondre les comptes d’utilisateur dans le Connecteur de diagnostics MX3 pour les opérations de mise à jour. Si vous choisissez de modifier l’[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vous devez vérifier que le Connecteur de diagnostics MX3 prend en charge le filtrage des utilisateurs sur la base de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    |Attribut|Type|Pris en charge pour le filtrage|
    |---|---|---|
    |userName|String|&check;
    |externalId|String|&check;
    |active|Boolean|
    |name.givenName|String|
    |name.familyName|String|

1. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec le Connecteur de diagnostics MX3**.

1. Dans la section **Mappages des attributs**, passez en revue les attributs de groupe qui sont synchronisés entre Azure AD et le Connecteur de diagnostics MX3. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les groupes dans le Connecteur de diagnostics MX3 pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

      |Attribut|Type|Pris en charge pour le filtrage|
      |---|---|---|
      |displayName|String|&check;
      |externalId|String|
      |membres|Informations de référence|

1. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Pour activer le service d’approvisionnement Azure AD pour le Connecteur de diagnostics MX3, affectez la valeur **Activé** au paramètre **Statut d’approvisionnement** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

1. Définissez les utilisateurs et/ou groupes que vous souhaitez approvisionner sur le Connecteur de diagnostics MX3 en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

1. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs et groupes définis dans **Étendue** dans la section **Paramètres**. Le cycle de synchronisation initiale prend plus de temps que les cycles de synchronisation suivants, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. 

## <a name="step-6-monitor-your-deployment"></a>Étape 6. Surveiller votre déploiement
Une fois que vous avez configuré l’approvisionnement, utilisez les ressources suivantes pour surveiller votre déploiement :

* Utilisez les [journaux d’approvisionnement](../reports-monitoring/concept-provisioning-logs.md) pour déterminer quels utilisateurs ont été configurés avec succès ou échoué.
* Consultez la [barre de progression](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) pour afficher l’état du cycle d’approvisionnement et quand il se termine
* Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour en savoir plus sur les états de quarantaine, cliquez [ici](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="more-resources"></a>Plus de ressources

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)