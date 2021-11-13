---
title: Provisionnement de l’agent Device Update pour Azure IoT Hub | Microsoft Docs
description: Provisionnement de l’agent Device Update pour Azure IoT Hub
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: ecfbd8ac385a3449ce2934ec93b10c255d621fe1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130259072"
---
# <a name="device-update-agent-provisioning"></a>Approvisionnement de l’agent Device Update

L’agent du module Device Update peut s’exécuter aux côtés d’autres processus système et [modules IoT Edge](../iot-edge/iot-edge-modules.md) qui se connectent à votre hub IoT dans le cadre du même périphérique logique. Cette section explique comment approvisionner l’agent Device Update en tant qu’identité de module. 


## <a name="module-identity-vs-device-identity"></a>Identité de module et identité d’appareil

Dans IoT Hub, sous chaque identité d’appareil, vous pouvez créer jusqu’à 50 identités de module. Chaque identité de module génère implicitement un jumeau de module. Côté appareil, les kits IoT Hub device SDK vous permettent de créer des modules dont chacun ouvre une connexion indépendante à IoT Hub. L’identité de module et le jumeau de module offrent les mêmes capacités que l’identité d’appareil et le jumeau d’appareil, mais avec une plus grande précision. [En savoir plus sur les identités de module dans IoT Hub](../iot-hub/iot-hub-devguide-module-twins.md).

Si vous passez d’un agent au niveau appareil à l’ajout de l’agent en tant qu’identité de module sur l’appareil, supprimez l’ancien agent qui communiquait sur le jumeau d’appareil. Lorsque vous approvisionnez l’agent Device Update en tant qu’identité de module, toutes les communications entre l’appareil et le service Device Update se produisent sur le jumeau de module. N’oubliez donc pas d’étiqueter le jumeau de module de l’appareil quand vous créez des [groupes](device-update-groups.md) et quand toutes les [communications](device-update-plug-and-play.md) doivent avoir lieu sur le jumeau de module.

## <a name="support-for-device-update"></a>Prise en charge pour Device Update

Les types d’appareils IoT suivants sont actuellement pris en charge par Device Update :

* Appareils Linux (appareils IoT Edge et non IoT Edge) :
    * Mise à jour de l’image A/B :
        - Yocto – ARM64 (image de référence), extensible via l’open source pour [créer vos propres images](device-update-agent-provisioning.md#how-to-build-and-run-device-update-agent) pour d’autres architectures, le cas échéant.
        - Simulateur Ubuntu 18.04
       
    * Builds de l’agent de package prises en charge pour les plateformes/architectures suivantes :
        - Agent de package Ubuntu Server 18.04 x64 
        - Debian 9 
        
* Appareils avec contraintes :
    * Exemples d’agent AzureRTOS Device Update : [Tutoriel sur Device Update pour Azure IoT Hub pour Azure-Real-Time-Operating-System](device-update-azure-real-time-operating-system.md)

* Appareils déconnectés : 
    * [Comprendre la prise en charge de la mise à jour des appareils déconnectés](connected-cache-disconnected-device-update.md)


## <a name="prerequisites"></a>Prérequis  

Si vous configurez l’appareil IoT ou l’appareil IoT Edge pour des [mises à jour basées sur un package](./understand-device-update.md#support-for-a-wide-range-of-update-artifacts), ajoutez packages.microsoft.com aux référentiels de votre ordinateur en procédant comme suit :

1. Connectez-vous à l’ordinateur ou à l’appareil IoT sur lequel vous souhaitez installer l’agent Device Update.

1. Ouvrez une fenêtre de terminal.

1. Installez la configuration du référentiel qui correspond au système d’exploitation de votre appareil.

    ```shell
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```
    
1. Copiez la liste générée dans le répertoire sources.list.d.

    ```shell
    sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
    ```
    
1. Installez la clé publique Microsoft GPG.

    ```shell
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    ```

    ```shell
    sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
    ```

## <a name="how-to-provision-the-device-update-agent-as-a-module-identity"></a>Comment approvisionner l’agent Device Update en tant qu’identité de module

Cette section explique comment approvisionner l’agent Device Update en tant qu’identité de module. 
* Appareils IoT Edge ou 
* Appareils IoT non-Edge ou
* Autres appareils IoT 
 
Pour ajouter l’agent de mise à jour des appareils en fonction du type d’appareil IoT que vous gérez, suivez la totalité ou une des sections ci-dessous. 

### <a name="on-iot-edge-enabled-devices"></a>Sur des appareils IoT Edge

Suivez ces instructions pour approvisionner l’agent Device Update sur [des appareils IoT Edge](../iot-edge/index.yml).

1. Suivez les instructions pour [provisionner manuellement un seul appareil IoT Edge sur Linux](../iot-edge/how-to-provision-single-device-linux-symmetric.md?preserve-view=true&view=iotedge-2020-11).

1. Installer l'agent de mise à jour d'image Device Update

    Nous fournissons des exemples d’images dans le référentiel [Artefacts](https://github.com/Azure/iot-hub-device-update/releases). Le fichier swUpdate est l’image de base que vous pouvez flasher sur une carte Raspberry Pi B3+. Le fichier .gz est la mise à jour que vous importeriez via Device Update pour IoT Hub. Consultez l'exemple montrant [comment flasher l'image sur votre appareil IoT Hub](./device-update-raspberry-pi.md#flash-sd-card-with-image).  

1. Installer l'agent de mise à jour de package Device Update

    - Pour obtenir les dernières versions de l'agent, accédez à packages.miscrosoft.com : Mettez à jour les listes de packages sur votre appareil, puis installez le package de l'agent Device Update et ses dépendances en utilisant :   

        ```shell
        sudo apt-get update
        ```
    
        ```shell
        sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt
        ```
    
    - Pour toutes les versions d’agent Release Candidate (rc) d’[Artifacts](https://github.com/Azure/iot-hub-device-update/releases) : téléchargez le fichier .dep sur la machine sur laquelle vous souhaitez installer l’agent Device Update, puis :
   
        ```shell
        sudo apt-get install -y ./"<PATH TO FILE>"/"<.DEP FILE NAME>"
        ```
    
1. Vous êtes maintenant prêt à démarrer l'agent Device Update sur votre appareil IoT Edge. 

### <a name="on-non-edge-iot-linux-devices"></a>Sur les appareils IoT Linux non Edge

Suivez ces instructions pour approvisionner l’agent Device Update sur vos appareils IoT Linux.

1. Installez le service d’identité IoT et ajoutez la version la plus récente sur votre appareil IoT. 
    1. Connectez-vous à l’ordinateur ou à l’appareil IoT.
    1. Ouvrez une fenêtre de terminal.
    1.  Installez la version la plus récente du [service d’identité IoT](https://github.com/Azure/iot-identity-service/blob/main/docs-dev/packaging.md#installing-and-configuring-the-package) sur votre appareil IoT à l’aide de la commande suivante :
    > [!Note]
    > Le service d’identité IoT inscrit des identités de module auprès d’IoT Hub en utilisant actuellement des clés symétriques.

    ```shell
    sudo apt-get install aziot-identity-service
    ```
        
1. Approvisionnez le service d’identité IoT pour récupérer les informations de l’appareil IoT.

    Créez une copie personnalisée du modèle de configuration pour pouvoir ajouter les informations d’approvisionnement. Dans le terminal, entrez la commande suivante :
      
    ```shell
    sudo cp /etc/aziot/config.toml.template /etc/aziot/config.toml 
    ```
   
1. Modifiez ensuite le fichier config pour inclure la chaîne de connexion de l’appareil que vous souhaitez utiliser comme approvisionneur pour cet appareil ou cet ordinateur. Dans un terminal, entrez la commande ci-dessous.

    ```shell
    sudo nano /etc/aziot/config.toml
    ```
   
1. Un message similaire à l’exemple ci-dessous doit s’afficher :

    :::image type="content" source="media/understand-device-update/config.png" alt-text="Diagramme du fichier config du service d’identité IoT." lightbox="media/understand-device-update/config.png":::

    1. Dans la même fenêtre nano, recherchez le bloc avec « Manual provisioning with connection string » (Approvisionnement manuel avec chaîne de connexion).
    1. Dans la fenêtre, supprimez le symbole « # » devant « provisioning ».
    1. Dans la fenêtre, supprimez le symbole « # » devant « source ». 
    1. Dans la fenêtre, supprimez le symbole « # » devant « connection_string ».
    1. Dans la fenêtre, supprimez la chaîne entre guillemets à droite de « connection_string », puis ajoutez votre chaîne de connexion à la place. 
    1. Enregistrez vos modifications dans le fichier en appuyant sur les touches « Ctrl+X », puis « Y » et appuyez sur la touche « Entrée » pour enregistrer vos modifications. 
    
1. À présent, appliquez et redémarrez le service d’identité IoT à l’aide de la commande ci-dessous. Vous devez maintenant voir une impression « Done! » (Terminé !) qui signifie que vous avez correctement configuré le service d’identité IoT.

    > [!Note]
    > Le service d’identité IoT inscrit des identités de module auprès d’IoT Hub en utilisant actuellement des clés symétriques.
    
    ```shell
    sudo aziotctl config apply
    ```
    
1. Enfin, installez l'agent Device Update. Nous fournissons des exemples d’images dans les [Artefacts](https://github.com/Azure/iot-hub-device-update/releases). Le fichier swUpdate est l’image de base que vous pouvez appliquer sur une carte Raspberry Pi B3+, et le fichier .gz est la mise à jour que vous importez par le biais de la mise à jour de l’appareil pour IoT Hub. Consultez l'exemple montrant [comment flasher l'image sur votre appareil IoT Hub](./device-update-raspberry-pi.md#flash-sd-card-with-image).

1. Vous êtes maintenant prêt à démarrer l'agent Device Update sur votre appareil IoT. 

### <a name="other-iot-devices"></a>Autres appareils IoT

L’agent Device Update peut également être configuré sans le service d’identité IoT à des fins de test ou sur des appareils avec contraintes. Suivez les étapes ci-dessous pour approvisionner l’agent Device Update à l’aide d’une chaîne de connexion (à partir du module ou du périphérique).

1. Nous fournissons des exemples d’images dans le référentiel [Artefacts](https://github.com/Azure/iot-hub-device-update/releases). Le fichier swUpdate est l’image de base que vous pouvez flasher sur une carte Raspberry Pi B3+. Le fichier .gz est la mise à jour que vous importeriez via Device Update pour IoT Hub. Consultez l'exemple montrant [comment flasher l'image sur votre appareil IoT Hub](./device-update-raspberry-pi.md#flash-sd-card-with-image).

1. Connectez-vous à l’ordinateur ou à l’appareil IoT ou IoT Edge.
    
1. Ouvrez une fenêtre de terminal.

1. Ajoutez la chaîne de connexion dans le [fichier config de Device Update](device-update-configuration-file.md) :

    1. Entrez ce qui suit dans la fenêtre du terminal :
   
        - [Pour les mises à jour de package](device-update-ubuntu-agent.md), utilisez : sudo nano /etc/adu/adu-conf.txt
        - [Pour les mises à jour d’image](device-update-raspberry-pi.md), utilisez : sudo nano /adu/adu-conf.txt
       
    1. Vous devez voir une fenêtre ouverte avec du texte. Supprimez l’intégralité de la chaîne suivant « connection_String= » la première fois que vous approvisionnez l’agent Device Update sur l’appareil IoT. Il s’agit simplement d’un texte de remplacement.
    
    1. Dans le terminal, remplacez \<your-connection-string\> par la chaîne de connexion de l’appareil pour votre instance de l’agent Device Update. Sélectionnez Entrée puis **Enregistrer.** Vous devriez obtenir un résultat semblable au suivant :
    
        ```text
        connection_string=<ADD CONNECTION STRING HERE>
        ```   
        
    > [!Important]
    > N’ajoutez pas de guillemets autour de la chaîne de connexion.
    
1. Vous êtes maintenant prêt à démarrer l'agent Device Update sur votre appareil IoT. 


## <a name="how-to-start-the-device-update-agent"></a>Procédure de démarrage de l’agent Device Update

Cette section décrit comment démarrer l’agent Device Update en tant qu’identité de module et vérifier qu’il fonctionne correctement sur votre appareil IoT.

1. Connectez-vous à l’ordinateur ou à l’appareil sur lequel l’agent Device Update est installé.

1. Ouvrez une fenêtre de terminal et entrez la commande ci-dessous.

    ```shell
    sudo systemctl restart adu-agent
    ```
    
1. Vous pouvez vérifier l’état de l’agent en utilisant la commande ci-dessous. Si vous rencontrez des problèmes, reportez-vous à ce [guide de résolution des problèmes](troubleshoot-device-update.md).
    
    ```shell
    sudo systemctl status adu-agent
    ```
    
    L’état OK doit s’afficher.

1. Dans le portail IoT Hub, accédez à l’appareil IoT ou aux appareils IoT Edge pour trouver l’appareil sur lequel vous avez approvisionné l’agent Device Update. Vous verrez que l’agent Device Update s’exécute en tant que module. Par exemple :

    :::image type="content" source="media/understand-device-update/device-update-module.png " alt-text="Diagramme du nom du module Device Update." lightbox="media/understand-device-update/device-update-module.png":::


## <a name="how-to-build-and-run-device-update-agent"></a>Procédure de création et d’exécution de l’agent Device Update

Vous pouvez également créer et modifier votre propre agent Device Update client.

Suivez ces instructions pour [générer](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md) l’agent Device Update à partir de la source.

Une fois l'agent correctement généré, vous pouvez l'[exécuter](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md).

À présent, apportez les modifications nécessaires pour incorporer l’agent dans votre image.  Découvrez comment [modifier](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.md) l’agent Device Update pour obtenir des instructions.


## <a name="troubleshooting-guide"></a>Guide de résolution des problèmes

Si vous rencontrez des problèmes, consultez le [Guide de dépannage](troubleshoot-device-update.md) de Device Update pour IoT Hub pour résoudre les éventuels problèmes et collecter les informations nécessaires à fournir à Microsoft.


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez utiliser les images et les fichiers binaires prédéfinis suivants pour une démonstration simple de Device Update pour IoT Hub :

- [Mise à jour d’image : Démarrer avec l’image de référence Yocto Raspberry Pi 3 B+](device-update-raspberry-pi.md), extensible via l’open source pour générer vos propres images pour d’autres architectures, le cas échéant

- [Démarrer avec l’agent de référence du simulateur Ubuntu (18.04 x64)](device-update-simulator.md)

- [Mise à jour du package : Démarrer avec l’agent de package Ubuntu Server 18.04 x64](device-update-ubuntu-agent.md)

- [Tutoriel sur Device Update pour Azure IoT Hub pour Azure-Real-Time-Operating-System](device-update-azure-real-time-operating-system.md)
