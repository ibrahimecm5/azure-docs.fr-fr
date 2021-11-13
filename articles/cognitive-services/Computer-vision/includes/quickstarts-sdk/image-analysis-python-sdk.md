---
title: "Démarrage rapide : Bibliothèque de client Analyse d'images pour Python"
description: Utilisez ce guide de démarrage rapide pour vous familiariser avec la bibliothèque de client Analyse d'images pour Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 3f6425edff78fd74bfa875d9c0978ece281b7237
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520383"
---
<a name="HOLTop"></a>

Utilisez la bibliothèque de client Analyse d'images pour rechercher des étiquettes, une description textuelle, des visages, du contenu pour adultes ou autre dans une image.

[Documentation de référence](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-computervision) | [Package (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-computervision/) | [Exemples C#](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * Votre installation Python doit inclure [pip](https://pip.pypa.io/en/stable/). Vous pouvez vérifier si pip est installé en exécutant `pip --version` sur la ligne de commande. Procurez-vous pip en installant la dernière version de Python.
* Une fois que vous avez votre abonnement Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Créer une ressource Vision par ordinateur"  target="_blank">créez une ressource Vision par ordinateur </a> dans le portail Azure pour obtenir votre clé et votre point de terminaison. Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.

    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application au service Vision par ordinateur. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

## <a name="setting-up"></a>Configuration

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Vous pouvez installer la bibliothèque de client avec :

```console
pip install --upgrade azure-cognitiveservices-vision-computervision
```

Installez aussi la bibliothèque Pillow.

```console
pip install pillow
```

### <a name="create-a-new-python-application"></a>Créer une application Python

Créez un fichier Python&mdash;*quickstart-file.py*, par exemple. Puis, ouvrez-le dans votre éditeur ou IDE habituel et importez les bibliothèques suivantes.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_imports)]

> [!TIP]
> Vous voulez voir l’intégralité du fichier de code de démarrage rapide à la fois ? Vous le trouverez sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ImageAnalysisQuickstart.py), qui contient les exemples de code utilisés dans ce guide de démarrage rapide.

Ensuite, créez des variables pour le point de terminaison et la clé Azure de votre ressource.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_vars)]

> [!IMPORTANT]
> Accédez au portail Azure. Si la ressource Vision par ordinateur que vous avez créée dans la section **Prérequis** a été déployée, cliquez sur le bouton **Accéder à la ressource** sous **Étapes suivantes**. La clé et le point de terminaison se trouvent dans la page **Clé et point de terminaison** de la ressource, sous **Gestion des ressources**. 
>
> N’oubliez pas de supprimer la clé de votre code une fois que vous avez terminé, et ne la postez jamais publiquement. Pour la production, envisagez d’utiliser une méthode de stockage et d’accès sécurisée pour vos informations d’identification. Par exemple, [Azure Key Vault](../../../../key-vault/general/overview.md).

> [!div class="nextstepaction"]
> [J’ai configuré les variables](?success=set-up-client#object-model) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=set-up-client&product=computer-vision&page=image-analysis-python-sdk)

## <a name="object-model"></a>Modèle objet

Les classes et interfaces suivantes prennent en charge certaines des fonctionnalités principales du kit SDK Analyse d'images pour Python.

|Nom|Description|
|---|---|
|[ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin)| Cette classe prend en charge directement toutes les opérations relatives aux images, par exemple l’analyse d’image, la détection de texte et la génération de miniatures.|
| [ComputerVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient) | Cette classe est nécessaire pour toutes les fonctionnalités de Vision par ordinateur. Vous pouvez l’instancier avec vos informations d’abonnement et l’utiliser pour produire des instances d’autres classes. Elle implémente **ComputerVisionClientOperationsMixin**.|
|[VisualFeatureTypes](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| Cette énumération définit les différents types d’analyse d’image qui peuvent être effectués dans le cadre d’une opération d’analyse standard. Vous spécifiez un ensemble de valeurs **VisualFeatureTypes** en fonction de vos besoins. |

## <a name="code-examples"></a>Exemples de code

Ces extraits de code vous montrent comment effectuer les tâches suivantes avec la bibliothèque de client Analyse d'images pour Python :

* [Authentifier le client](#authenticate-the-client)
* [Analyser une image](#analyze-an-image)

## <a name="authenticate-the-client"></a>Authentifier le client

Instanciez un client avec votre point de terminaison et la clé. Créez un objet [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) à l’aide de votre clé, puis utilisez-le avec votre point de terminaison pour créer un objet [ComputerVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient).

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_client)]

> [!div class="nextstepaction"]
> [J’ai authentifié le client](?success=authenticate-client#analyze-an-image) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=authenticate-client&product=computer-vision&page=image-analysis-python-sdk)

## <a name="analyze-an-image"></a>Analyser une image

Utilisez votre objet client pour analyser les fonctionnalités visuelles d’une image distante. Tout d’abord, enregistrez une référence à l’URL d’une image que vous souhaitez analyser.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_remoteimage)]

> [!TIP]
> Vous pouvez également analyser une image locale. Consultez les méthodes [ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin), comme **analyze_image_in_stream**. Ou consultez l’exemple de code sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ImageAnalysisQuickstart.py) pour obtenir des scénarios impliquant des images locales.

### <a name="get-image-description"></a>Obtenir une description d’image

Le code suivant obtient la liste des légendes générées pour l’image. Pour plus d’informations, consultez [Décrire des images](../../concept-describing-images.md).

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_describe)]

### <a name="get-image-category"></a>Obtenir une catégorie d’image

Le code suivant obtient la catégorie détectée de l’image. Pour plus d’informations, consultez [Classer des images](../../concept-categorizing-images.md).

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_categorize)]

### <a name="get-image-tags"></a>Obtenir des étiquettes d’image

Le code suivant obtient l’ensemble d’étiquettes détectées dans l’image. Pour plus d’informations, consultez [Étiquettes de contenu](../../concept-tagging-images.md).

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_tags)]

### <a name="detect-objects"></a>Détecter des objets

Le code suivant détecte les objets courants présents dans l’image et les affiche sur la console. Pour plus d’informations, consultez [Détection d’objets](../../concept-object-detection.md).

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_objects)]        

### <a name="detect-brands"></a>Détecter les marques

Le code suivant détecte les marques et logos d’entreprise dans l’image, et les affiche sur la console. Pour plus d’informations, consultez [Détection des marques](../../concept-brand-detection.md).

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_brands)]

### <a name="detect-faces"></a>Détecter des visages

Le code suivant retourne les visages détectés dans l’image avec les coordonnées de leur rectangle et sélectionne les attributs du visage. Pour plus d’informations, consultez [Détection des visages](../../concept-detecting-faces.md).

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Détecter des contenus pour adultes, choquants ou sordides

Le code suivant imprime la présence détectée de contenu pour adultes dans l’image. Pour plus d’informations, consultez [Contenu pour adultes choquant ou sordide](../../concept-detecting-adult-content.md).

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Obtenir le modèle de couleurs d’une image

Le code suivant imprime les attributs de couleur détectés dans l’image, comme les couleurs dominantes et la couleur d’accentuation. Pour plus d’informations, consultez [Modèles de couleurs](../../concept-detecting-color-schemes.md).

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Obtenir le contenu spécifique à un domaine

Analyse d'images peut utiliser un modèle spécialisé pour effectuer une analyse approfondie des images. Pour plus d’informations, consultez [Contenu spécifique à un domaine](../../concept-detecting-domain-content.md). 

Le code suivant analyse des données relatives aux célébrités détectées dans l’image.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_celebs)]

Le code suivant analyse des données relatives aux monuments détectés dans l’image.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Obtenir le type d’image

Le code suivant affiche des informations sur le type d’image, qu’il s’agisse d’une image clipart ou d’un dessin au trait.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_type)]

> [!div class="nextstepaction"]
> [J’ai analysé une image](?success=analyze-image#run-the-application) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=analyze-image&product=computer-vision&page=image-analysis-python-sdk)



## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `python` de votre fichier de démarrage rapide.

```console
python quickstart-file.py
```

> [!div class="nextstepaction"]
> [J’ai exécuté l’application](?success=run-the-application#clean-up-resources) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=run-the-application&product=computer-vision&page=image-analysis-python-sdk)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [J’ai nettoyé des ressources](?success=clean-up-resources#next-steps) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=clean-up-resources&product=computer-vision&page=image-analysis-python-sdk)

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à installer la bibliothèque de client Analyse d’image et à effectuer des appels d’analyse d’image de base. À présent, découvrez-en plus sur les fonctionnalités de l’API Analyser.

> [!div class="nextstepaction"]
>[Appeler l’API Analyser](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Vue d’ensemble de l’analyse d’image](../../overview-image-analysis.md)
* Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ImageAnalysisQuickstart.py).
