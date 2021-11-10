---
title: Fichier include
description: Fichier include
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 09/17/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: d96e331727c1c4d306746c35565f759fcd12a4c8
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860383"
---
[![Parcourir le code](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/javascript)

Dans ce guide de démarrage rapide, vous allez découvrir un workflow simple de développement d’application Azure IoT. Vous utilisez l’interface Azure CLI pour créer un hub Azure IoT et un appareil. Ensuite, vous utilisez un exemple d’Azure IoT device SDK pour exécuter un contrôleur de température, le connecter en toute sécurité au hub et envoyer de la télémétrie. L’exemple d’application de contrôleur de température s’exécute sur votre ordinateur local et génère des données de capteur simulées à envoyer à IoT Hub.

## <a name="prerequisites"></a>Prérequis
Ce guide de démarrage rapide s’exécute sur Windows, Linux et Raspberry Pi. Il a été testé sur les versions de système d’exploitation et d’appareil suivantes :

- Windows 10
- Ubuntu 20.04 LTS
- Système d’exploitation Raspberry Pi version 10 (Raspian) exécuté sur un Raspberry Pi 3 modèle B+

Installez les prérequis suivants sur votre ordinateur de développement, sauf mention prévue pour Raspberry Pi :

- Si vous n’avez pas d’abonnement Azure, [créez-en un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- [Git](https://git-scm.com/downloads).
- [Node.js](https://nodejs.org) version 10 ou ultérieure. Pour vérifier la version de Node, exécutez `node --version`.
- [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases) : utilitaire multiplateforme basé sur l’interface graphique utilisateur permettant de surveiller et gérer Azure IoT. Si vous utilisez Raspberry Pi comme plateforme de développement, nous vous recommandons d’installer IoT Explorer sur un autre ordinateur. Si vous ne souhaitez pas installer IoT Explorer, vous pouvez utiliser l’interface Azure CLI pour effectuer la même procédure. 
- Azure CLI. Vous avez le choix entre deux options pour exécuter les commandes Azure CLI dans ce guide de démarrage rapide :
    - Utilisez Azure Cloud Shell, un interpréteur de commandes interactif qui exécute des commandes CLI dans votre navigateur. Cette option est recommandée, car vous n’avez pas besoin d’installer quoi que ce soit. Si vous utilisez Cloud Shell pour la première fois, connectez-vous au [portail Azure](https://portal.azure.com). Suivez les étapes décrites dans [Démarrage rapide de Cloud Shell](../articles/cloud-shell/quickstart.md) pour **démarrer Cloud Shell** et **sélectionner l’environnement Bash**.
    - Si vous le souhaitez, exécutez Azure CLI sur votre ordinateur local. Si l’interface de ligne de commande Azure est déjà installée, exécutez `az upgrade` pour la mettre à niveau, ainsi que les extensions, vers la version actuelle. Pour installer l’interface de ligne de commande Azure, consultez [Installer l’interface de ligne de commande Azure]( /cli/azure/install-azure-cli). Si vous utilisez Raspberry Pi comme plateforme de développement, nous vous recommandons d’utiliser Azure Cloud Shell ou d’installer Azure CLI sur un autre ordinateur.

[!INCLUDE [iot-hub-include-create-hub-iot-explorer](iot-hub-include-create-hub-iot-explorer.md)]

## <a name="run-the-device-sample"></a>Exécuter l’exemple d’appareil
Dans cette section, vous allez utiliser le Kit de développement logiciel (SDK) Node.js pour envoyer des messages d’un appareil à votre hub IoT. Vous allez exécuter un exemple qui implémente un contrôleur de température avec deux capteurs de thermostat.

1. Ouvrez une nouvelle console telle que Windows CMD, PowerShell ou Bash. Dans la procédure suivante, vous allez utiliser cette console pour installer le kit SDK Node.js et travailler avec un exemple de code Node.js.

    > [!NOTE]
    > Si vous utilisez une installation locale d’Azure CLI, vous avez peut-être maintenant deux fenêtres de console ouvertes. Veillez à entrer les commandes de cette section dans la console que vous venez d’ouvrir et non dans celle que vous avez utilisée pour l’interface CLI.

1. Clonez les [exemples d’appareils Azure IoT avec kit SDK Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) sur votre ordinateur local :

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. Accédez au répertoire des exemples :

    **Windows**
    ```console
    cd azure-iot-sdk-node\device\samples\javascript
    ```

    **Système d’exploitation Linux ou Raspberry Pi**
    ```console
    cd azure-iot-sdk-node/device/samples/javascript
    ```

1. Installez le Kit de développement logiciel (SDK) Azure IoT Node.js et les dépendances nécessaires :

    ```console
    npm install
    ```

    Cette commande installe les dépendances appropriées comme spécifié dans le fichier *package.json* du répertoire des exemples d’appareils.

1. Définissez les deux variables d’environnement suivantes pour permettre à votre appareil de se connecter à Azure IoT.
    * Définissez une variable d’environnement appelée `IOTHUB_DEVICE_CONNECTION_STRING`. Pour la valeur de la variable, utilisez la chaîne de connexion de l’appareil que vous avez enregistrée dans la section précédente.
    * Définissez une variable d’environnement appelée `IOTHUB_DEVICE_SECURITY_TYPE`. Pour la variable, utilisez la valeur de chaîne littérale `connectionString`.

    **CMD (Windows)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```

    > [!NOTE]
    > Pour Windows CMD, il n’y a pas de guillemets avant et après les valeurs de chaîne pour chaque variable.

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_CONNECTION_STRING='<your connection string here>'
    $env:IOTHUB_DEVICE_SECURITY_TYPE='connectionString'
    ```

    **Bash**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```
1. Exécutez l’exemple de code suivant :

    ```console
    node pnp_temperature_controller.js
    ```
    > [!NOTE]
    > Cet exemple de code utilise Azure IoT Plug-and-Play, qui vous permet d’intégrer des appareils intelligents dans vos solutions sans aucune configuration manuelle.  Par défaut, la plupart des exemples de cette documentation utilisent IoT Plug-and-Play. Pour en savoir plus sur les avantages et les cas d’utilisation d’IoT PnP, consultez [Qu’est ce qu’IoT Plug-and-Play ?](../articles/iot-develop/overview-iot-plug-and-play.md).

L’échantillon se connecte en toute sécurité à votre hub IoT en tant qu’appareil que vous avez inscrit et commence à envoyer des messages de télémétrie. L’exemple de sortie s’affiche dans votre console.

## <a name="view-telemetry"></a>Afficher les données de télémétrie

Vous pouvez afficher la télémétrie des appareils avec l’Explorateur IoT. Si vous le souhaitez, vous pouvez afficher les données de télémétrie à l’aide d’Azure CLI.

Pour afficher la télémétrie dans Azure IoT Explorer :

1. À partir de votre hub IoT dans IoT Explorer, sélectionnez **Voir les appareils dans ce hub**, puis sélectionnez votre appareil dans la liste. 
1. Dans le menu de gauche pour votre appareil, sélectionnez **Télémétrie**.
1. Confirmez que **Utiliser un Event Hub intégré** est défini sur *Oui*, puis sélectionnez **Démarrer**.
1. Affichez la télémétrie à mesure que l’appareil envoie des messages au cloud.

    :::image type="content" source="media/iot-develop-send-telemetry-iot-hub-node/iot-explorer-device-telemetry.png" alt-text="Capture d’écran de télémétrie d’appareil dans IoT Explorer":::

1. Sélectionnez **Arrêter** pour cesser de recevoir des événements.

Pour lire les données de télémétrie envoyées par les composants d’appareils individuels, vous pouvez utiliser les fonctionnalités Plug-and-Play dans l’Explorateur IoT. Par exemple, le contrôleur de température dans ce démarrage rapide a deux thermostats : thermostat1 et thermostat2. Pour afficher la température signalée par thermostat1 : 

1. Sur votre appareil dans l’Explorateur IoT, sélectionnez **Composants IoT Plug-and-Play** dans le menu de gauche. Ensuite, sélectionnez **thermostat1** dans la liste des composants.

1. Sur le volet du composant **thermostat1**, sélectionnez **Télémétrie** dans le menu supérieur.

1. Sur le volet **Télémétrie**, suivez les mêmes étapes que précédemment. Vérifiez que **Utiliser un Event Hub intégré** est défini sur *Oui*, puis sélectionnez **Démarrer**.

Pour afficher la télémétrie de l’appareil avec Azure CLI :

1. Exécutez la commande [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) pour surveiller les événements envoyés par l’appareil à votre hub IoT. Utilisez les noms que vous avez créés précédemment dans Azure IoT pour votre appareil et le hub IoT.

    ```azurecli
    az iot hub monitor-events --output table --device-id mydevice --hub-name {YourIoTHubName}
    ```

1. Visualisez les détails de la connexion et la sortie de la télémétrie dans la console.

    ```output
    Starting event monitor, filtering on device: mydevice, use ctrl-c to stop...
    event:
      component: thermostat1
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: mydevice
      payload:
        temperature: 70.5897683228018
    
    event:
      component: thermostat2
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: mydevice
      payload:
        temperature: 52.87582619316418
    ```
    
