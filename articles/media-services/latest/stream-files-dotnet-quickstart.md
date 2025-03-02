---
title: Diffuser en continu des fichiers vidéo avec Azure Media Services - .NET
description: Suivez les étapes de ce tutoriel utiliser .NET afin de créer un compte Azure Media Services, encoder un fichier et le streamer vers le Lecteur multimédia Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: azure media services, diffuser en continu
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc
ms.date: 07/23/2021
ms.author: inhenkel
ms.openlocfilehash: 079575148585c508224b6767b2eba61a16463fa0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732739"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---net"></a>Tutoriel : Encoder un fichier distant basé sur une URL et diffuser la vidéo en continu - .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ce tutoriel vous montre combien il est facile de coder et de lancer la diffusion en continu de vidéos sur une grande variété de navigateurs et d’appareils à l’aide d’Azure Media Services. Un contenu d’entrée peut être spécifié à l’aide d’URL HTTPS, d’URL SAP ou de chemins d’accès aux fichiers situés dans le Stockage Blob Azure.
Dans cette rubrique, l’exemple encode du contenu que vous mettez à disposition via une URL HTTPS. Notez qu’AMS v3 ne prend pas en charge l’encodage de transfert mémorisé en bloc sur les URL HTTPS.

À la fin du tutoriel, vous serez en mesure de diffuser en continu une vidéo.  

![Lire la vidéo](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

- Installez [Visual Studio Code pour Windows/macOS/Linux](https://code.visualstudio.com/) ou [Visual Studio 2019 pour Windows ou Mac](https://visualstudio.microsoft.com/).
- Installer le [Kit de développement logiciel (SDK) .NET 5.0](https://dotnet.microsoft.com/download)
- [Créer un compte Media Services](./account-create-how-to.md). Veillez à copier les détails de l’**accès à l’API** au format JSON ou à stocker les valeurs nécessaires pour se connecter au compte Media Services dans le format de fichier *.env* utilisé dans cet exemple.
- Suivez les étapes décrites dans [Accéder à l’API Azure Media Services avec Azure CLI](./access-api-howto.md). Assurez-vous d’*enregistrer les informations d’identification*. Vous devez les utiliser pour accéder à l’API dans cet exemple ou les entrer dans le format de fichier *.env*.

## <a name="download-and-configure-the-sample"></a>Télécharger et configurer l’exemple

Clonez un référentiel GitHub qui contient l’exemple .NET de diffusion en continu sur votre machine à l’aide de la commande suivante :  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts.git
 ```

L’exemple se trouve dans le dossier [EncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/tree/master/AMSV3Quickstarts/EncodeAndStreamFiles) sous AMSV3Quickstarts.

[!INCLUDE [appsettings or .env file](./includes/note-appsettings-or-env-file.md)]

L’exemple effectue les actions suivantes :

1. Crée une **transformation** (vérifie d’abord si la transformation spécifiée existe). 
2. Crée un **actif multimédia** de sortie qui est utilisé comme sortie du **travail** d’encodage.
3. Crée l’entrée du **travail** qui est basée sur une URL HTTPS.
4. Soumet le **travail** d’encodage à l’aide de l’entrée et de la sortie créées précédemment.
5. Vérifie l’état du travail.
6. Crée un **localisateur de streaming**.
7. Crée des URL de diffusion en continu.

Pour obtenir des explications sur chaque fonction dans l’exemple, examinez le code et lisez les commentaires dans [ce fichier source](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="run-the-sample-app"></a>Exécution de l'exemple d'application

Lorsque vous exécutez l’application, les URL qui peuvent être utilisées pour lire la vidéo à l’aide de différents protocoles s’affichent. 

1. Ouvrez AMSV3Quickstarts dans VSCode.
2. Appuyez sur Ctrl + F5 pour exécuter l’application *EncodeAndStreamFiles* avec .NET. Cela peut prendre quelques minutes.
3. L’application génèrera trois URL. Vous allez utiliser ces URL pour tester le flux à l’étape suivante.

![Capture d’écran de la sortie de l’application EncodeAndStreamFiles dans Visual Studio montrant trois URL de streaming à utiliser dans le Lecteur multimédia Azure.](./media/stream-files-tutorial-with-api/output.png)

Le [code source](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) de l’exemple montre comment l’URL est construite. Pour générer cette URL, vous devez concaténer le nom d’hôte du point de terminaison de streaming et le chemin d’accès au localisateur de diffusion en continu.  

## <a name="test-with-azure-media-player"></a>Test avec Azure Media Player

Pour tester la diffusion en continu, cet article utilise le lecteur multimédia Azure. 

> [!NOTE]
> Si un lecteur est hébergé sur un site https, veillez à mettre à jour l’URL vers « https ».

1. Ouvrez un navigateur web et accédez à [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Dans le champ **URL :** , collez l’une des valeurs d’URL de diffusion en continu que vous avez obtenues lors de l’exécution de l’application. 
 
     Vous pouvez coller l’URL dans le format HLS, Dash ou Smooth : le lecteur multimédia Azure passe automatiquement à un protocole de streaming approprié pour la lecture sur votre appareil.
3. Appuyez sur **Mise à jour du Lecteur Windows Media**. Ce fichier vidéo doit commencer à être lu dans le référentiel.

Le lecteur multimédia Azure peut être utilisé pour effectuer des tests, mais ne doit pas être utilisé dans un environnement de production. 

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin des ressources de votre groupe de ressources, notamment les comptes Media Services et de stockage que vous avez créés dans ce tutoriel rapide, supprimez le groupe de ressources.

Exécutez la commande CLI suivante :

```azurecli
az group delete --name amsResourceGroup
```

## <a name="examine-the-code"></a>Examiner le code

Pour obtenir des explications sur chaque fonction dans l’exemple, examinez le code et lisez les commentaires dans [ce fichier source](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

Le tutoriel [Charger, encoder et diffuser en continu des fichiers](stream-files-tutorial-with-api.md) vous donne un exemple de diffusion en continu plus élaboré, avec des explications détaillées. 

### <a name="job-error-codes"></a>Codes d’erreur des tâches

Consultez [Codes d’erreur](/rest/api/media/jobs/get#joberrorcode).

## <a name="multithreading"></a>Multithreading

Les Kits de développement logiciel (SDK) Azure Media Services v3 ne sont pas thread-safe. Lorsque vous travaillez avec une application multithread, vous devez générer un nouvel objet AzureMediaServicesClient par thread.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Charger, encoder et diffuser en continu des fichiers](stream-files-tutorial-with-api.md)
