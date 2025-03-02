---
title: "Démarrage rapide : Bibliothèque de client Analyse d'images pour Node.js"
description: Utilisez ce guide de démarrage rapide pour vous familiariser avec la bibliothèque de client Analyse d'images pour Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: b3a20beb6e4df422e071a2035e642d26d08dfea3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520853"
---
<a name="HOLTop"></a>

Utilisez la bibliothèque de client Analyse d'images pour rechercher des étiquettes, une description textuelle, des visages, du contenu pour adultes ou autre dans une image.

[Documentation de référence](/javascript/api/@azure/cognitiveservices-computervision/) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision) | [Package (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision) | [Exemples](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)
* Version actuelle de [Node.js](https://nodejs.org/)
* Une fois que vous avez votre abonnement Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="créez une ressource Vision par ordinateur"  target="_blank">créer une ressource Vision par ordinateur </a> dans le portail Azure pour obtenir votre clé et votre point de terminaison. Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application au service Vision par ordinateur. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-nodejs-application"></a>Création d’une application Node.js

Dans une fenêtre de console (telle que cmd, PowerShell ou bash), créez un répertoire pour votre application et accédez-y.

```console
mkdir myapp && cd myapp
```

Exécutez la commande `npm init` pour créer une application de nœud avec un fichier `package.json`.

```console
npm init
```

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Installez le package NPM `@azure/cognitiveservices-computervision` et `ms-rest-azure` :

```console
npm install @azure/cognitiveservices-computervision
```

Installez également le module async :

```console
npm install async
```

Le fichier `package.json` de votre application sera mis à jour avec les dépendances.

Créez un fichier, *index.js*, puis ouvrez-le dans un éditeur de texte. Ajoutez les instructions d’importation suivantes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_imports)]

> [!TIP]
> Vous voulez voir l’intégralité du fichier de code de démarrage rapide à la fois ? Vous le trouverez sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ImageAnalysisQuickstart.js), qui contient les exemples de code utilisés dans ce guide de démarrage rapide.

Créez des variables pour le point de terminaison et la clé Azure de votre ressource.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_vars)]

> [!IMPORTANT]
> Accédez au portail Azure. Si la ressource Vision par ordinateur que vous avez créée dans la section **Prérequis** a été déployée, cliquez sur le bouton **Accéder à la ressource** sous **Étapes suivantes**. La clé et le point de terminaison se trouvent dans la page **Clé et point de terminaison** de la ressource, sous **Gestion des ressources**. 
>
> N’oubliez pas de supprimer la clé de votre code une fois que vous avez terminé, et ne la postez jamais publiquement. Pour la production, envisagez d’utiliser une méthode de stockage et d’accès sécurisée pour vos informations d’identification. Pour plus d’informations, consultez l’article sur la [sécurité](../../../cognitive-services-security.md) de Cognitive Services.

> [!div class="nextstepaction"]
> [J’ai configuré les variables](?success=set-up-client#object-model) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=set-up-client&product=computer-vision&page=image-analysis-node-sdk)

## <a name="object-model"></a>Modèle objet

Les classes et interfaces suivantes prennent en charge certaines des fonctionnalités principales du kit SDK Analyse d'images pour Node.js.

|Nom|Description|
|---|---|
| [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) | Cette classe est nécessaire pour toutes les fonctionnalités de Vision par ordinateur. Vous l’instanciez avec vos informations d’abonnement, et vous l’utilisez pour effectuer la plupart des opérations relatives aux images.|
|[VisualFeatureTypes](/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes)| Cette énumération définit les différents types d’analyse d’image qui peuvent être effectués dans le cadre d’une opération d’analyse standard. Vous spécifiez un ensemble de valeurs **VisualFeatureTypes** en fonction de vos besoins. |

## <a name="code-examples"></a>Exemples de code

Ces extraits de code vous montrent comment effectuer les tâches suivantes avec la bibliothèque de client Analyse d'images pour Node.js :

* [Authentifier le client](#authenticate-the-client)
* [Analyser une image](#analyze-an-image)

## <a name="authenticate-the-client"></a>Authentifier le client


Instanciez un client avec votre point de terminaison et la clé. Créez un objet [ApiKeyCredentials](/python/api/msrest/msrest.authentication.apikeycredentials) à l’aide de votre clé et point de terminaison, puis utilisez-le pour créer un objet [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_client)]

Ensuite, définissez une fonction `computerVision` et déclarez une série asynchrone avec la fonction principale et la fonction de rappel. Vous allez ajouter votre code de démarrage rapide à la fonction principale, et appeler `computerVision` en bas du script. Le reste du code de ce guide de démarrage rapide va à l’intérieur de la fonction `computerVision`.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_functiondef_end)]

> [!div class="nextstepaction"]
> [J’ai authentifié le client](?success=authenticate-client#analyze-an-image) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=authenticate-client&product=computer-vision&page=image-analysis-node-sdk)

## <a name="analyze-an-image"></a>Analyser une image

Le code de cette section analyse les images distantes pour extraire différentes fonctionnalités visuelles. Vous pouvez effectuer ces opérations dans le cadre de la méthode **analyzeImage** de l’objet client, ou vous pouvez les appeler à l’aide de méthodes individuelles. Consultez la [documentation de référence](/javascript/api/@azure/cognitiveservices-computervision/) pour plus d’informations.

> [!NOTE]
> Vous pouvez également analyser une image locale. Consultez les méthodes [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient), comme **describeImageInStream**. Ou consultez l’exemple de code sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ImageAnalysisQuickstart.js) pour obtenir des scénarios impliquant des images locales.

### <a name="get-image-description"></a>Obtenir une description d’image

Le code suivant obtient la liste des légendes générées pour l’image. Pour plus d’informations, consultez [Décrire des images](../../concept-describing-images.md).

Tout d’abord, définissez l’URL d’une image à analyser :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_describe_image)]

Ajoutez ensuite le code suivant pour récupérer la description de l’image et l’imprimer sur la console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>Obtenir une catégorie d’image

Le code suivant obtient la catégorie détectée de l’image. Pour plus d’informations, consultez [Classer des images](../../concept-categorizing-images.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_categories)]

Définissez la fonction d’assistance `formatCategories` :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>Obtenir des étiquettes d’image

Le code suivant obtient l’ensemble d’étiquettes détectées dans l’image. Pour plus d’informations, consultez [Étiquettes de contenu](../../concept-tagging-images.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_tags)]

Définissez la fonction d’assistance `formatTags` :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>Détecter des objets

Le code suivant détecte les objets courants présents dans l’image et les affiche sur la console. Pour plus d’informations, consultez [Détection d’objets](../../concept-object-detection.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_objects)]

Définissez la fonction d’assistance `formatRectObjects` pour renvoyer les coordonnées du haut, de gauche, du bas et de droite, ainsi que la largeur et la hauteur.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>Détecter les marques

Le code suivant détecte les marques et logos d’entreprise dans l’image, et les affiche sur la console. Pour plus d’informations, consultez [Détection des marques](../../concept-brand-detection.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>Détecter des visages

Le code suivant retourne les visages détectés dans l’image avec les coordonnées de leur rectangle et sélectionne les attributs du visage. Pour plus d’informations, consultez [Détection des visages](../../concept-detecting-faces.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_faces)]

Définissez la fonction d’assistance `formatRectFaces` :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-racy-or-gory-content"></a>Détecter des contenus pour adultes, choquants ou sordides

Le code suivant imprime la présence détectée de contenu pour adultes dans l’image. Pour plus d’informations, consultez [Contenu pour adultes choquant ou sordide](../../concept-detecting-adult-content.md).

Définissez l’URL de l’image à utiliser :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_adult_image)]

Ajoutez ensuite le code suivant pour détecter le contenu pour adultes et imprimer les résultats sur la console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Obtenir le modèle de couleurs d’une image

Le code suivant imprime les attributs de couleur détectés dans l’image, comme les couleurs dominantes et la couleur d’accentuation. Pour plus d’informations, consultez [Modèles de couleurs](../../concept-detecting-color-schemes.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_colors)]

Définissez la fonction d’assistance `printColorScheme` pour imprimer les détails du modèle de couleurs sur la console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>Obtenir le contenu spécifique à un domaine

Analyse d'images peut utiliser un modèle spécialisé pour effectuer une analyse approfondie des images. Pour plus d’informations, consultez [Contenu spécifique à un domaine](../../concept-detecting-domain-content.md).

Tout d’abord, définissez l’URL d’une image à analyser :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_domain_image)]

Le code suivant analyse des données relatives aux monuments détectés dans l’image.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_landmarks)]

Définissez la fonction d’assistance `formatRectDomain` pour analyser les données d’emplacement relatives aux points de repère détectés.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_landmarks_rect)]

### <a name="get-the-image-type"></a>Obtenir le type d’image

Le code suivant affiche des informations sur le type d’image, qu’il s’agisse d’une image clipart ou d’un dessin au trait.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_imagetype)]

Définissez la fonction d’assistance `describeType` :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_imagetype_describe)]

> [!div class="nextstepaction"]
> [J’ai analysé une image](?success=analyze-image#run-the-application) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=analyze-image&product=computer-vision&page=image-analysis-node-sdk)



## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `node` de votre fichier de démarrage rapide.

```console
node index.js
```

> [!div class="nextstepaction"]
> [J’ai exécuté l’application](?success=run-the-application#clean-up-resources) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=run-the-application&product=computer-vision&page=image-analysis-node-sdk)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [J’ai nettoyé des ressources](?success=clean-up-resources#next-steps) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=clean-up-resources&product=computer-vision&page=image-analysis-node-sdk)

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à installer la bibliothèque de client Analyse d’image et à effectuer des appels d’analyse d’image de base. À présent, découvrez-en plus sur les fonctionnalités de l’API Analyser.

> [!div class="nextstepaction"]
>[Appeler l’API Analyser](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Vue d’ensemble de l’analyse d’image](../../overview-image-analysis.md)
* Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ImageAnalysisQuickstart.js).

