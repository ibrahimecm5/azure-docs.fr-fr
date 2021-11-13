---
title: 'Démarrage rapide : Bibliothèque de client Reconnaissance optique de caractères pour Go'
titleSuffix: Azure Cognitive Services
description: Ce guide de démarrage rapide vous permettra de vous familiariser avec la bibliothèque de client Reconnaissance optique de caractères pour Go.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: a6dbc2b54c8fac9f2b1bdb219784f873d99432f8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520529"
---
<a name="HOLTop"></a>

Utilisez la bibliothèque de client OCR pour lire du texte imprimé et manuscrit à partir d'images.

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

> [!TIP]
> Vous voulez voir l’intégralité du fichier de code de démarrage rapide à la fois ? Vous le trouverez sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go), qui contient les exemples de code utilisés dans ce guide de démarrage rapide.

Ouvrez `sample-app.go` dans l’éditeur de texte ou l’environnement de développement intégré de votre choix.

Déclarez un contexte à la racine de votre script. Vous aurez besoin de cet objet pour exécuter la plupart des appels de fonction Vision par ordinateur.

### <a name="find-the-subscription-key-and-endpoint"></a>Rechercher la clé d’abonnement et le point de terminaison

Accédez au portail Azure. Si la ressource Vision par ordinateur que vous avez créée dans la section **Prérequis** a été déployée, cliquez sur le bouton **Accéder à la ressource** sous **Étapes suivantes**. Votre clé d’abonnement et votre point de terminaison se trouvent dans la page **Clé et point de terminaison** de la ressource, sous **Gestion des ressources**. 

Créez des variables pour votre clé d’abonnement et votre point de terminaison Vision par ordinateur. Collez votre clé d’abonnement et votre point de terminaison dans le code suivant à l’endroit indiqué. Votre point de terminaison Vision par ordinateur a le format `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/`.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> N’oubliez pas de supprimer la clé d’abonnement de votre code une fois que vous avez terminé, et ne la publiez jamais publiquement. Pour la production, envisagez d’utiliser une méthode de stockage et d’accès sécurisée pour vos informations d’identification. Par exemple, [Azure Key Vault](../../../../key-vault/general/overview.md).

Ensuite, vous commencerez à ajouter du code pour effectuer différentes opérations OCR.

> [!div class="nextstepaction"]
> [J’ai configuré les variables](?success=set-up-client#object-model) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=set-up-client&product=computer-vision&page=go-sdk)

## <a name="object-model"></a>Modèle objet

Les classes et interfaces suivantes gèrent certaines des principales fonctionnalités du SDK OCR Go.

|Nom|Description|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Cette classe est nécessaire pour toutes les fonctionnalités Vision par ordinateur, telles que l’analyse d’images et la lecture de texte. Vous l’instanciez avec vos informations d’abonnement, et vous l’utilisez pour effectuer la plupart des opérations relatives aux images.|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| Ce type contient les résultats d’une opération de lecture par lots. |

## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les tâches suivantes avec la bibliothèque de client OCR pour Go :

* [Authentifier le client](#authenticate-the-client)
* [Lire du texte imprimé et manuscrit](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Authentifier le client

> [!NOTE]
> Cette étape suppose que vous avez [créé des variables d’environnement](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) pour votre clé et votre point de terminaison Vision par ordinateur nommées `COMPUTER_VISION_SUBSCRIPTION_KEY` et `COMPUTER_VISION_ENDPOINT`.

Créez une fonction `main` et ajoutez-lui le code suivant pour instancier un client avec votre point de terminaison et votre clé.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> [J’ai authentifié le client](?success=authenticate-client#read-printed-and-handwritten-text) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=authenticate-client&product=computer-vision&page=go-sdk)



## <a name="read-printed-and-handwritten-text"></a>Lire du texte imprimé et manuscrit

Le service OCR peut lire du texte visible dans une image et le convertir en flux de caractères. Le code de cette section définit une fonction (`RecognizeTextReadAPIRemoteImage`) qui utilise l’objet client pour détecter et extraire du texte imprimé ou manuscrit dans l’image.

Ajoutez la référence et l’appel de fonction de l’exemple d’image dans votre fonction `main`.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!TIP]
> Vous pouvez également extraire du texte d’une image locale. Consultez les méthodes [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient), telles que **BatchReadFileInStream**. Ou consultez l’exemple de code sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) pour obtenir des scénarios impliquant des images locales.

### <a name="call-the-read-api"></a>Appeler l’API Lire

Définissez la nouvelle fonction de lecture du texte (`RecognizeTextReadAPIRemoteImage`). Ajoutez le code ci-dessous afin d’appeler la méthode **BatchReadFile** pour l’image donnée. Cette méthode retourne un ID d’opération et démarre un processus asynchrone pour lire le contenu de l’image.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>Obtenir les résultats de la lecture

Récupérez ensuite l’ID d’opération retourné à partir de l’appel de **BatchReadFile**, puis utilisez la méthode **GetReadOperationResult** pour interroger le service et obtenir les résultats de l’opération. Le code suivant vérifie l’opération par intervalles d’une seconde jusqu’à ce que les résultats soient retournés. Il affiche ensuite les données textuelles extraites sur la console.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>Afficher les résultats de la lecture

Ajoutez le code suivant pour analyser et afficher les données textuelles récupérées, puis achevez la définition de la fonction.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

> [!div class="nextstepaction"]
> [J’ai lu du texte](?success=read-printed-handwritten-text#run-the-application) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=read-printed-handwritten-text&product=computer-vision&page=go-sdk)

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application à partir de votre répertoire d’application avec la commande `go run`.

```bash
go run sample-app.go
```

> [!div class="nextstepaction"]
> [J’ai exécuté l’application](?success=run-the-application#clean-up-resources) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=run-the-application&product=computer-vision&page=go-sdk)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [J’ai nettoyé des ressources](?success=clean-up-resources#next-steps) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=clean-up-resources&product=computer-vision&page=go-sdk)

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à installer la bibliothèque de client OCR et à utiliser l’API Lire. À présent, découvrez-en plus sur les fonctionnalités de l’API Lire.

> [!div class="nextstepaction"]
>[Appeler l’API Lire](../../Vision-API-How-to-Topics/call-read-api.md)

* [Vue d’ensemble de la reconnaissance OCR](../../overview-ocr.md)
* Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).
