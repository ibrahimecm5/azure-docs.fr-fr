---
title: Exécuter une fonction Azure en réponse à un événement de réactivation d’objet blob
titleSuffix: Azure Storage
description: Découvrez comment développer une fonction Azure avec .NET, puis configurer Azure Event Grid pour exécuter la fonction en réponse à un événement déclenché lorsqu’un objet blob est réactivé à partir du niveau archive.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/25/2021
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: 2385bfead10efc82e0a1f3c8f0f02f1cd8d9f2f4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131019264"
---
# <a name="run-an-azure-function-in-response-to-a-blob-rehydration-event"></a>Exécuter une fonction Azure en réponse à un événement de réactivation d’objet blob

Pour lire un objet blob qui se trouve dans le niveau Archive, vous devez d’abord réalimenter l’objet blob au niveau chaud ou froid. Le processus de réactivation peut prendre plusieurs heures à se terminer. Au lieu d’interroger à plusieurs reprises l’état de l’opération de réactivation, vous pouvez configurer [Azure Event Grid](../../event-grid/overview.md) pour déclencher un événement lorsque l’opération de réactivation de blob est complète et gérer cet événement dans votre application.

Lorsqu’un événement se produit, Event Grid envoie l’événement à un gestionnaire d’événements via un point de terminaison. Un certain nombre de services Azure peuvent servir de gestionnaires d’événements, y compris [Azure Fonctions](../../azure-functions/functions-overview.md). Une fonction Azure est un bloc de code qui peut s’exécuter en réponse à un événement. Cette procédure vous guide tout au long du processus de développement d’une fonction Azure, puis de configuration d’Event Grid pour exécuter la fonction en réponse à un événement qui se produit lorsqu’un objet blob est réactivé.

Cet article vous montre comment créer et tester une fonction Azure avec .NET à partir de Visual Studio. Vous pouvez créer des fonctions Azure à partir d’un large éventail d’environnements de développement locaux et de langages de programmation différents. Pour plus d’informations sur les langages pris en charge pour les fonctions Azure, voir [Langages pris en charge dans Azure Fonctions](../../azure-functions/supported-languages.md). Pour plus d’informations sur les options de développement pour les fonctions Azure, voir [Coder et tester Azure Functions localement](../../azure-functions/functions-develop-local.md).

Pour plus d’informations sur la réactivation des blobs à partir du niveau Archive, voir [Vue d’ensemble de la réactivation des objets blob à partir du niveau Archive](archive-rehydrate-overview.md).

## <a name="prerequisites"></a>Configuration requise

Cet article vous montre comment utiliser [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) pour développer une fonction Azure avec .NET. Vous pouvez installer gratuitement Visual Studio Community. Assurez-vous que vous [configurez Visual Studio pour un développement Azure avec .NET](/dotnet/azure/configure-visual-studio).

Pour déboguer la fonction Azure localement, vous devez utiliser un outil qui peut envoyer une requête HTTP, comme Postman.

Un [abonnement Azure](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) est requis. Si ce n’est déjà fait, [créez un compte gratuit](https://azure.microsoft.com/free/dotnet/) avant de commencer.

## <a name="create-an-azure-function-app"></a>Création d’une application Azure Function

Une application de fonction est une ressource Azure qui sert de conteneur à Azure Functions. Vous pouvez utiliser une application de fonction nouvelle ou existante pour effectuer les étapes décrites dans cet article.

Suivez ces étapes pour créer une nouvelle Function App dans le portail Azure :

1. Dans le portail Azure, recherchez *Function App*. Sélectionnez l’icône **Application de fonction** pour accéder à la liste des applications de fonction de votre abonnement.
1. Sélectionnez le bouton **Créer** pour créer une nouvelle application de fonction.
1. Sur l’onglet **Général**, spécifiez un groupe de ressources et fournissez un nom unique pour la nouvelle application de fonction.
1. Assurez-vous que l’option **Publier** est définie sur *Code*.
1. À partir de la liste déroulante **Pile d’exécution**, sélectionnez *.NET*. Le champ **Version** est automatiquement renseigné pour utiliser la dernière version de .NET Core.
1. Sélectionnez la région de la nouvelle application de fonction.

    :::image type="content" source="media/archive-rehydrate-handle-event/create-function-app-basics-tab.png" alt-text="Capture d’écran montrant comment créer une application de fonction dans Azure- Onglet Concepts de base":::

1. Après avoir terminé l’onglet **Général**, accédez à l’onglet **Hébergement**.
1. Sur l’onglet **Hébergement**, sélectionnez le compte de stockage où votre fonction Azure sera stockée. Vous pouvez choisir un compte de stockage ou en créer un.
1. Assurez-vous que le champ **Système d’exploitation** est défini sur  *Windows*.
1. Dans le champ **Type de plan**, sélectionnez *Consommation (serverless)* . Pour plus d’informations sur ce plan, voir [Hébergement du plan Consommation d’Azure Functions](../../azure-functions/consumption-plan.md).

    :::image type="content" source="media/archive-rehydrate-handle-event/create-function-app-hosting-tab.png" alt-text="Capture d’écran montrant comment créer une application de fonction dans Azure- Onglet Hébergement":::

1. Sélectionnez **Vérifier + créer** pour créer la nouvelle application de fonction.

Pour en savoir plus sur la configuration de votre application de fonction, consultez [Gérer votre application de fonction](../../azure-functions/functions-how-to-use-azure-function-app-settings.md) dans la documentation d’Azure Functions.

## <a name="create-an-azure-function-as-an-event-grid-trigger"></a>Créer une fonction Azure en tant que déclencheur Event Grid

Ensuite, créez une fonction Azure qui s’exécute quand un objet blob est réactivé dans un compte de stockage particulier. Pour créer une fonction Azure dans Visual Studio avec C# et .Net Core, procédez comme suit :

1. Lancez Visual Studio 2019 et créez un projet Azure Functions. Pour plus de détails, suivez les instructions décrites dans [Créer un projet d’application de fonction](../../azure-functions/functions-create-your-first-function-visual-studio.md#create-a-function-app-project).
1. À l’étape **Créer une nouvelle application Azure Functions**, sélectionnez les valeurs suivantes :
    - Par défaut, le runtime Azure Functions est défini sur **Azure Functions V3 (.NET Core)** . Microsoft recommande d’utiliser cette version du runtime Azure Functions.
    - Dans la liste des déclencheurs possibles, sélectionnez **Déclencheur Event Grid**. Pour plus d’informations sur la raison pour laquelle un déclencheur Event Grid est le type de déclencheur recommandé pour la gestion d’un événement Stockage d’objet blob avec une fonction Azure, consultez [Utiliser une fonction comme gestionnaire d’événements pour les événements Event Grid](../../event-grid/handler-functions.md).
    - Le paramètre **Compte de stockage**  indique l’endroit où votre fonction Azure sera stockée. Vous pouvez sélectionner un compte de stockage existant ou en créer un.
1. Sélectionnez **Créer** pour créer le nouveau projet dans Visual Studio.
1. Ensuite, renommez la classe et la fonction Azure, comme décrit dans [Renommer la fonction](../../azure-functions/functions-create-your-first-function-visual-studio.md#rename-the-function). Choisissez un nom approprié pour votre scénario.
1. Dans Visual Studio, sélectionnez **Outils** | **Gestionnaire de package NuGet** | **Console du gestionnaire de package**, puis installez les packages suivants à partir de la console :

    ```powershell
    Install-Package Azure.Storage.Blobs
    Install-Package Microsoft.ApplicationInsights.WorkerService
    Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights
    ```

1. Dans le fichier de classe de votre fonction Azure, collez les instructions suivantes :

    ```csharp
    using System;
    using System.IO;
    using System.Text;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.EventGrid.Models;
    using Microsoft.Azure.WebJobs.Extensions.EventGrid;
    using Microsoft.Extensions.Logging;
    using Azure;
    using Azure.Storage.Blobs;
    using Azure.Storage.Blobs.Models;
    ```

1. Localisez la méthode **Exécuter** dans le fichier de classe. Il s’agit de la méthode qui s’exécute lorsqu’un événement se produit. Remplacez le corps de la méthode **Run** par le code suivant. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

    ```csharp
    // When either Microsoft.Storage.BlobCreated or Microsoft.Storage.BlobTierChanged
    // event occurs, write the event details to a log blob in the same container
    // as the event subject (the blob for which the event occurred).

    // Create a unique name for the log blob.
    string logBlobName = string.Format("function-log-{0}.txt", DateTime.UtcNow.Ticks);

    // Populate connection string with your Shared Key credentials.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net";

    // Get data from the event.
    dynamic data = eventGridEvent.Data;
    string eventBlobUrl = Convert.ToString(data.url);
    string eventApi = Convert.ToString(data.api);

    // Build string containing log information.
    StringBuilder eventInfo = new StringBuilder();
    eventInfo.AppendLine(string.Format("{0} operation occurred.", eventApi));
    eventInfo.AppendLine(string.Format("Blob URL: {0}", eventBlobUrl));
    eventInfo.AppendLine($@"Additional event details:
        Id=[{eventGridEvent.Id}]
        EventType=[{eventGridEvent.EventType}]
        EventTime=[{eventGridEvent.EventTime}]
        Subject=[{eventGridEvent.Subject}]
        Topic=[{eventGridEvent.Topic}]");

    // If event was BlobCreated and API call was CopyBlob, respond to the event.
    bool copyBlobEventOccurred = (eventGridEvent.EventType == "Microsoft.Storage.BlobCreated") &&
                                 (eventApi == "CopyBlob");

    // If event was BlobTierChanged and API call was SetBlobTier, respond to the event.
    bool setTierEventOccurred = (eventGridEvent.EventType == "Microsoft.Storage.BlobTierChanged") &&
                                (eventApi == "SetBlobTier");

    // If one of these two events occurred, write event info to a log blob.
    if (copyBlobEventOccurred | setTierEventOccurred)
    {
        // Create log blob in same account and container.
        BlobUriBuilder logBlobUriBuilder = new BlobUriBuilder(new Uri(eventBlobUrl))
        {
            BlobName = logBlobName
        };

        BlobClient logBlobClient = new BlobClient(ConnectionString,
                                                  logBlobUriBuilder.BlobContainerName,
                                                  logBlobName);

        byte[] byteArray = Encoding.ASCII.GetBytes(eventInfo.ToString());

        try
        {
            // Write the log info to the blob.
            // Overwrite if the blob already exists.
            using (MemoryStream memoryStream = new MemoryStream(byteArray))
            {
                BlobContentInfo blobContentInfo =
                    logBlobClient.Upload(memoryStream, overwrite: true);
            }
        }
        catch (RequestFailedException e)
        {
            Console.WriteLine(e.Message);
            throw;
        }
    }
    ```

Pour plus d’informations sur le développement de fonctions Azure, voir [Guidage pour développer des fonctions Azure](../../azure-functions/functions-reference.md).

Pour en savoir plus sur les informations incluses lorsqu’un événement de Stockage Blob est publié sur un gestionnaire d’événements, voir [Stockage Blob Azure en tant que source Event Grid](../../event-grid/event-schema-blob-storage.md).

## <a name="run-the-azure-function-locally-in-the-debugger"></a>Exécuter la fonction Azure localement dans le débogueur

Pour tester votre code de fonction Azure localement, vous devez envoyer manuellement une requête HTTP qui déclenche l’événement. Vous pouvez envoyer la requête à l’aide d’un outil comme Postman.

En haut du fichier de classe de votre fonction Azure se trouve un point de terminaison d’URL que vous pouvez utiliser pour le test dans l’environnement local. La publication de la requête avec cette URL déclenche l’événement dans l’environnement local afin que vous puissiez déboguer votre code. L’URL présente au format suivant :

```http
http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
```

La requête que vous envoyez à ce point de terminaison est une requête simulée. Elle n’envoie ni ne reçoit de données de votre compte Stockage Azure.

Procédez comme suit pour construire et envoyer une requête à ce point de terminaison. Cet exemple montre comment envoyer la requête avec Postman.

1. Dans Postman, créez une requête.
1. Collez l’URL présentée ci-dessus dans le champ pour l’URL de la requête, en remplaçant le nom de votre fonction pour `{functionname}` et en supprimant les accolades bouclées. Assurez-vous que le verbe de requête est défini sur GET.

    :::image type="content" source="media/archive-rehydrate-handle-event/trigger-azure-function-postman-url.png" alt-text="Capture d’écran montrant comment spécifier l’URL locale pour le déclencheur d’événement dans Postman ":::

1. Ajoutez l’en-tête **Content-Type** et affectez-lui la valeur *application/json*.
1. Ajoutez l’en-tête **aeg-event-type** et réglez-le sur *Notification*.

    :::image type="content" source="media/archive-rehydrate-handle-event/trigger-azure-function-postman-headers.png" alt-text="Capture d’écran montrant la configuration d’en-tête pour une requête locale pour déclencher un événement":::

1. Dans Postman, spécifiez le corps de la requête, avec le type de corps défini sur *JSON* et le format sur *raw*. L’exemple suivant simule une requête **Copier un objet blob**. Remplacez les valeurs d’espace réservé entre crochets par vos propres valeurs. Notez qu’il n’est pas nécessaire de modifier les valeurs de date/heure ou d’identificateur, car il s’agit d’une requête simulée :

    ```json
    [{
      "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>",
      "subject": "/blobServices/default/containers/<container-name>/blobs/<blob-name>",
      "eventType": "Microsoft.Storage.BlobCreated",
      "id": "2bfb587b-501e-0094-2746-8b2884065d32",
      "data": {
        "api": "CopyBlob",
        "clientRequestId": "3d4dedc7-6c27-4816-9405-fdbfa806b00c",
        "requestId": "2bfb587b-501e-0094-2746-8b2884000000",
        "eTag": "0x8D9595DCA505BDF",
        "contentType": "text/plain",
        "contentLength": 48,
        "blobType": "BlockBlob",
        "url": "https://<storage-account>.blob.core.windows.net/<container-name>/<blob-name>",
        "sequencer": "0000000000000000000000000000201B00000000004092a5",
        "storageDiagnostics": {
          "batchId": "8a92736a-6006-0026-0046-8bd7f5000000"
        }
      },
      "dataVersion": "",
      "metadataVersion": "1",
      "eventTime": "2021-08-07T04:42:41.0730463Z"
    }]
    ```

1. Dans Visual Studio, placez les points d’arrêt souhaités dans votre code et appuyez sur **F5** pour exécuter le débogueur.
1. Dans Postman, sélectionnez le bouton **Envoyer** pour envoyer la requête au point de terminaison.

Lorsque vous envoyez la requête, Event Grid appelle votre fonction Azure et vous pouvez la déboguer normalement. Pour plus d’informations et d’exemples, voir [Publier manuellement la requête](../../azure-functions/functions-bindings-event-grid-trigger.md#manually-post-the-request) dans la documentation d’Azure Functions.

La requête qui déclenche l’événement est simulée, mais la fonction Azure qui s’exécute lorsque l’événement se déclenche enregistre les informations de journalisation dans un nouvel objet blob de votre compte de stockage. Vous pouvez vérifier le contenu de l’objet blob et afficher son heure de dernière modification dans le portail Azure, comme illustré dans l’image suivante :

:::image type="content" source="media/archive-rehydrate-handle-event/log-blob-contents-portal.png" alt-text="Capture d’écran montrant le contenu de l’objet blob de journal dans le portail Azure":::

## <a name="publish-the-azure-function"></a>Publier Azure Function

Une fois que vous avez testé votre fonction Azure localement, l’étape suivante consiste à publier la fonction Azure sur l’application de fonction Azure que vous avez créée précédemment. La fonction doit être publiée afin que vous puissiez configurer Event Grid pour envoyer les événements qui se produisent sur le compte de stockage au point de terminaison de la fonction.

Suivez ces étapes pour publier la fonction :

1. Dans l’Explorateur de solutions, sélectionnez votre projet Azure Functions et maintenez-le enfoncé, puis choisissez **Publier**.
1. Dans la fenêtre **Publier**, sélectionnez **Azure** comme cible, puis choisissez **Suivant**.
1. Sélectionnez **Application Azure Function** comme cible spécifique, puis choisissez **Suivant**.
1. Sur l’onglet **Instance Functions**, sélectionnez votre abonnement dans le menu déroulant, puis localisez votre application de fonction Azure dans la liste des applications de fonction disponibles.
1. Assurez-vous que la case à cocher **Exécuter à partir d’un fichier de package** est sélectionnée.
1. Sélectionnez **Terminer** pour vous préparer à publier la fonction.
1. Sur la page **Publier**, vérifiez que la configuration est correcte. Si vous voyez un avertissement indiquant que la dépendance de service à Application Insights n’est pas configurée, vous pouvez la configurer à partir de cette page.
1. Sélectionnez le bouton **Publier** pour commencer à publier la fonction Azure sur l’application de fonction Azure que vous avez créée précédemment.

    :::image type="content" source="media/archive-rehydrate-handle-event/visual-studio-publish-azure-function.png" alt-text="Capture d’écran montrant la page de publication d’une fonction Azure à partir de Visual Studio":::

Chaque fois que vous apportez des modifications au code de votre fonction Azure, vous devez publier la fonction mise à jour dans Azure.

## <a name="subscribe-to-blob-rehydration-events-from-a-storage-account"></a>S’abonner à des événements de réactivation d’objets blob à partir d’un compte de stockage

Vous disposez maintenant d’une application de fonction qui contient une fonction Azure qui peut s’exécuter en réponse à un événement. L’étape suivante consiste à créer un abonnement à un événement à partir de votre compte de stockage. L’abonnement aux événements configure le compte de stockage pour publier un événement via Event Grid en réponse à une opération sur un objet blob dans votre compte de stockage. Event Grid envoie ensuite l’événement au point de terminaison du gestionnaire d’événements que vous avez spécifié. Dans ce cas, le gestionnaire d’événements est la fonction Azure que vous avez créée dans la section précédente.

Lorsque vous créez l’abonnement aux événements, vous pouvez filtrer les événements qui sont envoyés au gestionnaire d’événements. Les événements à capturer lors de la réactivation d’un objet blob de niveau Archive sont **Microsoft.Storage.BlobTierChanged**, correspondant à une opération [Définir le niveau d’objet blob](/rest/api/storageservices/set-blob-tier), et **Microsoft.Storage.BlobCreated**, correspondant à une opération [Copier un objet blob](/rest/api/storageservices/copy-blob). Selon votre scénario, vous souhaiterez peut-être gérer un seul de ces événements.

Pour créer l’abonnement à l’événement, procédez comme suit :

1. Dans le portail Azure, accédez au compte de stockage qui contient les objets blob à réactiver à partir du niveau Archive.
1. Sélectionnez le paramètre **Événements** dans le volet de navigation de gauche.
1. Sur la page **Événements**, sélectionnez **Plus d’options**.
1. Sélectionnez **Créer un abonnement à un événement**.
1. Sur la page **Créer un abonnement à un événement** , dans la section **Détails de l’abonnement à un événement**, fournissez un nom pour l’abonnement à l’événement.
1. Dans la section **Détails sur la rubrique**, fournissez un nom pour la rubrique système. La rubrique système représente un ou plusieurs événements publiés par le Stockage Azure. Pour plus d’informations sur les rubriques système, voir [Rubriques système dans Azure Event Grid](../../event-grid/system-topics.md).
1. Dans la section **Types d’événement**, sélectionnez les événements **Objet blob créé** et **Niveau d’objet blob**. Selon la façon dont vous choisissez de réactiver un objet blob à partir du niveau Archive, l’un de ces deux événements se déclenche.

    :::image type="content" source="media/archive-rehydrate-handle-event/select-event-types-portal.png" alt-text="Capture d’écran montrant comment sélectionner les types d’événements pour les événements de réactivation des objets blob dans le portail Azure":::

1. Dans la section **Détails du point de terminaison**, sélectionnez *Azure Function* dans le menu déroulant.
1. Choisissez **Sélectionner un point de terminaison** pour spécifier la fonction que vous avez créée dans la section précédente. Dans la boîte de dialogue **Sélectionner une fonction Azure**, choisissez l’abonnement, le groupe de ressources et l’application de fonction pour votre fonction Azure. Enfin, sélectionnez le nom de la fonction dans la liste déroulante et choisissez **Confirmer la sélection**.

    :::image type="content" source="media/archive-rehydrate-handle-event/select-azure-function-endpoint-portal.png" alt-text="Capture d’écran montrant comment sélectionner une fonction Azure comme point de terminaison pour un abonnement Event Grid":::

1. Sélectionnez le bouton **Créer** pour créer l’abonnement à l’événement et commencer à envoyer des événements au gestionnaire d’événements d’Azure Function.

Pour en savoir plus sur les abonnements aux événements, consultez [ Concepts d’Azure Event Grid](../../event-grid/concepts.md#event-subscriptions).

## <a name="test-the-azure-function-event-handler"></a>Tester le gestionnaire d’événements Azure Function

Pour tester la fonction Azure, vous pouvez déclencher un événement dans le compte de stockage qui contient l’abonnement aux événements. L’abonnement à l’événement que vous avez créé précédemment filtre sur deux événements, **Microsoft.Storage.BlobCreated** et **Microsoft.Storage.BlobTierChanged**. Lorsque l’un de ces événements se déclenche, votre fonction Azure est déclenchée.

La fonction Azure illustrée dans cet article écrit dans un objet blob de journal dans deux scénarios :

- Lorsque l’événement est **Microsoft.Storage.BlobCreated** et l’opération API **Copier un objet blob**.
- Lorsque l’événement est **Microsoft.Storage.BlobTierChanged** et l’opération API **Définir un niveau d’objet blob**.

Pour savoir comment tester la fonction en réactivant un objet blob, consultez l’une des deux procédures suivantes :

- [Réactiver un objet blob à l’aide d’une opération de copie](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-with-a-copy-operation)
- [Réactiver un objet blob en modifiant son niveau](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-by-changing-its-tier)

Une fois la réactivation terminée, l’objet blob de journal est écrit dans le même conteneur que l’objet blob que vous avez réactivé. Par exemple, une fois que vous avez réactivé un objet blob à l’aide d’une opération de copie, vous pouvez voir dans le portail Azure que l’objet blob source d’origine reste dans le niveau Archive, l’objet blob de destination entièrement réactivé apparaît dans la couche ciblée en ligne, et l’objet blob de journal créé par la fonction Azure s’affiche également dans la liste.

:::image type="content" source="media/archive-rehydrate-handle-event/copy-blob-archive-tier-rehydrated-with-log-blob.png" alt-text="Capture d’écran montrant l’objet blob d’origine dans le niveau Archive, l’objet blob réactivé dans le niveau chaud et l’objet blob de journal écrit par le gestionnaire d’événements":::

Gardez à l’esprit que la réactivation d’un objet blob peut prendre jusqu’à 15 heures, en fonction du paramètre de priorité de réactivation. Si vous définissez la priorité de réactivation sur **Élevée**, la réactivation peut s’effectuer en moins d’une heure pour les objets blob inférieurs à 10 Go de taille. Toutefois, une réactivation à priorité élevée entraîne un coût plus élevé. Pour plus d’informations, voir [Vue d’ensemble de la réactivation d’objets blob à partir du niveau Archive](archive-rehydrate-overview.md).

> [!TIP]
> Bien que l’objectif de cet objectif est de gérer ces événements dans le contexte de la réactivation de l’objet blob, à des fins de test, il peut également être utile d’observer ces événements en réponse au téléchargement d’un objet blob ou de changer de niveau d’objet blob en ligne (*par ex.* de chaud à froid), parce que l’événement se déclenche immédiatement.

Pour plus d’informations sur le filtrage des événements dans Event Grid, consultez [Guide pratique pour filtrer des événements pour Azure Event Grid](../../event-grid/how-to-filter-events.md).

## <a name="see-also"></a>Voir aussi

- [Niveaux d’accès chaud, froid et archive pour les données d’objet blob](access-tiers-overview.md)
- [Vue d’ensemble de la réactivation des objets blob à partir du niveau Archive](archive-rehydrate-overview.md)
- [Réalimenter un objet blob archivé dans un niveau en ligne](archive-rehydrate-to-online-tier.md)
- [Réaction aux événements de stockage Blob](storage-blob-event-overview.md)
