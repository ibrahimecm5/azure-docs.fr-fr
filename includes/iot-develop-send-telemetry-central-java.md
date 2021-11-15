---
title: Fichier include
description: Fichier include
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 11/02/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 6e3a7b7b37e02f6f351caf985fd997d7daafd70c
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861059"
---
[![Parcourir le code](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-java/tree/main/device/iot-device-samples/pnp-device-sample)

Dans ce guide de démarrage rapide, vous allez découvrir un workflow simple de développement d’application Azure IoT. Tout d’abord, vous créez une application Azure IoT Central pour héberger des appareils. Ensuite, vous utilisez un exemple Azure IoT device SDK pour créer un contrôleur de température, le connecter en toute sécurité à IoT Central et envoyer la télémétrie. L’exemple d’application de contrôleur de température s’exécute sur votre ordinateur local et génère des données de capteur simulées à envoyer à IoT Central.

## <a name="prerequisites"></a>Prérequis

Ce guide de démarrage rapide s’exécute sur Windows, Linux et Raspberry Pi. Il a été testé sur les versions de système d’exploitation et d’appareil suivantes :

- Windows 10
- Ubuntu 20.04 LTS
- Système d’exploitation Raspberry Pi version 10 (Buster) exécuté sur un Raspberry Pi 3 modèle B+

Installez les prérequis suivants sur votre ordinateur de développement :

- [Git](https://git-scm.com/downloads).

Installez les composants requis restants pour votre système d’exploitation.

### <a name="windows"></a>Windows

Pour effectuer ce guide de démarrage rapide sur Windows, installez les logiciels suivants :

- Java SE Development Kit 8 ou ultérieur. Vous pouvez télécharger le Kit JDK Java 8 (LTS) pour plusieurs plateformes à partir du site [Download Zulu Builds of OpenJDK](https://www.azul.com/downloads/zulu-community/). Dans le programme d’installation, sélectionnez l’option **Ajouter au chemin**.

- [Apache Maven 3](https://maven.apache.org/download.cgi). Après avoir extrait le téléchargement dans un dossier local, ajoutez le chemin complet du dossier */bin* de Maven à la variable d’environnement Windows `PATH`.

### <a name="linux-or-raspberry-pi-os"></a>Système d’exploitation Linux ou Raspberry Pi

Pour effectuer ce démarrage rapide sur le système d’exploitation Linux ou Raspberry Pi, installez les logiciels suivants :

> [!NOTE]
> Les étapes de cette section sont basées sur les distributions Linux Ubuntu/Debian. (Raspberry Pi OS est basé sur Debian.) Si vous utilisez une autre distribution Linux, vous devez modifier les étapes en conséquence.

- OpenJDK (Open Java Development Kit) 8 ou ultérieur. Vous pouvez utiliser la commande `java -version` pour vérifier la version de Java installée sur votre système. Vérifiez que le JDK est installé, et pas seulement le runtime Java (JRE).

    1. Pour installer OpenJDK pour votre système, entrez les commandes suivantes :

        Pour installer la version par défaut d’OpenJDK pour votre système (OpenJDK 11 pour Ubuntu 20.04 et Raspberry Pi OS 10 au moment de la rédaction de cet article) :

        ```bash
        sudo apt update
        sudo apt install default-jdk
        ```

        Vous pouvez également spécifier une version du JDK à installer. Par exemple :

        ```bash
        sudo apt update
        sudo apt install openjdk-8-jdk
        ```

    1. Si plusieurs versions de Java sont installées sur votre système, vous pouvez utiliser les commandes suivantes pour configurer les versions (auto) par défaut de Java et le compilateur Java.

        ```bash
        update-java-alternatives --list          #list the Java versions installed
        sudo update-alternatives --config java   #set the default Java version
        sudo  update-alternatives --config javac #set the default Java compiler version
        ```

    1. Définissez la variable d’environnement `JAVA_HOME` sur le chemin de votre installation JDK. (Il s’agit généralement d’un sous-répertoire avec version dans le répertoire **/usr/lib/jvm**.)

        ```bash
        export JAVA_HOME=$(readlink -f /usr/bin/java | sed "s:bin/java::")
        ```

        > [!IMPORTANT]
        > Cette commande définit la variable `JAVA_HOME` dans votre environnement shell actuel. Nous vous recommandons d’ajouter la commande à votre fichier `~/.bashrc` ou `/etc/profile` pour la rendre disponible chaque fois que vous ouvrez un nouveau shell.

    1. Vérifiez que la version du JDK Java (et de JRE) est installée, que la version de votre compilateur Java correspond à la version du JDK et que la variable d’environnement `JAVA_HOME` est correctement définie.

        ```bash
        java -version
        javac -version
        echo $JAVA_HOME
        ```

- Apache Maven 3. Vous pouvez utiliser la commande `mvn --version` pour vérifier la version de Maven installée sur votre système.

    1. Pour installer Maven, entrez les commandes suivantes :

        ```bash
        sudo apt-get update
        sudo apt-get install maven
        ```

    1. Entrez la commande suivante pour vérifier votre installation.

        ```bash
        mvn --version
        ```

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-the-device-sample"></a>Exécuter l’exemple d’appareil

Dans cette section, vous allez configurer votre environnement local, installer le kit Azure IoT Java device SDK et exécuter un exemple qui crée un contrôleur de température.

### <a name="configure-your-environment"></a>Configurer votre environnement

1. Ouvrez une console comme Windows CMD ou Bash.

    **Linux et Raspberry Pi OS**

    Vérifiez que la variable d’environnement JAVA_HOME (`echo $JAVA_HOME`) est définie. Cette variable d’environnement doit être définie pour que le SDK et les exemples soient correctement créés. Pour plus d’informations sur la définition de JAVA_HOME, consultez [Prérequis de Linux/Raspberry Pi](#linux-or-raspberry-pi-os).

1. Définissez les variables d’environnement suivantes à l’aide des commandes appropriées pour votre console. L’appareil utilise ces valeurs pour se connecter à IoT Central. Pour `IOTHUB_DEVICE_DPS_ID_SCOPE`, `IOTHUB_DEVICE_DPS_DEVICE_KEY` et `IOTHUB_DEVICE_DPS_DEVICE_ID`, utilisez les valeurs de connexion d’appareil que vous avez enregistrées précédemment.

    **Windows CMD**

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

### <a name="build-and-run-the-code"></a>Générer et exécuter le code

1. Clonez le SDK d’appareil Java Azure IoT sur votre machine locale.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-java.git
    ```

1. Accédez au dossier racine du SDK et exécutez la commande suivante pour créer le SDK et mettre à jour les exemples.

    ```console
    cd azure-iot-sdk-java
    mvn install -T 2C -DskipTests
    ```

    Cette opération prend plusieurs minutes.

1. Accédez au répertoire des exemples.

    **Windows**

    ```console
    cd device\iot-device-samples\pnp-device-sample\temperature-controller-device-sample
    ```

    **Système d’exploitation Linux ou Raspberry Pi**

    ```bash
    cd device/iot-device-samples/pnp-device-sample/temperature-controller-device-sample
    ```

1. Exécutez l’exemple de code suivant à partir du SDK. L’exemple crée un contrôleur de température avec des capteurs de thermostat.

    ```console
    mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
    ```

    Une fois que votre appareil se connecte à votre application IoT Central, il se connecte à l’instance d’appareil que vous avez créée dans l’application et commence à envoyer de la télémétrie. Après certains détails de provisionnement initiaux, la console commence à générer la télémétrie pour le contrôleur de température.

    ```output
    2021-05-13 15:39:26.411 DEBUG Mqtt:253 - Sending MQTT SUBSCRIBE packet for topic $iothub/twin/res/#
    2021-05-13 15:39:26.428 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Request Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Device Operation Type [DEVICE_OPERATION_TWIN_UPDATE_REPORTED_PROPERTIES_REQUEST] )
    2021-05-13 15:39:26.432 DEBUG TemperatureController:427 - Property: Update - component = "deviceInformation" is COMPLETED.
    2021-05-13 15:39:26.436 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] )
    2021-05-13 15:39:26.438 DEBUG TemperatureController:438 - Telemetry: Sent - {"workingSet": 1024.0KiB }
    2021-05-13 15:39:26.439 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Request Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Device Operation Type [DEVICE_OPERATION_TWIN_UPDATE_REPORTED_PROPERTIES_REQUEST] )
    2021-05-13 15:39:26.439 DEBUG TemperatureController:446 - Property: Update - {"serialNumber": SR-123456} is COMPLETED
    2021-05-13 15:39:26.447 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] )
    2021-05-13 15:39:26.447 DEBUG TemperatureController:465 - Telemetry: Sent - {"temperature": 44.4░C} with message Id xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.
    ```