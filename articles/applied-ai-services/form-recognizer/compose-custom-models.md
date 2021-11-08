---
title: 'Guide pratique : Utiliser des modèles personnalisés et composés'
titleSuffix: Azure Applied AI Services
description: Découvrez comment créer, utiliser et gérer des modèles personnalisés et composés de Form Recognizer.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5389e30e2aca2d93ba0fb27c71a6b934d7bf10e0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027781"
---
# <a name="use-custom-and-composed-models"></a>Utiliser des modèles personnalisés et composés

Form Recognizer utilise la technologie de Machine Learning avancée pour détecter et extraire des informations à partir d’images de document et retourner les données extraites dans une sortie JSON structurée. Avec Form Recognizer, vous pouvez effectuer l’apprentissage de modèles personnalisés autonomes ou combiner des modèles personnalisés pour créer des modèles composés.

* **Modèles personnalisés**. Les modèles personnalisés Form Recognizer vous permettent d’analyser et d’extraire des données à partir de formulaires et de documents spécifiques à votre entreprise. Les modèles personnalisés sont entraînés pour vos données et cas d’usage spécifiques.

* **Modèles composés**. Un modèle composé est créé sur la base d’une collection de modèles personnalisés qui sont affectés à un modèle unique englobant les types de formulaire. Quand un document est soumis à un modèle composé, le service effectue une étape de classification pour déterminer le modèle personnalisé qui représente précisément le formulaire présenté pour l’analyse.

***Fenêtre Configuration du modèle dans Form Recognizer Studio***

:::image type="content" source="media/studio/composed-model.png" alt-text="Capture d’écran : Fenêtre Configuration du modèle dans Form Recognizer Studio.":::

Dans cet article, nous allons découvrir la création de modèles personnalisés et composés de Form Recognizer à l’aide de notre [outil d’étiquetage des exemples Form Recognizer](label-tool.md), nos [API REST](quickstarts/client-library.md?branch=main&pivots=programming-language-rest-api#train-a-custom-model) ou nos [Kits de développement logiciel (SDK) de bibliothèque de client](quickstarts/client-library.md?branch=main&pivots=programming-language-csharp#train-a-custom-model).

## <a name="sample-labeling-tool"></a>Outil d’étiquetage d’exemples

Vous pouvez voir comment les données sont extraites de formulaires personnalisés en testant notre outil d'étiquetage des exemples. Vous aurez besoin des éléments suivants :

* Un abonnement Azure : [vous pouvez en créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)

* Une [instance Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) dans le portail Azure. Vous pouvez utiliser le niveau tarifaire gratuit (`F0`) pour tester le service. Une fois votre ressource déployée, sélectionnez **Accéder à la ressource** pour accéder à la clé et au point de terminaison de votre API.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Capture d’écran : clés et emplacement du point de terminaison dans le Portail Azure.":::

> [!div class="nextstepaction"]
> [Essayer](https://fott-2-1.azurewebsites.net/projects/create)

Dans l’interface utilisateur de Form Recognizer :

1. Sélectionnez **Utiliser Personnalisé pour effectuer l’apprentissage d’un modèle avec des étiquettes et rechercher des paires clé-valeur**.
  
      :::image type="content" source="media/label-tool/fott-use-custom.png" alt-text="Capture d’écran : outil fott, sélection de l’option Personnalisé.":::

1. Dans la fenêtre suivante, sélectionnez **Nouveau projet** :

    :::image type="content" source="media/label-tool/fott-new-project.png" alt-text="Capture d’écran : outil fott, sélection de Nouveau projet."::: 

## <a name="create-your-models"></a>Créer vos modèles

Voici les étapes de création, d’apprentissage et d’utilisation des modèles personnalisés et composés :

* [**Assembler votre jeu de données d’apprentissage**](#assemble-your-training-dataset)
* [**Charger votre set d’apprentissage sur le Stockage Blob Azure**](#upload-your-training-dataset)
* [**Effectuer l’apprentissage de votre modèle personnalisé**](#train-your-custom-model)
* [**Composer des modèles personnalisés**](#create-a-composed-model)
* [**Analyser des documents**](#analyze-documents-with-your-custom-or-composed-model)
* [**Gérer vos modèles personnalisés**](#manage-your-custom-models)

## <a name="assemble-your-training-dataset"></a>Assembler votre jeu de données d’apprentissage

La création d’un modèle personnalisé commence par l’établissement de votre jeu de données d’apprentissage. Vous aurez besoin d’un minimum de cinq formulaires remplis du même type pour votre exemple de jeu de données. Il peut s’agir de différents types de fichiers (jpg, png, pdf, tiff). Ils peuvent contenir à la fois du texte et de l’écriture manuscrite. Vos formulaires doivent respecter les [critères des entrées](build-training-data-set.md#custom-model-input-requirements) de Form Recognizer.

## <a name="upload-your-training-dataset"></a>Charger votre jeu de données d’apprentissage

Vous devez [charger votre jeu de données d’apprentissage](build-training-data-set.md#upload-your-training-data) dans un conteneur Stockage Blob Azure. Si vous ignorez comment créer un compte de stockage Azure avec un conteneur, *consultez* [Démarrage rapide de Stockage Azure pour Portail Azure](../../storage/blobs/storage-quickstart-blobs-portal.md). Vous pouvez utiliser le niveau tarifaire Gratuit (F0) pour tester le service, puis passer par la suite à un niveau payant pour la production.

## <a name="train-your-custom-model"></a>Effectuer l’apprentissage de votre modèle personnalisé

Vous pouvez [entraîner votre modèle](./quickstarts/try-sdk-rest-api.md#train-a-custom-model) avec des jeux de données étiquetés ou sans. Les jeux de données sans étiquette reposent uniquement sur [l’API de disposition](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync) pour détecter et identifier les informations clés, sans intervention humaine supplémentaire. Les jeux de données étiquetés s’appuient également sur l’API de disposition, mais une autre intervention humaine est incluse, comme vos étiquettes spécifiques et l’emplacement des champs. Pour utiliser à la fois des données avec et sans étiquettes, commencez avec au moins cinq formulaires remplis du même type pour les données d’apprentissage avec étiquettes, puis ajoutez des données sans étiquettes au jeu de données requis.

### <a name="train-without-labels"></a>Effectuer l’entraînement sans étiquettes

Form Recognizer utilise l’apprentissage non supervisé pour comprendre la disposition et les relations entre les champs et les entrées de vos formulaires. Quand vous soumettez vos formulaires d’entrée, l’algorithme groupe les formulaires par types, découvre les clés et les tableaux présents, et associe les valeurs aux clés et les entrées aux tableaux. L’entraînement sans étiquette ne nécessitant pas d’étiquetage manuel des données ni de programmation et de maintenance intensives, nous vous recommandons d’essayer cette méthode en premier.

Consultez [Créer un jeu de données d’entraînement](./build-training-data-set.md) pour obtenir des conseils sur la collecte de vos documents d’entraînement.

### <a name="train-with-labels"></a>Effectuer l’entraînement avec des étiquettes

Quand vous effectuez l’entraînement avec des données étiquetées, le modèle utilise l’apprentissage supervisé afin d’extraire les valeurs dignes d’intérêt, à l’aide des formulaires étiquetés que vous fournissez. Des données étiquetées aboutissent à des modèles plus performants et peuvent engendrer des modèles qui fonctionnent avec des formulaires complexes ou des formulaires contenant des valeurs sans clés.

Form Recognizer utilise l’API [Layout](concept-layout.md) pour connaître les tailles et les positions attendues des éléments de texte imprimés et manuscrits et pour extraire des tables. Ensuite, il utilise des étiquettes spécifiées par l’utilisateur pour connaître les associations clé/valeur et les tables dans les documents. Nous vous recommandons d’utiliser cinq formulaires étiquetés manuellement du même type (même structure) pour commencer l’entraînement d’un nouveau modèle et d’ajouter des données étiquetées en fonction des besoins afin d’améliorer la justesse du modèle. Form Recognizer permet d’effectuer l’entraînement d’un modèle pour extraire des paires clé-valeur et des tables à l’aide de fonctionnalités d’apprentissage supervisé.

[Bien démarrer avec l’entraînement avec des étiquettes](label-tool.md)

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="create-a-composed-model"></a>Créer un modèle composé

> [!NOTE]
> **La composition de modèle est disponible uniquement pour les modèles personnalisés entraînés _avec_ des étiquettes.** Toute tentative de composition de modèles sans étiquette génère une erreur.

L’opération de composition de modèle permet d’affecter un ID de modèle unique à jusqu’à 100 modèles personnalisés entraînés. Quand vous appelez la fonction d’analyse avec l’ID de modèle composé, Form Recognizer commence par classifier le formulaire envoyé. Il choisit le modèle affecté qui correspond le mieux, puis retourne les résultats pour ce modèle. Cette opération est utile quand les formulaires entrants sont susceptibles d’appartenir à l’un des différents modèles.

À l’aide de l’outil d’étiquetage des exemples Form Recognizer, de l’API REST ou des kits SDK de la bibliothèque de client, procédez comme suit pour configurer un modèle composé :

1. [**Collecter vos ID de modèles personnalisés**](#gather-your-custom-model-ids)
1. [**Composer vos modèles personnalisés**](#compose-your-custom-models)

#### <a name="gather-your-custom-model-ids"></a>Collecter vos ID de modèles personnalisés

Une fois le processus d’apprentissage réussi, un ID de modèle est affecté à votre modèle personnalisé. Vous pouvez récupérer un ID de modèle comme suit :

### <a name="form-recognizer-sample-labeling-tool"></a>[**Outil d’étiquetage des exemples Form Recognizer**](#tab/fott)

Quand vous effectuez l’apprentissage des modèles à l’aide de [**l’outil d’étiquetage des exemples Form Recognizer**](https://fott-2-1.azurewebsites.net/), l’ID de modèle se trouve dans la fenêtre de résultat de l’apprentissage :

:::image type="content" source="media/fott-training-results.png" alt-text="Capture d’écran : fenêtre des résultats de l’apprentissage.":::

### <a name="rest-api"></a>[**API REST**](#tab/rest-api)

[**L’API REST**](./quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#train-a-custom-model) retourne une `201 (Success)` réponse avec en-tête **Emplacement**. La valeur du dernier paramètre dans cet en-tête est l’ID de modèle pour le modèle dont l’apprentissage vient d’être réalisé :

:::image type="content" source="media/model-id.png" alt-text="Capture d’écran : en-tête d’emplacement retourné contenant l’ID de modèle.":::

### <a name="client-library-sdks"></a>[**Kits SDK de bibliothèque de client**](#tab/sdks)

 Les kits [**SDK de bibliothèque de client**](./quickstarts/try-sdk-rest-api.md?pivots=programming-language-csharp#train-a-custom-model) retournent un objet de modèle pouvant être interrogé pour retourner l’ID de modèle entraîné :

* C\#  | [Classe CustomFormModel](/dotnet/api/azure.ai.formrecognizer.training.customformmodel?view=azure-dotnet&preserve-view=true#properties "Kit SDK Azure pour .NET")

* Java | [Classe CustomFormModelInfo](/java/api/com.azure.ai.formrecognizer.training.models.customformmodelinfo?view=azure-java-stable&preserve-view=true#methods "Kit SDK Azure pour Java")

* JavaScript | [Interface CustomFormModelInfo](/javascript/api/@azure/ai-form-recognizer/customformmodelinfo?view=azure-node-latest&preserve-view=true&branch=main#properties "Kit SDK Azure pour JavaScript")

* Python | [Classe CustomFormModelInfo](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.customformmodelinfo?view=azure-python&preserve-view=true&branch=main#variables "Kit SDK Azure pour Python")

---

#### <a name="compose-your-custom-models"></a>Composer vos modèles personnalisés

Une fois que vous avez collecté vos modèles personnalisés correspondant à un type de formulaire unique, vous pouvez les combiner dans un modèle unique.

### <a name="form-recognizer-sample-labeling-tool"></a>[**Outil d’étiquetage des exemples Form Recognizer**](#tab/fott)

**L’outil d’étiquetage des exemples** vous permet de commencer rapidement à effectuer l’apprentissage des modèles et à les combiner pour composer un ID de modèle unique.

Une fois l’apprentissage réalisé, composez vos modèles comme suit :

1. Dans le menu de rail de gauche, sélectionnez **l’icône de composition du modèle** (flèche de fusion).

1. Dans la fenêtre principale, sélectionnez les modèles auxquels vous souhaitez affecter un ID de modèle unique. Les modèles présentant une icône de flèche sont déjà des modèles composés.

1. Choisissez le **bouton Composer** dans le coin supérieur gauche.

1. Dans la fenêtre contextuelle, nommez le modèle que vous venez de composer, puis sélectionnez **Composer**.

Une fois l’opération effectuée, le modèle récemment composé apparaît dans la liste.

  :::image type="content" source="media/custom-model-compose.png" alt-text="Capture d’écran : fenêtre de la composition du modèle." lightbox="media/custom-model-compose-expanded.png":::

### <a name="rest-api"></a>[**API REST**](#tab/rest-api)

À l’aide de **l’API REST**, vous pouvez créer une demande de [**modèle composé personnalisé**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/Compose) pour créer un modèle composé unique à partir de modèles existants. Le corps de la demande requiert un tableau de chaînes de vos ID `modelIds` pour composer et vous pouvez éventuellement définir `modelName`.  *Consultez* [Composer des modèles Async](/rest/api/formrecognizer/2.1preview2/compose-custom-models-async/compose-custom-models-async).

### <a name="client-library-sdks"></a>[**Kits SDK de bibliothèque de client**](#tab/sdks)

Utilisez le code du langage de programmation de votre choix pour créer un modèle composé qui sera appelé avec un ID de modèle unique. Vous trouverez ci-dessous des liens vers des exemples de code qui montrent comment créer un modèle composé à partir de modèles personnalisés existants :

* [**C#/.NET**](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/Sample_ModelCompose.md).

* [**Java**](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/java/com/azure/ai/formrecognizer/administration/CreateComposedModel.java).

* [**JavaScript**](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/formrecognizer/ai-form-recognizer/samples/v3/javascript/createComposedModel.js).

* [**Python**](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/samples/v3.2-beta/sample_create_composed_model.py)

---

## <a name="analyze-documents-with-your-custom-or-composed-model"></a>Analyser des documents avec votre modèle personnalisé ou composé

 L’opération **Analyse** du formulaire personnalisé vous oblige à fournir le `modelID` dans l’appel à Form Recognizer. Vous pouvez fournir un ID de modèle personnalisé unique ou un ID de modèle composé pour le paramètre `modelID`.

### <a name="form-recognizer-sample-labeling-tool"></a>[**Outil d’étiquetage des exemples Form Recognizer**](#tab/fott)

1. Dans le menu du volet de gauche de l’outil, sélectionnez **l’icône Analyse** (ampoule).

1. Choisissez une URL de fichier ou d’image locale à analyser.

1. Sélectionnez le bouton **Exécuter l’analyse**.

1. L’outil applique des étiquettes dans les cadres englobants et signale le pourcentage de confiance de chaque étiquette.

:::image type="content" source="media/analyze.png" alt-text="Capture d’écran : outil Form Recognizer, fenêtre de l’analyse de formulaire personnalisé.":::

### <a name="rest-api"></a>[**API REST**](#tab/rest-api)

À l’aide de l’API REST, vous pouvez effectuer une requête [Analyser le formulaire](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm) pour analyser un document et extraire des paires clé-valeur et des données de table.

### <a name="client-library-sdks"></a>[**Kits SDK de bibliothèque de client**](#tab/sdks)

Utilisation du langage de programmation de votre choix pour analyser un formulaire ou un document avec un modèle personnalisé ou composé. Vous aurez besoin de votre point de terminaison Form Recognizer, de votre clé API et de votre ID de modèle.

* [**C#/.NET**](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/Sample_ModelCompose.md)

* [**Java**](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/java/com/azure/ai/formrecognizer/AnalyzeCustomDocumentFromUrl.java)

* [**JavaScript**](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/formrecognizer/ai-form-recognizer/samples/v3/javascript/recognizeCustomForm.js)

* [**Python**](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/samples/v3.1/sample_recognize_custom_forms.py)

---

Testez les modèles dont l’apprentissage vient d’être réalisé en [analysant les formulaires](./quickstarts/try-sdk-rest-api.md#analyze-forms-with-a-custom-model) qui ne faisaient pas partie du jeu de données d’apprentissage. Selon la justesse signalée, vous souhaiterez peut-être effectuer d’autres entraînements pour améliorer le modèle. Vous pouvez poursuivre l’apprentissage pour [améliorer les résultats](label-tool.md#improve-results).

## <a name="manage-your-custom-models"></a>Gérer vos modèles personnalisés

Vous pouvez [gérer vos modèles personnalisés](./quickstarts/try-sdk-rest-api.md#manage-custom-models) tout au long de leur cycle de vie en affichant une [liste de tous les modèles personnalisés](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModels) sous votre abonnement, en récupérant des informations sur [un modèle personnalisé spécifique](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModel) et en [supprimant des modèles personnalisés](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/DeleteCustomModel) de votre compte.

Très bien ! Vous avez découvert les étapes permettant de créer des modèles personnalisés et composés, puis de les utiliser dans vos projets et applications Form Recognizer.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la bibliothèque de client Form Recognizer, explorez notre documentation de référence sur les API.

> [!div class="nextstepaction"]
> [Informations de référence sur l’API Form Recognizer](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)
>
