---
title: 'Tutoriel : Configurer Real Links pour le provisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment provisionner et déprovisionner automatiquement des comptes d’utilisateur entre Azure AD et Real Links.
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: a024c7db-ffe6-4fc9-a0ec-7075930bbf75
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/02/2021
ms.author: thwimmer
ms.openlocfilehash: bd15b2e58eb1959287b64397e4f99ba807c2be33
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495389"
---
# <a name="tutorial-configure-real-links-for-automatic-user-provisioning"></a>Tutoriel : Configurer Real Links pour le provisionnement automatique d’utilisateurs

Ce tutoriel décrit les étapes à suivre dans Real Links et Azure Active Directory (Azure AD) pour configurer le provisionnement automatique d’utilisateurs. Une fois configuré, Azure AD provisionne et déprovisionne automatiquement les utilisateurs et les groupes dans [Real Links](https://www.reallinks.io) en utilisant le service de provisionnement Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans Real Links
> * Supprimer des utilisateurs dans Real Links quand ils n’ont plus besoin de l’accès
> * Synchroniser les attributs utilisateur entre Azure AD et Real Links
> * [Authentification unique](real-links-tutorial.md) dans Real Links (recommandé).

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](../roles/permissions-reference.md) de configurer l’approvisionnement (par exemple, administrateur d’application, administrateur d’application Cloud, propriétaire d’application ou administrateur général). 
* Un abonnement [Real Links](https://www.reallinks.io/) :tous les niveaux comprennent le provisionnement automatique d’utilisateurs.

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
1. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Déterminez les données à [mapper entre Azure AD et Real Links](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-real-links-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer Real Links pour prendre en charge le provisionnement avec Azure AD

Pour configurer le provisionnement dans votre plateforme Real Links, vous devez contacter l’[équipe de support Real Links](mailto:support@reallinks.io) et demander les détails de provisionnement de votre SCIM-v2. Ceci comprend les comptes suivants :

* URL de locataire de votre plateforme
* Votre jeton secret unique

## <a name="step-3-add-real-links-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter Real Links à partir de la galerie d’applications Azure AD

Ajoutez Real Links à partir de la galerie d’applications Azure AD pour commencer à gérer le provisionnement dans Real Links. Si vous avez déjà configuré Real Links pour SSO, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quand vous attribuez des utilisateurs et des groupes à Real Links, vous devez sélectionner un rôle autre que **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible sur l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste d’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter plus de rôles. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-real-links"></a>Étape 5. Configurer le provisionnement automatique d’utilisateurs dans Real Links 

Cette section vous guide tout au long des étapes de configuration du service de provisionnement Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans Real Links en fonction des attributions d’utilisateurs et/ou de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-real-links-in-azure-ad"></a>Pour configurer le provisionnement automatique d’utilisateurs pour Real Links dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

1. Dans la liste des applications, sélectionnez **Real Links**.

    ![Lien Real Links dans la liste des applications](common/all-applications.png)

1. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Provisionnement automatique](common/provisioning-automatic.png)

5. Dans la section **Informations d’identification de l’administrateur**, entrez l’URL de votre locataire et le jeton secret de Real Links. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Real Links. Si la connexion échoue, vérifiez que votre compte Real Links a des autorisations d’administrateur et réessayez.

    ![par jeton](common/provisioning-testconnection-tenanturltoken.png)

1. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

1. Sélectionnez **Enregistrer**.

1. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Real Links**.

1. Dans la section **Mappages des attributs**, vérifiez les attributs d’utilisateur qui sont synchronisés entre Azure AD et Real Links. Les attributs sélectionnés qui ont des propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Real Links pour les opérations de mise à jour. Si vous choisissez de changer l’[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vous devez vérifier que l’API Real Links prend en charge le filtrage des utilisateurs en fonction de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

   |Attribut|Type|Pris en charge pour le filtrage|Demandé par Real Links|
   |---|---|---|---|
   |userName|String|&check;|&check;
   |active|Boolean||&check;
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:firstName|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:lastName|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:emailAddress|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:phoneNumber|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:title|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:preferredLanguage|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:organization|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:location|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:countryCode|String|

1. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Pour activer le service de provisionnement Azure AD pour Real Links, définissez **État de provisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

1. Définissez les utilisateurs et/ou les groupes à provisionner dans Real Links en choisissant les valeurs souhaitées dans **Étendue** de la section **Paramètres**.

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