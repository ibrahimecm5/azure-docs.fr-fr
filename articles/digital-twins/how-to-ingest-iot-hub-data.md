---
title: Ingérer la télémétrie depuis IoT Hub
titleSuffix: Azure Digital Twins
description: Découvrez comment ingérer des messages de télémétrie d’appareil issus d’Azure IoT Hub dans les jumeaux numériques d’une instance d’Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 10/28/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1b10a87312fd7b27c22bbfa56a928a0357d4698e
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500923"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Ingérer des données de télémétrie IoT Hub dans Azure Digital Twins

Ce guide décrit le processus d’écriture d’une fonction capable d’ingérer des données de télémétrie issues d’IoT Hub et de les envoyer à une instance d’Azure Digital Twins.

Azure Digital Twins se base sur les données des appareils IoT et d’autres sources. Pour les données d’appareil, il est courant d’utiliser la source [IoT Hub](../iot-hub/about-iot-hub.md) dans Azure Digital Twins.

Le processus d’ingestion de données dans Azure Digital Twins consiste à configurer une ressource de calcul externe, telle qu’une fonction créée à l’aide d’[Azure Functions](../azure-functions/functions-overview.md). La fonction reçoit les données et utilise les [API DigitalTwins](/rest/api/digital-twins/dataplane/twins) pour définir des propriétés ou déclencher des événements de télémétrie sur des [jumeaux numériques](concepts-twins-graph.md) en conséquence. 

Ce document explique le processus d’écriture d’une fonction pouvant ingérer des données de télémétrie à partir d’IoT Hub.

## <a name="prerequisites"></a>Prérequis

Avant de poursuivre avec cet exemple, vous devez mettre en place les ressources suivantes en guise de conditions préalables :
* **Un hub IoT**. Pour obtenir des instructions, consultez la section *Créer un hub IoT* de [ce démarrage rapide d’IoT Hub](../iot-hub/quickstart-send-telemetry-cli.md).
* **Une instance Azure Digital Twins** qui recevra la télémétrie de votre appareil. Pour obtenir des instructions, consultez [Configurer une instance Azure Digital Twins et l’authentification](./how-to-set-up-instance-portal.md).

Cet article utilise également **Visual Studio**. Vous pouvez télécharger la dernière version à partir de la page [Téléchargements de Visual Studio](https://visualstudio.microsoft.com/downloads/).

## <a name="example-telemetry-scenario"></a>Exemple de scénario de télémétrie

Cet article explique comment envoyer des messages entre IoT Hub et Azure Digital Twins à l’aide d’une fonction dans Azure. Pour envoyer des messages, il existe de nombreuses configurations et stratégies possibles. Cependant, l’exemple de cet article contient les éléments suivants :
* Un thermostat situé dans IoT Hub, ayant un ID d’appareil connu
* Un jumeau numérique représentant l’appareil, avec un ID correspondant

> [!NOTE]
> Cet exemple utilise une correspondance d’ID simple entre l’ID de l’appareil et celui d’un jumeau numérique. Toutefois, il est possible de fournir des mappages plus sophistiqués entre l’appareil et son jumeau (par exemple, avec une table de mappage).

Chaque fois qu’un événement de télémétrie de température est envoyé par le thermomètre, une fonction traite la télémétrie, et la propriété *temperature* du jumeau numérique doit se mettre à jour. Ce scénario est présenté dans un diagramme ci-dessous :

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Diagramme d’appareil IoT Hub envoyant des données de télémétrie de température à une fonction dans Azure, qui met à jour une propriété de température sur un jumeau dans Azure Digital Twins." border="false":::

## <a name="add-a-model-and-twin"></a>Ajouter un modèle et un jumeau

Dans cette section, vous allez configurer un [jumeau numérique](concepts-twins-graph.md) dans Azure Digital Twins qui représentera le thermostat et sera mis à jour avec les informations d’IoT Hub.

Pour créer un jumeau de type thermostat, vous devez d’abord charger le [modèle](concepts-models.md) de thermostat sur votre instance, qui décrit les propriétés d’un thermostat et sera utilisé ultérieurement pour créer le jumeau.

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

Vous devez ensuite **créer un jumeau à l’aide de ce modèle**. Utilisez la commande suivante pour créer un jumeau de thermostat nommé thermostat67 et définissez 0,0 comme valeur initiale de température.

```azurecli-interactive
az dt twin create  --dt-name <instance-name> --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}'
```

Lorsque le jumeau est créé, la sortie CLI de la commande doit ressembler à ceci :
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "lastUpdateTime": "2021-09-09T20:32:46.6692326Z"
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-a-function"></a>Créer une fonction

Dans cette section, vous allez créer une fonction Azure pour accéder à Azure Digital Twins et mettre à jour des jumeaux en fonction des événements de télémétrie IoT qu’elle reçoit. Suivez les étapes ci-dessous pour créer et publier la fonction.

1. Tout d’abord, créez un projet d’application de fonction dans Visual Studio. Pour obtenir des instructions sur la façon de procéder, consultez [Développer des fonctions Azure Functions à l’aide de Visual Studio](../azure-functions/functions-develop-vs.md#create-an-azure-functions-project).

2. Ajoutez les packages suivants à votre projet :
    * [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
    * [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
    * [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid/)

3. Renommez l’exemple de fonction *Function1.cs* que Visual Studio a généré en *IoTHubtoTwins.cs*. Remplacez le code du fichier par le code suivant :

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs":::

    Enregistrez le code de votre fonction.

4. Publiez le projet avec la fonction *IoTHubtoTwins.cs* dans une application de fonction dans Azure. Pour obtenir des instructions sur la façon de procéder, consultez [Développer des fonctions Azure Functions à l’aide de Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

[!INCLUDE [digital-twins-verify-function-publish.md](../../includes/digital-twins-verify-function-publish.md)]

Pour accéder à Azure Digital Twins, votre application de fonction devra disposer d’une identité managée par le système avec les autorisations nécessaires pour accéder à votre instance d’Azure Digital Twins. Vous allez maintenant configurer cela.

### <a name="configure-the-function-app"></a>Configurer l’application de fonction

Ensuite, **attribuez un rôle d’accès** à la fonction et **configurez les paramètres de l’application** afin qu’elle puisse accéder à votre instance d’Azure Digital Twins.

[!INCLUDE [digital-twins-configure-function-app.md](../../includes/digital-twins-configure-function-app.md)]

## <a name="connect-your-function-to-iot-hub"></a>Connecter votre fonction à IoT Hub

Dans cette section, vous allez configurer votre fonction comme destination d’événement pour les données de l’appareil IoT Hub. Configurer ainsi votre fonction permet de veiller à ce que les données du thermostat dans IoT Hub soient envoyées à la fonction Azure à des fins de traitement.

Dans le [Portail Azure](https://portal.azure.com/), accédez à l’instance IoT Hub que vous avez créée dans la section [Prérequis](#prerequisites). Sous **Événements**, créez un abonnement pour votre fonction.

:::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Capture d’écran du Portail Azure qui montre l’ajout d’un abonnement aux événements.":::

Dans la page **Créer un abonnement aux événements**, renseignez les champs de la façon suivante :
  1. Pour **Nom**, choisissez le nom de votre choix pour l’abonnement aux événements.
  2. Pour **Schéma d’événement**, choisissez _Schéma Event Grid_.
  3. Pour **Nom de la rubrique système**, choisissez le nom de votre choix.
  1. Pour **Filtrer les types d’événements**, cochez la case _Télémétrie d’appareil_ et décochez les autres types d’événements.
  1. Comme **Type de point de terminaison**, sélectionnez _Fonction Azure_.
  1. Pour **Point de terminaison**, utilisez le lien _Sélectionner un point de terminaison_ pour choisir la fonction Azure à utiliser pour le point de terminaison.
    
:::image type="content" source="media/how-to-ingest-iot-hub-data/create-event-subscription.png" alt-text="Capture d’écran du portail Azure pour créer les détails de l’abonnement aux événements.":::

Dans la page _Sélectionner une fonction Azure_ qui s’ouvre, vérifiez ou remplissez les détails ci-dessous.
 1. **Abonnement**: Votre abonnement Azure.
 2. **Groupe de ressources** : Votre groupe de ressources.
 3. **Application de fonction** : Nom de votre application de fonction.
 4. **Emplacement** : _Production_.
 5. **Fonction**: sélectionnez la fonction ci-dessus, *IoTHubtoTwins*, dans la liste déroulante.

Enregistrez vos détails avec le bouton _Confirmer la sélection_.            
      
:::image type="content" source="media/how-to-ingest-iot-hub-data/select-azure-function.png" alt-text="Capture d’écran du Portail Azure pour sélectionner la fonction":::

Sélectionnez le bouton _Créer_ pour créer l’abonnement aux événements.

## <a name="send-simulated-iot-data"></a>Envoyer des données IoT simulées

Pour tester votre nouvelle fonction d’entrée, utilisez le simulateur d’appareil à partir de [Connecter une solution de bout en bout](./tutorial-end-to-end.md). Ce tutoriel est piloté par [cet exemple de projet de bout en bout Azure Digital Twins écrit en C#](/samples/azure-samples/digital-twins-samples/digital-twins-samples). Vous utiliserez le projet **DeviceSimulator** dans ce référentiel.

Dans ce tutoriel intégral, vous allez effectuer les étapes suivantes :
1. [Inscrire l’appareil simulé auprès d’IoT Hub](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [Configurer et démarrer la simulation](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>Valider vos résultats

Lors de l’exécution du simulateur d’appareil ci-dessus, la valeur de température de votre jumeau numérique sera modifiée. Dans Azure CLI, exécutez la commande suivante pour afficher la valeur de la température.

```azurecli-interactive
az dt twin query --query-command "select * from digitaltwins" --dt-name <Digital-Twins-instance-name>
```

Votre sortie doit contenir une valeur de température comme ceci :

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"dbf2fea8-d3f7-42d0-8037-83730dc2afc5\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "lastUpdateTime": "2021-06-03T17:05:52.0062638Z"
        }
      },
      "Temperature": 70.20518558807913
    }
  ]
}
```

Pour voir la modification de la valeur, exécutez à plusieurs reprises la commande de requête ci-dessus.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’entrée et la sortie de données avec Azure Digital Twins :
* [Entrée et sortie de données](concepts-data-ingress-egress.md)
