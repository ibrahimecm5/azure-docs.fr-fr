---
title: Personnaliser l’interface utilisateur avec des modèles HTML
titleSuffix: Azure AD B2C
description: Découvrez comment personnaliser l’interface utilisateur avec des modèles HTML pour vos applications qui utilisent Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/14/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f662b1a1a47dba27457c4c5754d600bb920c7b2f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130222663"
---
# <a name="customize-the-user-interface-with-html-templates-in-azure-active-directory-b2c"></a>Personnaliser l’interface utilisateur avec des modèles HTML dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

La personnalisation de l’interface utilisateur qu’Azure Active Directory B2C (Azure AD B2C) présente à vos clients permet d’offrir une expérience utilisateur fluide au sein de votre application. Ces expériences incluent la modification du profil, l’inscription, la connexion et la réinitialisation du mot de passe. Cet article présente les méthodes de personnalisation de l’interface utilisateur. 

> [!TIP]
> Si vous souhaitez modifier uniquement le logo de la bannière, l’image et la couleur d’arrière-plan de vos pages de flux utilisateur, vous pouvez essayer la fonctionnalité [Personnalisation de la société](customize-ui.md).

## <a name="custom-html-and-css-overview"></a>Vue d’ensemble des fichiers HTML et CSS personnalisés

Azure AD B2C exécute le code dans le navigateur de votre client à l’aide de [Partage des ressources cross-origin (CORS)](https://www.w3.org/TR/cors/). Au moment de l’exécution, le contenu est chargé depuis une URL que vous spécifiez dans votre flux utilisateur ou stratégie personnalisée. Chaque page de l’expérience utilisateur charge son contenu à partir de l’URL que vous spécifiez pour cette page. Une fois le contenu chargé à partir de votre URL, il est fusionné avec un fragment HTML inséré par Azure AD B2C, puis la page est présentée à votre client.

![Marge du contenu de page personnalisé](./media/customize-ui-with-html/html-content-merging.png)

### <a name="custom-html-page-content"></a>Contenu de page HTML personnalisé

Créez une page HTML avec votre propre personnalisation pour servir le contenu de votre page personnalisée. Cette page peut être une page `*.html` statique ou une page dynamique comme .NET, Node.js ou PHP.

Le contenu de votre page personnalisée peut contenir tous les éléments HTML, y compris CSS et JavaScript, mais ne peut pas inclure d’éléments non sécurisés tels que des IFrames. Le seul élément requis dans votre page HTML est un élément div dont `id` est défini sur `api`, `<div id="api"></div>` par exemple.

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Product Brand Name</title>
</head>
<body>
    <div id="api"></div>
</body>
</html>
```

#### <a name="customize-the-default-azure-ad-b2c-pages"></a>Personnaliser les pages Azure AD B2C par défaut

Au lieu de créer le contenu de votre page personnalisée à partir de zéro, vous pouvez personnaliser le contenu de la page par défaut d’Azure AD B2C.

Le tableau suivant répertorie le contenu de page par défaut fourni par Azure AD B2C. Téléchargez les fichiers et utilisez-les comme point de départ pour créer vos propres pages personnalisées.

| Page par défaut | Description | ID de définition du contenu<br/>(stratégie personnalisée uniquement) |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Page d’erreur**. Cette page s’affiche lorsqu’une exception ou une erreur est rencontrée. | *api.error* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Page autodéclarée**. Utilisez ce fichier en tant que contenu de page personnalisé pour une page d’inscription à un compte de réseau social, une page d’inscription à un compte local, une page de connexion à un compte local, une page de réinitialisation du mot de passe et bien plus encore. Le formulaire peut contenir différentes commandes de saisie telles qu’une zone de saisie de texte, une zone de saisie de mot de passe, un bouton radio, des zones de liste déroulante à sélection unique ou des cases à sélection multiples. | *api.localaccountsignin*, *api.localaccountsignup*, *api.localaccountpasswordreset*, *api.selfasserted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Page d’authentification multi-facteur**. Cette page permet aux utilisateurs de vérifier leurs numéros de téléphone (par voie textuelle ou vocale) au cours de l’inscription ou de la connexion. | *api.phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Page de mise à jour de profil**. Cette page contient un formulaire auquel l’utilisateur peut accéder pour mettre à jour son profil. Cette page est similaire à la page d’inscription au compte de réseau social, à l’exception des champs de saisie de mot de passe. | *api.selfasserted.profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Page de connexion ou d’inscription unifiée**. Cette page gère le processus d’inscription et de connexion des utilisateurs. Les utilisateurs peuvent utiliser des fournisseurs d’identité d’entreprise, des fournisseurs d’identité de réseaux sociaux tels que Facebook et Google + ou des comptes locaux. | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>Hébergement du contenu de la page

Lorsque vous utilisez vos propres fichiers HTML et CSS pour personnaliser l’interface utilisateur, hébergez votre contenu d’interface utilisateur sur n’importe quel point de terminaison HTTPS disponible publiquement qui prend en charge CORS. Par exemple, [Stockage Blob Azure](../storage/blobs/storage-blobs-introduction.md), [Azure App Services](../app-service/index.yml), serveurs web, réseaux de distribution de contenu, AWS S3 ou systèmes de partage de fichiers.

## <a name="guidelines-for-using-custom-page-content"></a>Instructions pour l’utilisation de contenu de page personnalisé

- Utilisez une URL absolue lorsque vous incluez des ressources externes telles que des fichiers multimédias, CSS et JavaScript dans votre fichier HTML.
- Avec la [version 1.2.0 de la mise en page](page-layout.md) et les suivantes, vous pouvez ajouter l’attribut `data-preload="true"` dans vos balises HTML pour contrôler l’ordre de chargement des fichiers CSS et JavaScript. Grâce à `data-preload="true"`, la page est construite avant d’être affichée à l’utilisateur. Cet attribut permet d’éviter le « scintillement » de la page en préchargeant le fichier CSS, sans que le code HTML non stylisé soit affiché à l’utilisateur. L’extrait de code HTML suivant montre l’utilisation de la balise `data-preload`.

  ```html
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- Nous vous recommandons de commencer par le contenu de la page par défaut et de créer par-dessus.
- Vous pouvez [inclure du JavaScript](javascript-and-page-layout.md) dans votre contenu personnalisé.
- Les versions de navigateur prises en charge sont les suivantes :
  - Internet Explorer 11, 10 et Microsoft Edge
  - Prise en charge limitée pour Internet Explorer 9 et 8
  - Google Chrome 42.0 et ultérieur
  - Mozilla Firefox 38.0 et ultérieur
  - Safari pour iOS et macOS, version 12 et versions ultérieures
- En raison des restrictions de sécurité, Azure AD B2C ne prend pas en charge les éléments HTML `frame`, `iframe` ni `form`.

## <a name="localize-content"></a>Localiser le contenu

Vous localisez votre contenu HTML en activant la [personnalisation de la langue](language-customization.md) dans votre locataire Azure AD B2C. L’activation de cette fonctionnalité permet à Azure AD B2C de définir l’attribut de langage de page HTML et de passer le paramètre OpenID Connecter `ui_locales` à votre point de terminaison.

#### <a name="single-template-approach"></a>Approche à un seul modèle

Pendant le chargement de la page, Azure AD B2C définit l’attribut de langage de page HTML avec la langue actuelle. Par exemple : `<html lang="en">`. Pour afficher différents styles selon la langue actuelle, utilisez le sélecteur `:lang` CSS avec votre définition CSS.

L'exemple suivant définit les classes suivantes :

* `imprint-en` -Utilisé lorsque la langue actuelle est l’anglais.
* `imprint-de` -Utilisé lorsque la langue actuelle est l’allemand.
* `imprint` -Classe par défaut utilisée lorsque la langue actuelle n’est ni l’anglais ni l’allemand.

```css
.imprint-en:lang(en),
.imprint-de:lang(de) {
    display: inherit !important;
}
.imprint {
    display: none;
}
```

Les éléments HTML suivants seront affichés ou masqués en fonction de la langue de la page :

```html
<a class="imprint imprint-en" href="Link EN">Imprint</a>
<a class="imprint imprint-de" href="Link DE">Impressum</a>
```

#### <a name="multi-template-approach"></a>Approche à plusieurs modèles

La fonctionnalité de personnalisation de la langue permet à Azure AD B2C de transmettre le paramètre Open ID Connect `ui_locales` à votre point de terminaison. Votre serveur de contenu peut utiliser ce paramètre pour fournir des pages HTML propres à la langue.

> [!NOTE]
> Azure AD B2C ne transmet pas les paramètres OpenID Connect, tel que `ui_locales` aux [pages Exception](page-layout.md#exception-page-globalexception).

Le contenu peut être extrait de différents emplacements en fonction des paramètres régionaux utilisés. Dans votre point de terminaison avec CORS activé, vous configurez une structure de dossiers pour héberger du contenu pour des langues spécifiques. Vous devez appeler celui qui convient si vous utilisez la valeur générique `{Culture:RFC5646}`.

Par exemple, votre URI de page personnalisée peut ressembler à ceci :

```http
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

Vous pouvez charger la page en français en extrayant le contenu à partir de :

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="custom-page-content-walkthrough"></a>Procédure pas à pas pour le contenu de page personnalisé

Voici une vue d’ensemble du processus :

1. Préparez un emplacement pour héberger le contenu de votre page personnalisée (un point de terminaison HTTPS accessible publiquement et compatible avec CORS).
1. Téléchargez et personnalisez un fichier de contenu de page par défaut, par exemple `unified.html`.
1. Publiez le contenu de votre page personnalisée sur votre point de terminaison HTTPS disponible publiquement.
1. Définir un partage de ressources cross-origin (CORS) pour votre application web.
1. Pointez votre stratégie vers votre URI de contenu de stratégie personnalisé.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

### <a name="1-create-your-html-content"></a>1. Créer votre contenu HTML

Créez un contenu de page personnalisé dont le titre intègre le nom de la marque de votre produit.

1. Copiez l’extrait de code HTML suivant. Il s’agit de code HTML5 bien formé comportant un élément vide appelé *\<div id="api"\>\</div\>* , situé dans les balises *\<body\>* . Cet élément indique l’endroit où le contenu Azure AD B2C doit être inséré.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. Collez l’extrait copié dans un éditeur de texte.
1. Utilisez les feuilles de style en cascade pour donner du style aux éléments d’interface utilisateur insérés par Azure AD B2C dans votre page. L’exemple suivant montre un fichier CSS simple qui inclut également des paramètres pour les éléments HTML d’inscription injectés :

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

1.  Enregistrez le fichier sous *customize-ui.html*.

> [!NOTE]
> Les éléments de formulaire HTML seront supprimés à cause des restrictions de sécurité si vous utilisez login.microsoftonline.com. Si vous souhaitez utiliser des éléments de formulaire HTML dans votre contenu HTML personnalisé, [utilisez b2clogin.com](b2clogin.md).

### <a name="2-create-an-azure-blob-storage-account"></a>2. Créer un compte de stockage d’objets blob Azure

Dans cet article, nous utilisons le stockage Blob Azure pour héberger notre contenu. Vous pouvez choisir d’héberger votre contenu sur un serveur web, mais vous devez [activer CORS sur votre serveur web](https://enable-cors.org/server.html).

> [!NOTE]
> Dans un locataire Azure AD B2C, vous ne pouvez pas approvisionner le stockage d’objets Blob. Vous devez créer cette ressource dans votre locataire Azure AD.

Pour héberger votre contenu HTML dans Stockage Blob, effectuez les étapes suivantes :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à bien utiliser le répertoire qui contient votre locataire Azure AD et qui dispose d’un abonnement : 
    1. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
    1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire Azure AD dans la liste Nom de répertoire, puis sélectionnez **Basculer**.
1. Dans le Portail Azure, recherchez et sélectionnez **Comptes de stockage**
1. Sélectionnez **+ Créer**.
1. Sélectionnez un **abonnement** pour votre compte de stockage.
1. Créez un **Groupe de ressources** ou sélectionnez-en un.
1. Entrez un **Nom de compte de stockage** pour votre compte de stockage.
1. Sélectionnez la **Région** géographique de votre compte de stockage.
1. Le champ **Performances** peut conserver la valeur **Standard**.
1. La **redondance** peut rester **un stockage géoredondant (GRS)**
1. Sélectionnez **Examiner + créer** et patientez quelques secondes pour que Azure AD exécute une validation. 
1. Sélectionnez **Créer** pour créer le compte de stockage. Une fois le déploiement terminé, la page Compte de stockage s’ouvre automatiquement, ou sélectionnez **Accéder à la ressource**.
#### <a name="21-create-a-container"></a>2.1 Créer un conteneur

Pour créer un conteneur public dans Stockage Blob, effectuez les étapes suivantes :

1. Sous **Stockage des données** dans le menu de gauche, sélectionnez **Conteneurs**.
1. Sélectionnez **+ Conteneur**.
1. Pour **Nom**, entrez *root*. Il peut s’agir du nom de votre choix, par exemple *contoso*, mais nous utilisons *root* dans cet exemple par souci de simplicité.
1. Pour **Niveau d’accès public**, sélectionnez **Objet blob**.
1. Sélectionnez **Créer** pour créer le conteneur.
1. Sélectionnez **root** pour ouvrir le nouveau conteneur.

#### <a name="22-upload-your-custom-page-content-files"></a>2.2 Charger vos fichiers de contenu de page personnalisé

1. Sélectionnez **Télécharger**.
1. Sélectionnez l’icône de dossier à côté du champ **Sélectionner un fichier**.
1. Accédez au fichier **customize-ui.html** que vous avez précédemment créé dans la section Personnalisation de l’interface utilisateur de la page, et sélectionnez-le.
1. Si vous souhaitez le charger dans un sous-dossier, développez **Avancé** et entrez un nom de dossier dans **Charger dans le dossier**.
1. Sélectionnez **Télécharger**.
1. Sélectionnez l’objet blob **customize-ui.html** que vous venez de charger.
1. À droite de la zone de texte **URL**, sélectionnez l’icône **Copier dans le Presse-papiers** pour copier l’URL dans le Presse-papiers.
1. Dans le navigateur web, accédez à l’URL que vous avez copiée pour vérifier que l’objet blob que vous avez chargé est accessible. S’il est inaccessible, par exemple si vous rencontrez une erreur `ResourceNotFound`, assurez-vous que le type d’accès du conteneur est défini sur **objet blob**.

### <a name="3-configure-cors"></a>3. Configuration de CORS

Configurez Stockage Blob pour le Partage des ressources cross-origin en effectuant les étapes suivantes :

1. Accédez à votre compte de stockage. 
1. Dans le menu de gauche, sous **Paramètres**, sélectionnez **Partage des ressources (CORS)** .
1. Pour **Origines autorisées**, entrez `https://your-tenant-name.b2clogin.com`. Remplacez `your-tenant-name` par le nom de votre locataire Azure AD B2C. Par exemple : `https://fabrikam.b2clogin.com`. Utilisez des minuscules quand vous entrez le nom de votre locataire.
1. Pour **Méthodes autorisées**, sélectionnez `GET` et `OPTIONS`.
1. Pour **En-têtes autorisés**, saisissez un astérisque (*).
1. Pour **En-têtes exposés**, saisissez un astérisque (*).
1. Pour **Âge maximal**, tapez 200.
1. En haut de la page, sélectionnez **Enregistrer**.

#### <a name="31-test-cors"></a>3.1 Tester CORS

Vérifiez que vous êtes prêt en effectuant les étapes suivantes :

1. Répétez l’étape Configuration de CORS. Dans **Origines autorisées**, entrez `https://www.test-cors.org`.
1. Accédez à [www.test-cors.org](https://www.test-cors.org/). 
1. Dans la zone **URL distante**, collez l’URL de votre fichier HTML. Par exemple : `https://your-account.blob.core.windows.net/root/azure-ad-b2c/unified.html`
1. Sélectionnez **Envoyer une demande**.
    Le résultat devrait être `XHR status: 200`. 
    Si vous recevez une erreur, assurez-vous que vos paramètres CORS sont corrects. Vous serez peut-être amené à vider le cache de votre navigateur ou à ouvrir une fenêtre de navigation privée, en appuyant sur Ctrl+Maj+P.

En savoir plus sur [la création et la gestion des comptes de stockage Azure](../storage/common/storage-account-create.md).

::: zone pivot="b2c-user-flow"

### <a name="4-update-the-user-flow"></a>4. Mettre à jour le flux d'utilisateurs

1. Veillez à bien utiliser le répertoire qui contient votre locataire Azure AD B2C : 
    1. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
    1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire Azure AD B2C dans la liste de noms de répertoires, puis sélectionnez **Basculer**.
1. Dans le portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Dans le menu de gauche, sélectionnez **Flux d’utilisateurs**, puis le flux utilisateur *B2C_1_signupsignin1*.
1. Sélectionnez **Mises en page**, puis sous **Page unifiée d'inscription ou de connexion**, sélectionnez **Oui** pour **Utiliser un contenu de page personnalisé**.
1. Dans **URI la page personnalisée**, entrez l'URI du fichier *custom-ui.html* notée précédemment.
1. En haut de la page, sélectionnez **Enregistrer**.

### <a name="5-test-the-user-flow"></a>5. Tester le flux utilisateur

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d'utilisateurs** et choisissez le flux d'utilisateurs *B2C_1_signupsignin1*.
1. En haut de la page, sélectionnez **Exécuter le flux d’utilisateur**.
1. Dans le volet de droite, sélectionnez le bouton **Exécuter le flux d’utilisateur**.

Vous devez voir une page semblable à l’exemple suivant avec les éléments centrés conformément au fichier CSS que vous avez créé :

![Navigateur web présentant une page d’inscription ou de connexion avec des éléments d’interface utilisateur personnalisés](./media/customize-ui-with-html/run-now.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="4-modify-the-extensions-file"></a>4. Modifier le fichier d’extensions

Pour configurer la personnalisation de l’interface utilisateur, copiez l’élément **ContentDefinition** et ses éléments enfants du fichier de base vers le fichier d’extension.

1. Ouvrez le fichier de base de votre stratégie. Par exemple <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em>. Ce fichier de base est un des fichiers de stratégie inclus dans le pack de démarrage des stratégies personnalisées, que vous avez dû obtenir en suivant la rubrique prérequise [Bien démarrer avec les stratégies personnalisées](./tutorial-create-user-flows.md?pivots=b2c-custom-policy).
1. Recherchez et copiez le contenu entier de l’élément **ContentDefinitions**.
1. Ouvrez le fichier d’extension. Par exemple, *TrustFrameworkExtensions.xml*. Recherchez l’élément **BuildingBlocks**. Si l’élément n’existe pas, ajoutez-le.
1. Collez le contenu entier de l’élément **ContentDefinitions** que vous avez copié en tant qu’enfant de l’élément **BuildingBlocks**.
1. Recherchez l’élément **ContentDefinition** contenant `Id="api.signuporsignin"` dans le code XML que vous avez copié.
1. Remplacez la valeur de **LoadUri** par l’URL du fichier HTML que vous avez chargé dans le stockage. Par exemple : `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Votre stratégie personnalisée doit se présenter comme l’extrait de code suivant :

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Enregistrez le fichier d’extensions.

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. Téléchargez et testez votre stratégie personnalisée mise à jour

#### <a name="51-upload-the-custom-policy"></a>5.1 Charger la stratégie personnalisée

1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Recherchez et sélectionnez **Azure AD B2C**.
1. Sous **Stratégies**, sélectionnez **Identity Experience Framework**.
1. Sélectionnez **Charger une stratégie personnalisée**.
1. Chargez le fichier d’extensions que vous avez modifié précédemment.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5.2 Tester la stratégie personnalisée en utilisant **Exécuter maintenant**

1. Sélectionnez la stratégie que vous avez téléchargée, puis sélectionnez **Exécuter maintenant**.
1. Vous devriez pouvoir vous inscrire avec une adresse e-mail.

## <a name="configure-dynamic-custom-page-content-uri"></a>Configurer l’URI du contenu de la page personnalisée dynamique

En utilisant des stratégies personnalisées Azure AD B2C, vous pouvez envoyer un paramètre dans le chemin de l’URL ou une chaîne de requête. En transmettant le paramètre à votre point de terminaison HTML, vous pouvez changer le contenu de la page de façon dynamique. Par exemple, vous pouvez changer l’image d’arrière-plan dans la page de connexion ou d’inscription Azure AD B2C en fonction d’un paramètre que vous transmettez depuis votre application web ou mobile. Le paramètre peut être n’importe quel [résolveur de revendications](claim-resolver-overview.md), comme l’ID d’application, l’ID de langue ou le paramètre de chaîne de requête personnalisée, comme `campaignId`.

### <a name="sending-query-string-parameters"></a>Envoi des paramètres de chaîne de requête

Pour envoyer des paramètres de chaîne de requête, dans la [stratégie de partie de confiance](relyingparty.md), ajoutez un élément `ContentDefinitionParameters` comme indiqué ci-dessous.

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        <Parameter Name="lang">{Culture:LanguageName}</Parameter>
        <Parameter Name="appId">{OIDC:ClientId}</Parameter>
    </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ...
</RelyingParty>
```

Dans votre définition de contenu, remplacez la valeur de `LoadUri` par `https://<app_name>.azurewebsites.net/home/unified`. La `ContentDefinition` de votre stratégie personnalisée doit se présenter comme l’extrait de code suivant :

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Quand Azure AD B2C charge la page, il fait un appel au point de terminaison de votre serveur web :

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>URI du contenu d’une page dynamique

Le contenu peut être extrait de différents emplacements en fonction des paramètres utilisés. Dans votre point de terminaison activé pour CORS, configurez une structure de dossiers pour héberger le contenu. Par exemple, vous pouvez organiser le contenu selon la structure suivante. *Dossier racine/dossier par langue/vos fichiers html*. Par exemple, votre URI de page personnalisée peut ressembler à ceci :

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C envoie le code ISO à deux lettres pour la langue, `fr` pour le français :

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

::: zone-end

## <a name="sample-templates"></a>Exemples de modèles

Vous trouverez ici des exemples de modèles pour la personnalisation de l’interface utilisateur :

```bash
git clone https://github.com/azure-ad-b2c/html-templates
```

Ce projet contient les modèles suivants :
- [Bleu océan](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/AzureBlue)
- [Gris ardoise](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/MSA)
- [Classique](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/classic)
- [Ressources de modèle](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/src)

Pour utiliser l’exemple :

1. Clonez le référentiel sur votre ordinateur local. Choisissez un dossier de modèles `/AzureBlue`, `/MSA` ou `/classic`.
1. Chargez tous les fichiers du dossier de modèles et du dossier `/src` dans le stockage Blob, comme décrit dans les sections précédentes.
1. Ensuite, ouvrez chaque fichier `\*.html` dans le dossier de modèles. Remplacez ensuite toutes les instances des URL `https://login.microsoftonline.com` par l’URL que vous avez chargée à l’étape 2. Par exemple :
    
    De :
    ```html
    https://login.microsoftonline.com/templates/src/fonts/segoeui.WOFF
    ```

    Par :
    ```html
    https://your-storage-account.blob.core.windows.net/your-container/templates/src/fonts/segoeui.WOFF
    ```
    
1. Enregistrez les fichiers `\*.html` et chargez-les dans le stockage blob.
1. Modifiez à présent la stratégie, en la faisant pointer vers votre fichier HTML, comme indiqué précédemment.
1. Si vous constatez qu’il manque des polices, des images ou des feuilles de style CSS, vérifiez vos références dans la stratégie d’extensions et les fichiers `\*.html`.

## <a name="use-company-branding-assets-in-custom-html"></a>Utiliser des ressources de marque de société dans du code HTML personnalisé

Pour utiliser des ressources de [Marque de société](customize-ui.md#configure-company-branding) dans du code HTML personnalisé, ajoutez les balises suivantes en dehors de la balise `<div id="api">`. La source de l’image est remplacée par celle de l’image d’arrière-plan et du logo de bannière.

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment activer le [code JavaScript côté client](javascript-and-page-layout.md).