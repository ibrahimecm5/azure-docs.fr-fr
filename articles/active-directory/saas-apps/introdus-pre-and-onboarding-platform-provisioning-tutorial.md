---
title: 'Tutoriel : Configurer introDus Pre and Onboarding Platform pour l’attribution automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment attribuer des comptes d’utilisateur et supprimer leurs privilèges d’accès automatiquement entre Azure AD et introDus Pre and Onboarding Platform.
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 829376bb-41d8-42da-89ee-853f5630121b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2021
ms.author: thwimmer
ms.openlocfilehash: b244d621be7590d6039da5cb8860a66a3a2be222
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478982"
---
# <a name="tutorial-configure-introdus-pre-and-onboarding-platform-for-automatic-user-provisioning"></a>Tutoriel : Configurer introDus Pre and Onboarding Platform pour l’attribution automatique d’utilisateurs

Ce tutoriel décrit les étapes que vous devez effectuer dans introDus Pre and Onboarding Platform et Azure Active Directory (Azure AD) pour configurer l’attribution automatique d’utilisateurs. Une fois configuré, Azure AD approvisionne les utilisateurs et les groupes et supprime leurs privilèges d’accès automatiquement pour [introDus Pre and Onboarding Platform](https://introdus.dk/) à l’aide du service d’approvisionnement d’Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md).


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans introDus Pre and Onboarding Platform
> * Supprimer les utilisateurs dans introDus Pre and Onboarding Platform quand ils n’ont plus besoin d’accès
> * Maintenir la synchronisation des attributs utilisateur entre Azure AD et introDus Pre and Onboarding Platform
> * [Authentification unique](../manage-apps/add-application-portal-setup-oidc-sso.md) auprès d’introDus Pre and Onboarding Platform (recommandé)

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md)
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](../roles/permissions-reference.md) de configurer l’approvisionnement (par exemple, Administrateur d’application, Administrateur d’application cloud, Propriétaire d’application ou Administrateur général).
* Un abonnement introdus, qui comprend l’authentification unique.
* Un jeton d’API introdus valide. Vous trouverez un guide sur la génération de jeton [ici](https://help.introdus.dk/en/articles/2011815-introdus-open-api).

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
2. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Déterminez les données à [mapper entre Azure AD et introDus Pre and Onboarding Platform](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-introdus-pre-and-onboarding-platform-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer introDus Pre and Onboarding Platform pour prendre en charge l’approvisionnement avec Azure AD

Un abonnement qui autorise l’authentification unique. Aucune autre configuration n’est nécessaire côté introdus.

## <a name="step-3-add-introdus-pre-and-onboarding-platform-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter introDus Pre and Onboarding Platform à partir de la galerie d’applications d’Azure AD

Ajoutez introDus Pre and Onboarding Platform à partir de la galerie d’applications d’Azure AD pour commencer à gérer l’approvisionnement d’introDus Pre and Onboarding Platform. Si vous avez déjà configuré introDus Pre and Onboarding Platform pour l’authentification unique, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Lorsque vous attribuez des utilisateurs et des groupes à introDus Pre and Onboarding Platform, vous devez sélectionner un rôle différent du rôle **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible dans l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste de l’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter des rôles supplémentaires.

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


## <a name="step-5-configure-automatic-user-provisioning-to-introdus-pre-and-onboarding-platform"></a>Étape 5. Configurer l’attribution automatique d’utilisateurs sur introDus Pre and Onboarding Platform

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans TestApp en fonction des assignations d’utilisateurs et/ou de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-introdus-pre-and-onboarding-platform-in-azure-ad"></a>Pour configurer l’attribution automatique d’utilisateurs pour introDus Pre and Onboarding Platform dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **introDus Pre and Onboarding Platform**.

    ![Lien introDus Pre and Onboarding Platform dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionner](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Approvisionnement](common/provisioning-automatic.png)

5. Dans la section **Informations d’identification de l’administrateur**, entrez l’URL de locataire et le jeton secret d’introDus Pre and Onboarding Platform. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à introDus Pre and Onboarding Platform. Si la connexion échoue, vérifiez que votre compte introDus Pre and Onboarding Platform dispose des autorisations d’administrateur et réessayez.

    ![par jeton](common/provisioning-testconnection-tenanturltoken.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Sélectionnez **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory sur introDus Pre and Onboarding Platform**.

9. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et introDus Pre and Onboarding Platform. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans introDus Pre and Onboarding Platform pour les opérations de mise à jour. Si vous choisissez de modifier l’[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vous devez vérifier que l’API introDus Pre and Onboarding Platform prend en charge le filtrage des utilisateurs en fonction de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

   |Attribut|Type|Pris en charge pour le filtrage|
   |---|---|---|
   |userName|String|&check;
   |active|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |externalId|String|

10. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pour activer le service d’approvisionnement Azure AD pour introDus Pre and Onboarding Platform, définissez le paramètre **État de l’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

12. Définissez les utilisateurs et/ou les groupes que vous souhaitez approvisionner dans introDus Pre and Onboarding Platform en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

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
