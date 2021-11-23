---
title: Démarrage rapide - Publier des messages en utilisant le SDK du service pour l’instance Azure Web PubSub
description: Démarrage rapide illustrant l’utilisation du kit de développement logiciel (SDK) de service
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: quickstart
ms.date: 11/01/2021
ms.openlocfilehash: c3e422699d03e50bfd860ded2e281c8199ac7a5e
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546554"
---
# <a name="quickstart-publish-messages-using-the-service-sdk-for-the-azure-web-pubsub-instance"></a>Démarrage rapide : Publier des messages en utilisant le SDK du service pour l’instance Azure Web PubSub

Ce guide de démarrage rapide vous montre comment publier des messages sur les clients à l’aide du kit de développement logiciel (SDK) de service.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ce guide de démarrage rapide nécessite la version 2.22.0 ou ultérieure de l’interface Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [Create a resource group](includes/cli-rg-creation.md)]

## <a name="create-a-web-pubsub-instance"></a>Créer une instance Web PubSub

[!INCLUDE [Create a Web PubSub instance](includes/cli-awps-creation.md)]

## <a name="get-the-connectionstring-for-future-use"></a>Obtenir la ConnectionString pour une utilisation ultérieure

[!INCLUDE [Get the connection string](includes/cli-awps-connstr.md)]

Copiez la **ConnectionString** extraite et elle sera utilisée ultérieurement lors de l’utilisation du kit de développement logiciel (SDK) de service comme valeur de `<connection_string>`.

## <a name="connect-to-the-instance"></a>Se connecter à l’instance

[!INCLUDE [az webpubsub client](includes/cli-awps-client-connect.md)]

## <a name="publish-messages-using-service-sdk"></a>Publier des messages à l’aide du SDK de service

Nous allons maintenant utiliser le kit de développement logiciel (SDK) Azure Web PubSub pour publier un message sur le client connecté.

### <a name="prerequisites"></a>Prérequis

# <a name="c"></a>[C#](#tab/csharp)

* [.NET Core 2.1 ou ultérieur](https://dotnet.microsoft.com/download)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* [Node.js 12.x (ou version ultérieure)](https://nodejs.org)

# <a name="python"></a>[Python](#tab/python)
* [Python](https://www.python.org/)

# <a name="java"></a>[Java](#tab/java)
- [Java Development Kit (JDK)](/java/azure/jdk/) version 8 ou ultérieure.
- [Apache Maven](https://maven.apache.org/download.cgi).

---

### <a name="set-up-the-project-to-publish-messages"></a>Configurer le projet pour publier des messages

# <a name="c"></a>[C#](#tab/csharp)

1. Ajoutez un nouveau projet `publisher` et ajoutez le package du kit de développement logiciel (SDK) `package Azure.Messaging.WebPubSub`.

    ```bash
    mkdir publisher
    cd publisher
    dotnet new console
    dotnet add package Azure.Messaging.WebPubSub
    ```

2. Mettez à jour le fichier `Program.cs` de façon à utiliser la classe `WebPubSubServiceClient` et à envoyer des messages aux clients.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Messaging.WebPubSub;

    namespace publisher
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                if (args.Length != 3) {
                    Console.WriteLine("Usage: publisher <connectionString> <hub> <message>");
                    return;
                }
                var connectionString = args[0];
                var hub = args[1];
                var message = args[2];

                var service = new WebPubSubServiceClient(connectionString, hub);
                
                // Send messages to all the connected clients
                // You can also try SendToConnectionAsync to send messages to the specific connection
                await service.SendToAllAsync(message);
            }
        }
    }
    ```

    L’appel `service.SendToAllAsync()` envoie simplement un message à tous les clients connectés dans le hub.

3. Exécutez la commande ci-dessous, en remplaçant `<connection_string>` par la chaîne **ConnectionString** extraite à l’[étape précédente](#get-the-connectionstring-for-future-use) :

    ```bash
    dotnet run "<connection_string>" "myHub1" "Hello World"
    ```

4. Vous pouvez voir que le client CLI précédent a reçu le message.
   
    ```json
    {"type":"message","from":"server","dataType":"text","data":"Hello World"}
    ```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

1. Tout d’abord, créons un nouveau dossier `publisher` pour ce projet et installons les dépendances requises :

    ```bash
    mkdir publisher
    cd publisher
    npm init -y
    npm install --save @azure/web-pubsub
    ```

2. Nous allons maintenant utiliser le kit de développement logiciel (SDK) Azure Web PubSub pour publier un message dans le service. Créez un fichier `publish.js` avec le code ci-dessous :

    ```javascript
    const { WebPubSubServiceClient } = require('@azure/web-pubsub');

    if (process.argv.length !== 3) {
      console.log('Usage: node publish <message>');
      return 1;
    }
    const hub = "pubsub";
    let service = new WebPubSubServiceClient(process.env.WebPubSubConnectionString, hub);
    // by default it uses `application/json`, specify contentType as `text/plain` if you want plain-text
    service.sendToAll(process.argv[2], { contentType: "text/plain" });
    ```

    L’appel `sendToAll()` envoie simplement un message à tous les clients connectés dans un hub.

3. Exécutez la commande ci-dessous, en remplaçant `<connection_string>` par la chaîne **ConnectionString** extraite à l’[étape précédente](#get-the-connectionstring-for-future-use) :

    ```bash
    export WebPubSubConnectionString="<connection-string>"
    node publish "Hello World"
    ```

4. Vous pouvez voir que le client CLI précédent a reçu le message.
   
    ```json
    {"type":"message","from":"server","dataType":"text","data":"Hello World"}
    ```

# <a name="python"></a>[Python](#tab/python)

1. Tout d’abord, créons un nouveau dossier `publisher` pour ce projet et installons les dépendances requises :
    ```bash
    mkdir publisher
    cd publisher
    # Create venv
    python -m venv env
    # Active venv
    source ./env/bin/activate

    pip install azure-messaging-webpubsubservice

    ```
2. Nous allons maintenant utiliser le kit de développement logiciel (SDK) Azure Web PubSub pour publier un message dans le service. Créez un fichier `publish.py` avec le code ci-dessous :

    ```python
    import sys
    from azure.messaging.webpubsubservice import WebPubSubServiceClient
    
    if __name__ == '__main__':
    
        if len(sys.argv) != 4:
            print('Usage: python publish.py <connection-string> <hub-name> <message>')
            exit(1)
    
        connection_string = sys.argv[1]
        hub_name = sys.argv[2]
        message = sys.argv[3]
    
        service = WebPubSubServiceClient.from_connection_string(connection_string, hub=hub_name)
        res = service.send_to_all(message, content_type='text/plain')
        print(res)
    ```

    La méthode `service.send_to_all()` envoie le message à tous les clients connectés dans un hub.

3. Exécutez la commande ci-dessous, en remplaçant `<connection_string>` par la chaîne **ConnectionString** extraite à l’[étape précédente](#get-the-connectionstring-for-future-use) :

    ```bash
    python publish.py "<connection_string>" "myHub1" "Hello World"
    ```

4. Vous pouvez voir que le client CLI précédent a reçu le message.
   
    ```json
    {"type":"message","from":"server","dataType":"text","data":"Hello World"}
    ```

# <a name="java"></a>[Java](#tab/java)

1. Tout d’abord, nous allons utiliser Maven pour créer une application console `webpubsub-quickstart-publisher` et basculer vers le dossier *webpubsub-quickstart-publisher* :
    ```console
    mvn archetype:generate --define interactiveMode=n --define groupId=com.webpubsub.quickstart --define artifactId=webpubsub-quickstart-publisher --define archetypeArtifactId=maven-archetype-quickstart --define archetypeVersion=1.4
    cd webpubsub-quickstart-publisher
    ```

2. Nous allons ajouter la dépendance du kit de développement logiciel (SDK) Azure Web PubSub dans le nœud `dependencies` de `pom.xml` :

    ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-webpubsub</artifactId>
        <version>1.0.0-beta.6</version>
    </dependency>
    ```

3. Nous allons maintenant utiliser le kit de développement logiciel (SDK) Azure Web PubSub pour publier un message dans le service. Nous allons accéder au répertoire */src/main/java/com/webpubsub/quickstart*, ouvrir le fichier *App.java* dans votre éditeur et remplacer le code par ce qui suit :

    ```java
    package com.webpubsub.quickstart;
    
    import com.azure.messaging.webpubsub.*;
    import com.azure.messaging.webpubsub.models.*;
    
    /**
    * Publish messages using Azure Web PubSub service SDK
    *
    */
    public class App 
    {
        public static void main( String[] args )
        {
            if (args.length != 3) {
                System.out.println("Expecting 3 arguments: <connection-string> <hub-name> <message>");
                return;
            }
    
            WebPubSubServiceClient service = new WebPubSubServiceClientBuilder()
                .connectionString(args[0])
                .hub(args[1])
                .buildClient();
            service.sendToAll(args[2], WebPubSubContentType.TEXT_PLAIN);
        }
    }

    ```

    L’appel `service.sendToAll()` envoie simplement un message à tous les clients connectés dans un hub.

4. Accédez au répertoire contenant le fichier *pom.xml*, puis compilez le projet à l’aide de la commande `mvn` suivante.

    ```console
    mvn compile
    ```
5. Ensuite, générez le package

    ```console
    mvn package
    ```
6. Exécutez la commande `mvn` suivante pour exécuter l’application, en remplaçant `<connection_string>` par la chaîne **ConnectionString** extraite à l’[étape précédente](#get-the-connectionstring-for-future-use) :

    ```console
    mvn exec:java -Dexec.mainClass="com.webpubsub.quickstart.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>' 'myHub1' 'Hello World'"
    ```

7. Vous pouvez voir que le client CLI précédent a reçu le message.
   
    ```json
    {"type":"message","from":"server","dataType":"text","data":"Hello World"}
    ```

---

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel donne une idée de base de la procédure à suivre pour se connecter au service Web PubSub et publier des messages sur les clients connectés.

Dans les applications réelles, vous pouvez utiliser des kits de développement logiciel (SDK) dans différents langages pour créer votre propre application. Nous fournissons également des extensions de fonction qui vous permettent de créer facilement des applications serverless.

[!INCLUDE [next step](includes/include-next-step.md)]
