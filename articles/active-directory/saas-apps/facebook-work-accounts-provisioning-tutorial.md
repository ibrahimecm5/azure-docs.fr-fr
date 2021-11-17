---
title: 'Didacticiel : configurer des Comptes professionnels Facebook pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment approvisionner et déprovisionner automatiquement des Comptes d’utilisateur d’Azure AD vers des comptes professionnels Facebook.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 6563eaea-0328-4923-9a08-f1f07d38998b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2021
ms.author: Zhchia
ms.openlocfilehash: ed6686374dc36a9fa4b1d3250d359fb2272a679c
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063983"
---
# <a name="tutorial-configure-facebook-work-accounts-for-automatic-user-provisioning"></a>Didacticiel : configurer des Comptes professionnels Facebook pour l’approvisionnement automatique d’utilisateurs

Ce didacticiel décrit les étapes que vous devez effectuer dans des Comptes professionnels Facebook et Azure Active Directory (Azure AD) pour configurer l’approvisionnement automatique d’utilisateurs. Une fois configuré, Azure AD approvisionne et désapprovisionne automatiquement des utilisateurs et des groupes pour des [Comptes professionnels Facebook](https://work.facebook.com) à l’aide du service d’approvisionnement d’Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md).


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans des Comptes professionnels Facebook
> * Supprimer les utilisateurs dans des Comptes professionnels Facebook quand ils ne nécessitent plus d’accès
> * Conserver les attributs utilisateur synchronisés entre Azure AD et les Comptes professionnels Facebook
> * [Authentification unique](facebook-work-accounts-tutorial.md) auprès des Comptes professionnels Facebook (recommandé)

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md)
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](../roles/permissions-reference.md) de configurer l’approvisionnement (par exemple, administrateur d’application, administrateur d’application Cloud, propriétaire d’application ou administrateur général).
* Un compte d’administrateur dans les Comptes professionnels, autorisé à modifier les paramètres de la société et à configurer les intégrations.

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
1. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Déterminez les données à [mapper entre Azure AD et Vonage](../app-provisioning/customize-application-attributes.md).


## <a name="step-2-add-facebook-work-accounts-from-the-azure-ad-application-gallery"></a>Étape 2. Ajouter des Comptes professionnels Facebook à partir de la galerie d’applications Azure AD

Ajoutez des Comptes professionnels facebook à partir de la galerie d’applications Azure AD pour commencer à gérer l’approvisionnement des Comptes professionnels facebook. Si vous avez déjà configuré des Comptes professionnels Facebook pour l’authentification unique, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](../manage-apps/add-application-portal.md).

## <a name="step-3-define-who-will-be-in-scope-for-provisioning"></a>Étape 3. Définir qui sera dans l’étendue pour l’approvisionnement

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Lorsque vous attribuez des utilisateurs et des groupes à des Comptes professionnels Facebook, vous devez sélectionner un rôle différent d’**Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible sur l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste d’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter plus de rôles.

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


## <a name="step-4-configure-automatic-user-provisioning-to-facebook-work-accounts"></a>Étape 4. Configurer l’affectation automatique d’utilisateurs à des Comptes professionnels Facebook

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans TestApp en fonction des assignations d’utilisateurs et/ou de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-facebook-work-accounts-in-azure-ad"></a>Pour configurer l’approvisionnement automatique d’utilisateurs pour des Comptes professionnels Facebook dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Facebook Work Accounts**.

    ![Lien Comptes professionnels Facebook dans la liste Applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionner](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Approvisionnement](common/provisioning-automatic.png)

5. Sous la section **Informations d’identification de l’administrateur**, cliquez sur **Autoriser**. Vous serez redirigé vers la page d’autorisation des **Comptes professionnels Facebook**. Entrez le nom d’utilisateur de vos Comptes professionnels Facebook, puis cliquez sur le bouton **Continuer**. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter aux Comptes professionnels Facebook. Si la connexion échoue, vérifiez que votre compte de Comptes professionnels Facebook dispose des autorisations d’administrateur, puis réessayez.

    :::image type="content" source="media/facebook-work-accounts-provisioning-tutorial/azure-connect.png" alt-text="Écran OAuth":::

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Sélectionnez **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser des utilisateurs Azure Active Directory sur des Comptes professionnels Facebook**.

9. Dans la section **Mappages d’attributs**, passez en revue les attributs utilisateur qui sont synchronisés à partir d’Azure AD vers des Comptes professionnels Facebook. Les attributs sélectionnés en tant que propriétés de **Correspondance** servent à faire correspondre les comptes d’utilisateur dans les Comptes professionnels Facebook, en vue d’opérations de mise à jour. Si vous choisissez de modifier l’[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vous devez vous assurer que l’API Comptes professionnels Facebook prend en charge le filtrage des utilisateurs en fonction de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    |Attribut|Type|Pris en charge pour le filtrage|
    |---|---|---|
    |userName|String|&check;
    |externalId|String|
    |active|Boolean|
    |title|String|
    |emails[type eq "work"].value|String|
    |preferredLanguage|String|
    |name.givenName|String|
    |name.familyName|String|
    |name.formatted|String|
    |addresses[type eq "work"].formatted|String|
    |addresses[type eq "work"].streetAddress|String|
    |addresses[type eq "work"].locality|String|
    |addresses[type eq "work"].region|String|
    |addresses[type eq "work"].postalCode|String|
    |addresses[type eq "work"].country|String|
    |phoneNumbers[type eq "work"].value|String|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

10. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pour activer le service d’approvisionnement Azure AD pour les Comptes professionnels Facebook, modifiez le paramètre **État de l’approvisionnement** en spécifiant **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

12. Définissez les utilisateurs et/ou groupes que vous souhaitez approvisionner pour les Comptes professionnels Facebook en choisissant les valeurs souhaitées dans **Étendue** de la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

13. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs et groupes définis dans **Étendue** dans la section **Paramètres**. Le cycle de synchronisation initiale prend plus de temps que les cycles de synchronisation suivants, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution.

## <a name="step-5-monitor-your-deployment"></a>Étape 5. Surveiller votre déploiement
Une fois que vous avez configuré l’approvisionnement, utilisez les ressources suivantes pour surveiller votre déploiement :

* Utilisez les [journaux d’approvisionnement](../reports-monitoring/concept-provisioning-logs.md) pour déterminer quels utilisateurs ont été configurés avec succès ou échoué.
* Consultez la [barre de progression](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) pour afficher l’état du cycle d’approvisionnement et quand il se termine
* Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour en savoir plus sur les états de quarantaine, cliquez [ici](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="more-resources"></a>Plus de ressources

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)
