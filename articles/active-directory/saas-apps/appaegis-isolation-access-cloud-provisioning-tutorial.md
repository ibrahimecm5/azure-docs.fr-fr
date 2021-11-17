---
title: 'Tutoriel : Configurer Appaegis Isolation Access Cloud pour le provisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment provisionner et déprovisionner automatiquement des comptes d’utilisateur entre Azure AD et Appaegis Isolation Access Cloud.
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: c845e98a-6fcd-4285-94b7-a72a2175ca7e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/29/2021
ms.author: thwimmer
ms.openlocfilehash: c1700980368095e31da1d41b31ddccdf7c3ea89b
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894948"
---
# <a name="tutorial-configure-appaegis-isolation-access-cloud-for-automatic-user-provisioning"></a>Tutoriel : Configurer Appaegis Isolation Access Cloud pour le provisionnement automatique d’utilisateurs

Ce tutoriel décrit les étapes à suivre dans Appaegis Isolation Access Cloud et Azure Active Directory (Azure AD) pour configurer le provisionnement automatique d’utilisateurs. Une fois celui-ci configuré, Azure AD provisionne et déprovisionne automatiquement les utilisateurs et les groupes dans [Appaegis Isolation Access Cloud](https://www.appaegis.com) à l’aide du service Provisionnement Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="supported-capabilities"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans Appaegis Isolation Access Cloud
> * Supprimer des utilisateurs dans Appaegis Isolation Access Cloud lorsqu’ils n’ont plus besoin de l’accès
> * Maintenir la synchronisation des attributs utilisateur entre Azure AD et Appaegis Isolation Access Cloud
> * [Authentification unique](appaegis-isolation-access-cloud-tutorial.md) auprès d’Appaegis Isolation Access Cloud (recommandé)

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](../roles/permissions-reference.md) de configurer l’approvisionnement (par exemple, administrateur d’application, administrateur d’application Cloud, propriétaire d’application ou administrateur général).
* Un compte [Appaegis Cloud](https://www.appaegis.com) avec niveau d’abonnement Professionel 
* Un compte d’utilisateur Appaegis Cloud disposant d’autorisations d’**Administrateur général**


## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
2. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Déterminez les données à [mapper entre Azure AD et Appaegis Isolation Access Cloud](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-appaegis-isolation-access-cloud-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer Appaegis Isolation Access Cloud pour prendre en charge le provisionnement avec Azure AD

1. [Authentification unique](appaegis-isolation-access-cloud-tutorial.md) activée avec Appaegis Cloud.
2. Dans la page **Détails du fournisseur d’identité** (la page indique l’ID d’entité et l’URL ACS), vous trouverez l’URL SCIM et le jeton SCIM.

## <a name="step-3-add-appaegis-isolation-access-cloud-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter Appaegis Isolation Access Cloud à partir de la galerie d’applications Azure AD

Ajoutez Appaegis Isolation Access Cloud à partir de la galerie d’applications Azure AD pour commencer à gérer le provisionnement dans Appaegis Isolation Access Cloud. Si vous avez déjà configuré Appaegis Isolation Access Cloud pour l’authentification unique, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quand vous affectez des utilisateurs et des groupes dans Appaegis Isolation Access Cloud, vous devez sélectionner un rôle autre que **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible sur l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste d’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter plus de rôles. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-appaegis-isolation-access-cloud"></a>Étape 5. Configurer le provisionnement automatique d’utilisateurs vers Appaegis Isolation Access Cloud 

Cette section vous guide tout au long des étapes de configuration du service de provisionnement Azure AD pour créer, mettre à jour et désactiver des utilisateurs ou des groupes dans TestApp en fonction des affectations d’utilisateurs et/ou de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-appaegis-isolation-access-cloud-in-azure-ad"></a>Pour configurer le provisionnement automatique d’utilisateurs vers Appaegis Isolation Access Cloud dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Appaegis Isolation Access Cloud**.

    ![Lien Appaegis Isolation Access Cloud dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionner](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Approvisionnement](common/provisioning-automatic.png)

5. Dans la section **Informations d’identification de l’administrateur**, entrez l’URL de locataire et le Jeton secret d’Appaegis Isolation Access Cloud. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Appaegis Isolation Access Cloud. En cas d’échec de la connexion, vérifiez que votre compte Appaegis Isolation Access Cloud dispose d’autorisations d’administrateur et réessayez.

    ![par jeton](common/provisioning-testconnection-tenanturltoken.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Sélectionnez **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory sur Appaegis Isolation Access Cloud**.

9. Passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Appaegis Isolation Access Cloud dans la section **Mappages d’attributs**. Les attributs sélectionnés en tant que propriétés **correspondantes** sont utilisés pour la mise en correspondance des comptes d’utilisateur dans Appaegis Isolation Access Cloud dans le cadre des opérations de mise à jour. Si vous décidez de modifier l’[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vous devez vérifier que l’API Appaegis Isolation Access Cloud prend en charge le filtrage des utilisateurs en fonction de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    |Attribut|Type|Pris en charge pour le filtrage|
    |---|---|---|
    |userName|String|&check;
    |active|Boolean|
    |displayName|String|
    |name.givenName|String|
    |name.familyName|String|

10. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec Contoso**.

11. Dans la section **Mappage d’attributs**, passez en revue les attributs de groupe qui sont synchronisés entre Azure AD et Contoso. Les attributs sélectionnés en tant que propriétés **correspondantes** sont utilisés pour la mise en correspondance des groupes dans Contoso dans le cadre des opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

      |Attribut|Type|Pris en charge pour le filtrage|
      |---|---|---|
      |displayName|String|&check;
      |membres|Informations de référence|

12. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pour activer le service de provisionnement Azure AD pour Appaegis Isolation Access Cloud, définissez l’**État de l’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

14. Définissez les utilisateurs et/ou groupes que vous voulez provisionner dans Appaegis Isolation Access Cloud en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

15. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

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
