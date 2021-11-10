---
title: Fichier include
description: Fichier include
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 09/10/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 7f02dba36ceee1d3acba9f359f26fc3bbe7aa1ae
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860376"
---
[![Parcourir le code](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp)

Dans ce guide de démarrage rapide, vous allez découvrir un workflow simple de développement d’application Azure IoT. Tout d’abord, vous créez une application Azure IoT Central pour héberger des appareils. Ensuite, vous utilisez un exemple Azure IoT device SDK pour créer un contrôleur de température, le connecter en toute sécurité à IoT Central et envoyer la télémétrie. L’exemple d’application de contrôleur de température s’exécute sur votre ordinateur local et génère des données de capteur simulées à envoyer à IoT Central.

## <a name="prerequisites"></a>Prérequis
Ce guide de démarrage rapide s’exécute sur Windows, Linux et Raspberry Pi. Il a été testé sur les versions de système d’exploitation et d’appareil suivantes :

- Windows 10
- Ubuntu 20.04 LTS
- Système d’exploitation Raspberry Pi version 10 (Buster) exécuté sur un Raspberry Pi 3 modèle B+

Installez les composants requis restants pour votre système d’exploitation.

### <a name="linux-or-raspberry-pi-os"></a>Système d’exploitation Linux ou Raspberry Pi
Pour effectuer ce démarrage rapide sur le système d’exploitation Linux ou Raspberry Pi, installez les logiciels suivants :

Installez **GCC**, **Git**, **cmake** et les dépendances nécessaires à l’aide de la commande `apt-get` :

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Vérifiez que la version de `cmake` est supérieure à **2.8.12** et que la version de **GCC** est supérieure à **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows
Pour suivre ce guide de démarrage rapide sur Windows, installez Visual Studio 2019 et ajoutez les composants requis pour le développement C et C++.

1. Pour les nouveaux utilisateurs, installez [Visual Studio (Community, Professional ou Enterprise) 2019](https://visualstudio.microsoft.com/downloads/). Téléchargez l’édition que vous souhaitez installer et démarrez le programme d’installation.
    > [!NOTE]
    > Pour les utilisateurs de Visual Studio 2019 existants, sélectionnez **Démarrer** dans Windows, tapez *Visual Studio Installer*, puis démarrez le programme d’installation.
1. Sous l’onglet **Charges de travail** du programme d’installation, sélectionnez la charge de travail **Développement Desktop en C++** .
1. Sous l’onglet **Composants individuels** du programme d’installation, sélectionnez **Git pour Windows**.
1. Exécutez l’installation.

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-the-device-sample"></a>Exécuter l’exemple d’appareil
Dans cette section, vous allez configurer votre environnement local, installer le kit Azure IoT C device SDK et exécuter un exemple qui crée un contrôleur de température.

### <a name="configure-your-environment"></a>Configurer votre environnement

1. Ouvrez une console pour installer le kit SDK C d’appareil Azure IoT et exécuter l’exemple de code. Pour Windows, sélectionnez **Démarrer**, tapez *Invite de commandes développeur pour VS 2019*, puis ouvrez la console. Pour le système d’exploitation Linux ou Raspberry Pi, ouvrez un terminal pour les commandes Bash. 

1. Définissez les variables d’environnement suivantes à l’aide des commandes appropriées pour votre console. L’appareil utilise ces valeurs pour se connecter à IoT Central. Pour `IOTHUB_DEVICE_DPS_ID_SCOPE`, `IOTHUB_DEVICE_DPS_DEVICE_KEY` et `IOTHUB_DEVICE_DPS_DEVICE_ID`, utilisez les valeurs de connexion d’appareil que vous avez enregistrées précédemment.

    **CMD**

    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=DPS
    set IOTHUB_DEVICE_DPS_ID_SCOPE=<application ID scope>
    set IOTHUB_DEVICE_DPS_DEVICE_KEY=<device primary key>
    set IOTHUB_DEVICE_DPS_DEVICE_ID=<your device ID>
    set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
    ```

    > [!NOTE]
    > Pour Windows CMD, il n’y a pas de guillemets avant et après les valeurs de variable.

    **Bash**

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    export IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    export IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    export IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    export IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net' 
    ```

### <a name="install-the-sdk-and-samples"></a>Installer le kit SDK et les exemples

1. Accédez au dossier local où vous souhaitez cloner l’exemple de référentiel.

1. Copiez le kit SDK C d’appareil Azure IoT sur votre ordinateur local.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-c.git
    ```

1. Accédez au dossier racine du kit SDK et exécutez la commande suivante pour mettre à jour les dépendances :
    ```console
    cd azure-iot-sdk-c
    git submodule update --init
    ```
    Cette opération prend quelques minutes.

1. Exécutez les commandes suivantes pour générer le kit SDK et les exemples :

    ```console
    cmake -Bcmake -Duse_prov_client=ON -Dhsm_type_symm_key=ON -Drun_e2e_tests=OFF
    cmake --build cmake
    ```

### <a name="run-the-code"></a>Exécuter le code

1. Exécutez l’exemple de code à l’aide de la commande appropriée pour votre console :

    **CMD**
    ```console
    cmake\iothub_client\samples\pnp\pnp_temperature_controller\Debug\pnp_temperature_controller.exe
    ```

    **Bash**
    ```bash
    cmake/iothub_client/samples/pnp/pnp_temperature_controller/pnp_temperature_controller
    ```

    Une fois que votre appareil se connecte à votre application IoT Central, il se connecte à l’instance d’appareil que vous avez créée dans l’application et commence à envoyer de la télémétrie. Les détails de la connexion et la sortie de la télémétrie sont affichés dans votre console : 
    
    ```output
    Info: Initiating DPS client to retrieve IoT Hub connection information
    -> 17:03:08 CONNECT | VER: 4 | KEEPALIVE: 0 | FLAGS: 194 | USERNAME: xxxxxxxxxxxxxxx/registrations/my-sdk-device/api-version=2019-03-31&ClientVersion=1.6.0 | PWD: XXXX | CLEAN: 1
    <- 17:03:09 CONNACK | SESSION_PRESENT: false | RETURN_CODE: 0x0
    -> 17:03:10 SUBSCRIBE | PACKET_ID: 1 | TOPIC_NAME: $dps/registrations/res/# | QOS: 1
    <- 17:03:11 SUBACK | PACKET_ID: 1 | RETURN_CODE: 1
    Info: Provisioning callback indicates success.  iothubUri=iotc-xxxxxxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx.azure-devices.net, deviceId=my-sdk-device
    -> 17:03:27 DISCONNECT
    Info: DPS successfully registered.  Continuing on to creation of IoTHub device client handle.
    Info: Successfully created device client.  Hit Control-C to exit program
    
    Info: Sending serialNumber property to IoTHub
    Info: Sending device information property to IoTHub.  propertyName=swVersion, propertyValue="1.0.0.0"
    Info: Sending device information property to IoTHub.  propertyName=manufacturer, propertyValue="Sample-Manufacturer"
    Info: Sending device information property to IoTHub.  propertyName=model, propertyValue="sample-Model-123"
    Info: Sending device information property to IoTHub.  propertyName=osName, propertyValue="sample-OperatingSystem-name"
    Info: Sending device information property to IoTHub.  propertyName=processorArchitecture, propertyValue="Contoso-Arch-64bit"
    Info: Sending device information property to IoTHub.  propertyName=processorManufacturer, propertyValue="Processor Manufacturer(TM)"
    Info: Sending device information property to IoTHub.  propertyName=totalStorage, propertyValue=10000
    Info: Sending device information property to IoTHub.  propertyName=totalMemory, propertyValue=200
    Info: Sending maximumTemperatureSinceLastReboot property to IoTHub for component=thermostat1
    Info: Sending maximumTemperatureSinceLastReboot property to IoTHub for component=thermostat2
    ```