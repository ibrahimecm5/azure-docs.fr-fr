---
title: Tutoriel pour migrer la fédération Okta vers l’authentification gérée par Azure Active Directory
titleSuffix: Active Directory
description: Découvrez comment migrer vos applications fédérées Okta vers l’authentification gérée par Azure AD.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: ede3537ba6bd849a3792b11dffc96a073302db20
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451575"
---
# <a name="tutorial-migrate-okta-federation-to-azure-active-directory-managed-authentication"></a>Tutoriel : Migrer la fédération Okta vers l’authentification gérée par Azure Active Directory

Ce tutoriel explique comment fédérer vos locataires Office 365 existants avec Okta pour obtenir des capacités d’authentification unique (SSO).

Vous pouvez migrer la fédération vers Azure Active Directory (Azure AD) de manière échelonnée afin de garantir une bonne expérience d’authentification pour les utilisateurs. Dans le cadre d’une migration par étapes, vous pouvez également tester l’accès de la fédération inverse à toutes les applications d’authentification unique Okta restantes.

## <a name="prerequisites"></a>Prérequis

- Un locataire Office 365 fédéré à Okta pour l’authentification unique
- Un serveur Azure AD Connect ou des agents d’approvisionnement du cloud Azure AD Connect configurés pour l’attribution d’utilisateurs dans Azure AD

## <a name="configure-azure-ad-connect-for-authentication"></a>Configurer Azure AD Connect pour l’authentification

Il se peut que les clients qui ont fédéré leurs domaines Office 365 avec Okta ne disposent pas d’une méthode d’authentification valide configurée dans Azure AD. Avant de migrer vers l’authentification gérée, validez Azure AD Connect et configurez-le pour autoriser la connexion des utilisateurs.

Configurez la méthode de connexion la mieux adaptée à votre environnement :

- **Synchronisation de hachage du mot de passe** : La [synchronisation de hachage du mot de passe](../hybrid/whatis-phs.md) est une extension de la fonctionnalité de synchronisation d’annuaires implémentée par un serveur ou des agents d’approvisionnement du cloud Azure AD Connect. Vous pouvez utiliser cette fonctionnalité pour vous connecter à des services Azure AD comme Microsoft 365. Vous vous connectez au service à l’aide du mot de passe que vous utilisez pour vous connecter à votre instance locale d’Active Directory.
- **Authentification directe** : L’[authentification directe](../hybrid/how-to-connect-pta.md) d’Azure AD permet aux utilisateurs de se connecter à des applications locales et informatiques en utilisant les mêmes mots de passe. Lorsque les utilisateurs se connectent par l’intermédiaire d’Azure AD, l’agent d’authentification directe valide les mots de passe directement auprès d’Active Directory local.
- **Authentification unique transparente** : L’[authentification unique transparente Azure AD](../hybrid/how-to-connect-sso.md) connecte automatiquement les utilisateurs d’ordinateurs d’entreprise connectés au réseau d’entreprise. L’authentification unique transparente permet aux utilisateurs d’accéder facilement aux applications informatiques sans nécessiter d’autres composants sur site.

L’authentification unique transparente peut également être déployée pour la synchronisation de hachage du mot de passe ou l’authentification directe afin de créer une expérience d’authentification transparente pour les utilisateurs dans Azure AD.

Suivez le [guide de déploiement](../hybrid/how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) pour vous assurer que vous déployez tous les prérequis nécessaires à l’authentification unique transparente pour vos utilisateurs.

Pour notre exemple, nous allons configurer la synchronisation de hachage du mot de passe et l’authentification unique transparente.

### <a name="configure-azure-ad-connect-for-password-hash-synchronization-and-seamless-sso"></a>Configurer Azure AD Connect pour la Synchronisation de hachage du mot de passe et l’Authentification unique transparente

Pour configurer Azure AD Connect pour la synchronisation de hachage du mot de passe, procédez comme suit :

1. Sur le serveur Azure AD Connect, ouvrez l’application **Azure AD Connect**, puis sélectionnez **Configurer**.

   ![Capture d’écran montrant l’icône Azure AD et le bouton Configurer.](media/migrate-okta-federation-to-azure-active-directory/configure-azure-ad.png)

1. Sélectionnez **Modifier la connexion utilisateur** > **Suivant**.

   ![Capture d’écran montrant la page de modification de la connexion de l’utilisateur.](media/migrate-okta-federation-to-azure-active-directory/change-user-signin.png)

1. Entrez vos informations d’identification d’administrateur général.

   ![Capture d’écran montrant où entrer les informations d’identification d’administrateur général.](media/migrate-okta-federation-to-azure-active-directory/global-admin-credentials.png)

1. Actuellement, le serveur est configuré pour la fédération avec Okta. Changez la sélection en **Synchronisation de hachage du mot de passe**. Sélectionnez ensuite **Activer l’authentification unique**.

1. Sélectionnez **Suivant**.

Pour activer l’authentification unique transparente, procédez comme suit :

1. Entrez les informations d’identification de l’administrateur de domaine pour le système local. Sélectionnez ensuite **Suivant**.

   ![Capture d’écran montrant les paramètres de connexion de l’utilisateur.](media/migrate-okta-federation-to-azure-active-directory/domain-admin-credentials.png)

1. Sur la dernière page, sélectionnez **Configurer** pour mettre à jour le serveur Azure AD Connect.

   ![Capture d’écran montrant la page de configuration.](media/migrate-okta-federation-to-azure-active-directory/update-azure-ad-connect-server.png)

1. Ignorez pour l’instant l’avertissement concernant la jonction Azure AD hybride. Vous reconfigurerez les options de l’appareil après avoir désactivé la fédération à partir d’Okta.

   ![Capture d’écran montrant le lien permettant de configurer les options de l’appareil.](media/migrate-okta-federation-to-azure-active-directory/reconfigure-device-options.png)

## <a name="configure-staged-rollout-features"></a>Configurer les fonctionnalités de déploiement par étapes

Dans Azure AD, vous pouvez utiliser un [déploiement par étapes de l’authentification cloud](../hybrid/how-to-connect-staged-rollout.md) pour tester la défédéralisation des utilisateurs avant de tester la défédéralisation d’un domaine entier. Avant de procéder au déploiement, vérifiez les [prérequis](../hybrid/how-to-connect-staged-rollout.md#prerequisites).

Après avoir activé la synchronisation de hachage du mot de passe et l’authentification unique transparente sur le serveur Azure AD Connect, procédez comme suit pour configurer un déploiement par étapes :

1. Dans le [portail Azure](https://portal.azure.com/#home), sélectionnez **Afficher** ou **Gérer Azure Active Directory**.

   ![Capture d’écran montrant le portail Azure](media/migrate-okta-federation-to-azure-active-directory/azure-portal.png)

1. Dans le menu **Azure Active Directory**, sélectionnez **Azure AD Connect**. Confirmez ensuite que l’option **Synchronisation de hachage du mot de passe** est activée dans le locataire.

1. Sélectionnez **Activer le déploiement par étapes pour la connexion des utilisateurs gérés**.

   ![Capture d’écran montrant l’option permettant d’activer le déploiement par étapes.](media/migrate-okta-federation-to-azure-active-directory/enable-staged-rollout.png)

1. Votre paramètre **Synchronisation de hachage du mot de passe** peut avoir la valeur **Activé** après la configuration du serveur. Si ce paramètre n’est pas activé, activez-le maintenant.

   Notez que **Authentification unique fluide** est défini sur **Désactivé**. Si vous tentez de l’activer, un message d’erreur s’affiche, car elle est déjà activée pour les utilisateurs du locataire.

1. Sélectionnez **Gérer les groupes**.

   ![Capture d’écran montrant le bouton permettant de gérer les groupes.](media/migrate-okta-federation-to-azure-active-directory/password-hash-sync.png)

Suivez les instructions pour ajouter un groupe au déploiement de la synchronisation de hachage du mot de passe. Dans l’exemple suivant, le groupe de sécurité commence avec dix membres.

![Capture d’écran montrant un exemple de groupe de sécurité.](media/migrate-okta-federation-to-azure-active-directory/example-security-group.png)

Après avoir ajouté le groupe, attendez environ 30 minutes pour que la fonctionnalité prenne effet dans votre locataire. Une fois que la fonctionnalité a pris effet, vos utilisateurs ne sont plus redirigés vers Okta quand ils tentent d’accéder aux services Office 365.

Certains scénarios d’utilisation de la fonctionnalité de déploiement par étapes ne sont pas pris en charge :

- L’authentification héritée telle que POP3 et SMTP n’est pas prise en charge.
- Si vous avez configuré la jonction Azure AD hybride pour une utilisation avec Okta, l’acheminement de tous les flux de jonction Azure AD hybride vers Okta continue jusqu’à ce que le domaine soit défédéralisé. Une stratégie de connexion doit rester dans Okta pour permettre l’authentification héritée pour les clients Windows avec jonction Azure AD Hybride.

## <a name="create-an-okta-app-in-azure-ad"></a>Créer une application Okta dans Azure AD

Les utilisateurs qui se sont convertis à l’authentification gérée peuvent encore avoir besoin d’accéder aux applications dans Okta. Pour permettre aux utilisateurs d’accéder facilement à ces applications, vous pouvez inscrire une application Azure AD qui renvoie à la page d’accueil d’Okta.

Pour configurer l’inscription de l’application d’entreprise pour Okta :

1. Dans le [portail Azure](https://portal.azure.com/#home), sous **Gérer Azure Active Directory**, sélectionnez **Afficher**.

1. Dans le menu de gauche, sous **Gérer**, sélectionnez **Applications d’entreprise**.

   ![Capture d’écran montrant la sélection « Applications d’entreprise ».](media/migrate-okta-federation-to-azure-active-directory/enterprise-application.png)

1. Dans le menu **Toutes les applications**, sélectionnez **Nouvelle application**.

   ![Capture d’écran montrant la sélection « Nouvelle application ».](media/migrate-okta-federation-to-azure-active-directory/new-application.png)

1. Sélectionnez **Créer votre propre application**. Dans le menu qui s’ouvre, nommez l’application Okta et sélectionnez **Inscrire une application sur laquelle vous travaillez pour l’intégrer dans Azure AD**. Sélectionnez ensuite **Créer**.

   :::image type="content" source="media/migrate-okta-federation-to-azure-active-directory/register-application.png" alt-text="Capture d’écran montrant comment enregistrer une application." lightbox="media/migrate-okta-federation-to-azure-active-directory/register-application.png":::

1. Sélectionnez **Compte dans n’importe quel répertoire organisationnel (n’importe quel répertoire Azure AD – Multilocataire)**  > **Inscrire**.

   ![Capture d’écran montrant comment inscrire une application et modifier le compte d’application.](media/migrate-okta-federation-to-azure-active-directory/register-change-application.png)

1. Dans le menu Azure AD, sélectionnez **Inscriptions d’applications**. Ouvrez ensuite l’inscription nouvellement créée.

   ![Capture d’écran montrant l’inscription d’une nouvelle application.](media/migrate-okta-federation-to-azure-active-directory/app-registration.png)

1. Enregistrez votre ID de locataire et votre ID d’application.

   >[!Note]
   >Vous aurez besoin de l’ID de locataire et de l’ID d’application pour configurer le fournisseur d’identité dans Okta.

   ![Capture d’écran montrant l’ID de locataire et l’ID d’application.](media/migrate-okta-federation-to-azure-active-directory/record-ids.png)

1. Dans le menu de gauche, sélectionnez **Certificats et secrets**. Ensuite, sélectionnez **Nouveau secret client**. Donnez au secret un nom générique et définissez sa date d’expiration.

1. Enregistrez la valeur et l’ID du secret.

   >[!NOTE]
   >La valeur et l’ID ne seront pas affichés ultérieurement. Si vous n’enregistrez pas ces informations maintenant, vous devrez régénérer un secret.

   ![Capture d’écran montrant où enregistrer la valeur et l’ID du secret.](media/migrate-okta-federation-to-azure-active-directory/record-secrets.png)

1. Dans le menu gauche, sélectionnez **Autorisations d’API**. Accordez à l’application l’accès à la pile OpenID Connect (OIDC).

1. Sélectionnez **Ajouter une autorisation** > **Microsoft Graph** > **Permissions déléguées**.

    :::image type="content" source="media/migrate-okta-federation-to-azure-active-directory/delegated-permissions.png" alt-text="Capture d’écran montrant les permissions déléguées." lightbox="media/migrate-okta-federation-to-azure-active-directory/delegated-permissions.png":::

1. Dans la section Autorisations OpenID, ajoutez **adresse e-mail**, **openid** et **profil**. Sélectionnez ensuite **Ajouter des autorisations**.

    :::image type="content" source="media/migrate-okta-federation-to-azure-active-directory/add-permissions.png" alt-text="Capture d’écran montrant comment ajouter des autorisations." lightbox="media/migrate-okta-federation-to-azure-active-directory/add-permissions.png":::

1. Sélectionnez **Accorder le consentement de l’administrateur pour \<tenant domain name>** et attendez que l’état **Accordé** apparaisse.

    ![Capture d’écran montrant le consentement accordé.](media/migrate-okta-federation-to-azure-active-directory/grant-consent.png)

1. Dans le menu de gauche, sélectionnez **Personnalisation**. Pour **URL de la page d’accueil**, ajoutez la page d’accueil de l’application de votre utilisateur.

    ![Capture d’écran montrant comment ajouter une personnalisation.](media/migrate-okta-federation-to-azure-active-directory/add-branding.png)

1. Dans le portail d’administration d’Okta, sélectionnez **Sécurité** > **Fournisseurs d’identité** pour ajouter un nouveau fournisseur d’identité. Sélectionnez **Ajouter Microsoft**.

    ![Capture d’écran montrant comment ajouter le fournisseur d’identité.](media/migrate-okta-federation-to-azure-active-directory/configure-idp.png)

1. Sur la page **Fournisseur d’identité**, copiez l’ID de votre application dans le champ **ID client**. Copiez la clé secrète client dans le champ **Clé secrète client**.

1. Sélectionnez **Afficher les paramètres avancées**. Par défaut, cette configuration liera le nom d’utilisateur principal (UPN) dans Okta à l’UPN dans Azure AD pour l’accès à la fédération inverse.

    >[!IMPORTANT]
    >Si vos UPN dans Okta et Azure AD ne correspondent pas, sélectionnez un attribut commun aux utilisateurs.

1. Terminez vos sélections pour l’autoapprovisionnement. Par défaut, si un utilisateur ne correspond pas dans Okta, le système tentera de l’attribuer dans Azure AD. Si vous avez migré l’approvisionnement en dehors d’Okta, sélectionnez **Rediriger vers la page de connexion d’Okta**.

    ![Capture d’écran montrant l’option de redirection vers la page de connexion d’Okta.](media/migrate-okta-federation-to-azure-active-directory/redirect-okta.png)

    Maintenant que vous avez créé le fournisseur d’identité (IdP), vous devez envoyer des utilisateurs à l’IdP approprié.

1. Dans le menu **Fournisseurs d’identité**, sélectionnez **Règles d’acheminement** > **Ajouter une règle d’acheminement**. Utilisez l’un des attributs disponibles dans le profil Okta.

1. Pour diriger les connexions de tous les appareils et adresses IP vers Azure AD, configurez la stratégie comme l’illustre l’image suivante.

    Dans cet exemple, l’attribut **Division** n’est pas utilisé sur tous les profils Okta. Il s’agit donc d’un bon choix pour le routage de l’IdP.

    ![Capture d’écran montrant l’attribut Division pour le routage de l’IdP.](media/migrate-okta-federation-to-azure-active-directory/division-idp-routing.png)

1. Maintenant que vous avez ajouté la règle d’acheminement, enregistrez l’URI de redirection afin de pouvoir l’ajouter à l’inscription de l’application.

    ![Capture d’écran montrant l’emplacement de l’URI de redirection.](media/migrate-okta-federation-to-azure-active-directory/application-registration.png)

1. Dans l’inscription de votre application, dans le menu de gauche, sélectionnez **Authentification**. Sélectionnez ensuite **Ajouter une plateforme** > **Web**.

    :::image type="content" source="media/migrate-okta-federation-to-azure-active-directory/add-platform.png" alt-text="Capture d’écran montrant comment ajouter une plateforme web." lightbox="media/migrate-okta-federation-to-azure-active-directory/add-platform.png":::

1. Ajoutez l’URI de redirection que vous avez enregistré pour l’IdP dans Okta. Sélectionnez ensuite **Jetons d’accès** et **Jetons d’ID**.

    ![Capture d’écran montrant les jetons d’accès et d’ID d’Okta.](media/migrate-okta-federation-to-azure-active-directory/access-id-tokens.png)

1. Dans la console d’administration, sélectionnez **Répertoire** > **Personnes**. Sélectionnez votre premier utilisateur de test pour en modifier le profil.

1. Dans le profil, ajoutez **ToAzureAD** comme dans l’image suivante. Ensuite, sélectionnez **Enregistrer**.

    ![Capture d’écran montrant comment modifier un profil.](media/migrate-okta-federation-to-azure-active-directory/profile-editing.png)

1. Essayez de vous connecter au [portail Microsoft 356](https://portal.office.com) en tant qu’utilisateur modifié. Si votre utilisateur ne fait pas partie du pilote d’authentification gérée, vous remarquerez que votre action tourne en boucle. Pour sortir de la boucle, ajoutez l’utilisateur à l’expérience d’authentification gérée.

## <a name="test-okta-app-access-on-pilot-members"></a>Tester l’accès à l’application Okta sur les membres du pilote

Après avoir configuré l’application Okta dans Azure AD et l’IdP dans le portail Okta, vous devez affecter l’application aux utilisateurs.

1. Sur le portail Azure, sélectionnez **Azure Active Directory** > **Applications d’entreprise**.

1. Sélectionnez l’inscription d’application que vous avez créé précédemment et rendez-vous dans **Utilisateurs et groupes**. Ajoutez le groupe qui est en corrélation avec le pilote d’authentification gérée.

   >[!NOTE]
   >Vous pouvez ajouter des utilisateurs et des groupes uniquement à partir de la page **Applications d’entreprise**. Vous ne pouvez pas ajouter d’utilisateurs à partir du menu **Inscriptions d’applications**.

   ![Capture d’écran montrant comment ajouter un groupe.](media/migrate-okta-federation-to-azure-active-directory/add-group.png)

1. Après environ 15 minutes, connectez-vous en tant que l’un des utilisateurs du pilote d’authentification gérée, puis rendez-vous dans [Mes applications](https://myapplications.microsoft.com).

   ![Capture d’écran montrant la galerie Mes applications.](media/migrate-okta-federation-to-azure-active-directory/my-applications.png)

1. Sélectionnez la vignette **Accès à l’application Okta** pour ramener l’utilisateur à la page d’accueil d’Okta.

## <a name="test-managed-authentication-on-pilot-members"></a>Tester l’authentification gérée sur des membres pilotes

Après avoir configuré l’application de fédération inverse Okta, demandez à vos utilisateurs d’effectuer des tests complets sur l’expérience d’authentification gérée. Nous vous recommandons de configurer la personnalisation de la société pour aider vos utilisateurs à reconnaître le locataire auquel ils se connectent. Pour plus d’informations, consultez [Personnaliser la page de connexion Azure AD de votre organisation](../fundamentals/customize-branding.md).

>[!IMPORTANT]
>Identifiez toutes les stratégies d’accès conditionnel supplémentaires dont vous pourriez avoir besoin avant de défédéraliser complètement les domaines d’Okta. Pour sécuriser votre environnement avant la coupure complète, consultez [Migration des stratégies de connexion Okta vers l’accès conditionnel Azure AD](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md).

## <a name="defederate-office-365-domains"></a>Défédéraliser les domaines Office 365

Lorsque votre organisation est à l’aise avec l’expérience d’authentification gérée, vous pouvez défédéraliser votre domaine d’Okta. Pour commencer, utilisez les commandes suivantes pour vous connecter à MSOnline PowerShell. Si vous n’avez pas encore le module MSOnline PowerShell, téléchargez-le en entrant `install-module MSOnline`.

```PowerShell

import-module MSOnline
Connect-Msolservice
Set-msoldomainauthentication 
-domainname yourdomain.com -authentication managed

```

Après avoir configuré le domaine en authentification gérée, vous avez réussi à défédéraliser votre locataire Office 365 d’Okta tout en maintenant l’accès des utilisateurs à la page d’accueil d’Okta.

## <a name="next-steps"></a>Étapes suivantes

- [Migrer l’approvisionnement de synchronisation Okta vers la synchronisation basée sur Azure AD Connect](migrate-okta-sync-provisioning-to-azure-active-directory.md)
- [Migrer les stratégies de connexion Okta vers l’accès conditionnel Azure AD](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)
- [Migrer des applications d’Okta vers Azure AD](migrate-applications-from-okta-to-azure-active-directory.md)
