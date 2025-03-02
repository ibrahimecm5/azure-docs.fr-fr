---
title: 'Tutoriel : Configurer Insight4GRC pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment approvisionner et désapprovisionner automatiquement des comptes d’utilisateur d’Azure AD vers Insight4GRC.
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: thwimmer
ms.openlocfilehash: fa695dd3f4b693cad64b8a72c48adc3a62f506f2
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990130"
---
# <a name="tutorial-configure-insight4grc-for-automatic-user-provisioning"></a>Tutoriel : Configurer Insight4GRC pour l’approvisionnement automatique d’utilisateurs

Ce tutoriel décrit les étapes que vous devez effectuer dans Insight4GRC et Azure Active Directory (Azure AD) pour configurer l’approvisionnement automatique d’utilisateurs. Une fois configuré, Azure AD approvisionne et désapprovisionne automatiquement les utilisateurs et les groupes pour [Insight4GRC](https://www.rsmuk.com/) à l’aide du service d’approvisionnement d’Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans Insight4GRC
> * Supprimer les utilisateurs dans Insight4GRC lorsqu’ils ne nécessitent plus d’accès
> * Conserver les attributs utilisateur synchronisés entre Azure AD et Insight4GRC
> * Approvisionner des groupes et des appartenances aux groupes dans Insight4GRC
> * [Authentification unique](./insight4grc-tutorial.md) à Insight4GRC (recommandé)

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](../roles/permissions-reference.md) de configurer l’approvisionnement (par exemple, Administrateur d’application, Administrateur d’application cloud, Propriétaire d’application ou Administrateur général). 
* Un compte d’utilisateur Insight4GRC avec des autorisations d’administrateur.

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
2. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Déterminez les données à [mapper entre Azure AD et Insight4GRC](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-insight4grc-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer Insight4GRC pour prendre en charge l’approvisionnement avec Azure AD

Avant de configurer Insight4GRC pour l’approvisionnement automatique d’utilisateurs avec Azure AD, vous devez activer l’approvisionnement SCIM sur Insight4GRC.

1. Pour obtenir le jeton du porteur, le client final doit contacter [le support technique](mailto:support.ss@rsmuk.com).
2. Pour obtenir l’URL de point de terminaison SCIM, vous devez disposer de votre nom de domaine Insight4GRC, car il sera utilisé pour construire votre URL de point de terminaison SCIM. Vous pouvez récupérer votre nom de domaine Insight4GRC dans le cadre de l’achat initial de logiciels avec Insight4GRC.

## <a name="step-3-add-insight4grc-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter Insight4GRC à partir de la galerie d’applications Azure AD

Ajoutez Insight4GRC à partir de la galerie d’applications Azure AD pour commencer à gérer l’approvisionnement sur Insight4GRC. Si vous avez déjà configuré Insight4GRC pour l’authentification unique, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Lorsque vous attribuez des utilisateurs et des groupes à Insight4GRC, vous devez sélectionner un autre rôle qu’**Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible dans l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste de l’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter des rôles supplémentaires. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-insight4grc"></a>Étape 5. Configurer l’approvisionnement automatique d’utilisateurs sur Insight4GRC 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans TestApp en fonction des assignations d’utilisateurs et/ou de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-insight4grc-in-azure-ad"></a>Pour configurer l’approvisionnement automatique d’utilisateurs pour Insight4GRC dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Insight4GRC**.

    ![Lien Insight4GRC dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Capture d’écran des options Gérer avec l’option Provisionnement en évidence.](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Capture d’écran de la liste déroulante Mode de provisionnement avec l’option Automatique en évidence.](common/provisioning-automatic.png)

5. Sous la section **Informations d’identification de l’administrateur**, entrez l’URL du point de terminaison SCIM dans **URL de locataire**. L'URL du point de terminaison doit être au format `https://<Insight4GRC Domain Name>.insight4grc.com/public/api/scim/v2 `, où **Nom de domaine Insight4GRC** est la valeur récupérée dans les étapes précédentes. Saisissez le jeton du porteur récupéré précédemment dans **Jeton secret**. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Insight4GRC. Si la connexion échoue, vérifiez que votre compte Insight4GRC dispose des autorisations d’administrateur et réessayez.

    ![Capture d’écran de la boîte de dialogue Informations d’identification de l’administrateur permettant d’entrer l’URL du locataire et le jeton secret](./media/insight4grc-provisioning-tutorial/provisioning.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Sélectionnez **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory sur Insight4GRC**.

9. Dans la section **Mappages des attributs**, consultez les attributs utilisateur qui sont synchronisés entre Azure AD et Insight4GRC. Les attributs sélectionnés en tant que propriétés **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Insight4GRC pour les opérations de mise à jour. Si vous choisissez de modifier l’[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vous devez vous assurer que l’API Insight4GRC prend en charge le filtrage des utilisateurs en fonction de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

   |Attribut|Type|Pris en charge pour le filtrage|
   |---|---|--|
   |userName|String|&check;
   |externalId|String|&check;
   |active|Boolean|
   urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|String|
   |title|String|
   |name.givenName|String|
   |name.familyName|String|
   |emails[type eq "work"].value|String|
   |phoneNumbers[type eq "work"].value|String|

10. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory sur Insight4GRC**.

11. Dans la section **Mappages des attributs**, consultez les attributs de groupe qui sont synchronisés entre Azure AD et Insight4GRC. Les attributs sélectionnés en tant que propriétés **Correspondance** sont utilisés pour faire correspondre les groupes dans Insight4GRC pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

      |Attribut|Type|
      |---|---|
      |displayName|String|
      |externalId|String|
      |membres|Informations de référence|

10. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pour activer le service d’approvisionnement Azure AD pour Insight4GRC, modifiez **Statut d’approvisionnement** par **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

14. Définissez les utilisateurs ou les groupes que vous souhaitez approvisionner sur Insight4GRC en choisissant les valeurs souhaitées dans le champ **Étendue** de la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

15. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs et groupes définis dans **Étendue** dans la section **Paramètres**. Le cycle de synchronisation initiale prend plus de temps que les cycles de synchronisation suivants, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. 

## <a name="step-6-monitor-your-deployment"></a>Étape 6. Surveiller votre déploiement
Une fois que vous avez configuré l’approvisionnement, utilisez les ressources suivantes pour surveiller votre déploiement :

* Utilisez les [journaux d’approvisionnement](../reports-monitoring/concept-provisioning-logs.md) pour déterminer quels utilisateurs ont été configurés avec succès ou ceux pour laquelle la procédure a échoué.
* Consultez la [barre de progression](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) pour afficher l’état d’avancement du cycle d’approvisionnement et le moment où il se terminera.
* Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour en savoir plus sur les états de quarantaine, cliquez [ici](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="change-log"></a>Journal des modifications

* 19/08/2021 - L’attribut **manager** de l’extension Enterprise a été ajouté.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md).