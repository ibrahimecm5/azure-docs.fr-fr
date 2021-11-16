---
title: Didacticiel pour migrer vos applications d’Okta vers Azure Active Directory
titleSuffix: Active Directory
description: Découvrez comment migrer vos applications d’Okta vers Azure Active Directory.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: 25724ab674879d460c11e52a8360cb2c76a23a26
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451613"
---
# <a name="tutorial-migrate-your-applications-from-okta-to-azure-active-directory"></a>Didacticiel : Migrer vos applications d’Okta vers Azure Active Directory

Dans ce tutoriel, vous apprendrez à migrer vos applications d’Okta vers Azure Active Directory (Azure AD).

## <a name="create-an-inventory-of-current-okta-applications"></a>Créer un inventaire des applications Okta actuelles

Avant de commencer la migration, vous devez documenter l’environnement et les paramètres d’applications actuels. Vous pouvez utiliser l’API Okta pour collecter ces informations à partir d’un emplacement centralisé. Pour utiliser l’API, vous aurez besoin d’un outil d’exploration d’API tel que [Postman](https://www.postman.com/).

Pour dresser un inventaire des applications, procédez comme suit :

1. Installer l’application Postman. Ensuite, générez un jeton d’API à partir de la console d’administration Okta.

1. Dans le tableau de bord de l’API, sous **Sécurité**, sélectionnez **Jetons** > **Créer un jeton**.

   ![Capture d’écran montrant le bouton permettant de créer un jeton.](media/migrate-applications-from-okta-to-azure-active-directory/token-creation.png)

1. Insérez un nom de jeton, puis sélectionnez **Créer un jeton**.

   ![Capture d’écran montrant où nommer le jeton.](media/migrate-applications-from-okta-to-azure-active-directory/token-created.png)

1. Enregistrez la valeur du jeton et sauvegardez-la. Elle n’est plus accessible une fois que vous avez sélectionné **OK, c’est fait**.

   ![Capture d’écran montrant la zone valeur de jeton.](media/migrate-applications-from-okta-to-azure-active-directory/record-created.png)

1. Dans l’application Postman, dans l’espace de travail, sélectionnez **Import** (Importer).

   ![Capture d’écran montrant l’API d’importation.](media/migrate-applications-from-okta-to-azure-active-directory/import-api.png)

1. Sur la page **Import** (Importer), sélectionnez **Link** (Lier). Insérez ensuite le lien suivant pour importer l’API : `https://developer.okta.com/docs/api/postman/example.oktapreview.com.environment`.

   ![Capture d’écran montrant le lien d’importation.](media/migrate-applications-from-okta-to-azure-active-directory/link-to-import.png)

   >[!NOTE]
   >Ne modifiez pas le lien avec les valeurs de votre locataire.

1. Continuez en sélectionnant **Import** (Importer).

   ![Capture d’écran montrant la page Import suivante.](media/migrate-applications-from-okta-to-azure-active-directory/next-import-menu.png)

1. Après l’importation de l’API, changez la sélection **Environment** (Environnement) en la définissant sur **{yourOktaDomain}** .

   :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/change-environment.png" alt-text="Capture d’écran montrant comment modifier l’environnement." lightbox="media/migrate-applications-from-okta-to-azure-active-directory/change-environment.png":::

1. Modifiez votre environnement Okta en sélectionnant l’icône représentant un œil. Sélectionnez ensuite **Modifier**.

   ![Capture d’écran montrant comment modifier l’environnement Okta.](media/migrate-applications-from-okta-to-azure-active-directory/edit-environment.png)

1. Mettez à jour les valeurs de l’URL et de la clé API dans les champs **Initial Value** (Valeur initiale) et **Current Value** (Valeur actuelle). Modifiez le nom pour qu’il corresponde à votre environnement. Enregistrez ensuite les valeurs.

    ![Capture d’écran montrant comment mettre à jour les valeurs de l’API.](media/migrate-applications-from-okta-to-azure-active-directory/update-values-for-api.png)

1. [Chargez l’API dans Postman](https://app.getpostman.com/run-collection/377eaf77fdbeaedced17).

1. Sélectionnez **Apps (Applications)**  > **Get List Apps (Obtenir la liste des applications)**  > **Send (Envoyer)** .

   Vous pouvez maintenant imprimer toutes les applications de votre locataire Okta. La liste est au format JSON.

    ![Capture d’écran montrant une liste d’applications dans le locataire Okta.](media/migrate-applications-from-okta-to-azure-active-directory/list-of-applications.png)

Nous vous recommandons de copier et de convertir cette liste JSON au format CSV. Vous pouvez utiliser un convertisseur public tel que [Konklone](https://konklone.io/json/). Pour PowerShell, utilisez [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json) et [ConvertTo-CSV](/powershell/module/microsoft.powershell.utility/convertto-csv).

Téléchargez le fichier CSV pour garder une trace des applications dans votre locataire Okta à des fins de référence ultérieure.

## <a name="migrate-a-saml-application-to-azure-ad"></a>Migrer une application SAML vers Azure AD

Pour migrer une application SAML 2.0 vers Azure AD, commencez par configurer l’application dans votre client Azure AD pour l’accès à l’application. Dans cet exemple, nous allons convertir une instance Salesforce. Suivez [ce tutoriel](../saas-apps/salesforce-tutorial.md) pour configurer les applications.

Pour terminer la migration, répétez les étapes de configuration pour toutes les applications découvertes dans le locataire Okta.

1. Dans le [portail Azure AD](https://aad.portal.azure.com), sélectionnez **Azure Active Directory** > **Applications d’entreprise** > **Nouvelle application**.

   ![Capture d’écran montrant une liste de nouvelles applications.](media/migrate-applications-from-okta-to-azure-active-directory/list-of-new-applications.png)

1. Dans **Galerie Azure AD**, recherchez **Salesforce**, sélectionnez l’application, puis sélectionnez **Créer**.

   ![Capture d’écran montrant l’application Salesforce dans Galerie Azure AD.](media/migrate-applications-from-okta-to-azure-active-directory/salesforce-application.png)

1. Une fois l’application créée, dans l’onglet **Authentification unique** (SSO), sélectionnez **SAML**.

   ![Capture d’écran représentant l’application SAML.](media/migrate-applications-from-okta-to-azure-active-directory/saml-application.png)

1. Téléchargez le **certificat (brut)** et le fichier **XML de métadonnées de fédération** à importer dans Salesforce.

   ![Capture d’écran montrant où télécharger les métadonnées de fédération.](media/migrate-applications-from-okta-to-azure-active-directory/federation-metadata.png)

1. Dans la console d’administration Salesforce, sélectionnez **Identity (Identité)**  > **Single Sign-On Settings (Paramètres d’authentification unique)**  > **New from Metadata File (Nouveau à partir d’un fichier de métadonnées)** .

   ![Capture d’écran montrant la console d’administration Salesforce.](media/migrate-applications-from-okta-to-azure-active-directory/salesforce-admin-console.png)

1. Chargez le fichier XML que vous avez téléchargé à partir du portail Azure AD. Sélectionnez ensuite **Créer**.

   :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/upload-xml-file.png" alt-text="Capture d’écran montrant où charger le fichier XML." lightbox="media/migrate-applications-from-okta-to-azure-active-directory/upload-xml-file.png":::

1. Chargez le certificat que vous avez téléchargé à partir d’Azure. Sélectionnez ensuite **Save (Enregistrer)** pour créer le fournisseur SAML dans Salesforce.

   ![Capture d’écran montrant comment créer le fournisseur SAML dans Salesforce.](media/migrate-applications-from-okta-to-azure-active-directory/create-saml-provider.png)

1. Enregistrez les valeurs dans les champs suivants. Vous utiliserez ces valeurs dans Azure.
   * L’**ID d’entité**
   * **URL de connexion**
   * **URL de déconnexion**

   Sélectionnez ensuite **Télécharger les métadonnées**.

   ![Capture d’écran montrant les valeurs que vous devez enregistrer pour une utilisation dans Azure.](media/migrate-applications-from-okta-to-azure-active-directory/record-values-for-azure.png)

1. Sur la page **Applications d’entreprise** d’Azure AD, dans les paramètres d’authentification unique SAML, sélectionnez **Charger le fichier de métadonnées** pour charger le fichier sur le portail Azure AD. Avant d’enregistrer, assurez-vous que les valeurs importées correspondent aux valeurs enregistrées.

   ![Capture d’écran montrant comment charger le fichier de métadonnées dans Azure AD.](media/migrate-applications-from-okta-to-azure-active-directory/upload-metadata-file.png)

1. Dans la console d’administration Salesforce, sélectionnez **Company Settings (Paramètres de la société)**  > **My Domain (Mon domaine)** . Accédez à **Authentication Configuration (Configuration de l’authentification)** , puis sélectionnez **Edit (Modifier)** .

    ![Capture d’écran montrant comment modifier les paramètres de la société.](media/migrate-applications-from-okta-to-azure-active-directory/edit-company-settings.png)

1. Pour une option de connexion, sélectionnez le nouveau fournisseur SAML que vous avez configuré précédemment. Ensuite, sélectionnez **Enregistrer**.

    ![Capture d’écran montrant où enregistrer l’option de fournisseur SAML.](media/migrate-applications-from-okta-to-azure-active-directory/save-saml-provider.png)

1. Dans Azure AD, sur la page **Applications d’entreprise**, sélectionnez **Utilisateurs et groupes**. Ajoutez ensuite des utilisateurs de test.

    ![Capture d’écran montrant les utilisateurs de test ajoutés.](media/migrate-applications-from-okta-to-azure-active-directory/add-test-user.png)

1. Pour tester la configuration, connectez-vous en tant que l’un des utilisateurs de test. Rendez-vous dans votre [galerie d’applications](https://aka.ms/myapps) Microsoft, puis sélectionnez **Salesforce**.

    ![Capture d’écran montrant comment ouvrir Salesforce à partir de la galerie d’applications.](media/migrate-applications-from-okta-to-azure-active-directory/test-user-sign-in.png)

1. Sélectionnez le fournisseur d’identité (IdP) nouvellement configuré pour vous connecter.

    ![Capture d’écran montrant où se connecter.](media/migrate-applications-from-okta-to-azure-active-directory/new-identity-provider.png)

    Si tout a été correctement configuré, l’utilisateur de test arrivera sur la page d’accueil de Salesforce. Pour obtenir de l’aide en cas de problème, consultez le [guide de débogage](../manage-apps/debug-saml-sso-issues.md).

1. Sur la page **Applications d’entreprise**, affectez les utilisateurs restants à l’application Salesforce avec les rôles appropriés.

    >[!NOTE]
    >Une fois que vous avez ajouté les utilisateurs restants à l’application Azure AD, ceux-ci doivent tester la connexion pour s’assurer qu’ils y ont accès. Testez la connexion avant de passer à l’étape suivante.

1. Dans la console d’administration Salesforce, sélectionnez **Company Settings (Paramètres de la société)**  > **My Domain (Mon domaine)** .

1. Sous **Authentication Configuration (Configuration de l’authentification)** , sélectionnez **Edit (Modifier)** . Désactivez la sélection d’**Okta** comme service d’authentification.

    ![Capture d’écran montrant où désélectionner Okta en tant que service d’authentification.](media/migrate-applications-from-okta-to-azure-active-directory/deselect-okta.png)

Salesforce est maintenant correctement configuré avec Azure AD pour l’authentification unique.

## <a name="migrate-an-oidcoauth-20-application-to-azure-ad"></a>Migrer une application OIDC/OAuth 2.0 vers Azure AD

Pour migrer une application OpenID Connect (OIDC) ou OAuth 2.0 vers Azure AD, dans votre locataire Azure AD, configurez d’abord l’accès de l’application. Dans cet exemple, nous allons convertir une application OIDC personnalisée.

Pour terminer la migration, répétez les étapes de configuration suivantes pour toutes les applications découvertes dans le locataire Okta.

1. Dans le [portail Azure AD](https://aad.portal.azure.com), sélectionnez **Azure Active Directory** > **Applications d’entreprise**. Sous **Toutes les applications**, sélectionnez **Nouvelle application**.

1. Sélectionnez **Créer votre propre application**. Dans le menu qui s’affiche, nommez l’application OIDC, puis sélectionnez **Inscrire une application sur laquelle vous travaillez pour l’intégrer dans Azure AD**. Sélectionnez ensuite **Créer**.

   :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/new-oidc-application.png" alt-text="Capture d’écran montrant comment créer une application OIDC." lightbox="media/migrate-applications-from-okta-to-azure-active-directory/new-oidc-application.png":::

1. Sur la page suivante, configurez la location de l’inscription de votre application. Pour plus d’informations, consultez [Location dans Azure Active Directory](../develop/single-and-multi-tenant-apps.md).

   Dans cet exemple, nous choisirons **Compte dans n’importe quel répertoire organisationnel (n’importe quel répertoire Azure AD – Multilocataire)**  > **Inscrire**.

   ![Capture d’écran montrant comment sélectionner un répertoire Azure AD multilocataire.](media/migrate-applications-from-okta-to-azure-active-directory/multitenant-azure-ad-directory.png)

1. Sur la page **Inscriptions d’applications**, sous **Azure Active Directory**, ouvrez l’inscription nouvellement créée.

   Selon le [scénario d’application](../develop/authentication-flows-app-scenarios.md), différentes actions de configuration peuvent être nécessaires. La plupart des scénarios requièrent une clé secrète client d’application, nous allons donc couvrir ces scénarios.

1. Sur la page **Vue d’ensemble**, enregistrez l’**ID d’application (client)** . Vous utiliserez cet ID dans votre application.

   ![Capture d’écran montrant l’ID client de l’application.](media/migrate-applications-from-okta-to-azure-active-directory/application-client-id.png)

1. À gauche, sélectionnez **Certificats et secrets**. Ensuite, sélectionnez **Nouveau secret client**. Nommez la clé secrète client et définissez son expiration.

   ![Capture d’écran montrant la nouvelle clé secrète client.](media/migrate-applications-from-okta-to-azure-active-directory/new-client-secret.png)

1. Enregistrez la valeur et l’ID du secret.

   >[!NOTE]
   >Si vous perdez la clé secrète client, vous ne pouvez pas la récupérer. Au lieu de cela, vous devez régénérer un secret.

1. Sur la gauche, sélectionnez **Autorisations d’API**. Accordez ensuite à l’application l’accès à la pile OIDC.

1. Sélectionnez **Ajouter une autorisation** > **Microsoft Graph** > **Permissions déléguées**.

1. Dans la section **Autorisations OpenID**, sélectionnez **adresse e-mail**, **openid** et **profil**. Sélectionnez ensuite **Ajouter des autorisations**.

    :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/add-openid-permission.png" alt-text="Capture d’écran montrant où ajouter les autorisations OpenID." lightbox="media/migrate-applications-from-okta-to-azure-active-directory/add-openid-permission.png":::

1. Pour améliorer l’expérience utilisateur et supprimer les invites de consentement de l’utilisateur, sélectionnez **Accorder le consentement administrateur pour Nom de domaine du locataire**. Attendez ensuite que l’état **Accordé** apparaisse.

    ![Capture d’écran montrant où accorder le consentement administrateur.](media/migrate-applications-from-okta-to-azure-active-directory/grant-admin-consent.png)

1. Si votre application possède un URI de redirection, entrez l’URI approprié. Si l’URL de réponse cible l’onglet **Authentification**, puis **Ajouter une plateforme** et **Web**, entrez l’URL appropriée. Sélectionnez **Jetons d’accès** et **Jetons d’ID**. Ensuite, sélectionnez **Configurer**.

    :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/configure-tokens.png" alt-text="Capture d’écran montrant comment configurer des jetons." lightbox="media/migrate-applications-from-okta-to-azure-active-directory/configure-tokens.png":::
    
    Dans le menu **Authentification**, sous **Paramètres avancés** et **Autoriser les flux clients publics**, si nécessaire, sélectionnez **Oui**.

    ![Capture d’écran montrant comment autoriser des flux clients publics.](media/migrate-applications-from-okta-to-azure-active-directory/allow-client-flows.png)

1. Dans votre application configurée par OIDC, importez l’ID d’application et la clé secrète client avant de procéder au test. Suivez les étapes précédentes pour configurer votre application avec des paramètres tels que l’ID client, le secret et les étendues.

## <a name="migrate-a-custom-authorization-server-to-azure-ad"></a>Migrer un serveur d’autorisation personnalisé vers Azure AD

Les serveurs d’autorisation Okta mappent de un à un aux inscriptions d’application qui [exposent une API](../develop/quickstart-configure-app-expose-web-apis.md#add-a-scope).

Le serveur d’autorisation Okta par défaut doit être mappé à des étendues ou autorisations Microsoft Graph.

![Capture d’écran montrant l’autorisation Okta par défaut.](media/migrate-applications-from-okta-to-azure-active-directory/default-okta-authorization.png)

## <a name="next-steps"></a>Étapes suivantes

- [Migrer la fédération Okta vers Azure AD](migrate-okta-federation-to-azure-active-directory.md)
- [Migrer l’approvisionnement de synchronisation Okta vers la synchronisation basée sur Azure AD Connect](migrate-okta-sync-provisioning-to-azure-active-directory.md)
- [Migrer les stratégies de connexion Okta vers l’accès conditionnel Azure AD](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)
