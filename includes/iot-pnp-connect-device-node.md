---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 2970634988a174d67ce4b92daa376a9d393fb449
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520852"
---
Ce guide de démarrage rapide vous montre comment créer un exemple d’application d’appareil IoT Plug-and-Play, comment le connecter à votre hub IoT et comment utiliser l’outil Explorateur Azure IoT pour afficher les données de télémétrie qu’il envoie. L’exemple d’application est écrit en Node.js et est inclus dans le kit Azure IoT device SDK pour Node.js. Un créateur de solutions peut utiliser l’outil Explorateur Azure IoT pour comprendre les fonctionnalités d’un appareil IoT Plug-and-Play sans avoir besoin d’examiner du code d’appareil.

[![Parcourir le code](../articles/iot-central/core/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Pour suivre ce guide de démarrage rapide, vous avez besoin de Node.js sur votre ordinateur de développement. Vous pouvez télécharger la dernière version recommandée pour plusieurs plateformes à partir de [nodejs.org](https://nodejs.org).

Vous pouvez vérifier la version actuelle de Node.js sur votre machine de développement à l’aide de la commande suivante :

```cmd/sh
node --version
```

## <a name="download-the-code"></a>Téléchargement du code

Dans ce guide de démarrage rapide, vous préparez un environnement de développement pour cloner et générer le kit Azure IoT Hub Device SDK pour Node.js.

Ouvrez une invite de commandes dans le répertoire de votre choix. Exécutez la commande suivante pour cloner le dépôt GitHub [SDK Microsoft Azure IoT pour for Node.js](https://github.com/Azure/azure-iot-sdk-node) à cet emplacement :

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="install-required-libraries"></a>Installer les bibliothèques nécessaires

Vous utilisez le kit de développement logiciel (SDK) de l’appareil pour créer l’exemple de code inclus. L’application que vous créez simule un appareil qui se connecte à un hub IoT. L’application envoie les données de télémétrie et les propriétés et reçoit des commandes.

1. Dans une fenêtre de terminal locale, accédez au dossier de votre dépôt cloné, puis au dossier */azure-iot-sdk-node/device/samples/javascript*. Exécutez ensuite la commande suivante pour installer les bibliothèques requises :

    ```cmd/sh
    npm install
    ```

1. Configurez la variable d’environnement avec la chaîne de connexion d’appareil que vous avez notée précédemment :

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-sample-device"></a>Exécuter l’exemple d’appareil

Cet exemple implémente un appareil à thermostat IoT Plug-and-Play simple. Le modèle implémenté par cet exemple n’utilise pas de [composants](../articles/iot-develop/concepts-modeling-guide.md) IoT Plug-and-Play. Le [fichier de modèle DTDL pour l’appareil à thermostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) définit la télémétrie, les propriétés et les commandes implémentées par l’appareil.

Ouvrez le fichier _pnp_simple_thermostat.js_. Dans ce fichier, vous pouvez voir comment :

1. Importer les interfaces nécessaires.
1. Écrire un gestionnaire de mise à jour de propriété et un gestionnaire de commandes.
1. Gérer les correctifs de propriété souhaités et envoyer les données de télémétrie.
1. (Facultatif) Provisionner votre appareil à l’aide du service de provisionnement des appareils (DPS) Azure.

Dans la fonction principale, vous pouvez voir comment tout cela se résume :

1. Créez l’appareil à partir de votre chaîne de connexion ou provisionnez-le à l’aide de DPS.
1. Utilisez l’option **modelID** pour spécifier le modèle d’appareil IoT Plug-and-Play.
1. Activez le gestionnaire de commandes.
1. Envoyez les données de télémétrie de l’appareil à votre hub.
1. Récupérez les jumeaux d’appareil et mettez à jour les propriétés signalées.
1. Activez le gestionnaire de mise à jour de propriété souhaité.

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Pour en savoir plus sur l’exemple de configuration, consultez l’[exemple de fichier Lisez-moi](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/readme.md#iot-plug-and-play-device-samples).

Exécutez l’exemple d’application pour simuler un appareil IoT Plug-and-Play qui envoie des données de télémétrie à votre hub IoT. Pour exécuter l’exemple d’application, utilisez la commande suivante :

```cmd\sh
node pnp_simple_thermostat.js
```

Vous voyez la sortie suivante, qui indique que l’appareil a commencé à envoyer des données de télémétrie au hub et est maintenant prêt à recevoir des commandes et des mises à jour de propriétés.

![Messages de confirmation de l’appareil](media/iot-pnp-connect-device-node/device-confirmation-node.png)

Laissez l’exemple s’exécuter pendant que vous effectuez les étapes suivantes.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Utiliser l’Explorateur Azure IoT pour valider le code

Une fois l’exemple de client d’appareil démarré, utilisez l’outil Explorateur Azure IoT pour vérifier qu’il fonctionne.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]
