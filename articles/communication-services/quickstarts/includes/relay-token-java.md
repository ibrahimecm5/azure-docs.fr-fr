---
title: Fichier include
description: inclure fichier
services: azure-communication-services
author: rahulva
manager: shahen
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 09/20/2021
ms.topic: include
ms.custom: include file
ms.author: rahulva
ms.openlocfilehash: 9e55af5bed17c1701a443c934fc6e356fa63c032
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893215"
---
> [!NOTE]
> Vous trouverez le code finalisé pour ce guide de démarrage rapide sur [GitHub](https://github.com/Azure-Samples/communication-services-java-quickstarts/tree/main/relay-token-quickstart).

## <a name="prerequisites-for-java"></a>Prérequis pour Java

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/azure/developer/java/fundamentals/java-jdk-install) version 8 ou ultérieure.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Chaîne de connexion et ressource Communication Services déployée. [Créez une ressource Communication Services](../create-communication-resource.md).

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-java-application"></a>Créer une application Java

Ouvrez votre fenêtre de terminal ou de commande. Accédez au répertoire dans lequel vous souhaitez créer votre application Java. Exécutez la commande ci-dessous pour générer le projet Java à partir du modèle maven-archetype-quickstart.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Vous remarquerez que la tâche « generate » a créé un répertoire portant le même nom que l’`artifactId`. Sous ce répertoire, le répertoire src/main/java contient le code source du projet, `src/test/java directory` contient la source de test, et le fichier `pom.xml` est le modèle objet du projet (le POM).

### <a name="install-the-package"></a>Installer le package

Ouvrez le fichier **pom.xml** dans votre éditeur de texte. Ajoutez l’élément dépendance suivant au groupe de dépendances.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-identity</artifactId>
    <version>1.0.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Configurer le framework d’application 

À partir du répertoire de projet :

1. Accédez au répertoire `/src/main/java/com/communication/quickstart`.
2. Ouvrez le fichier `App.java` dans votre éditeur.
3. Remplacez l’instruction `System.out.println("Hello world!");`
4. Ajoutez des directives `import`.

Utilisez le code suivant pour commencer :

```java
package com.communication.quickstart;

import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.core.credential.*;
import com.communication.network.traversal.*;

import java.io.IOException;
import java.time.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Network Credentials Quickstart");
        // Quickstart code goes here
    }
}
```

## <a name="authenticate-the-client"></a>Authentifier le client

Instanciez un `CommunicationIdentityClient` avec la clé d’accès et le point de terminaison de votre ressource. Découvrez comment [gérer la chaîne de connexion de la ressource](../create-communication-resource.md#store-your-connection-string). En outre, vous pouvez initialiser le client avec n’importe quel client HTTP personnalisé qui implémente l’interface `com.azure.core.http.HttpClient`.

Ajoutez le code suivant à la méthode `main` :

```java
// Your can find your endpoint and access key from your resource in the Azure portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessKey = "SECRET";

CommunicationRelayClient communicationRelayClient = new CommunicationRelayClientBuilder()
    .endpoint(endpoint)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();

```

Vous pouvez également fournir la chaîne de connexion entière à l’aide de la fonction `connectionString()` au lieu de fournir le point de terminaison et la clé d’accès.
```java
// Your can find your connection string from your resource in the Azure portal
String connectionString = "<connection_string>";

CommunicationRelayClient communicationRelayClient = new CommunicationRelayClientBuilder()
    .connectionString(connectionString)
    .buildClient();
```

## <a name="create-an-identity"></a>Créer une identité

Azure Communication Services gère un répertoire LID (Lightweight Identity Directory). Utilisez la méthode `createUser` pour créer une entrée, avec une valeur `Id` unique, dans le répertoire. Stockez l’identité reçue avec un mappage aux utilisateurs de votre application. Par exemple, en les stockant dans la base de données de votre serveur d’applications. L’identité sera demandée ultérieurement pour émettre des jetons d’accès.

```java
CommunicationUserIdentifier user = communicationIdentityClient.createUser();
System.out.println("\nCreated an identity with ID: " + user.getId());
```

## <a name="getting-the-relay-configuration"></a>Obtention de la configuration de relais
Appeler le service de jeton Azure Communication pour échanger le jeton d’accès utilisateur pour un jeton de service TURN

```java
CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
            .connectionString(connectionString)
            .buildClient();

CommunicationUserIdentifier user = communicationIdentityClient.createUser();
System.out.println("User id: " + user.getId());

CommunicationRelayConfiguration config = communicationRelayClient.getRelayConfiguration(user);
        
        System.out.println("Expires on:" + config.getExpiresOn());
        List<CommunicationIceServer> iceServers = config.getIceServers();

        for (CommunicationIceServer iceS : iceServers) {
            System.out.println("URLS: " + iceS.getUrls());
            System.out.println("Username: " + iceS.getUsername());
            System.out.println("Credential: " + iceS.getCredential());
        } 
```

## <a name="run-the-code"></a>Exécuter le code

Accédez au répertoire contenant le fichier *pom.xml*, puis compilez le projet à l’aide de la commande `mvn` suivante.

```console
mvn compile
```

Ensuite, générez le package.

```console
mvn package
```

Exécutez la commande `mvn` suivante pour exécuter l’application.

Accédez au répertoire contenant le fichier *pom.xml*, puis compilez le projet à l’aide de la commande `mvn` suivante.

```console
mvn compile
```

Ensuite, générez le package.

```console
mvn package
```

Exécutez la commande `mvn` suivante pour exécuter l’application.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
