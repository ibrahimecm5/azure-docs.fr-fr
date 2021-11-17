---
title: 'Didacticiel : Configurer Asana pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment provisionner et déprovisionner automatiquement des comptes d’utilisateur d’Azure AD vers Asana.
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 274810a2-bd74-4500-95f1-c720abf23541
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: thwimmer
ms.openlocfilehash: fca7efc24770b9a920c88082566d0e7509ffe513
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990420"
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Didacticiel : Configurer Asana pour l’approvisionnement automatique d’utilisateurs

Ce tutoriel décrit les étapes à suivre dans Asana et Azure Active Directory (Azure AD) pour configurer le provisionnement automatique d’utilisateurs. Une fois configuré, Azure AD provisionne et déprovisionne automatiquement des utilisateurs et des groupes pour [Asana](https://www.asana.com/) à l’aide du service Provisionnement d’Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans Asana
> * Supprimer des utilisateurs dans Asana quand ils ne nécessitent plus d’accès
> * Conserver les attributs utilisateur synchronisés entre Azure AD et Asana
> * Provisionner des groupes et des appartenances aux groupes dans Asana
> * [Authentification unique](asana-tutorial.md) auprès d’Asana (recommandé)

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* un locataire Azure AD ;
* un locataire Asana avec un [plan Entreprise](https://www.asana.com/pricing) ou un plan supérieur ;
* un compte d’utilisateur dans Asana avec des autorisations d’administration.

> [!NOTE]
> L’intégration de l’attribution Azure AD repose sur [l’API Asana](https://asana.com/developers/api-reference/users), qui est accessible à Asana.

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
1. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Déterminez les données à [mapper entre Azure AD et Asana](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-asana-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer Asana pour prendre en charge le provisionnement avec Azure AD
 > [!TIP]
 > Pour activer l’authentification unique SAML pour Asana, suivez les instructions du Portail Azure. L’authentification unique peut être configurée indépendamment de l’attribution automatique, bien que ces deux fonctionnalités se complètent.

### <a name="generate-secret-token-in-asana"></a>Générer un jeton secret dans Asana

* Connectez-vous à [Asana](https://app.asana.com/) avec votre compte Administrateur.
* Dans la barre supérieure, sélectionnez la photo de profil, puis les paramètres actuels de nom de votre organisation.
* Accédez à l’onglet **Comptes de service**.
* Sélectionnez **Ajouter un compte de service**.
* Mettez à jour **Nom** et **À propos de** ainsi que la photo de profil si besoin. Copiez le jeton dans **Jeton**, puis sélectionnez-le dans Enregistrer les modifications.

## <a name="step-3-add-asana-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter Asana à partir de la galerie d’applications Azure AD

Ajoutez Asana à partir de la galerie d’applications Azure AD pour commencer à gérer le provisionnement pour Asana. Si vous avez déjà configuré Asana pour l’authentification unique, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Lorsque vous attribuez des utilisateurs et des groupes à Asana, vous devez sélectionner un rôle autre que **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible sur l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste d’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter plus de rôles. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-asana"></a>Étape 5. Configurer le provisionnement automatique d’utilisateurs vers Asana 

Cette section vous guide tout au long des étapes de configuration du service de provisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et des groupes dans Asana en fonction des attributions d’utilisateurs et/ou de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-asana-in-azure-ad"></a>Pour configurer le provisionnement automatique d’utilisateurs pour Asana dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

1. Dans la liste des applications, sélectionnez **Asana**.

    ![Lien Asana dans la liste des applications](common/all-applications.png)

1. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

1. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Provisionnement automatique](common/provisioning-automatic.png)

1. Dans la section **Informations d’identification de l’administrateur**, entrez votre URL de locataire Asana et le jeton secret fourni par Asana. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Asana. Si la connexion échoue, contactez Asana pour vérifier la configuration de votre compte.

    ![par jeton](common/provisioning-testconnection-tenanturltoken.png)

1. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

1. Sélectionnez **Enregistrer**.

1. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Asana**.

1. Dans la section **Mappages d’attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Asana. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateurs dans Asana pour les opérations de mise à jour. Si vous choisissez de modifier l’[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vous devez vérifier que l’API Asana prend en charge le filtrage des utilisateurs en fonction de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

   |Attribut|Type|Pris en charge pour le filtrage|Requis par Asana|
   |---|---|---|---|
   |userName|String|&check;|&check;|   
   |active|Boolean|||
   |name.formatted|String|||
   |preferredLanguage|String|||
   |title|String|||
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|||


1. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec Asana**.

1. Dans la section **Mappage d’attributs**, passez en revue les attributs de groupe qui sont synchronisés entre Azure AD et Asana. Les attributs sélectionnés comme propriétés de **Correspondance** sont utilisés pour la mise en correspondre des groupes dans Asana dans le cadre des opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

      |Attribut|Type|Pris en charge pour le filtrage|Requis par Asana|
      |---|---|---|---|
      |displayName|String|&check;|&check;      
      |membres|Informations de référence|||

1. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Pour activer le service de provisionnement Azure AD pour Asana, définissez le paramètre **État d’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

1. Définissez les utilisateurs et les groupes que vous voulez provisionner vers Asana en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

1. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs et groupes définis dans **Étendue** dans la section **Paramètres**. Le cycle de synchronisation initiale prend plus de temps que les cycles suivants, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. 

## <a name="step-6-monitor-your-deployment"></a>Étape 6. Surveiller votre déploiement
Une fois que vous avez configuré l’approvisionnement, utilisez les ressources suivantes pour surveiller votre déploiement :

* Utilisez les [journaux d’approvisionnement](../reports-monitoring/concept-provisioning-logs.md) pour déterminer quels utilisateurs ont été configurés avec succès ou échoué.
* Consultez la [barre de progression](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) pour afficher l’état du cycle d’approvisionnement et sa progression.
* Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour en savoir plus sur les états de quarantaine, cliquez [ici](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="change-log"></a>Journal des modifications

* 06/11/2021 : suppression de la prise en charge de **externalId, name.givenName and name.familyName**. ajout de la prise en charge de **preferredLanguage , title and urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department**. Et activation du **Provisionnement de groupe**.

## <a name="more-resources"></a>Plus de ressources

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)