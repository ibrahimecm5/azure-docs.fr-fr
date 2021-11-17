---
title: Bien démarrer avec les files d’attente Azure Service Bus (Azure.Messaging.ServiceBus)
description: Dans ce tutoriel, vous créez une application C# .NET Core afin d’échanger des messages avec une file d’attente Service Bus.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 10/11/2021
ms.custom: contperf-fy22q2
ms.openlocfilehash: c7e0ddee8e42f76c034ce79c7200e48fcb8bae04
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555904"
---
# <a name="get-started-with-azure-service-bus-queues-net"></a>Bien démarrer avec les files d’attente Azure Service Bus (.NET)
Dans ce guide de démarrage rapide, vous allez effectuer les étapes suivantes :

1. Créer un espace de noms Service Bus à l’aide du Portail Azure.
2. Créer une file d’attente Service Bus à l’aide du portail Azure.
3. Écrivez une application de console .NET Core pour envoyer un ensemble de messages à la file d’attente.
4. Écrivez une application de console .NET Core pour recevoir ces messages de la file d’attente.

> [!NOTE]
> Ce guide de démarrage rapide fournit des instructions pas à pas pour un scénario simple qui consiste à envoyer et recevoir un lot de messages à une file d’attente Service Bus. Vous trouverez des exemples .NET prédéfinis pour Azure Service Bus dans le [dépôt du kit SDK Azure pour .NET sur GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples). 

## <a name="prerequisites"></a>Prérequis
Si vous débutez avec le service, consultez [Vue d’ensemble de Service Bus](service-bus-messaging-overview.md) avant de suivre ce démarrage rapide. 

- **Abonnement Azure**. Pour utiliser des services Azure, dont Azure Service Bus, vous avez besoin d’un abonnement. Si vous n’avez pas de compte Azure existant, vous pouvez demander un [essai gratuit](https://azure.microsoft.com/free/).
- **Microsoft Visual Studio 2019**. La bibliothèque cliente Azure Service Bus utilise les nouvelles fonctionnalités introduites dans C# 8.0.  Vous pouvez toujours utiliser la bibliothèque avec les versions précédentes du langage C#, mais la nouvelle syntaxe ne sera pas disponible. Pour utiliser la syntaxe complète, nous vous recommandons d’effectuer la compilation avec la version 3.0 ou une version ultérieure du kit SDK .NET Core et la version du langage `latest`. Si vous utilisez Visual Studio, les versions antérieures à Visual Studio 2019 ne sont pas compatibles avec les outils nécessaires à la génération de projets C# 8.0.


[!INCLUDE [service-bus-create-namespace-portal](./includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](./includes/service-bus-create-queue-portal.md)]


## <a name="send-messages-to-the-queue"></a>Envoyer des messages à la file d’attente
Cette section montre comment créer une application console .NET Core pour envoyer des messages à une file d’attente Service Bus. 

### <a name="create-a-console-application"></a>Création d’une application console

1. Démarrez Visual Studio 2019. 
1. Sélectionnez **Créer un projet**. 
1. Dans la boîte de dialogue **Créer un projet**, effectuez les étapes suivantes : Si vous ne voyez pas cette boîte de dialogue, sélectionnez **Fichier** dans le menu, sélectionnez **Nouveau**, puis **Projet**. 
    1. Sélectionnez **C#** en guise de langage de programmation.
    1. Sélectionnez **Console** comme type de l’application. 
    1. Sélectionnez **Application console** dans la liste des résultats. 
    1. Ensuite, sélectionnez **Suivant**. 

        :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/new-send-project.png" alt-text="Image représentant la boîte de dialogue Créer un projet avec C# et la console sélectionnés":::
1. Entrez **QueueSender** pour le nom du projet, **ServiceBusQueueQuickStart** pour le nom de la solution, puis sélectionnez **Suivant**. 

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/project-solution-names.png" alt-text="Image représentant les noms de la solution et du projet dans la boîte de dialogue Configurer votre nouveau projet ":::
1. Dans la page **Informations supplémentaires**, sélectionnez **Créer** pour créer la solution et le projet. 

### <a name="add-the-service-bus-nuget-package"></a>Ajout du package NuGet Service Bus

1. Cliquez sur **Outils** > **Gestionnaire de package NuGet** > **Console du Gestionnaire de package** à partir du menu. 
1. Exécutez la commande suivante pour installer le package NuGet **Azure.Messaging.ServiceBus** :

    ```cmd
    Install-Package Azure.Messaging.ServiceBus
    ```

### <a name="add-code-to-send-messages-to-the-queue"></a>Ajouter du code pour envoyer des messages à la file d’attente

1. Dans **Program.cs**, ajoutez les instructions `using` suivantes en haut de la définition de l’espace de noms, avant la déclaration de classe.

    ```csharp
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
2. Dans la classe `Program`, déclarez les propriétés suivantes, juste avant la méthode `Main`. 

    Remplacez `<NAMESPACE CONNECTION STRING>` par la chaîne de connexion principale à votre espace de noms Service Bus. et remplacez `<QUEUE NAME>` par le nom de la file d’attente.

    ```csharp

    // connection string to your Service Bus namespace
    static string connectionString = "<NAMESPACE CONNECTION STRING>";

    // name of your Service Bus queue
    static string queueName = "<QUEUE NAME>";

    // the client that owns the connection and can be used to create senders and receivers
    static ServiceBusClient client;

    // the sender used to publish messages to the queue
    static ServiceBusSender sender;

    // number of messages to be sent to the queue
    private const int numOfMessages = 3;

    ```
3. Remplacez le code de la méthode `Main` par le code suivant. Pour plus d’informations sur le code, consultez les commentaires de code. Voici les étapes importantes du code.  
    1. Crée un objet [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) à l’aide de la chaîne de connexion principale à l’espace de noms. 
    1. Utilise la méthode [CreateSender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender) sur l’objet [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) pour créer un objet [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) pour la file d’attente Service Bus spécifique.     
    1. Crée un objet [ServiceBusMessageBatch](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch) à l’aide de la méthode [ServiceBusSender.CreateMessageBatchAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.createmessagebatchasync).
    1. Ajoutez des messages au lot à l’aide de [ServiceBusMessageBatch.TryAddMessage](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch.tryaddmessage). 
    1. Envoie le lot de messages à file d’attente Service Bus à l’aide de la méthode [ServiceBusSender.SendMessagesAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessagesasync).

        ```csharp
        static async Task Main()
        {
            // The Service Bus client types are safe to cache and use as a singleton for the lifetime
            // of the application, which is best practice when messages are being published or read
            // regularly.
            //
            // Create the clients that we'll use for sending and processing messages.
            client = new ServiceBusClient(connectionString);
            sender = client.CreateSender(queueName);
    
            // create a batch 
            using ServiceBusMessageBatch messageBatch = await sender.CreateMessageBatchAsync();
    
            for (int i = 1; i <= numOfMessages; i++)
            {
                // try adding a message to the batch
                if (!messageBatch.TryAddMessage(new ServiceBusMessage($"Message {i}")))
                {
                    // if it is too large for the batch
                    throw new Exception($"The message {i} is too large to fit in the batch.");
                }
            }
    
            try
            {
                // Use the producer client to send the batch of messages to the Service Bus queue
                await sender.SendMessagesAsync(messageBatch);
                Console.WriteLine($"A batch of {numOfMessages} messages has been published to the queue.");
            }
            finally
            {
                // Calling DisposeAsync on client types is required to ensure that network
                // resources and other unmanaged objects are properly cleaned up.
                await sender.DisposeAsync();
                await client.DisposeAsync();
            }
    
            Console.WriteLine("Press any key to end the application");
            Console.ReadKey();
        }    
        ```    
1. Voici à quoi doit ressembler votre fichier Program.cs : 
    
    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    
    namespace QueueSender
    {
        class Program
        {
            // connection string to your Service Bus namespace
            static string connectionString = "<NAMESPACE CONNECTION STRING>";

            // name of your Service Bus queue
            static string queueName = "<QUEUE NAME>";
    
            // the client that owns the connection and can be used to create senders and receivers
            static ServiceBusClient client;
    
            // the sender used to publish messages to the queue
            static ServiceBusSender sender;
    
            // number of messages to be sent to the queue
            private const int numOfMessages = 3;
    
            static async Task Main()
            {
                // The Service Bus client types are safe to cache and use as a singleton for the lifetime
                // of the application, which is best practice when messages are being published or read
                // regularly.
                //
                // Create the clients that we'll use for sending and processing messages.
                client = new ServiceBusClient(connectionString);
                sender = client.CreateSender(queueName);
    
                // create a batch 
                using ServiceBusMessageBatch messageBatch = await sender.CreateMessageBatchAsync();
    
                for (int i = 1; i <= numOfMessages; i++)
                {
                    // try adding a message to the batch
                    if (!messageBatch.TryAddMessage(new ServiceBusMessage($"Message {i}")))
                    {
                        // if it is too large for the batch
                        throw new Exception($"The message {i} is too large to fit in the batch.");
                    }
                }
    
                try
                {
                    // Use the producer client to send the batch of messages to the Service Bus queue
                    await sender.SendMessagesAsync(messageBatch);
                    Console.WriteLine($"A batch of {numOfMessages} messages has been published to the queue.");
                }
                finally
                {
                    // Calling DisposeAsync on client types is required to ensure that network
                    // resources and other unmanaged objects are properly cleaned up.
                    await sender.DisposeAsync();
                    await client.DisposeAsync();
                }
    
                Console.WriteLine("Press any key to end the application");
                Console.ReadKey();
            }
        }
    }   
    ``` 
1. Remplacez `<NAMESPACE CONNECTION STRING>` par la chaîne de connexion principale à votre espace de noms Service Bus. et remplacez `<QUEUE NAME>` par le nom de la file d’attente.
1. Générez le projet et vérifiez qu’il ne présente pas d’erreurs. 
1. Exécutez le programme et attendez le message de confirmation.
    
    ```bash
    A batch of 3 messages has been published to the queue
    ```
1. Dans le portail Azure, procédez comme suit :
    1. Accédez à votre espace de noms Service Bus. 
    1. Dans la page **Vue d’ensemble**, sélectionnez la file d’attente dans le volet central inférieur. 
    
        :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/select-queue.png" alt-text="Image représentant la page Espace de noms Service Bus dans le portail Azure avec la file d’attente sélectionnée." lightbox="./media/service-bus-dotnet-get-started-with-queues/select-queue.png":::
    1. Remarquez les valeurs figurant dans la section **Bases**.

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png" alt-text="Image représentant le nombre de messages reçus et la taille de la file d’attente" lightbox="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png":::

    Remarquez les valeurs suivantes :
    - La valeur **Nombre de messages actifs** pour la file d’attente est à présent égale à **3**. Chaque fois que vous exécutez cette application d’expéditeur sans récupérer les messages, cette valeur augmente de 3.
    - La **taille actuelle** de la file d’attente augmente chaque fois que l’application y ajoute des messages.
    - Dans le graphique **Messages** de la section inférieure **Métriques**, vous pouvez voir qu’il y a trois messages entrants pour la file d’attente. 

## <a name="receive-messages-from-the-queue"></a>Recevoir les messages de la file d’attente
Dans cette section, vous allez créer une application console .NET Core qui reçoit des messages de la file d’attente. 

### <a name="create-a-project-for-the-receiver"></a>Créer un projet pour le récepteur

1. Dans la fenêtre Explorateur de solutions, cliquez avec le bouton droit sur la solution **ServiceBusQueueQuickStart**, pointez sur **Ajouter**, puis sélectionnez **Nouveau projet**. 
1. Sélectionnez **Application console**, puis **Suivant**. 
1. Entrez **QueueReceiver** pour **Nom du projet**, puis sélectionnez **Créer**. 
1. Dans la fenêtre **Explorateur de solutions**, cliquez avec le bouton droit sur **QueueReceiver**, puis sélectionnez **Définir comme projet de démarrage**. 

### <a name="add-the-service-bus-nuget-package"></a>Ajout du package NuGet Service Bus

1. Cliquez sur **Outils** > **Gestionnaire de package NuGet** > **Console du Gestionnaire de package** à partir du menu. 
1. Dans la fenêtre **Console du gestionnaire de package**, vérifiez que **QueueReceiver** est sélectionné pour le **projet par défaut**. Si ce n’est pas le cas, utilisez la liste déroulante pour sélectionner **QueueReceiver**.

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/package-manager-console.png" alt-text="Capture d’écran montrant le projet QueueReceiver sélectionné dans la Console du Gestionnaire de package":::
1. Exécutez la commande suivante pour installer le package NuGet **Azure.Messaging.ServiceBus** :

    ```cmd
    Install-Package Azure.Messaging.ServiceBus
    ```

### <a name="add-the-code-to-receive-messages-from-the-queue"></a>Ajouter le code pour recevoir des messages de la file d’attente
Dans cette section, vous allez ajouter du code pour récupérer des messages à partir de la file d’attente.

1. Dans **Program.cs**, ajoutez les instructions `using` suivantes en haut de la définition de l’espace de noms, avant la déclaration de classe.

    ```csharp
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
2. Dans la classe `Program`, déclarez les propriétés suivantes, juste avant la méthode `Main`. 

    Remplacez `<NAMESPACE CONNECTION STRING>` par la chaîne de connexion principale à votre espace de noms Service Bus. et remplacez `<QUEUE NAME>` par le nom de la file d’attente.

    ```csharp
    // connection string to your Service Bus namespace
    static string connectionString = "<NAMESPACE CONNECTION STRING>";

    // name of your Service Bus queue
    static string queueName = "<QUEUE NAME>";

    // the client that owns the connection and can be used to create senders and receivers
    static ServiceBusClient client;

    // the processor that reads and processes messages from the queue
    static ServiceBusProcessor processor;
    ```
3. Ajoutez les méthodes suivantes à la classe `Program` pour gérer les messages reçus et les erreurs éventuelles. 

    ```csharp
    // handle received messages
    static async Task MessageHandler(ProcessMessageEventArgs args)
    {
        string body = args.Message.Body.ToString();
        Console.WriteLine($"Received: {body}");

        // complete the message. messages is deleted from the queue. 
        await args.CompleteMessageAsync(args.Message);
    }

    // handle any errors when receiving messages
    static Task ErrorHandler(ProcessErrorEventArgs args)
    {
        Console.WriteLine(args.Exception.ToString());
        return Task.CompletedTask;
    }
    ```
4. Remplacez le code de la méthode `Main` par le code suivant. Pour plus d’informations sur le code, consultez les commentaires de code. Voici les étapes importantes du code. 
    1. Crée un objet [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) à l’aide de la chaîne de connexion principale à l’espace de noms. 
    1. Appelle la méthode [CreateProcessor](/dotnet/api/azure.messaging.servicebus.servicebusclient.createprocessor) sur l'objet [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) pour créer un objet [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) pour la file d’attente Service Bus spécifiée. 
    1. Spécifie des gestionnaires pour les événements [ProcessMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) et [ProcessErrorAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processerrorasync) de l’objet [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor). 
    1. Démarre le traitement des messages en appelant l’objet [StartProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.startprocessingasync) sur l'objet [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor). 
    1. Lorsque l’utilisateur appuie sur une touche pour terminer le traitement, il appelle [StopProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.stopprocessingasync) sur l' objet [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor). 

        Pour plus d'informations, consultez les commentaires du code.

        ```csharp
                static async Task Main()
                {
                    // The Service Bus client types are safe to cache and use as a singleton for the lifetime
                    // of the application, which is best practice when messages are being published or read
                    // regularly.
                    //
        
                    // Create the client object that will be used to create sender and receiver objects
                    client = new ServiceBusClient(connectionString);
        
                    // create a processor that we can use to process the messages
                    processor = client.CreateProcessor(queueName, new ServiceBusProcessorOptions());
        
                    try
                    {
                        // add handler to process messages
                        processor.ProcessMessageAsync += MessageHandler;
        
                        // add handler to process any errors
                        processor.ProcessErrorAsync += ErrorHandler;
        
                        // start processing 
                        await processor.StartProcessingAsync();
        
                        Console.WriteLine("Wait for a minute and then press any key to end the processing");
                        Console.ReadKey();
        
                        // stop processing 
                        Console.WriteLine("\nStopping the receiver...");
                        await processor.StopProcessingAsync();
                        Console.WriteLine("Stopped receiving messages");
                    }
                    finally
                    {
                        // Calling DisposeAsync on client types is required to ensure that network
                        // resources and other unmanaged objects are properly cleaned up.
                        await processor.DisposeAsync();
                        await client.DisposeAsync();
                    }
                }
        ```
4. Voici à quoi doit ressembler votre fichier `Program.cs` :  

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    
    namespace QueueReceiver
    {
        class Program
        {
            // connection string to your Service Bus namespace
            static string connectionString = "<NAMESPACE CONNECTION STRING>";
    
            // name of your Service Bus queue
            static string queueName = "<QUEUE NAME>";
    
    
            // the client that owns the connection and can be used to create senders and receivers
            static ServiceBusClient client;
    
            // the processor that reads and processes messages from the queue
            static ServiceBusProcessor processor;
    
            // handle received messages
            static async Task MessageHandler(ProcessMessageEventArgs args)
            {
                string body = args.Message.Body.ToString();
                Console.WriteLine($"Received: {body}");
    
                // complete the message. messages is deleted from the queue. 
                await args.CompleteMessageAsync(args.Message);
            }
    
            // handle any errors when receiving messages
            static Task ErrorHandler(ProcessErrorEventArgs args)
            {
                Console.WriteLine(args.Exception.ToString());
                return Task.CompletedTask;
            }
    
            static async Task Main()
            {
                // The Service Bus client types are safe to cache and use as a singleton for the lifetime
                // of the application, which is best practice when messages are being published or read
                // regularly.
                //
    
                // Create the client object that will be used to create sender and receiver objects
                client = new ServiceBusClient(connectionString);
    
                // create a processor that we can use to process the messages
                processor = client.CreateProcessor(queueName, new ServiceBusProcessorOptions());
    
                try
                {
                    // add handler to process messages
                    processor.ProcessMessageAsync += MessageHandler;
    
                    // add handler to process any errors
                    processor.ProcessErrorAsync += ErrorHandler;
    
                    // start processing 
                    await processor.StartProcessingAsync();
    
                    Console.WriteLine("Wait for a minute and then press any key to end the processing");
                    Console.ReadKey();
    
                    // stop processing 
                    Console.WriteLine("\nStopping the receiver...");
                    await processor.StopProcessingAsync();
                    Console.WriteLine("Stopped receiving messages");
                }
                finally
                {
                    // Calling DisposeAsync on client types is required to ensure that network
                    // resources and other unmanaged objects are properly cleaned up.
                    await processor.DisposeAsync();
                    await client.DisposeAsync();
                }
            }
        }
    }
    ```
1. Remplacez `<NAMESPACE CONNECTION STRING>` par la chaîne de connexion principale à votre espace de noms Service Bus. et remplacez `<QUEUE NAME>` par le nom de la file d’attente. 
1. Générez le projet et vérifiez qu’il ne présente pas d’erreurs.
1. Exécutez l’application réceptrice. Vous devriez voir les messages reçus. Appuyez sur n’importe quelle touche pour arrêter le récepteur et l’application. 

    ```console
    Wait for a minute and then press any key to end the processing
    Received: Message 1
    Received: Message 2
    Received: Message 3
    
    Stopping the receiver...
    Stopped receiving messages
    ```
1. Vérifiez à nouveau le portail. Attendez quelques minutes et actualisez la page si vous ne voyez pas `0` pour **Nombre de messages actifs**. 

    - Les valeurs **Nombre de messages actifs** et **Taille actuelle** sont à présent égales à **0**.
    - Dans le graphique **Messages** de la section **Métriques** en bas, vous pouvez voir trois messages entrants et trois messages sortants pour la file d’attente. 
    
        :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png" alt-text="Messages actifs et taille après réception" lightbox="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png":::


## <a name="next-steps"></a>Étapes suivantes
Consultez la documentation et les exemples suivants :

- [Bibliothèque de client Azure Service Bus pour .NET – Fichier Lisez-moi](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [Exemples sur GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [Informations de référence sur l’API .NET](/dotnet/api/azure.messaging.servicebus)
