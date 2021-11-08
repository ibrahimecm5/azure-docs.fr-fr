---
title: 'Valider le schéma lors de l’envoi et de la réception d’événements : AMQP et .NET'
description: Cet article décrit la procédure à suivre pour créer une application .NET Core qui envoie/reçoit des événements vers/depuis Azure Event Hubs avec une validation de schéma à l’aide du registre de schémas.
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: d87fdaff91e46811319b259de01300fb2a70dff3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433858"
---
# <a name="validate-schema-when-sending-and-receiving-events---amqp-and-net"></a>Valider le schéma lors de l’envoi et de la réception d’événements : AMQP et .NET 
Ce guide de démarrage rapide montre comment recevoir des événements d’un hub d’événements et lui en envoyer avec une validation de schéma à l’aide de la bibliothèque .NET **Azure.Messaging.EventHubs**. 

## <a name="prerequisites"></a>Prérequis
Si vous débutez avec Azure Event Hubs, consultez la [vue d’ensemble d’Event Hubs](event-hubs-about.md) avant de suivre ce guide de démarrage rapide. 

Pour effectuer ce démarrage rapide, vous avez besoin de ce qui suit :
- Suivez les instructions du démarrage rapide : [Créer un espace de noms Event Hubs et un Event Hub](event-hubs-create.md).
- Pour obtenir la chaîne de connexion de votre espace de noms Event Hubs, suivez les instructions indiquées dans [Obtenir une chaîne de connexion](event-hubs-get-connection-string.md). Notez les paramètres suivants que vous allez utiliser dans le démarrage rapide actuel :
    - Chaîne de connexion pour l’espace de noms Event Hubs
    - Nom du Event Hub
- **Complétez le [guide de démarrage rapide .NET](event-hubs-dotnet-standard-getstarted-send.md)** pour vous familiariser avec l’envoi et la réception d’événements à partir d’Event Hubs à l’aide de .NET. Si vous avez déjà effectué le démarrage rapide .NET avant, vous pouvez ignorer cette étape. 
- **Suivez les instructions de [Créer des schémas à l’aide du registre de schémas](create-schema-registry.md)** pour créer un groupe de schémas et un schéma. Lorsque vous créez un schéma, suivez les instructions de l’article [Créer un schéma](#create-a-schema) dans le démarrage rapide actuel. 
- **Microsoft Visual Studio 2019**. La bibliothèque cliente Azure Event Hubs utilise les nouvelles fonctionnalités introduites dans C# 8.0.  Vous pouvez toujours utiliser la bibliothèque avec les versions précédentes du langage C#, mais la nouvelle syntaxe ne sera pas disponible. Pour utiliser la syntaxe complète, nous vous recommandons d’effectuer la compilation avec la version 3.0 ou une version ultérieure du [kit SDK .NET Core](https://dotnet.microsoft.com/download) et la [version du langage](/dotnet/csharp/language-reference/configure-language-version#override-a-default) `latest`. Si vous utilisez Visual Studio, les versions antérieures à Visual Studio 2019 ne sont pas compatibles avec les outils nécessaires à la génération de projets C# 8.0. Visual Studio 2019, y compris l’édition Community gratuite, est téléchargeable [ici](https://visualstudio.microsoft.com/vs/).


## <a name="create-a-schema"></a>Créer un schéma 
1. Créez un groupe de schémas nommé **contoso-sg** à l’aide du portail Registre de schémas. Utilisez Avro comme type de sérialisation et **Aucun** pour le mode de compatibilité. 
1. Dans ce groupe de schémas, créez un nouveau schéma Avro avec le nom de schéma : ``Microsoft.Azure.Data.SchemaRegistry.example.Order`` à l’aide du contenu de schéma suivant. 

    ```json 
    {
      "namespace": "Microsoft.Azure.Data.SchemaRegistry.example",
      "type": "record",
      "name": "Order",
      "fields": [
        {
          "name": "id",
          "type": "string"
        },
        {
          "name": "amount",
          "type": "double"
        },
        {
          "name": "description",
          "type": "string"
        }
      ]
    } 
    ```

## <a name="produce-events-to-event-hubs-with-schema-validation"></a>Générer des événements pour Event hubs avec validation de schéma


### <a name="create-console-application-for-event-producer"></a>Créer une application console pour un producteur d’événements 

1. Démarrez Visual Studio 2019. 
1. Sélectionnez **Créer un projet**. 
1. Dans la boîte de dialogue **Créer un projet**, effectuez les étapes suivantes : Si vous ne voyez pas cette boîte de dialogue, sélectionnez **Fichier** dans le menu, sélectionnez **Nouveau**, puis **Projet**. 
    1. Sélectionnez **C#** en guise de langage de programmation.
    1. Sélectionnez **Console** comme type de l’application. 
    1. Sélectionnez **Application console** dans la liste des résultats. 
    1. Ensuite, sélectionnez **Suivant**. 

        :::image type="content" source="./media/getstarted-dotnet-standard-send-v2/new-send-project.png" alt-text="Image représentant la boîte de dialogue Nouveau projet.":::
1. Entrez **OrderProducer** comme nom de projet, **SRQuickStart** comme nom de solution, puis sélectionnez **OK** pour créer le projet. 


### <a name="add-the-event-hubs-nuget-package"></a>Ajout du package NuGet Event Hubs

1. Cliquez sur **Outils** > **Gestionnaire de package NuGet** > **Console du Gestionnaire de package** à partir du menu. 
1. Exécutez la commande suivante pour installer le package NuGet **Azure.Messaging.EventHubs** :

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    Install-Package Azure.Identity
    Install-Package Microsoft.Azure.Data.SchemaRegistry.ApacheAvro -Version 1.0.0-beta.2
    Install-Package Azure.ResourceManager.Compute -Version 1.0.0-beta.1

    ```
1. Authentifiez les applications de producteur pour la connexion à Azure via Visual Studio comme indiqué [ici](/dotnet/api/overview/azure/identity-readme#authenticating-via-visual-studio).  

### <a name="code-generation-using-the-avro-schema"></a>Génération de code à l’aide du schéma Avro  
1. Vous pouvez utiliser le même contenu de schéma et créer le fichier de schéma Avro ``Order.avsc`` dans le projet OrderProducer. 
1. Vous pouvez ensuite utiliser ce fichier de schéma pour générer du code pour .NET. Vous pouvez utiliser n’importe quel outil de génération de code externe, tel que [avrogen](https://www.nuget.org/packages/Apache.Avro.Tools/), pour la génération de code. (Par exemple, vous pouvez exécuter `` avrogen -s .\Order.avsc `` pour générer du code). 
1. Une fois que vous avez généré le code, vous devez disposer des types C# correspondants disponibles dans votre projet. Pour le schéma Avro ci-dessus, le projet génère les types C# dans l’espace de noms ``Microsoft.Azure.Data.SchemaRegistry.example``. 


### <a name="write-code-to-serialize-and-send-events-to-the-event-hub"></a>Écriture de code pour sérialiser et envoyer des événements au hub d’événements 

1. Ajoutez les instructions `using` ci-après en haut du fichier **Program.cs** :

    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using Azure.Data.SchemaRegistry;
    using Azure.Identity;
    using Microsoft.Azure.Data.SchemaRegistry.ApacheAvro;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    using System.Threading.Tasks;
    ```
1. Vous pouvez également importer les types générés associés au schéma ``Order`` comme indiqué ci-dessous. 
    ```csharp
    using Microsoft.Azure.Data.SchemaRegistry.example;   
    ```

2. Ajoutez des constantes à la classe `Program` pour la chaîne de connexion Event Hubs et le nom du hub d’événements.  

    ```csharp
        // connection string to the Event Hubs namespace
        private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";

        // name of the event hub
        private const string eventHubName = "<EVENT HUB NAME>";
        
        // Schema Registry endpoint 
        private const string schemaRegistryEndpoint = "<EVENT HUBS NAMESPACE>.servicebus.windows.net>";
            
        // name of the consumer group   
        private const string schemaGroup = "<SCHEMA GROUP>";

    ```

    > [!NOTE]
    > Remplacez les valeurs d’espace réservé par la chaîne de connexion à votre espace de noms, le nom du hub d’événements et le groupe de schémas.
3. Ajoutez la propriété statique suivante à la classe `Program`. Consultez les commentaires du code. 

    ```csharp
        // The Event Hubs client types are safe to cache and use as a singleton for the lifetime
        // of the application, which is best practice when events are being published or read regularly.
        static EventHubProducerClient producerClient;    
    ```
1. Remplacez la méthode `Main` par la méthode `async Main` suivante. Pour plus d’informations, consultez les commentaires du code. 

    ```csharp
        static async Task Main()
        {
            // Create a producer client that you can use to send events to an event hub
            producerClient = new EventHubProducerClient(connectionString, eventHubName); 
    
            // Create a schema registry client that you can use to serialize and validate data.  
            var schemaRegistryClient = new SchemaRegistryClient(endpoint: schemaRegistryEndpoint, credential: new DefaultAzureCredential());
    
            // Create a batch of events 
            using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();
            
            // Create a new order object using the generated type/class 'Order'. 
            var sampleOrder = new Order { id = "12345", amount = 55.99, description = "This is a sample order." };
    
            using var memoryStream = new MemoryStream();
            // Create an Avro object serializer using the Schema Registry client object. 
            var producerSerializer = new SchemaRegistryAvroObjectSerializer(schemaRegistryClient, schemaGroup, new SchemaRegistryAvroObjectSerializerOptions { AutoRegisterSchemas = true });
    
            // Serialize events data to the memory stream object. 
            producerSerializer.Serialize(memoryStream, sampleOrder, typeof(Order), CancellationToken.None);
    
            byte[] _memoryStreamBytes;
            _memoryStreamBytes = memoryStream.ToArray();
    
            // Create event data with serialized data and add it to an event batch. 
            eventBatch.TryAdd(new EventData(_memoryStreamBytes));
    
            // Send serilized event data to event hub. 
            await producerClient.SendAsync(eventBatch);
            Console.WriteLine("A batch of 1 order has been published.");
        }
    ```
5. Générez le projet et vérifiez qu’il ne présente pas d’erreurs.
6. Exécutez le programme et attendez le message de confirmation. 

    ```csharp
    A batch of 1 order has been published.
    ```
1. Sur le Portail Azure, vous pouvez vérifier que le hub d’événements a reçu les événements. Basculez vers l’affichage **Messages** dans la section **Métriques**. Actualisez la page pour mettre à jour le graphique. Cela peut prendre quelques secondes pour indiquer que les messages ont été reçus. 

    :::image type="content" source="./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png" alt-text="Image de la page du Portail Azure permettant de vérifier que le hub d’événements a reçu les événements." lightbox="./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png":::




## <a name="consume-events-from-event-hubs-with-schema-validation"></a>Consommer des événements à partir d’Event Hubs avec validation de schéma
Cette section montre comment écrire une application console .NET Core qui reçoit des événements à partir d’un Event Hub et utilise le registre de schémas pour désérialiser les données d’événement. 


### <a name="create-consumer-application"></a>Créer une application consommateur

1. Dans la fenêtre Explorateur de solutions, cliquez avec le bouton droit sur la solution **SRQuickStart**, pointez sur **Ajouter**, puis sélectionnez **Nouveau projet**. 
1. Sélectionnez **Application console**, puis **Suivant**. 
1. Entrez **OrderConsumer** pour **Nom du projet**, puis sélectionnez **Créer**. 
1. Dans la fenêtre **Explorateur de solutions**, cliquez avec le bouton droit sur **OrderConsumer**, puis sélectionnez **Définir comme projet de démarrage**. 

### <a name="add-the-event-hubs-nuget-package"></a>Ajout du package NuGet Event Hubs

1. Cliquez sur **Outils** > **Gestionnaire de package NuGet** > **Console du Gestionnaire de package** à partir du menu. 
1. Dans la fenêtre **Console du gestionnaire de package**, vérifiez que **OrderConsumer** est sélectionné pour le **projet par défaut**. Si ce n’est pas le cas, utilisez la liste déroulante pour sélectionner **OrderConsumer**.


1. Exécutez la commande suivante pour installer le package NuGet requis :

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    Install-Package Azure.Messaging.EventHubs.Processor
    Install-Package Azure.Identity
    Install-Package Microsoft.Azure.Data.SchemaRegistry.ApacheAvro -Version 1.0.0-beta.2
    Install-Package Azure.ResourceManager.Compute -Version 1.0.0-beta.1

    ```
1. Authentifiez les applications de producteur pour la connexion à Azure via Visual Studio comme indiqué [ici](/dotnet/api/overview/azure/identity-readme#authenticating-via-visual-studio). 



### <a name="code-generation-using-the-avro-schema"></a>Génération de code à l’aide du schéma Avro  
1. Vous pouvez utiliser le même contenu de schéma et créer le fichier de schéma Avro ``Order.avsc`` dans le projet ``OrderProducer``. 
1. Vous pouvez ensuite utiliser ce fichier de schéma pour générer du code pour .NET. Pour ce faire, vous pouvez utiliser n’importe quel outil de génération de code externe, tel que [avrogen](https://www.nuget.org/packages/Apache.Avro.Tools/). (Par exemple, vous pouvez exécuter `` avrogen -s .\Order.avsc `` pour générer du code). 
1. Une fois que vous avez généré le code, vous devez disposer des types C# correspondants disponibles dans votre projet. Pour le schéma Avro ci-dessus, le projet génère les types C# dans l’espace de noms ``Microsoft.Azure.Data.SchemaRegistry.example``. 


### <a name="write-code-to-receive-events-and-deserialize-them-using-schema-registry"></a>Écrire du code pour recevoir des événements et les désérialiser à l’aide du registre de schémas


1. Ajoutez les instructions `using` ci-après en haut du fichier **Program.cs** :

    ```csharp
    using System;
    using System.IO;
    using System.Text;
    using System.Threading.Tasks;
    using System.Threading;
    using Azure.Data.SchemaRegistry;
    using Azure.Identity;
    using Microsoft.Azure.Data.SchemaRegistry.ApacheAvro;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Vous pouvez également importer les types générés associés au schéma ``Order`` comme indiqué ci-dessous. 
    ```csharp
    using Microsoft.Azure.Data.SchemaRegistry.example;   
    ```

2. Ajoutez des constantes à la classe `Program` pour la chaîne de connexion Event Hubs et le nom du hub d’événements.  

    ```csharp
        // connection string to the Event Hubs namespace
        private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";

        // name of the event hub
        private const string eventHubName = "<EVENT HUB NAME>";

        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
        
        // Schema Registry endpoint 
        private const string schemaRegistryEndpoint = "<EVENT HUBS NAMESPACE>.servicebus.windows.net>";
            
        // name of the consumer group   
        private const string schemaGroup = "<SCHEMA GROUP>";

    ```

3. Ajoutez les propriétés statiques suivantes à la classe `Program`. 

    ```csharp
        static BlobContainerClient storageClient;

        // The Event Hubs client types are safe to cache and use as a singleton for the lifetime
        // of the application, which is best practice when events are being published or read regularly.        
        static EventProcessorClient processor;    
    ```
1. Remplacez la méthode `Main` par la méthode `async Main` suivante. Pour plus d’informations, consultez les commentaires du code. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 30 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(30));

            // Stop the processing
            await processor.StopProcessingAsync();
        }
    ```
1. À présent, ajoutez la méthode de gestionnaire d’événements suivante qui comprend la logique de désérialisation des événements avec le registre de schémas 
    ```csharp
        static async Task ProcessEventHandler(ProcessEventArgs eventArgs)
        {
            // Create a schema registry client that you can use to deserialize and validate data.  
            var schemaRegistryClient = new SchemaRegistryClient(endpoint: schemaRegistryEndpoint, credential: new DefaultAzureCredential());
    
            // Retrieve event data and convert it to a byte array. 
            byte[] _memoryStreamBytes = eventArgs.Data.Body.ToArray();
            using var consumerMemoryStream = new MemoryStream(_memoryStreamBytes);
    
            var consumerSerializer = new SchemaRegistryAvroObjectSerializer(schemaRegistryClient, schemaGroup, new SchemaRegistryAvroObjectSerializerOptions { AutoRegisterSchemas = false });
            consumerMemoryStream.Position = 0;
    
            // Deserialize event data and create order object using schema registry. 
            Order sampleOrder = (Order)consumerSerializer.Deserialize(consumerMemoryStream, typeof(Order), CancellationToken.None);
            Console.WriteLine("Received - Order ID: " + sampleOrder.id);                 
    
            // Update checkpoint in the blob storage so that the app receives only new events the next time it's run
            await eventArgs.UpdateCheckpointAsync(eventArgs.CancellationToken);   
        }

    ```

 
1. À présent, ajoutez à la classe les méthodes de gestionnaire d’erreurs suivantes. 

    ```csharp
        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            // Write details about the error to the console window
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }
  
    ```
1. Générez le projet et vérifiez qu’il ne présente pas d’erreurs.


6. Exécutez l’application réceptrice. 
1. Vous devriez voir un message indiquant que les événements ont été reçus. 

    ```bash
    Received - Order ID: 12345
    ```
    Ces événements sont les trois événements que vous avez envoyés au hub d’événements en exécutant le programme émetteur. 


## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez la [bibliothèque de client du registre de schémas Azure pour .NET](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/schemaregistry/Azure.Data.SchemaRegistry). 
