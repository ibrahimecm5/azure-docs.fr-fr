---
title: Tutoriel pour configurer Azure Active Directory B2C avec Arkose Labs
titleSuffix: Azure AD B2C
description: Tutoriel pour configurer Azure Active Directory B2C avec Arkose Labs afin d’identifier les utilisateurs à risque et frauduleux
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/22/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: c6e31a9026e4598d12367d623cfb166663c6fe0a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044814"
---
# <a name="tutorial-configure-arkose-labs-with-azure-active-directory-b2c"></a>Tutoriel : configurer Arkose Labs avec Azure Active Directory B2C

Dans cet exemple de tutoriel, découvrez comment intégrer l’authentification Azure Active Directory (AD) B2C avec [Arkose Labs](https://www.arkoselabs.com/). Arkose Labs aide les organisations à lutter contre les attaques de bot, les attaques de prise de contrôle de compte et les ouvertures de comptes frauduleuses.  

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous disposez des éléments suivants :

- Un abonnement Azure. Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).

- Un [locataire Azure AD B2C](tutorial-create-tenant.md) lié à votre abonnement Azure.

- Un compte [Arkose Labs](https://www.arkoselabs.com/book-a-demo/).

## <a name="scenario-description"></a>Description du scénario

L’intégration d’Arkose Labs inclut les composants suivants :

- **Arkose Labs** : service relatif aux fraudes et abus pour la protection contre les bots et autres abus automatisés.

- **Flux d’utilisateur d’inscription Azure AD B2C** : l’expérience d’inscription qui utilisera le service Arkose Labs. Utilise le code HTML et JavaScript personnalisé, ainsi que les connecteurs d’API pour l’intégration au service Arkose Labs.

- **Fonctions Azure** : point de terminaison d’API hébergé par vous et compatible avec la fonctionnalité de connecteurs d’API. Cette API est chargée d’exécuter la validation côté serveur du jeton de session Arkose Labs.

Le diagramme suivant décrit comment Arkose Labs s’intègre à Azure AD B2C.

![Image montrant le diagramme d’architecture d’Arkose Labs](media/partner-arkose-labs/arkose-labs-architecture-diagram.png)

| Étape  | Description |
|---|---|
|1     | Un utilisateur s’inscrit et crée un compte. Quand l’utilisateur sélectionne Envoyer, le protocole d’authentification défi-réponse (Enforcement Challenge) Arkose Labs s’affiche.         |
|2     |  Une fois que l’utilisateur a correctement effectué l’authentification défi-réponse, Azure AD B2C envoie l’état à Arkose Labs pour générer un jeton. |
|3     |  Arkose Labs génère un jeton et le renvoie à Azure AD B2C.   |
|4     |  Azure AD B2C appelle une API web intermédiaire pour transmettre le formulaire d’inscription.      |
|5     |  L’API web intermédiaire envoie le formulaire d’inscription à Arkose Labs pour la vérification du jeton.    |
|6     | Arkose Labs traite et renvoie les résultats de la vérification à l’API web intermédiaire.|
|7     | L’API web intermédiaire envoie le résultat de réussite ou d’échec de l’authentification défi-réponse à Azure AD B2C. |
|8     | Si l’authentification défi-réponse est réussie, un formulaire d’inscription est envoyé à Azure AD B2C et Azure AD B2C exécute l’authentification.|

## <a name="onboard-with-arkose-labs"></a>Intégration avec Arkose Labs

1. Contactez [Arkose](https://www.arkoselabs.com/book-a-demo/) et créez un compte.

2. Une fois le compte créé, accédez à https://dashboard.arkoselabs.com/login  

3. Dans le tableau de bord, accédez aux paramètres du site pour rechercher votre clé publique et votre clé privée. Ces informations seront nécessaires ultérieurement pour configurer Azure AD B2C. Les valeurs des clés publiques et privées sont appelées `ARKOSE_PUBLIC_KEY` et `ARKOSE_PRIVATE_KEY` dans [l’exemple de code](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose).

## <a name="integrate-with-azure-ad-b2c"></a>Intégrer avec Azure AD B2C

### <a name="part-1--create-a-arkosesessiontoken-custom-attribute"></a>Partie 1 : créer un attribut personnalisé ArkoseSessionToken

Pour créer un attribut personnalisé, procédez comme suit :  

1. Accédez au **portail Azure** > **Azure AD B2C**

2. Sélectionnez **Attributs utilisateur**

3. Sélectionnez **Ajouter**

4. Entrez **ArkoseSessionToken** comme nom d’attribut

5. Sélectionnez **Créer**

En savoir plus sur les [attributs personnalisés](./user-flow-custom-attributes.md?pivots=b2c-user-flow).

### <a name="part-2---create-a-user-flow"></a>Partie 2 : créer un flux d’utilisateur

Le flux d’utilisateur peut être pour l’**inscription** et la **connexion** ou simplement pour l’**inscription**. Le flux d’utilisateur Arkose Labs s’affiche uniquement lors de l’inscription.

1. Consultez les [instructions](./tutorial-create-user-flows.md) pour créer un flux d’utilisateur. Si vous utilisez un flux d’utilisateur existant, celui-ci doit être du type de version **Recommandé**.

2. Dans les paramètres de flux d’utilisateur, accédez à **Attributs utilisateur**, puis sélectionnez la revendication **ArkoseSessionToken**.

![Image montrant comment sélectionner des attributs personnalisés](media/partner-arkose-labs/select-custom-attribute.png)

### <a name="part-3---configure-custom-html-javascript-and-page-layouts"></a>Partie 3 : configurer les mises en page HTML, JavaScript et de page personnalisées

Accédez au [script HTML](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html) fourni. Le fichier contient un modèle HTML avec des étiquettes JavaScript `<script>` qui effectuent trois opérations :

1. Charger le script Arkose Labs qui affiche le widget Arkose Labs et effectue la validation Arkose Labs côté client.

2. Masquer l’étiquette et l’élément d’entrée `extension_ArkoseSessionToken`, correspondant à l’attribut personnalisé `ArkoseSessionToken`, depuis l’interface utilisateur affichée à l’utilisateur.

3. Quand un utilisateur effectue l’authentification défi-réponse Arkose Labs, Arkose Labs vérifie la réponse de l’utilisateur et génère un jeton. Le rappel `arkoseCallback` dans le code JavaScript personnalisé définit la valeur de `extension_ArkoseSessionToken` sur la valeur du jeton généré. Cette valeur est envoyée au point de terminaison d’API, comme décrit dans la section suivante.

    Consultez [cet article](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/214176229/Standard+Setup) pour en savoir plus sur la validation côté client d’Arkose Labs.

Suivez les étapes indiquées pour utiliser le code HTML et JavaScript personnalisé pour votre flux d’utilisateur.

1. Modifiez le fichier [selfAsserted.html](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html) afin que `<ARKOSE_PUBLIC_KEY>` corresponde à la valeur que vous avez générée pour la validation côté client et utilisée pour charger le script Arkose Labs pour votre compte.

2. Hébergez la page HTML sur un point de terminaison web compatible avec le partage des ressources cross-origin (CORS). [Créez un compte de Stockage Blob Azure](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) et [configurez CORS](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services).

  >[!NOTE]
  >Si vous avez votre propre code HTML personnalisé, copiez et collez les éléments `<script>` dans votre page HTML.

3. Procédez comme suit pour configurer les mises en page

   a. À partir du portail Azure, accédez à **Azure AD B2C**

   b. Accédez à **Flux d’utilisateur**, puis sélectionnez votre flux d’utilisateur

   c. Sélectionnez **Mises en page**

   d. Sélectionnez **Mise en page de la page d’inscription de compte local**

   e. Basculez **Utiliser un contenu de page personnalisé** sur **OUI**

   f. Collez l’URI où réside votre code HTML personnalisé dans **Utiliser du contenu de page personnalisé**

   g. Si vous utilisez des fournisseurs d’identité sociale, répétez les **étapes e** et **f** pour la mise en page de la **Page d’inscription de compte social**.

   ![Image montrant des mises en page](media/partner-arkose-labs/page-layouts.png)

4. À partir de votre flux d’utilisateur, accédez à **Propriétés**, puis sélectionnez **Activer JavaScript** en appliquant la mise en page (préversion). Pour en savoir plus, consultez [cet article](./javascript-and-page-layout.md?pivots=b2c-user-flow).

### <a name="part-4---create-and-deploy-your-api"></a>Partie 4 : créer et déployer votre API

Installez l’[extension Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pour Visual Studio Code.

>[!Note]
>Les étapes mentionnées dans cette section supposent que vous utilisez Visual Studio Code pour déployer la fonction Azure. Vous pouvez également utiliser le portail Azure, un terminal ou une invite de commandes, ou tout autre éditeur de code pour le déploiement.

#### <a name="run-the-api-locally"></a>Exécuter l’API localement

1. Accédez à l’extension Azure dans Visual Studio Code dans la barre de navigation de gauche. Sélectionnez le dossier **Projet local** représentant votre fonction Azure locale.

2. Appuyez sur **F5** ou utilisez le menu **Déboguer** > **Démarrer le débogage** pour lancer le débogueur et le joindre à l’hôte Azure Functions. Cette commande utilise automatiquement l’unique configuration de débogage créée par Azure Functions.

3. L’extension Azure Functions génère automatiquement quelques fichiers pour le développement local, installe les dépendances et installe les outils Function Core s’ils ne sont déjà présents. Ces outils aident dans l’expérience de débogage.

4. La sortie de l’outil Function Core s’affiche dans le volet **Terminal** de Visual Studio Code. Après le démarrage de l’hôte, ouvrez le navigateur en utilisant **Alt+clic** sur l’URL locale indiquée dans la sortie et exécuter la fonction. Dans l’explorateur Azure Functions, cliquez avec le bouton droit sur la fonction pour voir l’URL de la fonction hébergée localement.

Pour redéployer l’instance locale pendant le test, répétez les étapes 1 à 4.

#### <a name="add-environment-variables"></a>Ajouter les variables d’environnement

Cet exemple protège le point de terminaison de l’API web à l’aide de [l’authentification HTTP de base](https://tools.ietf.org/html/rfc7617).

Le nom d’utilisateur et le mot de passe sont stockés sous la forme de variables d’environnement et non dans le référentiel. Pour plus d’informations, consultez le fichier [local.settings.json](../azure-functions/functions-develop-local.md#local-settings-file).

1. Créer un fichier local.settings.json dans votre dossier racine

2. Copiez et collez le code ci-dessous dans le fichier :

```
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "BASIC_AUTH_USERNAME": "<USERNAME>",
    "BASIC_AUTH_PASSWORD": "<PASSWORD>",
    "ARKOSE_PRIVATE_KEY": "<ARKOSE_PRIVATE_KEY>",
    "B2C_EXTENSIONS_APP_ID": "<B2C_EXTENSIONS_APP_ID>"
  }
}
```
Les valeurs **BASIC_AUTH_USERNAME** et **BASIC_AUTH_PASSWORD** sont les informations d’identification qui seront utilisées pour authentifier l’appel d’API à votre fonction Azure. Choisissez les valeurs souhaitées.

`<ARKOSE_PRIVATE_KEY>` est le secret côté serveur que vous avez généré dans le service Arkose Labs. Il est utilisé pour appeler [l’API de validation côté serveur d’Arkose Labs](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/266214758/Server-Side+Instructions) pour valider la valeur `ArkoseSessionToken` générée par le serveur front-end.

`<B2C_EXTENSIONS_APP_ID>` est l’ID d’application de l’application utilisée par Azure AD B2C pour stocker des attributs personnalisés dans le répertoire. Vous trouverez cet ID d’application en accédant à Inscriptions d’applications, en recherchant « b2c-extensions-app » et en copiant l’ID de l’application (client) à partir du volet **Vue d’ensemble**. Supprimez les caractères `-`.

![Image montrant la recherche par ID d’application](media/partner-arkose-labs/search-app-id.png)

#### <a name="deploy-the-application-to-the-web"></a>Déployer l’application sur le web

1. Suivez les étapes mentionnées dans [ce](/azure/javascript/tutorial-vscode-serverless-node-04) guide pour déployer votre fonction Azure dans le cloud. Copiez l’URL web du point de terminaison de votre fonction Azure.

2. Après le déploiement, sélectionnez l’option **Charger les paramètres**. Cette option charge vos variables d’environnement dans les [Paramètres de l’application](../azure-functions/functions-develop-vs-code.md?tabs=csharp#application-settings-in-azure) du service d’application. Ces paramètres de l’application peuvent également être configurés ou [managés via le portail Azure.](../azure-functions/functions-how-to-use-azure-function-app-settings.md)

Consultez [cet article](../azure-functions/functions-develop-vs-code.md?tabs=csharp#republish-project-files) pour en savoir plus sur le développement de Visual Studio Code pour Azure Functions.

#### <a name="configure-and-enable-the-api-connector"></a>Configurer et activer le connecteur d’API

[Créez un connecteur d’API](./add-api-connector.md) et activez-le pour votre flux d’utilisateur. La configuration de votre connecteur d’API doit se présenter comme suit :

![Image montrant comment configurer le connecteur d’API](media/partner-arkose-labs/configure-api-connector.png)

- **URL de point de terminaison** : l’URL de la fonction que vous avez copiée précédemment quand vous avez déployé votre fonction Azure.

- **Nom d’utilisateur et mot de passe** : le nom d’utilisateur et le mot de passe que vous avez définis comme variables d’environnement précédemment.

Pour activer le connecteur d’API, dans les paramètres du **connecteur d’API** pour votre flux d’utilisateur, sélectionnez le connecteur d’API à appeler à l’étape **Avant la création de l’utilisateur**. L’API est alors appelée quand un utilisateur sélectionne **Créer** dans le flux d’inscription. L’API effectue une validation côté serveur de la valeur `ArkoseSessionToken` qui a été définie par le rappel du widget Arkose `arkoseCallback`.

![Image montrant l’activation du connecteur d’API](media/partner-arkose-labs/enable-api-connector.png)

## <a name="test-the-user-flow"></a>Tester le flux utilisateur

1. Ouvrez le locataire Azure AD B2C, puis sélectionnez **Flux d’utilisateurs** sous Stratégies.

2. Sélectionnez votre flux utilisateur précédemment créé.

3. Sélectionnez **Exécuter le flux utilisateur** puis sélectionnez les paramètres :

   a. Application : sélectionnez l’application inscrite (JWT dans l’exemple).

   b. URL de réponse : sélectionnez l’URL de redirection.

   c. Sélectionnez **Exécuter le flux utilisateur**.

4. Suivre le flux d’inscription et créer un compte

5. Se déconnecter

6. Suivre le flux de connexion  

7. Un puzzle Arkose Labs s’affiche lorsque vous sélectionnez **continuer**.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Exemples de code](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose) pour le flux d’utilisateur d’inscription Azure AD B2C

- [Stratégies personnalisées dans Azure AD B2C](./custom-policy-overview.md)

- [Bien démarrer avec les stratégies personnalisées dans Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)
