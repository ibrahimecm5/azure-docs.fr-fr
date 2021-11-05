---
title: 'Tutoriel : Utiliser la configuration dynamique avec l’actualisation en mode push dans une application Java Spring avec une seule instance'
titleSuffix: Azure App Configuration
description: Dans ce tutoriel, vous allez apprendre à mettre à jour dynamiquement les données de configuration pour une application Java Spring avec l’actualisation en mode push
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: java
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: mametcal
ms.openlocfilehash: 340638bd5fba981525ac3ff148ed4076f093e178
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020917"
---
# <a name="tutorial-use-dynamic-configuration-using-push-refresh-in-a-java-spring-app"></a>Tutoriel : Utiliser la configuration dynamique avec l’actualisation en mode push dans une application Java Spring

La bibliothèque de client Java Spring App Configuration prend en charge la mise à jour de la configuration à la demande, sans entraîner le redémarrage de l’application. Une application peut être configurée pour détecter les changements dans App Configuration à l’aide de l’une ou des deux approches suivantes.

- Modèle par interrogation (polling) : il s’agit du comportement par défaut qui utilise l’interrogation pour détecter des changements de configuration. Quand la valeur mise en cache d’un paramètre expire, l’appel suivant au `refreshConfigurations` de `AppConfigurationRefresh` envoie une demande au serveur pour vérifier si la configuration a changé et tire (pull) la configuration mise à jour si nécessaire.

- Modèle d’envoi (push) : utilise les [événements App Configuration](./concept-app-configuration-event.md) pour détecter des changements de configuration. Une fois App Configuration configuré pour envoyer les événements de changement de paire clé-valeur avec Event Grid via un [webhook](../event-grid/handler-event-hubs.md), l’application peut utiliser ces événements pour optimiser le nombre total de demandes nécessaires pour tenir la configuration à jour.

Ce tutoriel vous montre comment implémenter des mises à jour de la configuration dynamique dans votre code à l’aide de l’actualisation en mode push. Il s’appuie sur l’application mentionnée dans les guides de démarrage rapide. Avant de continuer, suivez d’abord [Créer une application Java Spring avec App Configuration](./quickstart-java-spring-app.md).

Vous pouvez utiliser l’éditeur de code de votre choix pour exécuter les étapes de ce tutoriel. [Visual Studio Code](https://code.visualstudio.com/) est une excellente option qui est disponible sur les plateformes Windows, macOS et Linux.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Configurer un abonnement pour envoyer des événements de changement de configuration d’App Configuration à un webhook
> * Déployer une application Spring Boot sur App Service
> * Configurez votre application Java Spring pour mettre à jour sa configuration en réponse aux changements survenant dans App Configuration.
> * Utiliser la configuration la plus récente dans votre application.

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)
- Un [kit de développement Java (JDK)](/java/azure/jdk) pris en charge avec version 8
- [Apache Maven](https://maven.apache.org/download.cgi) version 3.0 ou ultérieure
- Un magasin Azure App Configuration existant.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="setup-push-refresh"></a>Configurer l’actualisation Push

1. Ouvrez *pom.xml* et mettez à jour le fichier avec les dépendances suivantes.

   ```xml
           <dependency>
               <groupId>com.azure.spring</groupId>
               <artifactId>azure-spring-cloud-appconfiguration-config-web</artifactId>
               <version>2.0.0</version>
           </dependency>
   
           <!-- Adds the Ability to Push Refresh -->
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-actuator</artifactId>
           </dependency>
   ```

1. Configurez [Maven App Service Deployment](../app-service/quickstart-java.md?tabs=javase) pour que l'application puisse être déployée sur Azure App Service via Maven.

   ```console
   mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config
   ```

1. Ouvrez bootstrap.properties et configurez l’actualisation Push et Azure Service Bus pour Azure App Configuration

   ```properties
   # Azure App Configuration Properties
   spring.cloud.azure.appconfiguration.stores[0].connection-string= ${AppConfigurationConnectionString}
   spring.cloud.azure.appconfiguration.stores[0].monitoring.enabled= true
   spring.cloud.azure.appconfiguration.stores[0].monitoring.cacheExpiration= 30d
   spring.cloud.azure.appconfiguration.stores[0].monitoring.triggers[0].key= sentinel
   spring.cloud.azure.appconfiguration.stores[0].monitoring.push-notification.primary-token.name= myToken
   spring.cloud.azure.appconfiguration.stores[0].monitoring.push-notification.primary-token.secret= myTokenSecret
   
   management.endpoints.web.exposure.include= "appconfiguration-refresh"
   ```

Un délai aléatoire est ajouté avant le marquage de la valeur mise en cache comme modifiée pour réduire la limitation potentielle. Le délai maximal par défaut avant que la valeur mise en cache soit marquée comme corrompue est de 30 secondes.

> [!NOTE]
> Le nom du jeton principal doit être stocké dans App configuration en tant que clé, puis le secret du jeton principal doit être stocké en tant que référence au coffre de clés d’App Configuration pour renforcer la sécurité.

## <a name="build-and-run-the-app-locally"></a>Générer et exécuter l’application localement

Les webhooks d’Event Grid doivent être validés lors de leur création. Vous pouvez les valider en suivant ce [Guide](../event-grid/webhook-event-delivery.md) ou en démarrant votre application avec la bibliothèque d’Azure App Configuration déjà configurée, ce qui permet d’inscrire votre application pour vous. Pour utiliser un abonnement aux événements, suivez les étapes décrites dans les deux sections suivantes.

1. Définissez la variable d’environnement sur la chaîne de connexion de votre instance d’App Configuration :

    #### <a name="windows-command-prompt"></a>[Invite de commandes Windows](#tab/cmd)

    ```cmd
    setx AppConfigurationConnectionString <connection-string-of-your-app-configuration-store>
    ```

    #### <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```PowerShell
    $Env:AppConfigurationConnectionString = <connection-string-of-your-app-configuration-store>
    ```

    #### <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    export AppConfigurationConnectionString = <connection-string-of-your-app-configuration-store>
    ```

1. Exécutez la commande suivante pour générer l’application console :

   ```shell
    mvn package
   ```

1. La génération terminée correctement, lancez la commande suivante pour exécuter l’application localement :

    ```shell
    mvn spring-boot:deploy
    ```

## <a name="set-up-an-event-subscription"></a>Configurer un abonnement à un événement

1. Ouvrez la ressource App Configuration dans le portail Azure, puis cliquez sur `+ Event Subscription` dans le volet `Events`.

    :::image type="content" source="./media/events-pane.png" alt-text="Le volet des événements contient une option permettant de créer des abonnements." :::

1. Entrez un nom pour `Event Subscription` et `System Topic`. Par défaut, les types d'événements valeur-clé modifiée et valeur-clé supprimée sont définis. Vous pouvez les modifier et utiliser l'onglet Filtres pour choisir les raisons exactes pour lesquelles un événement Push sera envoyé.

    :::image type="content" source="./media/create-event-subscription.png" alt-text="Les événements requièrent un nom, une rubrique et des filtres." :::

1. Sélectionnez `Endpoint Type` en tant que `Web Hook`, sélectionnez `Select an endpoint`.

    :::image type="content" source="./media/event-subscription-webhook-endpoint.png" alt-text="La sélection d’un point de terminaison crée un nouveau panneau pour entrer l’URL du point de terminaison." :::

1. Le point de terminaison est l’URL de l’application + « /actuator/appconfiguration-refresh?{your-token-name}={your-token-secret} ». Par exemple `https://my-azure-webapp.azurewebsites.net/actuator/appconfiguration-refresh?myToken=myTokenSecret`

1. Cliquez sur `Create` pour créer l’abonnement aux événements. Lorsque `Create` est sélectionné, une demande d’inscription pour le webhook est envoyée à votre application. Cette valeur est reçue par la bibliothèque cliente d’Azure App Configuration, vérifiée et renvoie une réponse valide.

1. Cliquez sur `Event Subscriptions` dans le volet `Events` pour valider que l’abonnement a bien été créé.

    :::image type="content" source="./media/event-subscription-view-webhook.png" alt-text="Le webhook apparaît dans une table en bas de la page." :::

> [!NOTE]
> Quand vous vous abonnez à des changements de configuration, vous pouvez utiliser un ou plusieurs filtres pour réduire le nombre d’événements envoyés à votre application. Vous pouvez les configurer comme [filtres d’abonnement Event Grid](../event-grid/event-filtering.md) ou comme [filtres d’abonnement Service Bus](../service-bus-messaging/topic-filters.md). Par exemple, vous pouvez utiliser un filtre d’abonnement pour vous abonner uniquement aux événements liés à des changements dans une clé qui commence par une chaîne spécifique.

## <a name="verify-and-test-application"></a>Vérifier et tester l’application

1. Lorsque votre application s’exécute, utilisez *curl* pour la tester. Par exemple :

   ```cmd
   curl -X GET http://localhost:8080
   ```

1. Ouvrez le **portail Azure** et accédez à la ressource App Configuration associée à votre application. Sélectionnez **Explorateur de configuration** sous **Opérations** et mettez à jour les valeurs des clés suivantes :

    | Clé | Valeur |
    |---|---|
    | application/config.message | Hello – Mis à jour |

1. Actualisez la page du navigateur pour afficher le nouveau message.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez permis à votre application Java d’actualiser dynamiquement les paramètres de configuration à partir d’App Configuration. Pour savoir comment utiliser une identité managée Azure afin de simplifier l’accès à App Configuration, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Intégration des identités managées](./howto-integrate-azure-managed-service-identity.md)