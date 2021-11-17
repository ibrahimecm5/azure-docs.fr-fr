---
title: Charger des fichiers sur Azure IoT Hub à partir d’appareils avec .NET | Microsoft Docs
description: Comment charger des fichiers sur le cloud à partir d’un appareil avec Azure IoT device SDK pour .NET. Les fichiers téléchargés sont stockés dans un conteneur d’objets blob de stockage Azure.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/24/2021
ms.author: lizross
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: c797c2668a1af750f0cb1ae4a743f1bc4b9f3aac
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556094"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>Charger des fichiers sur le cloud à partir d’un appareil avec IoT Hub (.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ce didacticiel vous montre comment utiliser la fonctionnalité de chargement de fichiers de IoT Hub avec les kits de développement logiciel pour les appareils et services Azure IoT.NET.

Le guide de démarrage rapide [Envoyer des données de télémétrie à partir d’un appareil à un hub IoT](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp) et le tutoriel [Envoyer des messages cloud-à-appareil avec IoT Hub](iot-hub-csharp-csharp-c2d.md) illustrent les fonctionnalités de messages appareil-à-cloud et cloud-à-appareil de base offertes par IoT Hub. Le tutoriel [Configurer le routage des messages avec IoT Hub](tutorial-routing.md) décrit un moyen de stocker de façon fiable les messages appareil-à-cloud dans le stockage d’objets blob Microsoft Azure. Toutefois, dans certains scénarios, vous ne pouvez pas facilement mapper les données que vos appareils envoient dans des messages appareil-à-cloud relativement petits et acceptés par IoT Hub. Par exemple :

* Fichiers volumineux qui contiennent des images

* Vidéos

* Données de vibration échantillonnées à une fréquence élevée

* Un certain type de données prétraitées

Ces fichiers sont généralement traités par lot dans le cloud à l’aide d’outils tels que [Azure Data Factory](../data-factory/introduction.md) ou de la pile [Hadoop](../hdinsight/index.yml). Si vous avez besoin de charger des fichiers à partir d’un appareil, vous pouvez toujours exploiter la sécurité et la fiabilité d’IoT Hub. Ce didacticiel vous explique les procédures.

À la fin de ce didacticiel, vous exécutez deux applications console .NET :

* **FileUploadSample**. Cet appareil charge un fichier de stockage à l’aide d’un URI SAP fourni par votre IoT Hub. Vous exécuterez cette application à partir du référentiel d’exemples C# Azure IoT que vous téléchargez dans les conditions préalables.

* **ReadFileUploadNotification**. Cette application de service reçoit les notifications de chargement de fichier à partir de votre hub IoT. Vous allez créer cette application.

> [!NOTE]
> IoT Hub offre la prise en charge de nombreux langages (notamment C, Java, Python et JavaScript) et plateformes d’appareils par le biais des Kits Azure IoT device SDK. Pour obtenir des instructions pas à pas expliquant comment connecter votre appareil à Azure IoT Hub, voir le [Centre de développement Azure IoT](https://azure.microsoft.com/develop/iot).

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Prérequis

* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) en quelques minutes.

* Les exemples d’applications que vous exécutez dans cet article sont écrits à l’aide de C#. Pour les exemples C# Azure IoT, nous vous recommandons d’utiliser le SDK .NET Core 3.1 ou une version ultérieure sur votre machine de développement.

    Vous pouvez télécharger le Kit SDK .NET Core pour plusieurs plateformes sur [.NET](https://dotnet.microsoft.com/download).

    Vous pouvez vérifier la version actuelle du SDK .NET Core sur votre machine de développement à l’aide de la commande suivante :

    ```cmd/sh
    dotnet --version
    ```

* Téléchargez les exemples C# Azure IoT à partir de [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/main.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/main.zip) et décompressez l’archive ZIP.

* Vérifiez que le port 8883 est ouvert dans votre pare-feu. L’exemple décrit dans cet article utilise le protocole MQTT, qui communique via le port 8883. Ce port peut être bloqué dans certains environnements réseau professionnels et scolaires. Pour plus d’informations sur les différentes façons de contourner ce problème, consultez [Connexion à IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Créer un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Inscrire un nouvel appareil dans le hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-include-associate-storage.md)]

## <a name="upload-file-from-a-device-app"></a>Charger un fichier à partir d’une application d’appareil

Dans cet article, vous allez utiliser un exemple du référentiel d’exemples C# Azure IoT que vous avez téléchargé précédemment en tant qu’application d’appareil. Vous pouvez ouvrir les fichiers ci-dessous à l’aide de Visual Studio, Visual Studio Code ou un éditeur de texte de votre choix.  

L’exemple se trouve dans le **azure-iot-samples-csharp-main\iot-hub\Samples\device\FileUploadSample** sous le dossier dans lequel vous avez extrait les exemples C# Azure IoT.

Examinez le code dans **FileUpLoadSample.cs**. Ce fichier contient l’exemple de logique principale. Après la création d’un client de périphérique IoT Hub, il suit la procédure en trois parties standard pour le téléchargement de fichiers à partir d’un appareil :

1. Le code appelle la méthode **GetFileUploadSasUriAsync** sur le client de l’appareil pour obtenir un URI SAS à partir du Hub IOT :

    ```csharp
    var fileUploadSasUriRequest = new FileUploadSasUriRequest
    {
        BlobName = fileName
    };

    // Lines removed for clarity

    FileUploadSasUriResponse sasUri = await _deviceClient.GetFileUploadSasUriAsync(fileUploadSasUriRequest);
    Uri uploadUri = sasUri.GetBlobUri();
    ```

1. Le code utilise l’URI SAS pour télécharger le fichier dans le stockage Azure. Dans cet exemple, il utilise l’URI SAS pour créer un client d’objet blob de blocs Azure Storage et charge le fichier :

    ```csharp
    var blockBlobClient = new BlockBlobClient(uploadUri);
    await blockBlobClient.UploadAsync(fileStreamSource, new BlobUploadOptions());
    ```

1. Le code informe l’IoT Hub qu’il a terminé le téléchargement. Cela indique à IoT Hub qu’il peut libérer les ressources associées au téléchargement (l’URI SAS). Si les notifications de téléchargement de fichier sont activées, IoT Hub envoie un message de notification aux services principaux.

    ```csharp
    var successfulFileUploadCompletionNotification = new FileUploadCompletionNotification
    {
        // Mandatory. Must be the same value as the correlation id returned in the sas uri response
        CorrelationId = sasUri.CorrelationId,
    
        // Mandatory. Will be present when service client receives this file upload notification
        IsSuccess = true,
    
        // Optional, user defined status code. Will be present when service client receives this file upload notification
        StatusCode = 200,
    
        // Optional, user-defined status description. Will be present when service client receives this file upload notification
        StatusDescription = "Success"
    };
    
    await _deviceClient.CompleteFileUploadAsync(successfulFileUploadCompletionNotification);
    ```

Si vous examinez le fichier **parameter.cs** , vous verrez que :

- L’exemple requiert que vous passiez un paramètre, *p*, qui prend une chaîne de connexion d’appareil. 

- Par défaut, l’exemple d’appareil utilise le protocole MQTT pour communiquer avec IoT Hub. Vous pouvez utiliser le paramètre *t* pour modifier ce protocole de transport. Sachez que, indépendamment de cette sélection, le client d’objet BLOB Azure utilise toujours HTTPS comme protocole pour télécharger le fichier stockage Azure.

## <a name="get-the-iot-hub-connection-string"></a>Obtenir la chaîne de connexion du hub IoT

Dans cet article, vous créez un service back-end pour recevoir les messages de notification de chargement de fichiers depuis votre hub IoT. Pour recevoir les messages de notification de chargement de fichiers, votre service a besoin de l'autorisation de **connexion de service**. Par défaut, chaque IoT Hub est créé avec une stratégie d’accès partagé nommée **service** qui accorde cette autorisation.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Recevoir une notification de téléchargement de fichier

Dans cette section, vous allez créer une application console C# qui reçoit des messages de notification de chargement de fichiers envoyés par votre Hub IoT.

1. Ouvrez une fenêtre de commande et accédez au dossier où vous voulez créer le projet. Créez un dossier nommé **ReadFileUploadNotifications** et modifiez les répertoires de ce dossier.

    ```cmd/sh
    mkdir ReadFileUploadNotification
    cd ReadFileUploadNotification
    ```

1. Exécutez la commande suivante pour créer un projet de console C#. Une fois la commande exécutée, le dossier contient un fichier **Program.cs** et un fichier **ReadFileUploadNotification. csproj** .

    ```cmd/sh
    dotnet new console --language c#
    ```

1. Exécutez la commande suivante pour ajouter le package **Microsoft. Azure.Devices** au fichier projet. Ce package est le kit de développement logiciel (SDK) du service Azure IoT.NET.

    ```cmd/sh
    dotnet add package Microsoft.Azure.Devices
    ```

1. Ouvrez le fichiez **Program.cs** et spécifiez au début de celui-ci les instructions suivantes :

    ```csharp
    using Microsoft.Azure.Devices;
    ```
1. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur de l’espace réservé `{iot hub connection string}` par la chaîne de connexion de l’IoT Hub que vous avez précédemment copiée dans [Obtention de la chaîne de connexion de l’IoT Hub](#get-the-iot-hub-connection-string) :

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. Ajoutez la méthode suivante à la classe **Program** :

    ```csharp
    private async static void ReceiveFileUploadNotificationAsync()
    {
        var notificationReceiver = serviceClient.GetFileNotificationReceiver();
        Console.WriteLine("\nReceiving file upload notification from service");
        while (true)
        {
            var fileUploadNotification = await notificationReceiver.ReceiveAsync();
            if (fileUploadNotification == null) continue;
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received file upload notification: {0}", 
              string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();
            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    Notez que ce modèle de réception est le même que celui utilisé pour recevoir des messages cloud-à-appareil à partir de l’application de l’appareil.

1. Enfin, remplacez les lignes de la méthode **Main** par les éléments suivants :

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```
    
## <a name="run-the-applications"></a>Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

1. Tout d’abord, exécutez l’application de service pour recevoir des notifications de téléchargement de fichier à partir d’IoT Hub. Depuis votre invite de commandes, dans le dossier **ReadFileUploadNotification**, exécutez les commandes suivantes : 

    ```cmd/sh
    dotnet restore
    dotnet run
    ```
    
    L’application démarre et attend une notification de téléchargement de fichier à partir de votre IoT Hub :

    ```cmd/sh
    Receive file upload notifications


    Receiving file upload notification from service
    Press Enter to exit
    ```



1. Ensuite, exécutez l’application de l’appareil pour télécharger le fichier dans le stockage Azure. Ouvrez une nouvelle invite de commandes et modifiez les dossiers **azure-iot-samples-csharp-main\iot-hub\Samples\device\FileUploadSample** sous le dossier dans lequel vous avez développé les exemples C# Azure IoT. Exécutez les commandes suivantes : Remplacez la valeur d’espace réservé `{Your device connection string}` de la deuxième commande par la chaîne de connexion de l’appareil que vous avez précédemment copiée dans [Inscrire un nouvel appareil dans le hub IoT](#register-a-new-device-in-the-iot-hub).

    ```cmd/sh
    dotnet restore
    dotnet run --p "{Your device connection string}"
    ```
    
    La sortie suivante provient de l’application d’appareil une fois le téléchargement terminé :
    
    ```cmd/sh
      Uploading file TestPayload.txt
      Getting SAS URI from IoT Hub to use when uploading the file...
      Successfully got SAS URI (https://contosostorage.blob.core.windows.net/contosocontainer/MyDevice%2FTestPayload.txt?sv=2018-03-28&sr=b&sig=x0G1Baf%2BAjR%2BTg3nW34zDNKs07p6dLzkxvZ3ZSmjIhw%3D&se=2021-05-04T16%3A40%3A52Z&sp=rw) from IoT Hub
      Uploading file TestPayload.txt using the Azure Storage SDK and the retrieved SAS URI for authentication
      Successfully uploaded the file to Azure Storage
      Notified IoT Hub that the file upload succeeded and that the SAS URI can be freed.
      Time to upload file: 00:00:01.5077954.
      Done.
    ```
    
1. Notez que l’application de service indique qu’elle a reçu la notification de téléchargement de fichier :

    ```cmd/sh
    Receive file upload notifications
    
    
    Receiving file upload notification from service
    Press Enter to exit
    
    Received file upload notification: myDeviceId/TestPayload.txt
    ```

## <a name="verify-the-file-upload"></a>Vérifier le chargement du fichier

Vous pouvez utiliser le portail pour afficher le fichier chargé dans le conteneur de stockage que vous avez configuré :

1. Accédez à votre compte de stockage dans le portail Azure.
1. Sur le volet gauche de votre compte de stockage, sélectionnez **Conteneurs**.
1. Sélectionnez le conteneur dans lequel vous avez chargé le fichier.
1. Sélectionnez le dossier portant le nom de votre appareil.
1. Sélectionnez l’objet blob dans lequel vous avez chargé votre fichier. Dans cet article, il s’agit de l’objet blob nommé **TestPayload.txt**.  

    :::image type="content" source="./media/iot-hub-csharp-csharp-file-upload/view-uploaded-file.png" alt-text="Capture d’écran de la sélection du fichier chargé dans le portail Azure.":::

1. Affichez les propriétés de l’objet blob sur la page qui s’ouvre. Vous pouvez sélectionner **Télécharger** pour télécharger le fichier et visualiser son contenu localement.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à utiliser les fonctionnalités de téléchargement de fichier d’IoT Hub pour simplifier les chargements de fichiers à partir d’appareils. Vous pouvez approfondir cette fonctionnalité en lisant les articles suivants :

* [Vue d’ensemble des chargements de fichiers avec IoT Hub](iot-hub-devguide-file-upload.md)

* [Configurer les chargements de fichiers IoT Hub](iot-hub-configure-file-upload.md)

* [Documentation Stockage Blob Azure](../storage/blobs/storage-blobs-introduction.md)

* [Informations de référence sur les API Stockage Blob Azure](../storage/blobs/reference.md)

* [Kits de développement logiciel (SDK) Azure IoT](iot-hub-devguide-sdks.md)