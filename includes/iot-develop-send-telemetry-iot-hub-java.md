---
title: Fichier include
description: Fichier include
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 11/02/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 093a75b77be2c24de11e97fe7d1627623fb1e2f9
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861518"
---
[![Parcourir le code](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-java/tree/main/device/iot-device-samples/pnp-device-sample)

Dans ce guide de démarrage rapide, vous allez découvrir un workflow simple de développement d’application Azure IoT. Vous utilisez l’interface Azure CLI pour créer un hub Azure IoT et un appareil. Ensuite, vous utilisez un exemple d’Azure IoT device SDK pour exécuter un contrôleur de température, le connecter en toute sécurité au hub et envoyer de la télémétrie. L’exemple d’application de contrôleur de température s’exécute sur votre ordinateur local et génère des données de capteur simulées à envoyer à IoT Hub.

## <a name="prerequisites"></a>Prérequis

Ce guide de démarrage rapide s’exécute sur Windows, Linux et Raspberry Pi. Il a été testé sur les versions de système d’exploitation et d’appareil suivantes :

- Windows 10
- Ubuntu 20.04 LTS
- Système d’exploitation Raspberry Pi version 10 (Raspian) exécuté sur un Raspberry Pi 3 modèle B+

Installez les prérequis suivants sur votre ordinateur de développement, sauf mention prévue pour Raspberry Pi :

- Si vous n’avez pas d’abonnement Azure, [créez-en un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- [Git](https://git-scm.com/downloads).
- [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases) : utilitaire multiplateforme basé sur l’interface graphique utilisateur permettant de surveiller et gérer Azure IoT. Si vous utilisez Raspberry Pi comme plateforme de développement, nous vous recommandons d’installer IoT Explorer sur un autre ordinateur. Si vous ne souhaitez pas installer IoT Explorer, vous pouvez utiliser l’interface Azure CLI pour effectuer la même procédure. 
- Azure CLI. Vous avez le choix entre deux options pour exécuter les commandes Azure CLI dans ce guide de démarrage rapide :
    - Utilisez Azure Cloud Shell, un interpréteur de commandes interactif qui exécute des commandes CLI dans votre navigateur. Cette option est recommandée, car vous n’avez pas besoin d’installer quoi que ce soit. Si vous utilisez Cloud Shell pour la première fois, connectez-vous au [portail Azure](https://portal.azure.com). Suivez les étapes décrites dans [Démarrage rapide de Cloud Shell](../articles/cloud-shell/quickstart.md) pour **démarrer Cloud Shell** et **sélectionner l’environnement Bash**.
    - Si vous le souhaitez, exécutez Azure CLI sur votre ordinateur local. Si l’interface de ligne de commande Azure est déjà installée, exécutez `az upgrade` pour la mettre à niveau, ainsi que les extensions, vers la version actuelle. Pour installer l’interface de ligne de commande Azure, consultez [Installer l’interface de ligne de commande Azure]( /cli/azure/install-azure-cli). Si vous utilisez Raspberry Pi comme plateforme de développement, nous vous recommandons d’utiliser Azure Cloud Shell ou d’installer Azure CLI sur un autre ordinateur.

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

[!INCLUDE [iot-hub-include-create-hub-iot-explorer](iot-hub-include-create-hub-iot-explorer.md)]

## <a name="run-the-device-sample"></a>Exécuter l’exemple d’appareil

Dans cette section, vous allez utiliser le Kit de développement logiciel (SDK) Java pour envoyer des messages d’un appareil à votre hub IoT. Vous allez exécuter un exemple qui implémente un contrôleur de température avec deux capteurs de thermostat.

1. Ouvrez une console pour installer le kit Azure IoT Java device SDK, créer l’exemple de code et l’exécuter. Vous utiliserez cette console dans la procédure suivante.

    > [!NOTE]
    > Si vous utilisez une installation locale d’Azure CLI, vous avez peut-être maintenant deux fenêtres de console ouvertes. Veillez à entrer les commandes de cette section dans la console que vous venez d’ouvrir et non dans celle que vous avez utilisée pour l’interface CLI.

    **Linux et Raspberry Pi OS**

    Vérifiez que la variable d’environnement JAVA_HOME (`echo $JAVA_HOME`) est définie. Pour plus d’informations sur la définition de JAVA_HOME, consultez [Prérequis de Linux/Raspberry Pi](#linux-or-raspberry-pi-os).

1. Clonez le kit SDK Java d’appareil Azure IoT sur votre ordinateur local :

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-java.git
    ```

1. Accédez au dossier racine du SDK et exécutez la commande suivante pour créer le SDK et mettre à jour les exemples.

    ```console
    cd azure-iot-sdk-java
    mvn install -T 2C -DskipTests
    ```

    Cette opération prend plusieurs minutes.

1. Définissez les variables d’environnement suivantes pour permettre à votre appareil de se connecter à Azure IoT.

    * Définissez une variable d’environnement appelée `IOTHUB_DEVICE_CONNECTION_STRING`. Pour la valeur de la variable, utilisez la chaîne de connexion de l’appareil que vous avez enregistrée dans la section précédente.
    * Définissez une variable d’environnement appelée `IOTHUB_DEVICE_SECURITY_TYPE`. Pour la variable, utilisez la valeur de chaîne littérale `connectionString`.

    **CMD**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```
    > [!NOTE]
    > Pour Windows CMD, il n’y a pas de guillemets avant et après les valeurs de chaîne pour chaque variable.

    **Bash**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```

1. Accédez au répertoire des exemples.

    **CMD**

    ```console
    cd device\iot-device-samples\pnp-device-sample\temperature-controller-device-sample
    ```

    **Bash**

    ```bash
    cd device/iot-device-samples/pnp-device-sample/temperature-controller-device-sample
    ```

1. Exécutez l’exemple de code.

    ```console
    mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
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

    :::image type="content" source="media/iot-develop-send-telemetry-iot-hub-java/iot-explorer-device-telemetry.png" alt-text="Capture d’écran de télémétrie d’appareil dans IoT Explorer":::

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
        temperature: 24.1
    
    event:
      component: thermostat2
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: mydevice
      payload:
        temperature: 33.3
    ```
    

