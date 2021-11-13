---
title: 'Démarrage rapide : Bibliothèque de client Reconnaissance optique de caractères pour Node.js'
description: Ce guide de démarrage rapide vous permettra de vous familiariser avec la bibliothèque de client Reconnaissance optique de caractères pour Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: 2260df8403a27d57f7bd5b9fe7612147c935f7d7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520047"
---
<a name="HOLTop"></a>

Utilisez la bibliothèque de client Reconnaissance optique de caractères pour lire des textes imprimés et manuscrits à l'aide de l'API Read.

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

> [!TIP]
> Vous voulez voir l’intégralité du fichier de code de démarrage rapide à la fois ? Vous le trouverez sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js), qui contient les exemples de code utilisés dans ce guide de démarrage rapide.

Créez un fichier, *index.js*, puis ouvrez-le dans un éditeur de texte.

### <a name="find-the-subscription-key-and-endpoint"></a>Rechercher la clé d’abonnement et le point de terminaison

Accédez au portail Azure. Si la ressource Vision par ordinateur que vous avez créée dans la section **Prérequis** a été déployée, cliquez sur le bouton **Accéder à la ressource** sous **Étapes suivantes**. Votre clé d’abonnement et votre point de terminaison se trouvent dans la page **Clé et point de terminaison** de la ressource, sous **Gestion des ressources**. 

Créez des variables pour votre clé d’abonnement et votre point de terminaison Vision par ordinateur. Collez votre clé d’abonnement et votre point de terminaison dans le code suivant à l’endroit indiqué. Votre point de terminaison Vision par ordinateur a le format `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/`.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> N’oubliez pas de supprimer la clé d’abonnement de votre code une fois que vous avez terminé, et ne la publiez jamais publiquement. Pour la production, envisagez d’utiliser une méthode de stockage et d’accès sécurisée pour vos informations d’identification. Par exemple, [Azure Key Vault](../../../../key-vault/general/overview.md).

> [!div class="nextstepaction"]
> [J’ai configuré les variables](?success=set-up-client#object-model) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=set-up-client&product=computer-vision&page=node-sdk)

## <a name="object-model"></a>Modèle objet

Les classes et interfaces suivantes gèrent certaines des principales fonctionnalités du SDK OCR Node.js.

|Nom|Description|
|---|---|
| [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) | Cette classe est nécessaire pour toutes les fonctionnalités de Vision par ordinateur. Vous l’instanciez avec vos informations d’abonnement, et vous l’utilisez pour effectuer la plupart des opérations relatives aux images.|

## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les tâches suivantes avec la bibliothèque de client OCR pour Node.js :

* [Authentifier le client](#authenticate-the-client)
* [Lire du texte imprimé et manuscrit](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Authentifier le client


Instanciez un client avec votre point de terminaison et la clé. Créez un objet [ApiKeyCredentials](/python/api/msrest/msrest.authentication.apikeycredentials) à l’aide de votre clé et point de terminaison, puis utilisez-le pour créer un objet [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

Ensuite, définissez une fonction `computerVision` et déclarez une série asynchrone avec la fonction principale et la fonction de rappel. À la fin du script, vous allez terminer cette définition de fonction et l’appeler.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

> [!div class="nextstepaction"]
> [J’ai authentifié le client](?success=authenticate-client#read-printed-and-handwritten-text) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=authenticate-client&product=computer-vision&page=node-sdk)



## <a name="read-printed-and-handwritten-text"></a>Lire du texte imprimé et manuscrit

Le service OCR peut extraire le texte visible dans une image et le convertir en flux de caractères. Cet exemple utilise les opérations Read.

### <a name="set-up-test-images"></a>Configurer des images de test

Enregistrez une référence à l’URL des images à partir desquelles vous souhaitez extraire du texte.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

> [!NOTE]
> Vous pouvez également lire du texte à partir d’une image locale. Consultez les méthodes [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient), comme **readInStream**. Ou consultez l’exemple de code sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) pour obtenir des scénarios impliquant des images locales.

### <a name="call-the-read-api"></a>Appeler l’API Lire

Définissez les champs suivants dans votre fonction pour indiquer les valeurs de l’état de l’appel de Read.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_statuses)]

Ajoutez le code ci-dessous afin d’appeler la fonction `readTextFromURL` pour les images données.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

Définissez la fonction `readTextFromURL`. Cela appelle la méthode **read** sur l’objet client, qui retourne un ID d’opération et démarre un processus asynchrone pour lire le contenu de l’image. Elle se sert ensuite de l’ID d’opération pour vérifier l’état de l’opération jusqu’à ce que les résultats soient retournés. Elle retourne ensuite les résultats extraits.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

Enfin, définissez la fonction d’assistance `printRecText`, qui imprime les résultats de l’opération Read dans la console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

> [!div class="nextstepaction"]
> [J’ai lu du texte](?success=read-printed-handwritten-text#run-the-application) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=read-printed-handwritten-text&product=computer-vision&page=node-sdk)

## <a name="close-the-function"></a>Fermer la fonction

Fermez la fonction `computerVision` et appelez-la.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `node` de votre fichier de démarrage rapide.

```console
node index.js
```

> [!div class="nextstepaction"]
> [J’ai exécuté l’application](?success=run-the-application#clean-up-resources) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=run-the-application&product=computer-vision&page=node-sdk)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [J’ai nettoyé des ressources](?success=clean-up-resources#next-steps) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=clean-up-resources&product=computer-vision&page=node-sdk)

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à installer la bibliothèque de client OCR et à utiliser l’API Lire. À présent, découvrez-en plus sur les fonctionnalités de l’API Lire.

> [!div class="nextstepaction"]
>[Appeler l’API Lire](../../Vision-API-How-to-Topics/call-read-api.md)

* [Vue d’ensemble de la reconnaissance OCR](../../overview-ocr.md)
* Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js).
