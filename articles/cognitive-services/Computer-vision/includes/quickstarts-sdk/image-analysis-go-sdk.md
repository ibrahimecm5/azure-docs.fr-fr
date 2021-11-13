---
title: "Démarrage rapide : Bibliothèque de client Analyse d'images pour Go"
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide de démarrage rapide pour vous familiariser avec la bibliothèque de client Analyse d'images pour Go.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: fce788e25c967d99e38607e242ba9db12899173e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520742"
---
<a name="HOLTop"></a>

Utilisez la bibliothèque de client Analyse d'images pour rechercher des étiquettes, une description textuelle, des visages, du contenu pour adultes ou autre dans une image.

[Documentation de référence](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision) | [Package](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)
* La dernière version de [Go](https://golang.org/dl/)
* Une fois que vous avez votre abonnement Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="créez une ressource Vision par ordinateur"  target="_blank">créer une ressource Vision par ordinateur </a> dans le portail Azure pour obtenir votre clé et votre point de terminaison. Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application au service Vision par ordinateur. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

## <a name="setting-up"></a>Configuration

### <a name="create-a-go-project-directory"></a>Créer un répertoire de projet Go

Dans une fenêtre de console (cmd, PowerShell, terminal, bash), créez un espace de travail pour votre projet Go (nommé `my-app`) et accédez-y.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Votre espace de travail contiendra trois dossiers :

* **src** : ce répertoire contient le code source et les packages. Tous les packages installés à l’aide de la commande `go get` se trouvent dans ce répertoire.
* **pkg** : ce répertoire contient les objets de package Go compilés. Ces fichiers ont tous une extension `.a`.
* **bin** : ce répertoire contient les fichiers exécutables binaires créés lors de l’exécution de `go install`.

> [!TIP]
> Pour plus d’informations sur la structure d’un espace de travail Go, consultez la [documentation du langage Go](https://golang.org/doc/code.html#Workspaces). Ce guide comprend des informations pour configurer `$GOPATH` et `$GOROOT`.

### <a name="install-the-client-library-for-go"></a>Installer la bibliothèque de client pour Go

Ensuite, installez la bibliothèque de client pour Go :

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

Ou, si vous utilisez dep, au sein de votre dépôt, exécutez :

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Créer une application Go 

Ensuite, créez un fichier nommé `sample-app.go` dans le répertoire **src** :

```bash
cd src
touch sample-app.go
```

Ouvrez `sample-app.go` dans l’éditeur de texte ou l’environnement de développement intégré de votre choix. Ajoutez ensuite le nom du package et importez les bibliothèques suivantes :

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_imports)]

Vous devez également déclarer un contexte à la racine de votre script. Vous aurez besoin de cet objet pour exécuter la plupart des appels de fonction Analyse d'images :

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_context)]

Ensuite, vous commencerez à ajouter du code pour effectuer différentes opérations Vision par ordinateur.

> [!div class="nextstepaction"]
> [J’ai configuré les variables](?success=set-up-client#object-model) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=set-up-client&product=computer-vision&page=image-analysis-go-sdk)

## <a name="object-model"></a>Modèle objet

Les classes et interfaces suivantes prennent en charge certaines des fonctionnalités principales du kit SDK Analyse d'images pour Go.

|Nom|Description|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Cette classe est nécessaire pour toutes les fonctionnalités Vision par ordinateur, telles que l’analyse d’images et la lecture de texte. Vous l’instanciez avec vos informations d’abonnement, et vous l’utilisez pour effectuer la plupart des opérations relatives aux images.|
|[ImageAnalysis](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| Ce type contient les résultats d’un appel de fonction **AnalyzeImage**. Il existe des types similaires pour chacune des fonctions propres à une catégorie.|
|[VisualFeatureTypes](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| Ce type définit les différentes analyses d’images qui peuvent être effectuées lors d’une opération d’analyse standard. Vous spécifiez un ensemble de valeurs VisualFeatureTypes en fonction de vos besoins. |

## <a name="code-examples"></a>Exemples de code

Ces extraits de code vous montrent comment effectuer les tâches suivantes avec la bibliothèque de client Analyse d'images pour Go :

* [Authentifier le client](#authenticate-the-client)
* [Analyser une image](#analyze-an-image)

## <a name="authenticate-the-client"></a>Authentifier le client

> [!NOTE]
> Cette étape suppose que vous avez [créé des variables d’environnement](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) pour votre clé et votre point de terminaison Vision par ordinateur nommées `COMPUTER_VISION_SUBSCRIPTION_KEY` et `COMPUTER_VISION_ENDPOINT`.

Créez une fonction `main` et ajoutez-lui le code suivant pour instancier un client avec votre point de terminaison et votre clé.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> [J’ai authentifié le client](?success=authenticate-client#analyze-an-image) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=authenticate-client&product=computer-vision&page=image-analysis-go-sdk)

## <a name="analyze-an-image"></a>Analyser une image

Le code suivant utilise l’objet client pour analyser une image distante et afficher les résultats dans la console. Vous pouvez obtenir une description textuelle, une catégorisation, une liste d’étiquettes, les marques détectées, les visages détectés, les indicateurs de contenu pour adultes, les couleurs principales et le type d’image.

### <a name="set-up-test-image"></a>Configurer une image de test

Enregistrez d’abord une référence à l’URL de l’image que vous souhaitez analyser. Placez ce contenu dans votre fonction `main`.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_analyze_url)]

> [!TIP]
> Vous pouvez également analyser une image locale. Consultez les méthodes [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient), telles que **AnalyzeImageInStream**. Ou consultez l’exemple de code sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ImageAnalysisQuickstart.go) pour obtenir des scénarios impliquant des images locales.

### <a name="specify-visual-features"></a>Spécifier les caractéristiques visuelles

Les appels de fonction suivants extraient différentes fonctionnalités visuelles de l’exemple d’image. Vous allez définir ces fonctions dans les sections suivantes.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>Obtenir une description d’image

La fonction suivante obtient la liste des légendes générées pour l’image. Pour plus d’informations sur la description des images, consultez [Décrire les images](../../concept-describing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>Obtenir une catégorie d’image

La fonction suivante obtient la catégorie détectée de l’image. Pour plus d’informations, consultez [Classer des images](../../concept-categorizing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>Obtenir des étiquettes d’image

La fonction suivante obtient l’ensemble des étiquettes détectées dans l’image. Pour plus d’informations, consultez [Étiquettes de contenu](../../concept-tagging-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>Détecter des objets

La fonction suivante détecte les objets courants présents dans l’image et les affiche sur la console. Pour plus d’informations, consultez [Détection des objets](../../concept-object-detection.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>Détecter les marques

Le code suivant détecte les marques et logos d’entreprise dans l’image, et les affiche sur la console. Pour plus d’informations, consultez [Détection des marques](../../concept-brand-detection.md).

Tout d’abord, déclarez une référence à une nouvelle image dans votre fonction `main`.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_brand_url)]

Le code suivant définit la fonction de détection des marques.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>Détecter des visages

La fonction suivante retourne les visages détectés dans l’image avec les coordonnées de leur rectangle, et sélectionne certains attributs du visage. Pour plus d’informations, consultez [Détection des visages](../../concept-detecting-faces.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Détecter des contenus pour adultes, choquants ou sordides

La fonction suivante imprime la présence détectée de contenu pour adultes dans l’image. Pour plus d’informations, consultez [Contenu pour adultes choquant ou sordide](../../concept-detecting-adult-content.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Obtenir le modèle de couleurs d’une image

La fonction suivante affiche les attributs de couleur détectés dans l’image, comme les couleurs dominantes et la couleur d’accentuation. Pour plus d’informations, consultez [Jeux de couleurs](../../concept-detecting-color-schemes.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Obtenir le contenu spécifique à un domaine

Analyse d'images peut utiliser des modèles spécialisés pour effectuer une analyse approfondie des images. Pour plus d’informations, consultez [Contenu spécifique à un domaine](../../concept-detecting-domain-content.md). 

Le code suivant analyse des données relatives aux célébrités détectées dans l’image.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_celebs)]

Le code suivant analyse des données relatives aux monuments détectés dans l’image.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Obtenir le type d’image

La fonction suivante affiche des informations sur le type d’image, qu’il s’agisse d’une image clipart ou d’un dessin au trait.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_type)]

> [!div class="nextstepaction"]
> [J’ai analysé une image](?success=analyze-image#run-the-application) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=analyze-image&product=computer-vision&page=image-analysis-go-sdk)


## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application à partir de votre répertoire d’application avec la commande `go run`.

```bash
go run sample-app.go
```

> [!div class="nextstepaction"]
> [J’ai exécuté l’application](?success=run-the-application#clean-up-resources) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=run-the-application&product=computer-vision&page=image-analysis-go-sdk)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [J’ai nettoyé des ressources](?success=clean-up-resources#next-steps) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=clean-up-resources&product=computer-vision&page=image-analysis-go-sdk)

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à installer la bibliothèque de client Analyse d’image et à effectuer des appels d’analyse d’image de base. À présent, découvrez-en plus sur les fonctionnalités de l’API Analyser.

> [!div class="nextstepaction"]
>[Appeler l’API Analyser](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Vue d’ensemble de l’analyse d’image](../../overview-image-analysis.md)
* Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ImageAnalysisQuickstart.go).

