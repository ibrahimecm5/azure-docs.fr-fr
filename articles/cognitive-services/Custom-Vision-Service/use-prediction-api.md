---
title: Utiliser le point de terminaison de prédiction pour tester les images par programmation avec un classifieur - Custom Vision
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser l’API pour tester par programmation des images avec votre classifieur Service Vision personnalisée.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 99e2fbce479b575759a442f9dc278723adb2b25f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132062372"
---
# <a name="call-the-prediction-api"></a>Appeler l’API de prédiction

Après avoir effectué l’apprentissage de votre modèle, vous pouvez tester des images programmatiquement en les envoyant au point de terminaison de l’API de prédiction. Dans ce guide, vous allez apprendre à appeler l’API de prédiction pour noter une image. Vous découvrirez les différentes façons dont vous pouvez configurer le comportement de cette API pour répondre à vos besoins.


> [!NOTE]
> Ce document illustre l’utilisation de la bibliothèque cliente .NET pour C# afin d’envoyer une image à l’API de prédiction. Pour en savoir plus et obtenir des exemples, consultez la [référence sur l’API Prediction](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="setup"></a>Programme d’installation

### <a name="publish-your-trained-iteration"></a>Publier votre itération formée

À partir de la [page Web Custom Vision](https://customvision.ai), sélectionnez votre projet, puis sélectionnez l’onglet __Performance__.

Pour envoyer des images à l’API de prédiction, vous devez d’abord publier votre itération pour la prédiction, ce que vous pouvez faire en sélectionnant __Publier__ et en spécifiant un nom pour l’itération publiée. Cela rend votre modèle accessible à l’API de prédiction de votre ressource Azure Custom Vision.

![L’onglet des performances s’affiche avec un rectangle rouge entourant le bouton Publier.](./media/use-prediction-api/unpublished-iteration.png)

Une fois votre modèle publié avec succès, vous verrez une étiquette « Publié » s’afficher à côté de votre itération dans la barre latérale gauche, et son nom s’affiche dans la description de l’itération.

![L’onglet Performances s’affiche avec un rectangle rouge autour de l’étiquette Publié et le nom de l’itération publiée.](./media/use-prediction-api/published-iteration.png)

### <a name="get-the-url-and-prediction-key"></a>Obtenir l’URL et la clé de prédiction

Une fois que votre modèle a été publié, vous pouvez récupérer les informations requises en sélectionnant __URL de prédiction__. Cela ouvrira une boîte de dialogue avec les informations sur l’utilisation de l’API de prédiction, y compris __l’URL de prédiction__ et la __Prediction-Key__.

![L’onglet des performances s’affiche avec un rectangle rouge entourant le bouton URL prédite.](./media/use-prediction-api/published-iteration-prediction-url.png)

![L’onglet Performances s’affiche avec un rectangle rouge autour de la valeur de l’URL de prédiction pour l’utilisation d’un fichier image et la valeur de Prediction-Key.](./media/use-prediction-api/prediction-api-info.png)

## <a name="submit-data-to-the-service"></a>Envoyer des données au service

Ce guide suppose que vous avez déjà construit un objet **[CustomVisionPredictionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-dotnet-preview)** , nommé `predictionClient`, avec votre URL de point de terminaison et votre clé d’abonnement Custom Vision. Pour obtenir des instructions sur la configuration de cette fonctionnalité, suivez un des guides de [démarrage rapide](quickstarts/image-classification.md).

Dans ce guide, vous allez utiliser une image locale. Par conséquent, téléchargez une image que vous souhaitez envoyer à votre modèle formé. Le code suivant invite l’utilisateur à spécifier un chemin d’accès local et obtient le flux d’octets du fichier sur ce chemin d’accès.

```csharp
Console.Write("Enter image file path: ");
string imageFilePath = Console.ReadLine();
byte[] byteData = GetImageAsByteArray(imageFilePath);
```

Incluez la méthode d’assistance suivante :

```csharp
private static byte[] GetImageAsByteArray(string imageFilePath)
{
    FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
    BinaryReader binaryReader = new BinaryReader(fileStream);
    return binaryReader.ReadBytes((int)fileStream.Length);
}
```

La méthode **[ClassifyImageAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclientextensions.classifyimageasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Prediction_CustomVisionPredictionClientExtensions_ClassifyImageAsync_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Prediction_ICustomVisionPredictionClient_System_Guid_System_String_System_IO_Stream_System_String_System_Threading_CancellationToken_)** prend l’ID de projet et l’image stockée localement, et évalue l’image par rapport au modèle donné.

```csharp
// Make a prediction against the new project
Console.WriteLine("Making a prediction:");
var result = predictionApi.ClassifyImageAsync(project.Id, publishedModelName, byteData);
```

## <a name="determine-how-to-process-the-data"></a>Déterminer le mode de traitement des données

Vous pouvez éventuellement configurer la manière dont le service effectue l’opération de notation en choisissant d’autres méthodes (consultez les méthodes de la classe **[CustomVisionPredictionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-dotnet)** ). 

Vous pouvez utiliser une version non asynchrone de la méthode ci-dessus pour des raisons de simplicité, mais cela peut entraîner le blocage du programme pendant un certain temps.

Les méthodes **-WithNoStore** requièrent que le service ne conserve pas l’image de prédiction une fois la prédiction terminée. Normalement, le service conserve ces images, ce qui vous donne la possibilité de les ajouter en tant que données d’apprentissage pour les prochaines itérations de votre modèle.

Les méthodes **-WithHttpMessages** retournent la réponse HTTP brute de l’appel d’API.

## <a name="get-results-from-the-service"></a>Obtenir les résultats du service

Le service retourne les résultats sous la forme d’un objet **[ImagePrediction](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.models.imageprediction?view=azure-dotnet)** . La propriété **Predictions** contient une liste d’objets **[PredictionModel](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.models.predictionmodel?view=azure-dotnet)** , chacun représentant une prédiction d’objet unique. Ils incluent le nom de l’étiquette et les coordonnées du cadre englobant où l’objet a été détecté dans l’image. Votre application peut ensuite analyser ces données pour, par exemple, afficher l’image avec des champs d’objet étiquetés sur un écran. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide, vous avez appris à envoyer des images sur votre détecteur/classifieur d’images personnalisé et recevoir une réponse par programmation avec le SDK C#. Nous allons maintenant apprendre à exécuter des scénarios de bout en bout avec C#, ou commencer à utiliser une autre langue de SDK.

* [Démarrage rapide : SDK Custom Vision](quickstarts/image-classification.md)
