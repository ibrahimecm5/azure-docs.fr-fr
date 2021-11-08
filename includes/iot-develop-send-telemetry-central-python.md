---
title: Fichier include
description: Fichier include
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 04/28/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: ce451558d91b164cd6b291edc6eeff05c7b8b749
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131032754"
---
[![Parcourir le code](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp)

Dans ce guide de démarrage rapide, vous allez découvrir un workflow simple de développement d’application Azure IoT. Tout d’abord, vous créez une application Azure IoT Central pour héberger des appareils. Ensuite, vous utilisez un exemple Azure IoT device SDK pour créer un contrôleur de température, le connecter de manière sécurisée à IoT Central et envoyer des données de télémétrie. L’exemple d’application de contrôleur de température s’exécute sur votre ordinateur local et génère des données de capteur simulées à envoyer à IoT Central.

## <a name="prerequisites"></a>Prérequis
Ce guide de démarrage rapide s’exécute sur Windows, Linux et Raspberry Pi. Il a été testé sur les versions de système d’exploitation et d’appareil suivantes :

- Windows 10
- Ubuntu 20.04 LTS
- Système d’exploitation Raspberry Pi version 10 (Buster) exécuté sur un Raspberry Pi 3 modèle B+

Installez les prérequis suivants sur votre ordinateur de développement :

- [Python](https://www.python.org/downloads/) version 3.7 ou ultérieure. Pour vérifier votre version de Python, exécutez `python3 --version`.
- [Git](https://git-scm.com/downloads).

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-a-device"></a>Exécution d’un appareil
Dans cette section, vous allez configurer votre environnement local, installer le kit Python Azure IoT device SDK et exécuter un exemple qui crée un contrôleur de température.

### <a name="configure-your-environment"></a>Configurer votre environnement

1. Ouvrez une console telle que Windows CMD, PowerShell ou Bash.

1. Définissez les variables d’environnement suivantes à l’aide des commandes appropriées pour votre console. L’appareil utilise ces valeurs pour se connecter à IoT Central. Pour `IOTHUB_DEVICE_DPS_ID_SCOPE`, `IOTHUB_DEVICE_DPS_DEVICE_KEY` et `IOTHUB_DEVICE_DPS_DEVICE_ID`, utilisez les valeurs de connexion d’appareil que vous avez enregistrées précédemment.

    **CMD (Windows)**

    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=DPS
    set IOTHUB_DEVICE_DPS_ID_SCOPE=<application ID scope>
    set IOTHUB_DEVICE_DPS_DEVICE_KEY=<device primary key>
    set IOTHUB_DEVICE_DPS_DEVICE_ID=<your device ID>
    set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
    ```

    > [!NOTE]
    > Pour Windows CMD, il n’y a pas de guillemets avant et après les valeurs de variable.

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    $env:IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    $env:IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    $env:IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    $env:IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net'
    ```

    **Bash**

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    export IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    export IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    export IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    export IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net' 
    ```

### <a name="install-the-sdk-and-samples"></a>Installer le kit SDK et les exemples

1. Copiez le kit SDK Python d’appareil Azure IoT sur votre ordinateur local.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```
1. Accédez au répertoire des exemples.

    **Windows**
    ```console
    cd azure-iot-sdk-python\azure-iot-device\samples\pnp
    ```

    **Système d’exploitation Linux ou Raspberry Pi**
    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples/pnp
    ```

1. Installez le Kit de développement logiciel (SDK) Azure IoT Python.
    ```console
    pip3 install azure-iot-device
    ```

### <a name="run-the-code"></a>Exécuter le code

1. Dans votre console, exécutez l’exemple de code suivant à partir du kit SDK. L’exemple crée un contrôleur de température comportant des capteurs de thermostat.
    ```console
    python3 temp_controller_with_thermostats.py
    ```

    Une fois connecté à votre application IoT Central, votre appareil se connecte à l’instance d’appareil que vous avez créée dans l’application et commence à envoyer des données de télémétrie. Les détails de la connexion et la sortie de la télémétrie sont affichés dans votre console : 
    
    ```output
    Device was assigned
    iotc-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.azure-devices.net
    my-sdk-device
    Updating pnp properties for root interface
    {'serialNumber': 'alohomora'}
    Updating pnp properties for thermostat1
    {'thermostat1': {'maxTempSinceLastReboot': 98.34, '__t': 'c'}}
    Updating pnp properties for thermostat2
    {'thermostat2': {'maxTempSinceLastReboot': 48.92, '__t': 'c'}}
    Updating pnp properties for deviceInformation
    {'deviceInformation': {'swVersion': '5.5', 'manufacturer': 'Contoso Device Corporation', 'model': 'Contoso 4762B-turbo', 'osName': 'Mac Os', 'processorArchitecture': 'x86-64', 'processorManufacturer': 'Intel', 'totalStorage': 1024, 'totalMemory': 32, '__t': 'c'}}
    Listening for command requests and property updates
    Press Q to quit
    Sending telemetry from various components
    Sent message
    {"temperature": 33}
    ```