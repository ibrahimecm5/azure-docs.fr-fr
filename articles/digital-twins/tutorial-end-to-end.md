---
title: 'Tutoriel : Connecter une solution de bout en bout'
titleSuffix: Azure Digital Twins
description: Suivez ce tutoriel pour savoir comment créer une solution Azure Digital Twins de bout en bout pilotée par les données d’appareil.
author: baanders
ms.author: baanders
ms.date: 10/19/2021
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 9233f33818e9961cc5daf8cf35b14457172ecae3
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131504832"
---
# <a name="tutorial-build-out-an-end-to-end-solution"></a>Tutoriel : Créer une solution de bout en bout

Ce tutoriel Azure Digital Twins décrit comment créer une solution de bout en bout qui illustre les fonctionnalités du service. Pour configurer une solution de bout en bout complète basée sur les données réelles de votre environnement, vous pouvez connecter votre instance Azure Digital Twins à d’autres services Azure pour la gestion des appareils et des données.

Ce tutoriel présente les procédures suivantes :
> [!div class="checklist"]
> * Configurer une instance Azure Digital Twins
> * Découvrir l’exemple de scénario d’un bâtiment et instancier les composants pré-écrits
> * Utiliser une application [Azure Functions](../azure-functions/functions-overview.md) pour router les données de télémétrie simulées depuis un appareil [IoT Hub](../iot-hub/about-iot-hub.md) vers les propriétés de jumeaux numériques
> * Propager les modifications par le biais du **graphe de jumeaux**, en traitant les notifications de jumeaux numériques avec Azure Functions, les points de terminaison et les routes

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="set-up-cloud-shell-session"></a>Configurer une session Cloud Shell
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="get-started-with-the-building-scenario"></a>Démarrer avec le scénario d’un bâtiment

L’exemple de projet utilisé dans ce tutoriel représente un **scénario de bâtiment** concret, comportant un étage, une pièce et un thermostat. Ces composants sont représentés numériquement dans une instance Azure Digital Twins, qui est ensuite connectée à [IoT Hub](../iot-hub/about-iot-hub.md), [Event Grid](../event-grid/overview.md) et à deux [fonctions Azure](../azure-functions/functions-overview.md) pour permettre le déplacement des données.

Voici un diagramme représentant le scénario complet. 

Vous allez successivement créer l’instance Azure Digital Twins (**section A** dans le diagramme), configurer le flux de données de télémétrie dans les jumeaux numériques (**flèche B**) et configurer la propagation des données par le biais du graphe de jumeaux (**flèche C**).

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="Diagramme du scénario de construction complet, qui montre les données circulant à partir d’un appareil vers et depuis Azure Digital Twins à travers différents services Azure.":::

Pour suivre le scénario, vous interagissez avec les composants de l’exemple d’application pré-écrite que vous avez téléchargé.

Voici les composants implémentés par l’exemple d’application *AdtSampleApp* du scénario d’un bâtiment :
* Authentification des appareils 
* Exemples d’utilisation du [SDK .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) (disponibles dans *CommandLoop.cs*)
* Interface de la console pour appeler l’API Azure Digital Twins
* *SampleClientApp* : exemple de solution Azure Digital Twins
* *SampleFunctionsApp* : application Azure Functions qui met à jour votre graphe Azure Digital Twins à partir des données de télémétrie issues des événements IoT Hub et Azure Digital Twins

### <a name="instantiate-the-pre-created-twin-graph"></a>Instancier le graphe de jumeaux pré-créé

Tout d’abord, vous allez utiliser la solution *AdtSampleApp* à partir de l’exemple de projet pour créer la partie Azure Digital Twins du scénario de bout en bout (**section A**) :

:::image type="content" source="media/tutorial-end-to-end/building-scenario-a.png" alt-text="Diagramme d’une partie du diagramme du scénario de construction complet soulignant la section de l’instance Azure Digital Twins.":::

Dans la fenêtre Visual Studio où le projet _**AdtE2ESample**_ est ouvert, exécutez le projet avec le bouton de la barre d’outils mis en évidence ci-dessous :

:::image type="content" source="media/tutorial-end-to-end/start-button-sample.png" alt-text="Capture d’écran du bouton de démarrage de Visual Studio avec le projet SampleClientApp ouvert.":::

Une fenêtre de console s’ouvre, exécute l’authentification et attend une commande. Dans cette console, exécutez la commande suivante pour instancier l’exemple de solution Azure Digital Twins.

> [!IMPORTANT]
> Si vous avez déjà des jumeaux numériques et des relations dans votre instance Azure Digital Twins, l’exécution de cette commande les supprime et les remplace par les jumeaux et les relations de l’exemple de scénario.

```cmd/sh
SetupBuildingScenario
```

Cette commande génère une série de messages confirmant la création et la connexion de trois [jumeaux numériques](concepts-twins-graph.md) dans votre instance d’Azure Digital Twins : un étage nommé floor1, une pièce nommée room21 et un capteur de température nommé thermostat67. Ces jumeaux numériques représentent les entités qui existeraient dans un environnement réel.

Ces entités sont connectées par le biais de relations dans le [graphe de jumeaux](concepts-twins-graph.md). Le graphe de jumeaux représente l’environnement dans son ensemble, y compris les interactions et les liens entre les entités.

:::image type="content" source="media/tutorial-end-to-end/building-scenario-graph.png" alt-text="Diagramme montrant que floor1 contient room21 et que room21 contient thermostat67." border="false":::

Vous pouvez vérifier les jumeaux qui ont été créées en exécutant la commande suivante, qui interroge l’instance Azure Digital Twins connectée afin de déterminer tous les jumeaux numériques qu’elle contient :

```cmd/sh
Query
```

[!INCLUDE [digital-twins-query-latency-note.md](../../includes/digital-twins-query-latency-note.md)]

Vous pouvez maintenant arrêter l’exécution du projet. Gardez toutefois la solution ouverte dans Visual Studio, car vous l’utiliserez tout au long du tutoriel.

## <a name="set-up-the-sample-function-app"></a>Configurer l’exemple d’application de fonction

L’étape suivante consiste à configurer une [application Azure Functions](../azure-functions/functions-overview.md) qui sera utilisée tout au long de ce tutoriel pour traiter les données. L’application de fonction, *SampleFunctionsApp*, contient deux fonctions :
* *ProcessHubToDTEvents* : traite les données IoT Hub entrantes et met à jour Azure Digital Twins en conséquence
* *ProcessDTRoutedData* : traite les données provenant des jumeaux numériques et met à jour les jumeaux parents dans Azure Digital Twins en conséquence

Dans cette section, vous allez publier l’application de fonction pré-écrite et lui attribuer une identité Azure Active Directory (Azure AD) pour qu’elle puisse accéder à Azure Digital Twins. Une fois ces étapes effectuées, vous pourrez utiliser les fonctions de l’application de fonction dans le reste du tutoriel. 

De retour dans la fenêtre Visual Studio dans laquelle le projet _**AdtE2ESample**_ est ouvert, l’application de fonction se trouve dans le fichier de projet _**SampleFunctionsApp**_. Vous pouvez l’afficher dans le volet *Explorateur de solutions*.

### <a name="update-dependencies"></a>Mettre à jour les dépendances

Avant de publier l’application, il est judicieux de vérifier que vos dépendances sont à jour pour être certain que vous disposez de la dernière version de tous les packages inclus.

Dans le volet *Explorateur de solutions*, développez _**SampleFunctionsApp** > Dépendances_. Cliquez avec le bouton droit sur *Packages*, puis sélectionnez *Gérer les packages NuGet...* .

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-1.png" alt-text="Capture d’écran de Visual Studio montrant le bouton de menu « Gérer les packages NuGet » pour le projet SampleFunctionsApp." border="false":::

Cette action a pour effet d’ouvrir le Gestionnaire de package NuGet. Sélectionnez l’onglet *Mises à jour* et, s’il y a des packages à mettre à jour, activez la case à cocher *Sélectionner tous les packages*. Sélectionnez ensuite *Mettre à jour*.

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-2.png" alt-text="Capture d’écran de Visual Studio montrant comment sélectionner pour mettre à jour tous les packages dans le Gestionnaire de packages NuGet.":::

### <a name="publish-the-app"></a>Publier l’application

Pour publier l’application de fonction sur Azure, vous devez d’abord créer un compte de stockage, puis créer l’application de fonction dans Azure et enfin publier les fonctions dans l’application de fonction Azure. Cette section complète ces actions à l’aide d’Azure CLI.

1. Créez un **compte de stockage Azure** en exécutant la commande suivante :

    ```azurecli-interactive
    az storage account create --name <name-for-new-storage-account> --location <location> --resource-group <resource-group> --sku Standard_LRS
    ```

1. Créez une **application de fonction Azure** en exécutant la commande suivante :

    ```azurecli-interactive
    az functionapp create --name <name-for-new-function-app> --storage-account <name-of-storage-account-from-previous-step> --consumption-plan-location <location> --runtime dotnet --resource-group <resource-group>
    ```

1. Ensuite, vous allez compresser les fonctions au format **zip** et les **publier** dans votre nouvelle application de fonction Azure.

    1. Ouvrez un terminal comme PowerShell sur votre ordinateur local, puis accédez aux [exemples de référentiel Digital Twins](https://github.com/azure-samples/digital-twins-samples/tree/master/) que vous avez téléchargés précédemment dans le tutoriel. Dans le dossier du référentiel téléchargé, accédez à *digital-twins-samples-master\AdtSampleApp\SampleFunctionsApp*.
    
    1. Dans votre terminal, exécutez la commande suivante pour publier le projet :

        ```powershell
        dotnet publish -c Release
        ```

        Cette commande publie le projet dans le répertoire *digital-twins-samples-master\AdtSampleApp\SampleFunctionsApp\bin\Release\netcoreapp3.1\publish*.

    1. Créez un zip des fichiers publiés qui se trouvent dans le répertoire *digital-twins-samples-master\AdtSampleApp\SampleFunctionsApp\bin\Release\netcoreapp3.1\publish*. 
        
        Si vous utilisez PowerShell, vous pouvez créer le fichier zip en copiant le chemin complet de ce répertoire *\publish* et en le collant dans la commande suivante :
    
        ```powershell
        Compress-Archive -Path <full-path-to-publish-directory>\* -DestinationPath .\publish.zip
        ```

        La cmdlet crée un fichier **publish.zip** dans le répertoire de votre terminal qui comprend un fichier *host.json*, ainsi que les répertoires *bin*, *ProcessDTRoutedData* et *ProcessHubToDTEvents*.

        Si vous n’utilisez pas PowerShell et que vous n’avez pas accès à la cmdlet `Compress-Archive`, vous devez compresser les fichiers au format zip à l’aide d’Explorateur de fichiers ou d’une autre méthode.

1. Dans Azure CLI, exécutez la commande suivante pour déployer les fonctions publiées et compressées dans votre application de fonction Azure :

    ```azurecli-interactive
    az functionapp deployment source config-zip --resource-group <resource-group> --name <name-of-your-function-app> --src "<full-path-to-publish.zip>"
    ```

    > [!NOTE]
    > Si vous utilisez Azure CLI localement, vous pouvez accéder au fichier ZIP sur votre ordinateur directement à l’aide de son chemin d’accès sur votre machine.
    > 
    >Si vous utilisez Azure Cloud Shell, chargez le fichier ZIP sur Cloud Shell à l’aide de ce bouton avant d’exécuter la commande :
    >
    > :::image type="content" source="media/tutorial-end-to-end/azure-cloud-shell-upload.png" alt-text="Capture d’écran d’Azure Cloud Shell avec mise en évidence la méthode de chargement des fichiers.":::
    >
    > Dans ce cas, le fichier sera téléchargé dans le répertoire racine de votre stockage Cloud Shell, de sorte que vous pouvez faire référence au fichier directement par son nom pour le paramètre `--src` de la commande (par exemple, `--src publish.zip`).

    Un déploiement réussi répond avec le code d’état 202 et génère un objet JSON contenant les détails de votre nouvelle fonction. Vous pouvez vérifier que le déploiement a réussi en recherchant ce champ dans le résultat :

    ```json
    {
      ...
      "provisioningState": "Succeeded",
      ...
    }
    ```

Vous avez maintenant publié les fonctions dans une application de fonction sur Azure.

Par ailleurs, pour que votre application de fonction puisse accéder à Azure Digital Twins, elle doit avoir l’autorisation d’accéder à votre instance Azure Digital Twins. Vous allez configurer cet accès dans la section suivante.

### <a name="configure-permissions-for-the-function-app"></a>Configurer des autorisations pour l’application de fonction

Deux paramètres doivent être définis pour que l’application de fonction ait accès à votre instance Azure Digital Twins. Vous pouvez les définir à l’aide d’Azure CLI. 

#### <a name="assign-access-role"></a>Attribuer le rôle d’accès

Le premier paramètre donne à l’application de fonction le rôle de **Propriétaire des données Azure Digital Twins** dans l’instance Azure Digital Twins. Ce rôle est requis pour tout utilisateur ou fonction souhaitant effectuer de nombreuses activités de plan de données sur l’instance. Vous pouvez en savoir plus sur la sécurité et les attributions de rôles dans [Sécurité pour les solutions Azure Digital Twins](concepts-security.md). 

1. Utilisez la commande suivante pour consulter les détails de l’identité managée par le système pour la fonction. Prenez note du champ **principalId** dans la sortie.

    ```azurecli-interactive 
    az functionapp identity show --resource-group <your-resource-group> --name <your-function-app-name> 
    ```

    >[!NOTE]
    > Si le résultat est vide au lieu d’afficher les détails d’une identité, créez une autre identité managée par le système pour la fonction à l’aide de cette commande :
    > 
    >```azurecli-interactive    
    >az functionapp identity assign --resource-group <your-resource-group> --name <your-function-app-name>  
    >```
    >
    > La sortie affiche alors les détails de l’identité, notamment la valeur **principalId** nécessaire pour la prochaine étape. 

1. Utilisez la valeur **principalId** dans la commande suivante afin d’attribuer l’identité de l’application de fonction au rôle **Propriétaire des données Azure Digital Twins** pour votre instance Azure Digital Twins.

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

Les informations générées par cette commande décrivent l’attribution de rôle que vous avez créée. L’application de fonction dispose maintenant des autorisations nécessaires pour accéder aux données dans votre instance Azure Digital Twins.

#### <a name="configure-application-settings"></a>Configurer les paramètres de l’application

Le deuxième paramètre crée une **variable d’environnement** pour la fonction avec l’URL de votre instance Azure Digital Twins. Le code de fonction utilisera la valeur de cette variable pour référencer votre instance. Pour plus d’informations sur les variables d’environnement, consultez [Gérer votre application de fonction](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). 

Exécutez la commande ci-dessous, en remplissant les espaces réservés avec les détails de vos ressources.

```azurecli-interactive
az functionapp config appsettings set --resource-group <your-resource-group> --name <your-function-app-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```

La sortie est la liste des paramètres de la fonction Azure, qui doit maintenant contenir une entrée appelée **ADT_SERVICE_URL**.


## <a name="process-simulated-telemetry-from-an-iot-hub-device"></a>Traiter les données de télémétrie simulées provenant d’un appareil IoT Hub

Un graphe Azure Digital Twins repose sur les données de télémétrie provenant d’appareils réels. 

Au cours de cette étape, vous allez connecter un thermostat simulé inscrit sur [IoT Hub](../iot-hub/about-iot-hub.md) au jumeau numérique qui le représente dans Azure Digital Twins. À mesure que l’appareil simulé émet des données de télémétrie, celles-ci sont acheminées via la fonction Azure *ProcessHubToDTEvents* qui déclenche une mise à jour correspondante dans le jumeau numérique. De cette façon, le jumeau numérique reste à jour avec les données de l’appareil réel. Dans Azure Digital Twins, le processus de redirection des données d’événements est appelé [événements de routage](concepts-route-events.md).

Le traitement de la télémétrie simulée a lieu dans cette partie du scénario de bout en bout (**flèche B**) :

:::image type="content" source="media/tutorial-end-to-end/building-scenario-b.png" alt-text="Diagramme d’une partie du diagramme du scénario de construction complet soulignant la section qui montre les éléments avant Azure Digital Twins.":::

Voici les actions que vous allez effectuer pour configurer la connexion de l’appareil :
1. Créer un hub IoT qui gérera l’appareil simulé
2. Connecter le hub IoT à la fonction Azure appropriée en configurant un abonnement aux événements
3. Inscrire l’appareil simulé dans le hub IoT
4. Exécuter l’appareil simulé et générer les données de télémétrie
5. Interroger Azure Digital Twins pour voir les résultats réels

### <a name="create-an-iot-hub-instance"></a>Créer une instance IoT Hub

Azure Digital Twins est conçu pour fonctionner avec [IoT Hub](../iot-hub/about-iot-hub.md), service Azure de gestion des appareils et de leurs données. Dans cette étape, vous allez configurer un hub IoT qui gérera l’appareil utilisé en exemple dans ce tutoriel.

Dans Azure Cloud Shell, utilisez cette commande pour créer un hub IoT :

```azurecli-interactive
az iot hub create --name <name-for-your-IoT-hub> --resource-group <your-resource-group> --sku S1
```

Les informations générées par cette commande décrivent le hub IoT qui a été créé.

Enregistrez le **nom** que vous avez donné à votre hub IoT. Vous le réutiliserez ultérieurement.

### <a name="connect-the-iot-hub-to-the-azure-function"></a>Connecter le hub IoT à la fonction Azure

Connectez ensuite votre hub IoT à la fonction Azure *ProcessHubToDTEvents* dans l’application de fonction que vous avez publiée, afin que les données puissent passer de l’appareil dans IoT Hub à la fonction, qui met à jour Azure Digital Twins.

Pour ce faire, vous créez un **abonnement aux événements** sur votre hub IoT, avec la fonction Azure comme point de terminaison. Cette opération « abonne » la fonction aux événements qui se produisent dans IoT Hub.

Dans le [portail Azure](https://portal.azure.com/), accédez au hub IoT tout juste créé en recherchant son nom dans la barre de recherche supérieure. Sélectionnez *Événements* dans le menu du hub, puis sélectionnez *+ Abonnement aux événements*.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1.png" alt-text="Capture d’écran du portail Azure montrant l’abonnement aux événements IoT Hub.":::

La sélection de cette option affiche la page *Créer un abonnement aux événements*.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-2.png" alt-text="Capture d’écran du portail Azure montrant comment créer un abonnement aux événements.":::

Renseignez les champs comme suit (les champs remplis par défaut ne sont pas mentionnés) :
* *DÉTAILS DE L’ABONNEMENT AUX ÉVÉNEMENTS* > **Nom** : Donnez un nom à votre abonnement aux événements.
* *DÉTAILS DE LA RUBRIQUE* > **Nom de la rubrique système** : spécifiez un nom à utiliser pour la rubrique système. 
* *TYPES D’ÉVÉNEMENTS* > **Filtrer les types d’événements** : Sélectionnez *Télémétrie d’appareil* dans les options de menu.
* *DÉTAILS DU POINT DE TERMINAISON* > **Type de point de terminaison** : Sélectionnez *Fonction Azure* dans les options de menu.
* *DÉTAILS DU POINT DE TERMINAISON* > **Point de terminaison** : sélectionnez le lien *Sélectionner un point de terminaison*. Cette action ouvre une fenêtre *Sélectionner une fonction Azure* :   :::image type="content" source="media/tutorial-end-to-end/event-subscription-3.png" alt-text="Capture d’écran de l’abonnement aux événements dans le portail Azure montrant la fenêtre de sélection d’une fonction Azure." border="false":::
    - Renseignez les valeurs **Abonnement**, **Groupe de ressources**, **Application de fonction** et **Fonction** (*ProcessHubToDTEvents*). Il est possible que certaines de ces valeurs soient automatiquement renseignées après votre sélection de l’abonnement.
    - Sélectionnez **Confirmer la sélection**.

Dans la page *Créer un abonnement aux événements*, sélectionnez **Créer**.

### <a name="register-the-simulated-device-with-iot-hub"></a>Inscrire l’appareil simulé auprès d’IoT Hub 

Dans cette section, vous allez créer une représentation d’appareil dans IoT Hub avec l’ID thermostat67. L’appareil simulé se connecte à cette représentation, qui décrit comment les événements de télémétrie sont envoyés de l’appareil vers IoT Hub. IoT Hub est l’endroit où la fonction Azure souscrite à l’étape précédente est à l’écoute, prête à récupérer les événements et à continuer le traitement.

Dans Azure Cloud Shell, créez un appareil dans IoT Hub à l’aide de la commande suivante :

```azurecli-interactive
az iot hub device-identity create --device-id thermostat67 --hub-name <your-IoT-hub-name> --resource-group <your-resource-group>
```

Les informations générées décrivent l’appareil qui a été créé.

### <a name="configure-and-run-the-simulation"></a>Configurer et démarrer la simulation

Ensuite, configurez le simulateur d’appareil pour envoyer des données à votre instance IoT Hub.

Commencez par obtenir la *chaîne de connexion IoT Hub* à l’aide de cette commande :

```azurecli-interactive
az iot hub connection-string show --hub-name <your-IoT-hub-name>
```

Ensuite, récupérez la *chaîne de connexion de l’appareil* à l’aide de la commande suivante :

```azurecli-interactive
az iot hub device-identity connection-string show --device-id thermostat67 --hub-name <your-IoT-hub-name>
```

Vous intégrerez ces valeurs au code du simulateur d’appareil de votre projet local pour connecter le simulateur à ce hub IoT et à l’appareil IoT Hub.

Dans une nouvelle fenêtre Visual Studio, ouvrez (à partir du dossier de solution téléchargé) _Device Simulator > **DeviceSimulator.sln**_.

>[!NOTE]
> Vous devez maintenant avoir deux fenêtres Visual Studio : une avec _**DeviceSimulator.sln**_ et une autre antérieure avec _**AdtE2ESample.sln**_.

Dans le volet *Explorateur de solutions* de cette nouvelle fenêtre Visual Studio, sélectionnez _DeviceSimulator/**AzureIoTHub.cs**_ pour l’ouvrir dans la fenêtre d’édition. Remplacez les valeurs de chaîne de connexion suivantes par les valeurs que vous venez de collecter :

```csharp
iotHubConnectionString = <your-hub-connection-string>
deviceConnectionString = <your-device-connection-string>
```

Enregistrez le fichier .

Maintenant, pour voir les résultats de la simulation de données que vous avez configurée, exécutez le projet **DeviceSimulator** avec le bouton de la barre d’outils mis en évidence ci-dessous :

:::image type="content" source="media/tutorial-end-to-end/start-button-simulator.png" alt-text="Capture d’écran du bouton de démarrage de Visual Studio avec le projet DeviceSimulator ouvert.":::

Une fenêtre de console s’ouvre, affichant les messages de télémétrie de température simulée. Ces messages sont envoyés à IoT Hub, avant d’être récupérés et traités par la fonction Azure.

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="Capture d’écran de la sortie de la console du simulateur d’appareil montrant les données de télémétrie de température envoyées.":::

Vous n’avez rien d’autre à faire dans cette console, mais laissez-la s’exécuter pendant que vous effectuez les étapes suivantes.

### <a name="see-the-results-in-azure-digital-twins"></a>Voir les résultats dans Azure Digital Twins

La fonction *ProcessHubToDTEvents* que vous avez publiée écoute les données IoT Hub et appelle une API Azure Digital Twins pour mettre à jour la propriété *Température* sur le jumeau thermostat67.

Pour voir les données du côté d’Azure Digital Twins, accédez à la fenêtre Visual Studio dans laquelle le projet _**AdtE2ESample**_ est ouvert et exécutez-le.

Dans la fenêtre de console du projet qui s’ouvre, exécutez la commande suivante pour récupérer les températures signalées par le jumeau numérique thermostat67 :

```cmd
ObserveProperties thermostat67 Temperature
```

Les températures mises à jour réelles *issues de votre instance Azure Digital Twins* doivent normalement être journalisées dans la console toutes les deux secondes.

>[!NOTE]
> La propagation des données de l’appareil vers le jumeau peut prendre quelques secondes. Les premières valeurs de température peuvent indiquer 0 avant l’arrivée des données.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry.png" alt-text="Capture d’écran de la sortie de la console montrant le journal des messages de température issus du jumeau numérique thermostat67.":::

Une fois que vous avez vérifié le bon fonctionnement de la journalisation des températures réelles, vous pouvez arrêter l’exécution des deux projets. Laissez les fenêtres Visual Studio ouvertes, car vous continuerez à les utiliser dans le reste du tutoriel.

## <a name="propagate-azure-digital-twins-events-through-the-graph"></a>Propager des événements Azure Digital Twins à travers le graphe

Jusqu’à ce stade du tutoriel, vous avez vu comment Azure Digital Twins peut être mis à jour à partir de données d’appareil externes. Vous allez à présent voir comment les modifications apportées à un seul jumeau numérique peuvent se propager dans le graphe Azure Digital Twins, en d’autres termes, comment mettre à jour les jumeaux à partir de données internes au service.

Pour ce faire, vous utilisez la fonction Azure *ProcessDTRoutedData* afin de mettre à jour un jumeau Room quand le jumeau Thermostat connecté est mis à jour. Cette fonctionnalité de mise à jour est exécutée dans cette partie du scénario de bout en bout (**flèche C**) :

:::image type="content" source="media/tutorial-end-to-end/building-scenario-c.png" alt-text="Diagramme d’une partie du diagramme du scénario de construction complet soulignant la section qui montre les éléments après Azure Digital Twins.":::

Voici les actions à effectuer pour configurer ce flux de données :
1. [Créer une rubrique Event Grid](#create-the-event-grid-topic) pour permettre le déplacement de données entre les services Azure
1. [Créer un point de terminaison](#create-the-endpoint) dans Azure Digital Twins qui connecte l’instance à la rubrique Event Grid
1. [Configurer une route](#create-the-route) dans Azure Digital Twins qui envoie les événements de modification de propriété de jumeau au point de terminaison
1. [Configurer une fonction Azure](#connect-the-azure-function) qui écoute la rubrique Event Grid sur le point de terminaison, reçoit les événements de modification de propriété de jumeau qui y sont envoyés et met à jour les autres jumeaux dans le graphe en conséquence

[!INCLUDE [digital-twins-twin-to-twin-resources.md](../../includes/digital-twins-twin-to-twin-resources.md)]

### <a name="connect-the-azure-function"></a>Connexion à la fonction Azure

Vous allez à présent abonner la fonction Azure *ProcessDTRoutedData* à la rubrique Event Grid que vous avez créée, afin que les données de télémétrie puissent aller du jumeau thermostat67, via la rubrique Event Grid, à la fonction, qui, une fois dans Azure Digital Twins, met à jour le jumeau Room21.

Pour ce faire, vous allez créer un **abonnement Event Grid** qui envoie des données à partir de la **rubrique Event Grid** créée précédemment à votre fonction Azure *ProcessDTRoutedData*.

Dans le [portail Azure](https://portal.azure.com/), accédez à votre rubrique Event Grid en recherchant son nom dans la barre de recherche supérieure. Sélectionnez *+ Abonnement aux événements*.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1b.png" alt-text="Capture d’écran du portail Azure montrant comment créer un abonnement aux événements Event Grid.":::

Les étapes de création de cet abonnement aux événements sont similaires à celles que vous avez suivies pour abonner la première fonction Azure à IoT Hub plus haut dans ce tutoriel. Cette fois, vous n’avez pas besoin de spécifier *Télémétrie d’appareil* comme type d’événement à écouter et vous allez vous connecter à une autre fonction Azure.

Dans la page *Créer un abonnement aux événements*, renseignez les champs comme suit (les champs remplis par défaut ne sont pas mentionnés) :
* *DÉTAILS DE L’ABONNEMENT AUX ÉVÉNEMENTS* > **Nom** : Donnez un nom à votre abonnement aux événements.
* *DÉTAILS DU POINT DE TERMINAISON* > **Type de point de terminaison** : Sélectionnez *Fonction Azure* dans les options de menu.
* *DÉTAILS DU POINT DE TERMINAISON* > **Point de terminaison** : sélectionnez le lien *Sélectionner un point de terminaison* pour ouvrir une fenêtre *Sélectionner une fonction Azure* :
    - Renseignez les valeurs **Abonnement**, **Groupe de ressources**, **Application de fonction** et **Fonction** (*ProcessDTRoutedData*). Il est possible que certaines de ces valeurs soient automatiquement renseignées après votre sélection de l’abonnement.
    - Sélectionnez **Confirmer la sélection**.

Dans la page *Créer un abonnement aux événements*, sélectionnez **Créer**.

## <a name="run-the-simulation-and-see-the-results"></a>Exécuter la simulation et afficher les résultats

À présent, les événements doivent pouvoir passer de l’appareil simulé vers Azure Digital Twins et, à travers le graphe Azure Digital Twins, mettre à jour les jumeaux selon les besoins. Dans cette section, vous allez réexécuter le simulateur d’appareil pour lancer le flux d’événements complet que vous avez configuré, puis interroger Azure Digital Twins pour voir les résultats réels

Accédez à la fenêtre Visual Studio dans laquelle le projet _**DeviceSimulator**_ est ouvert, puis exécutez le projet.

Comme quand vous avez exécuté le simulateur d’appareil plus haut, une fenêtre de console s’ouvre, affichant les messages de télémétrie de température simulée. Ces événements empruntent le flux que vous avez configuré plus haut pour mettre à jour le jumeau thermostat67, puis le flux que vous avez configuré récemment pour mettre à jour le jumeau Room21 en conséquence.

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="Capture d’écran de la sortie de la console du simulateur d’appareil montrant les données de télémétrie de température envoyées.":::

Vous n’avez rien d’autre à faire dans cette console, mais laissez-la s’exécuter pendant que vous effectuez les étapes suivantes.

Pour voir les données du côté d’Azure Digital Twins, accédez à la fenêtre Visual Studio dans laquelle le projet _**AdtE2ESample**_ est ouvert et exécutez-le.

Dans la fenêtre de console du projet qui s’ouvre, exécutez la commande suivante pour récupérer les températures signalées **à la fois** par le jumeau numérique thermostat67 et par le jumeau numérique room21.

```cmd
ObserveProperties thermostat67 Temperature room21 Temperature
```

Les températures mises à jour réelles *issues de votre instance Azure Digital Twins* doivent normalement être journalisées dans la console toutes les deux secondes. Notez que la température de Room21 est mise à jour pour correspondre aux mises à jour apportées à thermostat67.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry-b.png" alt-text="Capture d’écran de la sortie de la console montrant le journal des messages de température entre un thermostat et une pièce.":::

Une fois que vous avez vérifié le bon fonctionnement de la journalisation des températures réelles à partir de votre instance, vous pouvez arrêter l’exécution des deux projets. Vous pouvez également fermer les fenêtres de Visual Studio, car le tutoriel est maintenant terminé.

## <a name="review"></a>Révision

Voici une revue du scénario que vous avez créé au cours de ce tutoriel.

1. Une instance Azure Digital Twins représente numériquement un étage, une pièce et un thermostat (représentés par la **section A** du diagramme ci-dessous)
2. La télémétrie des appareils simulés est envoyée à IoT Hub, où la fonction Azure *ProcessHubToDTEvents* écoute les événements de télémétrie. La fonction Azure *ProcessHubToDTEvents* utilise les informations contenues dans ces événements pour définir la propriété *Temperature* sur thermostat67 (**flèche B** du diagramme).
3. Les événements de modification de propriété dans Azure Digital Twins sont routés vers une rubrique Event Grid, où la fonction Azure *ProcessDTRoutedData* écoute les événements. La fonction Azure *ProcessDTRoutedData* utilise les informations contenues dans ces événements pour définir la propriété *Temperature* sur room21 (**flèche C** du diagramme).

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="Diagramme du scénario de construction complet, qui montre les données circulant à partir d’un appareil vers et depuis Azure Digital Twins à travers différents services Azure.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

À l’issue de ce tutoriel, vous pourrez choisir les ressources à supprimer, en fonction de ce que vous souhaitez faire ensuite.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

* **Si vous souhaitez continuer à utiliser l’instance d’Azure Digital Twins que vous avez configurée dans cet article, tout en effaçant complètement ou partiellement ses modèles, jumeaux et relations**, vous pouvez utiliser les commandes CLI [az dt](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) dans une fenêtre [Azure Cloud Shell](https://shell.azure.com) pour supprimer les éléments à enlever.

    Cette option ne supprime pas les autres ressources Azure créées dans ce tutoriel (hub IoT, application Azure Functions, etc.). Vous pouvez les supprimer individuellement à l’aide des [ commande dt](/cli/azure/reference-index?view=azure-cli-latest&preserve-view=true) appropriées pour chaque type de ressource.

Vous pouvez également supprimer le dossier de projet de votre ordinateur local.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé un scénario de bout en bout qui montre comment exploiter Azure Digital Twins à l’aide de données d’appareil réelles.

Vous pouvez à présent commencer à consulter la documentation de concept pour en savoir plus sur les éléments avec lesquels vous avez travaillé dans le tutoriel :

> [!div class="nextstepaction"]
> [Modèles personnalisés](concepts-models.md)