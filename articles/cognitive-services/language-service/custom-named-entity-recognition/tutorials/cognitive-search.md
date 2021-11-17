---
title: Enrichir un index Recherche cognitive avec des entités personnalisées
titleSuffix: Azure Cognitive Services
description: Améliorer vos index de recherche cognitive à l’aide de la reconnaissance d’entité nommée (NER) personnalisée
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: tutorial
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 555269beeb5e8b36597998a8941e2b64120691b4
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484170"
---
# <a name="tutorial-enrich-a-cognitive-search-index-with-custom-entities-from-your-data"></a>Tutoriel : Enrichir un index Recherche cognitive avec des entités personnalisées à partir de vos données

En entreprise, effectuer des recherches parmi une abondance de documents électroniques peut être une tâche longue et coûteuse. [Recherche cognitive Azure](/azure/search/search-create-service-portal) est un service qui peut vous aider à effectuer des recherches parmi vos fichiers, en fonction de leur index. Une NER personnalisée peut vous être utile, car elle extrait les entités pertinentes de vos fichiers et enrichit le processus d’indexation de ces derniers.

Dans ce tutoriel, vous allez apprendre à :

* Créer un projet de reconnaissance d’entité nommée personnalisée.
* Publier une fonction Azure.
* Ajouter un index au service Recherche cognitive Azure.

## <a name="prerequisites"></a>Prérequis

* [Une ressource Azure Language connectée à un compte Stockage Blob Azure](../how-to/create-project.md).
    * Nous vous recommandons de suivre les instructions de création d’une ressource en utilisant le portail Azure pour faciliter la configuration. 
* [Un service Recherche cognitive Azure](/azure/search/search-create-service-portal) dans votre abonnement actuel
    * Vous pouvez utiliser n’importe quel niveau et n’importe quelle région pour ce service.
* Une [application de fonction Azure](/azure/azure-functions/functions-create-function-app-portal)
* Téléchargez ces [exemples de données](https://go.microsoft.com/fwlink/?linkid=2175226).

## <a name="create-a-custom-ner-project-through-language-studio"></a>Créer un projet de NER personnalisée via Language Studio

1. Connectez-vous via le [portail Language Studio](https://aka.ms/LanguageStudio), puis sélectionnez **Extraction d’entité personnalisée**.

2. Sélectionnez votre ressource Language. Vérifiez que vous avez [activé la gestion des identités](../how-to/create-project.md#enable-identity-management-for-your-resource) et les rôles pour votre compte de ressource et de stockage.

3. En haut de l’écran des projets, sélectionnez **Créer un projet**. Si nécessaire, choisissez votre compte de stockage dans le menu qui s’affiche.

    :::image type="content" source="../media/create-project.png" alt-text="Capture d’écran de la page de création de projet." lightbox="../media/create-project.png":::

4. Entrez les informations relatives à votre projet :

    | Clé | Description |
    |--|--|
    | Nom | Nom de votre projet. Vous ne pourrez pas renommer votre projet une fois que vous l’aurez créé. |
    | Description | Description de votre projet |
    | Langage | Langue des fichiers de votre projet.|

    > [!NOTE]
    > Si vos documents doivent être en plusieurs langues, sélectionnez l’option **plusieurs langues** durant la création du projet, puis affectez à l’option **langue** la langue dans laquelle se trouve la majorité de vos documents.

5. Pour ce tutoriel, utilisez un **fichier d’étiquettes existant**, puis sélectionnez le fichier d’étiquettes que vous avez téléchargé à partir des exemples de données.

## <a name="train-your-model"></a>Entraîner votre modèle

1. Dans le menu de gauche, sélectionnez **Entraîner**.

2. Pour entraîner un nouveau modèle, sélectionnez **Entraîner un nouveau modèle**, puis tapez le nom du modèle dans la zone de texte ci-dessous.

    :::image type="content" source="../media/train-model.png" alt-text="Créer un modèle" lightbox="../media/train-model.png":::

3. Sélectionnez au bas de la page le bouton **Entraîner**.

4. Une fois l’entraînement effectué, vous pouvez [voir les détails de l’évaluation du modèle](../how-to/view-model-evaluation.md) et [améliorer le modèle](../how-to/improve-model.md)

## <a name="deploy-your-model"></a>Déployer votre modèle

1. Dans le menu de gauche, sélectionnez **Déployer le modèle**.

2. Sélectionnez le modèle à déployer, puis dans le menu supérieur, cliquez sur **Déployer le modèle**. Vous voyez uniquement les modèles dont l’entraînement a réussi.

## <a name="prepare-your-secrets-for-the-azure-function"></a>Préparer vos secrets pour la fonction Azure

Vous devez ensuite préparer les secrets de votre fonction Azure. Les secrets de votre projet sont les suivants : 
* Point de terminaison
* Ressources clés
* ID de modèle

### <a name="get-your-custom-ner-project-secrets"></a>Obtenir les secrets de votre projet de NER personnalisée

* Vous aurez besoin de votre **nom de projet**. Les noms de projets respectent la casse.

* Vous aurez également besoin de l’emplacement de déploiement. 
   * Si vous avez déployé votre modèle via Language Studio, votre emplacement de déploiement est `prod` par défaut. 
   * Si vous avez déployé votre modèle par programmation, à l’aide de l’API, il s’agit du nom de déploiement que vous avez affecté dans votre requête.

### <a name="get-your-resource-keys-endpoint"></a>Obtenir vos clés de ressource et votre point de terminaison

1. Accédez à votre ressource dans le [portail Azure](https://ms.portal.azure.com/#home).

2. Dans le menu de gauche, sélectionnez **Clés et point de terminaison**. Vous aurez besoin du point de terminaison et de l’une des clés pour les requêtes d’API.

    :::image type="content" source="../../media/azure-portal-resource-credentials.png" alt-text="Capture d’écran montrant la clé et le point de terminaison dans le portail Azure" lightbox="../../media/azure-portal-resource-credentials.png":::
   
## <a name="edit-and-deploy-your-azure-function"></a>Modifier et déployer votre fonction Azure

1. Téléchargez et utilisez l’[exemple de fonction fourni](https://aka.ms/ct-cognitive-search-integration-tool).

2. Après avoir téléchargé l’exemple de fonction, ouvrez le fichier *program.cs*, puis entrez vos secrets d’application.

3. [Publier la fonction sur Azure](/azure/azure-functions/functions-develop-vs?tabs=in-process#publish-to-azure).

## <a name="use-the-integration-tool"></a>Utiliser l’outil d’intégration

Dans les sections suivantes, vous allez utiliser l’[outil d’intégration au service Recherche cognitive](https://aka.ms/ct-cognitive-search-integration-tool) pour intégrer votre projet à Recherche cognitive Azure. Téléchargez ce dépôt maintenant. 

### <a name="prepare-configuration-file"></a>Préparer le fichier config

1. Dans le dossier que vous venez de télécharger, recherchez l’[exemple de fichier config](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/dev/CustomTextAnalytics.CognitiveSearch/Samples/configs.json). Ouvrez le fichier dans un éditeur de texte. 

2. Obtenez la chaîne de connexion de votre compte de stockage en :
    1. Accédant à la page de vue d’ensemble de votre compte de stockage dans le [portail Azure](https://ms.portal.azure.com/#home).
    2. Dans la section supérieure de l’écran, copiez le nom de votre conteneur dans le champ `containerName` du fichier config, sous `blobStorage`.  
    3. Dans la section **Clés d’accès** du menu situé à gauche de l’écran, copiez votre **Chaîne de connexion** dans le champ `connectionString` du fichier config, sous `blobStorage`.

1. Obtenez votre point de terminaison et vos clés Recherche cognitive en :
    1. Accédant à la page de vue d’ensemble de votre ressource dans le [portail Azure](https://ms.portal.azure.com/#home).
    2. Copiez l’**URL** située dans la section en haut à droite de la page vers le champ `endpointUrl` au sein de `cognitiveSearch`.
    3. Accédez à la section **Clés** dans le menu situé à gauche de l’écran. Copiez votre **clé d’administration principale** dans le champ `apiKey` au sein de `cognitiveSearch`.

3. Obtenir le point de terminaison et les clés de fonction Azure
    
    1. Pour obtenir votre point de terminaison et vos clés de fonction Azure, accédez à la page de vue d’ensemble de votre fonction dans le [portail Azure](https://ms.portal.azure.com/#home).
    2. Accédez au menu **Fonctions** sur la gauche de l’écran, puis cliquez sur la fonction que vous avez créée.
    3. Dans le menu supérieur, cliquez sur **Obtenir l’URL de la fonction**. L’URL est au format suivant : `YOUR-ENDPOINT-URL?code=YOUR-API-KEY`. 
    4. Copiez `YOUR-ENDPOINT-URL` dans le champ `endpointUrl` du fichier config, sous `azureFunction`. 
    5. Copiez `YOUR-API-KEY` dans le champ `apiKey` du fichier config, sous `azureFunction`. 

### <a name="prepare-schema-file"></a>Préparer le fichier de schéma

Dans le dossier que vous avez téléchargé plus tôt, recherchez l’[exemple de fichier de schéma](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/dev/CustomTextAnalytics.CognitiveSearch/Samples/app-schema.json). Ouvrez le fichier dans un éditeur de texte. 

Les entrées du tableau `entityNames` correspondent aux noms d’entités que vous avez affectés au moment de la création de votre projet. Vous pouvez les copier et les coller à partir de votre projet dans [Language Studio](https://aka.ms/custom-extraction), ou 

### <a name="run-the-index-command"></a>Exécutez la commande `Index`.

Une fois que vous avez créé votre fichier config et votre fichier de schéma, vous pouvez indexer le projet. Placez votre fichier config dans le même chemin que l’outil CLI, puis exécutez la commande suivante :

```cli
    indexer index --schema <path/to/your/schema> --index-name <name-your-index-here>
```

Remplacez `name-your-index-here` par le nom d’index qui apparaît dans votre instance de Recherche cognitive.

## <a name="next-steps"></a>Étapes suivantes

* [Effectuer des recherches dans votre application avec le kit SDK Recherche cognitive](/azure/search/search-howto-dotnet-sdk#run-queries)
