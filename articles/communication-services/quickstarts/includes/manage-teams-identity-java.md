---
title: Fichier Include
description: Fichier Include
services: azure-communication-services
author: gistefan
manager: soricos
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 10/08/2021
ms.topic: include
ms.custom: include file
ms.author: gistefan
ms.openlocfilehash: 2efbb9772e70afd699d467668fd1eadd69502b04
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461144"
---
## <a name="set-up-prerequisites"></a>Configurer les prérequis

- [Java Development Kit (JDK)](/azure/developer/java/fundamentals/java-jdk-install) version 8 ou ultérieure.
- [Apache Maven](https://maven.apache.org/download.cgi).

## <a name="set-up"></a>Configurer

### <a name="create-a-new-java-application"></a>Créer une application Java

Ouvrez votre fenêtre de terminal ou de commande. Accédez au répertoire dans lequel vous souhaitez créer votre application Java. Exécutez la commande ci-dessous pour générer le projet Java à partir du modèle `maven-archetype-quickstart`.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Vous remarquerez que la tâche « generate » a créé un répertoire portant le même nom que l’`artifactId`. Sous ce répertoire, le répertoire `src/main/java` contient le code source du projet, le `src/test/java directory` contient la source de test et le fichier `pom.xml` est le modèle objet du projet (POM).

### <a name="install-the-package"></a>Installer le package

Ouvrez le fichier `pom.xml` dans votre éditeur de texte. Ajoutez les éléments de dépendance suivants au groupe de dépendances.

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-communication-identity</artifactId>
        <version>1.2.0-beta.1</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>msal4j</artifactId>
      <version>1.11.0</version>
    </dependency>
</dependencies>
```

### <a name="set-up-the-app-framework"></a>Configurer le framework d’application

À partir du répertoire de projet :

1. Accédez au répertoire `/src/main/java/com/communication/quickstart`.
1. Ouvrez le fichier `App.java` dans votre éditeur.
1. Remplacez l’instruction `System.out.println("Hello world!");`
1. Ajoutez des directives `import`.

Utilisez le code suivant pour commencer :

```java
package com.communication.quickstart;

import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.core.credential.*;
import com.microsoft.aad.msal4j.IAuthenticationResult;
import com.microsoft.aad.msal4j.InteractiveRequestParameters;
import com.microsoft.aad.msal4j.PublicClientApplication;

import java.io.IOException;
import java.time.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws Exception
    {
        System.out.println("Azure Communication Services - Communication access token Quickstart");
        // Quickstart code goes here
    }
}
```

### <a name="step-1-receive-the-azure-active-directory-user-token-via-the-msal-library"></a>Étape 1 : Recevoir le jeton utilisateur Azure Active Directory par le biais de la bibliothèque MSAL

La première étape du flux d’échange de jetons consiste à obtenir un jeton pour votre utilisateur Teams à l’aide de [Microsoft.Identity.Client](../../../active-directory/develop/reference-v2-libraries.md).

```java
String appId = "<contoso_application_id>";
String authority = "https://login.microsoftonline.com/<contoso_tenant_id>";

PublicClientApplication pca = PublicClientApplication.builder(appId)
        .authority(authority)
        .build();

String redirectUri = "http://localhost";
Set<String> scope = new HashSet<String>();
scope.add("https://auth.msft.communication.azure.com/VoIP");

InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI(redirectUri))
                    .scopes(scope)
                    .build();

IAuthenticationResult result = pca.acquireToken(parameters).get();
```

### <a name="step-2-initialize-the-communicationidentityclient"></a>Étape 2 : Initialiser CommunicationIdentityClient

Instanciez un `CommunicationIdentityClient` avec la clé d’accès et le point de terminaison de votre ressource. Découvrez comment [gérer la chaîne de connexion de la ressource](../create-communication-resource.md#store-your-connection-string). En outre, vous pouvez initialiser le client avec n’importe quel client HTTP personnalisé qui implémente l’interface `com.azure.core.http.HttpClient`.

Ajoutez le code suivant à la méthode `main` :

```java
//You can find your connection string from your resource in the Azure portal
String connectionString = "<connection_string>";

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .connectionString(connectionString)
    .buildClient();
```

### <a name="step-3-exchange-the-azure-ad-user-token-for-the-teams-access-token"></a>Étape 3 : Échanger le jeton utilisateur Azure AD contre le jeton d’accès Teams

Utilisez la méthode `getTokenForTeamsUser` afin d’émettre, pour l’utilisateur Teams, un jeton d’accès qui peut être utilisé avec les SDK Azure Communication Services.

```java
var accessToken = communicationIdentityClient.getTokenForTeamsUser(result.accessToken());
System.out.println("Token: " + accessToken.getToken());
```

## <a name="run-the-code"></a>Exécuter le code

Accédez au répertoire contenant le fichier `pom.xml`, puis compilez le projet à l’aide de la commande `mvn` suivante.

Ensuite, générez le package.

```console
mvn package
```

Exécutez la commande `mvn` suivante pour exécuter l’application.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
