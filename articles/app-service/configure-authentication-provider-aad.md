---
title: Configurer l’authentification Azure AD
description: Découvrez comment configurer l’authentification Azure Active Directory pour en faire le fournisseur d’identité de votre application App Service ou Azure Functions.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 10/26/2021
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: cc5761afe295bd9ef71c86cf6ffdafb8c0e847ff
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131435830"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>Configurer votre application App Service ou Azure Functions pour utiliser une connexion Azure AD

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cet article explique comment configurer l’authentification pour Azure App Service ou Azure Functions afin que votre application utilise la [Plateforme d’identités Microsoft](../active-directory/develop/v2-overview.md) (Azure AD) comme fournisseur d’authentification pour la connexion des utilisateurs.

La fonctionnalité d’authentification App Service peut créer automatiquement une inscription d’application avec la plateforme d’identités Microsoft. Vous pouvez également utiliser une inscription créée séparément par vous-même ou par un administrateur de répertoire.

- [Créer une inscription d’application automatiquement](#express)
- [Utiliser une inscription existante créée séparément](#advanced)

> [!NOTE]
> L’option permettant de créer une nouvelle inscription n’est pas disponible pour les clouds gouvernementaux. Au lieu de cela, [définissez une inscription séparément](#advanced).

## <a name="option-1-create-a-new-app-registration-automatically"></a><a name="express"> </a>Option 1 : Créer une inscription d’application automatiquement

Cette option est conçue pour simplifier l’authentification et ne nécessite que quelques clics.

1. Connectez-vous au [Portail Azure] et accédez à votre application.
1. Sélectionnez **Authentification** dans le menu de gauche. Cliquez sur **Add Identity Provider** (Ajouter un fournisseur d’identité).
1. Sélectionnez **Microsoft** dans la liste déroulante de fournisseurs d’identité. L’option permettant de créer une nouvelle inscription est sélectionnée par défaut. Vous pouvez modifier le nom de l’inscription ou les types de comptes pris en charge.

    Une clé secrète client est créée sous la forme d’un [paramètre d’application](./configure-common.md#configure-app-settings) d’emplacement fixe nommé `MICROSOFT_PROVIDER_AUTHENTICATION_SECRET`. Vous pouvez mettre à jour ce paramètre ultérieurement pour utiliser des [références Key Vault](./app-service-key-vault-references.md) si vous souhaitez gérer le secret dans Azure Key Vault.

1. S’il s’agit du premier fournisseur d’identité configuré pour l’application, vous êtes également invité à fournir une section **Paramètres d’authentification App Service**. Sinon, vous pouvez passer à l’étape suivante.
    
    Ces options déterminent la manière dont votre application répond aux requêtes non authentifiées, et les sélections par défaut redirigent toutes les requêtes de connexion à l’aide de ce nouveau fournisseur. Vous pouvez modifier ce comportement maintenant ou ajuster ces paramètres ultérieurement à partir de l’écran principal **Authentification** en choisissant **Modifier** en regard de **Paramètres d’authentification**. Pour en savoir plus sur ces options, consultez [Flux d’authentification](overview-authentication-authorization.md#authentication-flow).

1. (Facultatif) Cliquez sur **Suivant : Autorisations** et ajoutez les étendues nécessaires à l’application. Celles-ci sont ajoutées à l’inscription de l’application, mais vous pouvez également les modifier ultérieurement.
1. Cliquez sur **Add**.

Vous êtes maintenant prêt à utiliser la plateforme d’identités Microsoft pour l’authentification dans votre application. Le fournisseur est répertorié sur l’écran **Authentification**. À partir de là, vous pouvez modifier ou supprimer cette configuration de fournisseur.

Pour obtenir un exemple de configuration d’une connexion Azure AD pour une application web qui accède à Stockage Azure et à Microsoft Graph, consultez [ce tutoriel](scenario-secure-app-authentication-app-service.md).

## <a name="option-2-use-an-existing-registration-created-separately"></a><a name="advanced"> </a>Option 2 : Utiliser une inscription existante créée séparément

Vous pouvez également inscrire manuellement votre application pour la plateforme d’identités Microsoft, en personnalisant l’inscription et en configurant l’authentification App Service avec les détails de l’inscription. Cette option est utile, par exemple, si vous souhaitez utiliser une inscription d’application provenant d’un locataire Azure AD différent de celui où se trouve votre application.

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"></a>Créer une inscription d’application dans Azure AD pour votre application App Service

Tout d’abord, vous allez créer votre inscription d’application. Au cours de cette opération, recueillez les informations suivantes, car vous en aurez besoin plus tard pour configurer l’authentification dans l’application App Service :

- ID client
- ID client
- Clé secrète client (facultative)
- URI d’ID d’application

Pour inscrire l’application, effectuez les étapes suivantes :

1. Connectez-vous au [Azure portal], recherchez et sélectionnez **App Services**, puis sélectionnez votre application. Notez l’**URL** de votre application. Vous allez l’utiliser pour configurer l’inscription de votre application Azure Active Directory.
1. Dans le menu du portail, sélectionnez **Azure Active Directory**, accédez à l’onglet **Inscriptions d’applications**, puis sélectionnez **Nouvelle inscription**.
1. Sur la page **Inscrire une application**, entrez un **Nom** pour votre inscription d'application.
1. Sous **URI de redirection**, sélectionnez **Web**, puis entrez `<app-url>/.auth/login/aad/callback`. Par exemple : `https://contoso.azurewebsites.net/.auth/login/aad/callback`.
1. Sélectionnez **Inscription**.
1. Une fois l’inscription d’application créée, copiez l’**ID de l’application (client)** et l’**ID de l’annuaire (locataire)** pour plus tard.
1. Sélectionnez **Authentification**. Sous **Octroi implicite et flux hybrides**, activez **Jetons d’ID** pour autoriser les connexions utilisateur OpenID Connect à partir d’App Service.  Sélectionnez **Enregistrer**.
1. (Facultatif) Sélectionnez **Personnalisation**. Dans **URL de la page d’accueil**, entrez l’URL de votre application App Service, puis sélectionnez **Enregistrer**.
1. Sélectionnez **Exposer une API** , puis cliquez sur **Définir** en regard de « URI d’ID d’application ». Cette valeur identifie de façon unique l’application lorsqu’elle est utilisée en tant que ressource, ce qui permet de demander des jetons qui accordent l’accès. Il est utilisé comme préfixe pour les étendues que vous créez.

    Pour une application à locataire unique, vous pouvez utiliser la valeur par défaut, qui se présente sous la forme du formulaire `api://<application-client-id>`. Vous pouvez également spécifier un URI plus lisible comme `https://contoso.com/api` basé sur l’un des domaines vérifiés pour votre locataire. Pour une application mutualisée, vous devez fournir un URI personnalisé. Pour en savoir plus sur les formats acceptés pour les URI ID d’application, consultez les [informations de référence sur les recommandations relatives aux inscriptions d’applications](../active-directory/develop/security-best-practices-for-app-registration.md#appid-uri-configuration).

    Une fois que vous avez entré la valeur, cliquez sur **Enregistrer**.

1. sélectionner **Ajouter une étendue**.
   1. Dans **Ajouter une étendue**, l’**URI d’ID d’application** est la valeur que vous avez définie à l’étape précédente.  Sélectionnez **Enregistrer et continuer**.
   1. Dans **Nom de l’étendue**, entrez *user_impersonation*.
   1. Dans les zones de texte, entrez le nom et la description de l’étendue de consentement que vous voulez que les utilisateurs voient dans la page de consentement. Par exemple, entrez *Accéder à &lt;nom-application&gt;* .
   1. Sélectionnez **Ajouter une étendue**.
1. (Facultatif) Pour créer une clé secrète client, sélectionnez **Certificats et secrets** > **Clés secrètes client** > **Nouvelle clé secrète client**.  Entrez une description et une expiration, puis sélectionnez **Ajouter**. Copiez la valeur de la clé secrète client qui s'affiche sur la page. Elle ne s’affichera plus.
1. (Facultatif) Pour ajouter plusieurs **URL de réponse**, sélectionnez **Authentification**.

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>Activez Azure Active Directory dans votre Azure App Service

1. Connectez-vous au [Portail Azure] et accédez à votre application.
1. Sélectionnez **Authentification** dans le menu de gauche. Cliquez sur **Add Identity Provider** (Ajouter un fournisseur d’identité).
1. Sélectionnez **Microsoft** dans la liste déroulante de fournisseurs d’identité.
1. Pour le **Type d’inscription de l’application**, vous pouvez choisir de **Choisir une inscription d’application existante dans ce répertoire**, ce qui regroupera automatiquement les informations d’application nécessaires. Si votre inscription provient d’un autre locataire ou si vous n’avez pas l’autorisation d’afficher l’objet d’inscription, choisissez **Fournir les détails d’une inscription d’application existante**. Pour cette option, vous devez renseigner les détails de configuration suivants :

    |Champ|Description|
    |-|-|
    |ID d’application (client)| Utilisez l'**ID d’application (client)** de l’inscription de l’application. |
    |Clé secrète client| Utilisez la clé secrète client que vous avez générée lors de l’inscription de l’application. Avec une clé secrète client, le flux hybride est utilisé et App Service retourne les jetons d’accès et d’actualisation. Lorsque la clé secrète client n’est pas définie, le flux implicite est utilisé et seul un jeton d’ID est retourné. Ces jetons sont envoyés par le fournisseur et stockés dans le magasin de jetons EasyAuth.|
    |URL de l’émetteur| Utilisez `<authentication-endpoint>/<tenant-id>/v2.0`, puis remplacez *\<authentication-endpoint>* par le [point de terminaison d’authentification de votre environnement cloud](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) (par exemple, « https://login.microsoftonline.com  » pour Azure global), puis *\<tenant-id>* par l’**ID du répertoire (locataire)** dans lequel l’inscription de l’application a été créée. Cette valeur sert à rediriger les utilisateurs vers le bon locataire Azure AD, mais aussi à télécharger les métadonnées appropriées pour déterminer les clés de signature de jetons et la valeur de revendication de l’émetteur de jeton qui conviennent, par exemple. Pour les applications qui utilisent Azure AD v1 et pour les applications Azure Functions, omettez `/v2.0` dans l’URL.|
    |Audiences de jeton autorisées| S’il s’agit d’une application cloud ou serveur et que vous souhaitez autoriser les jetons d’authentification d’une application web, ajoutez l’**URI d’ID d’application** de l’application web ici. **L’ID client** configuré est *toujours* implicitement considéré comme une audience autorisée.|

    La clé secrète client est stockée sous la forme d’un [paramètre d’application](./configure-common.md#configure-app-settings) d’emplacement fixe nommé `MICROSOFT_PROVIDER_AUTHENTICATION_SECRET`. Vous pouvez mettre à jour ce paramètre ultérieurement pour utiliser des [références Key Vault](./app-service-key-vault-references.md) si vous souhaitez gérer le secret dans Azure Key Vault.

1. S’il s’agit du premier fournisseur d’identité configuré pour l’application, vous êtes également invité à fournir une section **Paramètres d’authentification App Service**. Sinon, vous pouvez passer à l’étape suivante.
    
    Ces options déterminent la manière dont votre application répond aux requêtes non authentifiées, et les sélections par défaut redirigent toutes les requêtes de connexion à l’aide de ce nouveau fournisseur. Vous pouvez modifier ce comportement maintenant ou ajuster ces paramètres ultérieurement à partir de l’écran principal **Authentification** en choisissant **Modifier** en regard de **Paramètres d’authentification**. Pour en savoir plus sur ces options, consultez [Flux d’authentification](overview-authentication-authorization.md#authentication-flow).

1. Cliquez sur **Add**.

Vous êtes maintenant prêt à utiliser la plateforme d’identités Microsoft pour l’authentification dans votre application. Le fournisseur est répertorié sur l’écran **Authentification**. À partir de là, vous pouvez modifier ou supprimer cette configuration de fournisseur.

## <a name="configure-client-apps-to-access-your-app-service"></a>Configurer des applications clientes pour qu’elles accèdent à votre application App Service

Dans la section précédente, vous avez inscrit votre application App Service ou Azure Functions pour authentifier les utilisateurs. Cette section explique comment inscrire des applications clientes ou démon natives afin qu’elles puissent demander l’accès aux API exposées par votre application App Service pour le compte d’utilisateurs ou pour elles-mêmes. Il n’est pas nécessaire d’effectuer les étapes de cette section si vous souhaitez uniquement authentifier les utilisateurs.

### <a name="native-client-application"></a>Application cliente native

Vous pouvez inscrire des clients natifs afin qu’ils puissent demander l’accès aux API de votre application App Service pour le compte d’un utilisateur connecté.

1. Dans le [Azure portal], sélectionnez **Active Directory** > **Inscriptions d’applications** > **Nouvelle inscription**.
1. Sur la page **Inscrire une application**, entrez un **Nom** pour votre inscription d'application.
1. Dans **URI de redirection**, sélectionnez **Client public (mobile et bureau)** et entrez l’URL `<app-url>/.auth/login/aad/callback`. Par exemple : `https://contoso.azurewebsites.net/.auth/login/aad/callback`.

    > [!NOTE]
    > Pour une application Microsoft Store, utilisez plutôt le [SID de package](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library#package-sid) en guise d’URI.
1. Sélectionnez **Create** (Créer).
1. Une fois l’inscription d’application créée, copiez la valeur de l’**ID d’application (client)** .
1. Sélectionnez **Autorisations des API** > **Ajouter une autorisation** > **Mes API**.
1. Sélectionnez l’inscription d'application que vous avez créée précédemment pour votre application App Service. Si celle-ci n’apparaît pas, vérifiez que vous avez ajouté l’étendue **user_impersonation** dans [Créer une inscription d’application dans Azure AD pour votre application App Service](#register).
1. Sous **Autorisations déléguées**, sélectionnez **user_impersonation**, puis **Ajouter des autorisations**.

Vous avez maintenant configuré une application cliente native qui peut demander l’accès à votre application App Service pour le compte d’un utilisateur.

### <a name="daemon-client-application-service-to-service-calls"></a>Application cliente démon (appels de service à service)

Votre application peut acquérir un jeton pour appeler une API web hébergée dans votre App Service ou application de fonction pour son propre compte (et non pour le compte d’un utilisateur). Ce scénario est utile pour les applications de démon non interactives qui effectuent des tâches sans utilisateur connecté. Il utilise l’octroi d’[informations d’identification du client](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) OAuth 2.0 standard.

1. Dans le [Azure portal], sélectionnez **Active Directory** > **Inscriptions d’applications** > **Nouvelle inscription**.
1. Dans la page **Inscrire une application**, entrez un **Nom** pour votre inscription d’application.
1. Pour une application démon, vous n’avez pas besoin d’un URI de redirection. Vous pouvez donc conserver cette zone vide.
1. Sélectionnez **Create** (Créer).
1. Une fois l’inscription d’application créée, copiez la valeur de l’**ID d’application (client)** .
1. Sélectionnez **Certificats & secrets** > **Nouvelle clé secrète client** > **Ajouter**. Copiez la valeur de la clé secrète client qui s'affiche sur la page. Elle ne s’affichera plus.

Vous pouvez maintenant [demander un jeton d’accès à l’aide de l’ID client et de la clé secrète client](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret) en définissant le paramètre `resource` sur l’**URI d’ID d’application** de l’application cible. Le jeton d’accès obtenu peut ensuite être présenté à l’application cible à l’aide de l’[en-tête d’autorisation OAuth 2.0](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#use-the-access-token-to-access-the-secured-resource) standard. La fonction Authentification/autorisation App Service valide et utilise le jeton comme d’habitude pour indiquer à présent que l’appelant (en l’occurrence, une application, pas un utilisateur) est authentifié.

À l’heure actuelle, cela permet à _n’importe quelle_ application cliente de votre locataire Azure AD de demander un jeton d’accès et de s’authentifier auprès de l’application cible. Si vous souhaitez également appliquer un _autorisation_ pour n’autoriser que certaines applications clientes, vous devez effectuer une configuration supplémentaire.

1. [Définissez un rôle d’application](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) dans le manifeste de l’inscription d’application représentant l’App Service ou l’application de fonction que vous souhaitez protéger.
1. Sur l’inscription de l’application représentant le client qui doit être autorisé, sélectionnez **Autorisations de l’API** > **Ajouter une autorisation** > **Mes API**.
1. Sélectionnez l’inscription d’application que vous avez créée précédemment. Si vous ne voyez pas l’inscription d’application, assurez-vous que vous avez [ajouté un rôle d’application](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md).
1. Sous **Autorisations d’application**, choisissez le rôle d’application que vous avez créé précédemment, puis sélectionnez **Ajouter des autorisations**.
1. Veillez à cliquer sur **Accorder un consentement administrateur** pour autoriser l’application cliente à demander l’autorisation.
1. Comme pour le scénario précédent (avant l’ajout de rôles), vous pouvez maintenant [demander un jeton d’accès](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret) pour la même `resource` cible, et le jeton d’accès inclut une revendication `roles` contenant les rôles d’application qui ont été autorisés pour l’application cliente.
1. Dans le code de l’App Service ou de l’application de fonction cibles, vous pouvez désormais vérifier que les rôles attendus sont présents dans le jeton (cela n’est pas effectué par la fonction Authentification/autorisation App Service). Pour plus d’informations, consultez la section [Revendications d’utilisateurs d’accès](configure-authentication-user-identities.md#access-user-claims-in-app-code).

Vous avez maintenant configuré une application cliente démon qui peut accéder à votre application App Service en utilisant sa propre identité.

## <a name="best-practices"></a>Meilleures pratiques

Quelle que soit la configuration que vous utilisez pour configurer l’authentification, les bonnes pratiques suivantes garantissent la sécurisation de votre locataire et de vos applications :

- Donnez à chaque application App Service ses propres autorisations et son propre consentement.
- Configurez chaque application App Service avec sa propre inscription.
- Évitez de partager des autorisations entre des environnements en utilisant des inscriptions d’application distinctes pour des emplacements de déploiement distincts. Dans le cadre des tests d’un nouveau code, cette pratique peut permettre d’éviter que des problèmes n’affectent l’application de production.

## <a name="next-steps"></a><a name="related-content"> </a>Étapes suivantes

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
* [Tutoriel : Authentifier et autoriser des utilisateurs dans une application Web qui accède à Azure Storage et à Microsoft Graph](scenario-secure-app-authentication-app-service.md)
* [Tutoriel : Authentifier et autoriser des utilisateurs de bout en bout dans Azure App Service](tutorial-auth-aad.md)
<!-- URLs. -->

[Azure portal]: https://portal.azure.com/
