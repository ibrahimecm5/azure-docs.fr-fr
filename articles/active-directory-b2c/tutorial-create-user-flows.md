---
title: Tutoriel - Créer des flux d’utilisateurs et des stratégies personnalisées - Azure Active Directory B2C
description: Suivez ce tutoriel pour découvrir comment créer des flux d’utilisateurs et des stratégies personnalisées dans le portail Azure afin de permettre l’inscription, la connexion et la modification de profil utilisateur pour vos applications dans Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 10/18/2021
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 5cdf2cfb9b530721a8de31501c2f763f0c61bd21
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130240886"
---
# <a name="tutorial-create-user-flows-and-custom-policies-in-azure-active-directory-b2c"></a>Tutoriel : créer des flux d’utilisateurs et des stratégies personnalisées dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Vos applications comportent peut-être des flux utilisateur qui permettent aux utilisateurs de s’inscrire, de se connecter ou de gérer leur profil. Vous pouvez créer plusieurs flux d’utilisateurs de types différents dans votre locataire Azure Active Directory B2C (Azure AD B2C) et les utiliser dans vos applications selon les besoins. Les flux d’utilisateurs peuvent être réutilisés entre les différentes applications.

::: zone pivot="b2c-user-flow"
Un flux d’utilisateur vous permet de déterminer la façon dont les utilisateurs interagissent avec votre application quand ils effectuent différentes opérations : connexion, inscription, modification d’un profil, réinitialisation d’un mot de passe, etc. Dans cet article, vous apprendrez comment :
::: zone-end

::: zone pivot="b2c-custom-policy"
Les [stratégies personnalisées](custom-policy-overview.md) sont des fichiers de configuration qui définissent le comportement de votre locataire Azure Active Directory B2C (Azure AD B2C). Dans cet article, vous apprendrez comment :
::: zone-end

> [!div class="checklist"]
> * Créer un flux d’utilisateur d’inscription et de connexion
> * Activer la réinitialisation du mot de passe libre-service
> * Créer un flux d’utilisateur de modification de profil

::: zone pivot="b2c-user-flow"
> [!IMPORTANT]
> Nous avons modifié la manière dont nous référençons les versions de flux utilisateur. Auparavant, nous proposions des versions V1 (prêtes pour la production) et des versions V1.1 et V2 (préversions). À présent, nous avons regroupé les flux utilisateur en deux versions : les flux utilisateur **recommandés** avec les dernières fonctionnalités et les flux utilisateur **standard (hérités)** . Tous les flux d’utilisateurs en préversion hérités (v1.1 et v2) sont déconseillés. Pour plus d’informations, consultez [Versions de flux utilisateur dans Azure AD B2C](user-flow-versions.md). *Ces modifications s’appliquent uniquement au cloud public Azure. Les autres environnements continueront à utiliser le [contrôle de version de flux utilisateur hérité](user-flow-versions-legacy.md).* 
::: zone-end

## <a name="prerequisites"></a>Prérequis

::: zone pivot="b2c-user-flow"
- Si vous n’en avez pas, [créez un locataire Azure AD B2C](tutorial-create-tenant.md) qui est lié à votre abonnement Azure.
- [Inscrivez une application web](tutorial-register-applications.md) et [activez l’octroi implicite de jeton d’ID](tutorial-register-applications.md#enable-id-token-implicit-grant).
::: zone-end

::: zone pivot="b2c-custom-policy"

- Si vous n’en avez pas, [créez un locataire Azure AD B2C](tutorial-create-tenant.md) qui est lié à votre abonnement Azure.
- [Inscrivez une application web](tutorial-register-applications.md) et [activez l’octroi implicite de jeton d’ID](tutorial-register-applications.md#enable-id-token-implicit-grant).


::: zone-end

::: zone pivot="b2c-user-flow"
## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Créer un flux d’utilisateur d’inscription et de connexion

Le flux d’utilisateur Inscription et connexion gère les expériences d’inscription et de connexion avec une seule configuration. Les utilisateurs de votre application sont dirigés vers le chemin approprié en fonction du contexte.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Dans le portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sous **Stratégies**, sélectionnez **Flux d’utilisateurs**, puis sélectionnez **Nouveau flux d’utilisateur**.

    ![Page Flux d'utilisateur du portail avec bouton Nouveau flux d'utilisateur en surbrillance](./media/tutorial-create-user-flows/sign-up-sign-in-user-flow.png)

1. Dans la page **Créer un flux d’utilisateur**, sélectionnez le flux utilisateur **Inscription et connexion**.

    ![Page Sélectionner un flux d'utilisateur avec Inscription et connexion en surbrillance](./media/tutorial-create-user-flows/select-user-flow-type.png)

1. Sous **Sélectionner une version**, sélectionnez **Recommandé**, puis **Créer**. ([Apprenez-en davantage](user-flow-versions.md) sur les version de flux utilisateur.)

    ![Page Créer un flux d'utilisateur du Portail Azure avec propriétés en surbrillance](./media/tutorial-create-user-flows/select-version.png)

1. Entrez un **Nom** pour le flux d’utilisateur. Par exemple, *signupsignin1*.
1. Pour **Fournisseurs d’identité**, sélectionnez **Inscription par e-mail**.
1. Pour **Attributs utilisateur et revendications**, choisissez les revendications et les attributs à collecter et envoyer par l’utilisateur pendant l’inscription. Par exemple, sélectionnez **Afficher plus**, puis choisissez des attributs et des revendications pour **Pays/région**, **Nom d’affichage** et **Code postal**. Cliquez sur **OK**.

    ![Page de sélection des attributs et revendications avec trois revendications sélectionnées](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Cliquez sur **Créer** pour ajouter le flux utilisateur. Un préfixe *B2C_1* est automatiquement ajouté au nom.

### <a name="test-the-user-flow"></a>Tester le flux utilisateur

1. Sélectionnez le flux utilisateur que vous avez créé pour ouvrir sa page de présentation, puis sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *webapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Cliquez sur **Exécuter le flux d’utilisateur**, puis sélectionnez **S’inscrire maintenant**.

    ![Page Exécuter le flux d'utilisateur du portail avec bouton Exécuter le flux d'utilisateur en surbrillance](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Entrez une adresse e-mail valide, cliquez sur **Envoyer un code de vérification**, entrez le code de vérification que vous recevez, puis sélectionnez **Vérifier le code**.
1. Entrez un nouveau mot de passe et confirmez-le.
1. Sélectionnez votre pays et votre région, entrez le nom que vous voulez afficher, entrez un code postal, puis cliquez sur **Créer**. Le jeton est envoyé à `https://jwt.ms` et vous devez le voir.
1. Vous pouvez maintenant réexécuter le flux d’utilisateur et vous connecter avec le compte que vous avez créé. Le jeton retourné comprend les revendications que vous avez sélectionnées pour le pays/région, le nom et le code postal.

> [!NOTE]
> L’expérience « Exécuter le flux utilisateur » n’est actuellement pas compatible avec le type d’URL de réponse SPA utilisant le flux de code d’autorisation. Pour utiliser l’expérience « Exécuter le flux d’utilisateur » avec ces types d’applications, enregistrez une URL de réponse de type « Web » et activez le flux implicite, comme décrit [ici](tutorial-register-spa.md).

## <a name="enable-self-service-password-reset"></a>Activer la réinitialisation du mot de passe libre-service

Pour activer la [réinitialisation du mot de passe en libre-service](add-password-reset-policy.md) pour le flux d’utilisateur d’inscription ou de connexion :

1. Sélectionnez le flux d’utilisateur d’inscription ou de connexion que vous avez créé.
1. Dans le menu de gauche, sous **Paramètres**, sélectionnez **Propriétés**.
1. Sous **Complexité du mot de passe**, sélectionnez **Réinitialisation du mot de passe en libre-service**.
1. Sélectionnez **Enregistrer**.

### <a name="test-the-user-flow"></a>Tester le flux utilisateur

1. Sélectionnez le flux utilisateur que vous avez créé pour ouvrir sa page de présentation, puis sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *webapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez **Exécuter le flux utilisateur**.
1. Dans la page d’inscription ou de connexion, sélectionnez **Vous avez oublié votre mot de passe ?** .
1. Vérifiez l’adresse e-mail du compte que vous avez précédemment créé, puis sélectionnez **Continuer**.
1. Vous avez maintenant la possibilité de changer le mot de passe de l’utilisateur. Changez le mot de passe et sélectionnez **Continuer**. Le jeton est envoyé à `https://jwt.ms` et vous devez le voir.

## <a name="create-a-profile-editing-user-flow"></a>Créer un flux d’utilisateur de modification de profil

Si vous voulez autoriser les utilisateurs à modifier leur profil dans votre application, vous utilisez un flux d’utilisateur de modification de profil.

1. Dans le menu de la page de vue d’ensemble du locataire Azure AD B2C, sélectionnez **Flux d’utilisateurs**, puis sélectionnez **Nouveau flux d’utilisateur**.
1. Dans la page **Créer un flux d’utilisateur**, sélectionnez le flux utilisateur **Modifications de profil**. 
1. Sous **Sélectionner une version**, sélectionnez **Recommandé**, puis **Créer**.
1. Entrez un **Nom** pour le flux d’utilisateur. Par exemple, *profileediting1*.
1. Sous **Fournisseurs d’identité**, sélectionnez **Connexion du compte local**.
2. Sous **Attributs utilisateur**, choisissez les attributs que le client peut modifier dans son profil. Par exemple, sélectionnez **Afficher plus**, puis choisissez des attributs et des revendications pour **Nom d’affichage** et **Poste**. Cliquez sur **OK**.
3. Cliquez sur **Créer** pour ajouter le flux utilisateur. Un préfixe *B2C_1* est automatiquement ajouté au nom.

### <a name="test-the-user-flow"></a>Tester le flux utilisateur

1. Sélectionnez le flux utilisateur que vous avez créé pour ouvrir sa page de présentation, puis sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *webapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Cliquez sur **Exécuter le flux d’utilisateur**, puis connectez-vous avec le compte que vous avez créé précédemment.
1. Vous avez désormais la possibilité de changer le nom d’affichage et le poste de l’utilisateur. Cliquez sur **Continuer**. Le jeton est envoyé à `https://jwt.ms` et vous devez le voir.
::: zone-end

::: zone pivot="b2c-custom-policy"
> [!TIP]
> Cet article explique comment configurer manuellement votre locataire. Vous pouvez automatiser l’ensemble du processus à partir de cet article. L’automatisation permettra de déployer le [pack de démarrage SocialAndLocalAccountsWithMFA](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack) Azure AD B2C, qui fournira les parcours Inscription et connexion, Réinitialisation de mot de passe et Modification de profil. Pour automatiser la procédure pas à pas ci-dessous, accédez à [l’application d’installation IEF](https://aka.ms/iefsetup) et suivez les instructions.


## <a name="add-signing-and-encryption-keys-for-identity-experience-framework-applications"></a>Ajouter des clés de signature et de chiffrement pour les applications Identity Experience Framework

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Dans la Portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Dans la page de vue d’ensemble, sous **Stratégies**, sélectionnez **Identity Experience Framework**.

### <a name="create-the-signing-key"></a>Créer la clé de signature

1. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
1. Pour **Options**, choisissez `Generate`.
1. Dans **Nom**, entrez `TokenSigningKeyContainer`. Il est possible que le préfixe `B2C_1A_` soit ajouté automatiquement.
1. Pour **Type de clé**, sélectionnez **RSA**.
1. Pour **Utilisation de la clé**, sélectionnez **Signature**.
1. Sélectionnez **Create** (Créer).

### <a name="create-the-encryption-key"></a>Créer la clé de chiffrement

1. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
1. Pour **Options**, choisissez `Generate`.
1. Dans **Nom**, entrez `TokenEncryptionKeyContainer`. Il est possible que le préfixe `B2C_1A`_ soit ajouté automatiquement.
1. Pour **Type de clé**, sélectionnez **RSA**.
1. Pour **Utilisation de la clé**, sélectionnez **Chiffrement**.
1. Sélectionnez **Create** (Créer).


## <a name="register-identity-experience-framework-applications"></a>Inscrire les applications de l’infrastructure d’expérience d’identité

Azure AD B2C exige que vous inscriviez deux applications qu’il utilise pour inscrire et connecter des utilisateurs avec des comptes locaux : *IdentityExperienceFramework*, API web, et *ProxyIdentityExperienceFramework*, application native avec autorisation déléguée à l’application IdentityExperienceFramework. Vos utilisateurs peuvent s’inscrire avec une adresse e-mail ou un nom d’utilisateur et un mot de passe pour accéder à vos applications inscrites par le locataire, ce qui crée un « compte local ». Les comptes locaux existent uniquement dans votre locataires Azure AD B2C.

Vous ne devez inscrire ces deux applications dans votre locataire Azure AD B2C qu’une seule fois.

### <a name="register-the-identityexperienceframework-application"></a>Inscrire l’application IdentityExperienceFramework

Pour inscrire une application pour votre locataire Azure AD B2C, vous pouvez utiliser la fonctionnalité d’**inscriptions d’applications**.

1. Sélectionnez **Inscriptions d’applications**, puis **Nouvelle inscription**.
1. Pour **Nom**, entrez `IdentityExperienceFramework`.
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans cet annuaire organisationnel uniquement**.
1. Sous **URI de redirection**, sélectionnez **Web**, puis entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, où `your-tenant-name` est le nom de domaine de votre locataires Azure AD B2C.
1. Sous **Autorisations**, activez la case à cocher *Accorder le consentement administrateur aux autorisations openid et offline_access*.
1. Sélectionnez **Inscription**.
1. Enregistrez l’**ID d’application (client)** pour l’utiliser dans une étape ultérieure.

Ensuite, exposez l’API en ajoutant une étendue :

1. Dans le menu de gauche, sous **Gérer**, sélectionnez **Exposer une API**.
1. Sélectionnez **Ajouter une étendue**, puis sélectionnez **Enregistrer et continuer** pour accepter l’URI d’ID d’application par défaut.
1. Entrez les valeurs suivantes pour créer une étendue qui autorise l’exécution de stratégie personnalisée dans votre locataires Azure AD B2C :
    * **Nom de l’étendue** : `user_impersonation`
    * **Nom d’affichage du consentement administrateur** : `Access IdentityExperienceFramework`
    * **Description du consentement de l’administrateur** : `Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. Sélectionnez **Ajouter une étendue**

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>Inscrire l’application ProxyIdentityExperienceFramework

1. Sélectionnez **Inscriptions d’applications**, puis **Nouvelle inscription**.
1. Pour **Nom**, entrez `ProxyIdentityExperienceFramework`.
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans cet annuaire organisationnel uniquement**.
1. Sous **URI de redirection**, utilisez la liste déroulante pour sélectionner **client public/natif (Bureau et mobile)** .
1. Pour **URI de redirection**, entrez `myapp://auth`.
1. Sous **Autorisations**, activez la case à cocher *Accorder le consentement administrateur aux autorisations openid et offline_access*.
1. Sélectionnez **Inscription**.
1. Enregistrez l’**ID d’application (client)** pour l’utiliser dans une étape ultérieure.

Ensuite, spécifiez que l’application doit être traitée comme un client public :

1. Dans le menu de gauche, sous **Gérer**, sélectionnez **Authentification**.
1. Sous **Paramètres avancés**, dans la section **Autoriser les flux de clients publics**, définissez **Activer les flux mobiles et de bureau suivants** sur **Oui**. 
1. Sélectionnez **Enregistrer**.
1. Assurez-vous que **« allowPublicClient » : true** est défini dans le manifeste de l’application :
    1. Dans le menu de gauche, sous **Gérer**, sélectionnez **Manifeste** pour ouvrir le manifeste d’application.
    1. Recherchez la clé **allowPublicClient** et assurez-vous que sa valeur est définie sur **true**.

Maintenant, accordez des autorisations à l’étendue de l’API que vous avez exposée précédemment dans l’inscription  *IdentityExperienceFramework* :

1. Dans le menu de gauche, sous **Gérer**, sélectionnez **Autorisations d’API**.
1. Sous **Autorisations configurées**, sélectionnez **Ajouter une autorisation**.
1. Sélectionnez l’onglet **Mes API**, puis sélectionnez l’application **IdentityExperienceFramework**.
1. Sous **Autorisation**, sélectionnez l’étendue **user_impersonation** que vous avez définie précédemment.
1. Sélectionnez **Ajouter des autorisations**. Comme vous l’indiquent les instructions, patientez quelques minutes avant de passer à l’étape suivante.
1. Sélectionnez **Accorder le consentement de l’administrateur pour *<nom de votre locataire)>***.
1. Sélectionnez **Oui**.
1. Sélectionnez **Actualiser**, puis vérifiez que « Accordé pour ... » apparaît sous **État** pour l’étendue.

* * *

## <a name="custom-policy-starter-pack"></a>Pack de démarrage de stratégie personnalisée

Les stratégies personnalisées sont un ensemble de fichiers XML que vous téléchargez vers votre locataire Azure AD B2C pour définir des profils techniques et des parcours utilisateur. Nous fournissons des packs de démarrage avec plusieurs stratégies prédéfinies pour vous permettre de vous familiariser rapidement. Chacun de ces packs de démarrage contient le plus petit nombre possible de profils techniques et de parcours utilisateur nécessaires pour réaliser les scénarios décrits :

- **LocalAccounts** : vous permet d’utiliser uniquement des comptes locaux.
- **SocialAccounts** : vous permet d’utiliser uniquement des comptes sociaux (ou fédérés).
- **SocialAndLocalAccounts** : vous permet d’utiliser à la fois des comptes locaux et des comptes sociaux.
- **SocialAndLocalAccountsWithMFA** : active les options social, local et authentification multifacteur.

Chaque pack de démarrage contient :

- **Ficher de base** : il n’y a que peu de modifications à apporter à la base. Exemple : *TrustFrameworkBase.xml*
- **Fichier de localisation** : c’est sur ce fichier que sont apportés les changements de localisation. Exemple : *TrustFrameworkLocalization.xml*
- **Fichier d’extension** : c’est sur ce fichier que portent la plupart des modifications de la configuration. Exemple : *TrustFrameworkExtensions.xml*
- **Fichiers de partie de confiance** : sont des fichiers propres à chaque tâche, appelés par l’application. Exemples : *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*

Dans cet article, vous allez modifier les fichiers de stratégie personnalisée XML dans le pack de démarrage **SocialAndLocalAccounts**. Si vous avez besoin d’un éditeur XML, essayez [Visual Studio Code](https://code.visualstudio.com/download), un éditeur multiplateforme léger.

### <a name="get-the-starter-pack"></a>Obtenez le pack de démarrage

Récupérez les packs de démarrage de stratégie personnalisés à partir de GitHub, puis mettez à jour les fichiers XML dans le pack de démarrage SocialAndLocalAccounts avec votre nom de locataire Azure AD B2C.

1. [Téléchargez le fichier .zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) ou clonez le référentiel :

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. Dans tous les fichiers du répertoire **SocialAndLocalAccounts**, remplacez la chaîne `yourtenant` par le nom de votre locataire Azure AD B2C.

    Par exemple, si le nom de votre locataire B2C est *contosotenant*, toutes les instances de `yourtenant.onmicrosoft.com` deviennent `contosotenant.onmicrosoft.com`.

### <a name="add-application-ids-to-the-custom-policy"></a>Ajouter des ID d’applications à la stratégie personnalisée

Ajoutez les ID d’applications au fichier d’extensions *TrustFrameworkExtensions.xml*.

1. Ouvrez `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** et trouvez l’élément `<TechnicalProfile Id="login-NonInteractive">`.
1. Remplacez les deux instances de `IdentityExperienceFrameworkAppId` par l’ID de l’application IdentityExperienceFramework que vous avez créée précédemment.
1. Remplacez les deux instances de `ProxyIdentityExperienceFrameworkAppId` par l’ID de l’application ProxyIdentityExperienceFramework que vous avez créée précédemment.
1. Enregistrez le fichier .

## <a name="add-facebook-as-an-identity-provider"></a>Ajouter Facebook en tant que fournisseur d’identité

Le pack de démarrage **SocialAndLocalAccounts** comprend la connexion au réseau social Facebook. Facebook n’est *pas* requis pour l’utilisation de stratégies personnalisées, mais nous l’utilisons ici pour illustrer comment vous pouvez activer la connexion fédérée aux réseaux sociaux dans une stratégie personnalisée.

### <a name="create-facebook-application"></a>Créer une application Facebook

Suivez les étapes décrites dans [Créer une application Facebook](identity-provider-facebook.md#create-a-facebook-application) pour obtenir un *ID d’application* et un *secret d’application* Facebook. Ignorez les prérequis et le reste des étapes de l’article [Configurer l’inscription et se connecter avec un compte Facebook](identity-provider-facebook.md). 

### <a name="create-the-facebook-key"></a>Créer la clé Facebook

Ajoutez le [secret d’application](identity-provider-facebook.md) de votre application Facebook en tant que clé de stratégie. Vous pouvez utiliser le secret d’application de l’application que vous avez créée dans le cadre des conditions préalables de cet article.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Dans la Portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Dans la page de vue d’ensemble, sous **Stratégies**, sélectionnez **Identity Experience Framework**.
1. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
1. Pour **Options**, choisissez `Manual`.
1. Pour **Nom**, entrez `FacebookSecret`. Il est possible que le préfixe `B2C_1A_` soit ajouté automatiquement.
1. Dans **Secret**, entrez le *Secret d’application* de votre application Facebook depuis developers.facebook.com. Cette valeur correspond au secret, pas à l’ID d’application.
1. Pour **Utilisation de la clé**, sélectionnez **Signature**.
1. Sélectionnez **Create** (Créer).

### <a name="update-trustframeworkextensionsxml-in-custom-policy-starter-pack"></a>Mettre à jour TrustFrameworkExtensions.xml dans le pack de démarrage de la stratégie personnalisée
Dans le fichier `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** , remplacez la valeur de `client_id` par l’ID d’application Facebook et enregistrez les changements.

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```


## <a name="upload-the-policies"></a>Charger les stratégies

1. Sélectionnez l’élément de menu **Identity Experience Framework** dans votre locataire B2C du portail Azure.
1. Sélectionnez **Charger une stratégie personnalisée**.
1. Dans cet ordre, téléchargez les fichiers de stratégie :
    1. *TrustFrameworkBase.xml*
    2. *TrustFrameworkLocalization.xml*
    3. *TrustFrameworkExtensions.xml*
    4. *SignUpOrSignin.xml*
    5. *ProfileEdit.xml*
    6. *PasswordReset.xml*

Lorsque vous chargez les fichiers, Azure ajoute le préfixe `B2C_1A_` à chacun d’eux.

> [!TIP]
> Si votre éditeur XML prend en charge la validation, validez la conformité des fichiers au schéma XML `TrustFrameworkPolicy_0.3.0.0.xsd`, qui se trouve dans le dossier racine du pack de démarrage. La validation du schéma XML identifie les erreurs avant le chargement.

## <a name="test-the-custom-policy"></a>Tester la stratégie personnalisée

1. Sous **Stratégies personnalisées**, sélectionnez **B2C_1A_signup_signin**.
1. Pour **sélectionner une application** dans la page de présentation de la stratégie personnalisée, sélectionnez l’application web nommée *webapp1* que vous avez inscrite précédemment.
1. Assurez-vous que l’**URL de réponse** soit `https://jwt.ms`.
1. Sélectionnez **Exécuter maintenant**.
1. Inscrivez-vous au moyen d’une adresse e-mail.
1. Sélectionnez **Exécuter maintenant** à nouveau.
1. Connectez-vous avec le même compte pour vérifier que votre configuration est correcte.
1. Sélectionnez **Exécuter maintenant** une nouvelle fois, puis sélectionnez Facebook pour vous connecter avec Facebook et tester la stratégie personnalisée.
::: zone-end

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un flux d’utilisateur d’inscription et de connexion
> * Créer un flux d’utilisateur de modification de profil
> * Créer un flux d’utilisateur de réinitialisation du mot de passe

Ensuite, découvrez comment utiliser Azure AD B2C pour connecter et inscrire des utilisateurs dans une application. Suivez les échantillons d’applications liés ci-dessous :

- [Configurer un échantillon d’application web ASP.NET Core](configure-authentication-sample-web-app.md)
- [Configurer un échantillon d’application web ASP.NET Core qui appelle une API web](configure-authentication-sample-web-app-with-api.md)
- [Configurer l’authentification dans un échantillon d’application web Python](configure-authentication-sample-python-web-app.md)
- [Configurer un échantillon d’application monopage (SPA)](configure-authentication-sample-spa-app.md)
- [Configurer un échantillon d’application monopage angulaire](configure-authentication-sample-angular-spa-app.md)
- [Configurer un échantillon d’application mobile Android](configure-authentication-sample-android-app.md)
- [Configurer un échantillon d’application mobile iOS](configure-authentication-sample-ios-app.md)
- [Configurer l’authentification dans un échantillon d’application de bureau WPF](configure-authentication-sample-wpf-desktop-app.md)
- [Activer l’authentification dans votre API web](enable-authentication-web-api.md)
- [Configurer une application SAML](saml-service-provider.md) 

Vous pouvez également en savoir plus dans la [série de présentation approfondie de l’architecture Azure AD B2C](https://www.youtube.com/playlist?list=PLOPotgzC07IKXXCTZcrpuLWbVe3y51kfm).
