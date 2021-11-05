---
title: Déployer Azure Video Analyzer sur un appareil IoT Edge - Azure
description: Cet article liste les étapes pour déployer Azure Video Analyzer sur votre appareil IoT Edge. Vous pouvez le faire, par exemple, si vous avez accès à une machine Linux locale.
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6bd045a6cbc1d85790119564a00783b74f759b97
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556594"
---
# <a name="deploy-azure-video-analyzer-to-an-iot-edge-device"></a>Déployer Azure Video Analyzer sur un appareil IoT Edge

[!INCLUDE [header](includes/edge-env.md)]

Cet article décrit comment déployer le module périphérique Azure Video Analyzer sur un appareil IoT Edge sur lequel aucun autre module n’a été installé précédemment. À la fin des étapes de cet article, vous aurez créé un compte Video Analyzer et déployé le module Video Analyzer sur votre appareil IoT Edge, ainsi qu’un module qui simule une caméra IP compatible RTSP. Le processus doit être utilisé avec les guides de démarrage rapide et les tutoriels de Video Analyzer. Vous devez consulter l’article sur la [préparation à la production et bonnes pratiques](production-readiness.md) si vous voulez déployer le module Video Analyzer pour l’utiliser en production.

> [!NOTE]
> Le processus décrit dans cet article désinstalle les modules périphériques, le cas échéant, qui sont installés sur votre appareil IoT Edge.

## <a name="prerequisites"></a>Prérequis

* Un appareil x86-64 ou ARM64 exécutant l’un des [systèmes d’exploitation Linux pris en charge](../../../iot-edge/support.md#operating-systems)
* Un compte Azure avec un abonnement actif
* [Créer et configurer IoT Hub](../../../iot-hub/iot-hub-create-through-portal.md)
* [Enregistrer un appareil IoT Edge](../../../iot-edge/how-to-register-device.md)
* [Installer le runtime Azure IoT Edge sur des systèmes Linux Debian](../../../iot-edge/how-to-install-iot-edge.md)


## <a name="create-resources-on-iot-edge-device"></a>Créer des ressources sur un appareil IoT Edge

Le module Azure Video Analyzer doit être configuré pour s’exécuter sur l’appareil IoT Edge avec un compte d’utilisateur local non privilégié. Le module a aussi besoin de certains dossiers locaux pour stocker les données de configuration d’application. Pour ce guide pratique, nous utilisons un [simulateur RTSP](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) qui relaie un flux vidéo en temps réel vers le module AVA à des fins d’analyse. Ce simulateur prend comme entrée des fichiers vidéo préenregistrés à partir d’un répertoire d’entrée. Le script suivant prépare votre appareil pour l’utiliser avec nos guides de démarrage rapide et nos tutoriels.

https://aka.ms/ava/prepare-device

`bash -c "$(curl -sL https://aka.ms/ava-edge/prep_device)"`

Le script prep-device utilisé ci-dessus automatise la tâche de création de dossiers d’entrée et de configuration, de téléchargement de fichiers d’entrée vidéo et de création de comptes d’utilisateur avec les privilèges appropriés. Une fois la commande terminée, les dossiers suivants auront normalement été créés sur votre appareil Edge. 

* `/home/localedgeuser/samples`
* `/home/localedgeuser/samples/input`
* `/var/lib/videoanalyzer`
* `/var/media`

    Notez les fichiers vidéo (« *.mkv ») dans le dossier /home/localedgeuser/samples/input, qui sont utilisés pour simuler la vidéo en direct. 

## <a name="creating-azure-resources-and-deploying-edge-modules"></a>Création de ressources Azure et déploiement de modules périphériques
Dans l’étape suivante, vous créez les ressources Azure nécessaires (compte Video Analyzer, compte de stockage, identité managée affectée par l’utilisateur), inscrivez un module périphérique Video Analyzer avec le compte Video Analyzer et déployez le module périphérique Video Analyzer ainsi que le module du simulateur RTSP sur l’appareil IoT Edge.

Cliquez sur le bouton **Déployer sur Azure**

> [!WARNING]
> Ne l’utilisez pas avec des appareils IoT Edge qui ont déjà des modules périphériques installés, par exemple Percept DK, ou qui ne sont pas pris en charge avec Azure Stack Edge.

[![Déployer sur Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava/click-to-deploy/form)

1. Sélectionnez votre **abonnement**
2. Sélectionnez la **région** de votre choix
3. Sélectionnez le **groupe de ressources** auquel appartient votre hub IoT et votre appareil IoT Edge
4. Dans le menu déroulant **Avez-vous besoin d’un périphérique ?** , sélectionnez l’option **_Utiliser un périphérique existant_**
5. Cliquez sur **Suivant**
![Capture d’écran du formulaire de déploiement initial](./media/deploy-iot-edge-device/project-details.png)

1. Sélectionnez le **Nom du hub IoT existant** auquel votre appareil IoT Edge est connecté
1. Cliquez sur **Suivant**
![Capture d’écran du deuxième formulaire de déploiement](./media/deploy-iot-edge-device/iot-hub-name.png)

1. Dans la dernière page, cliquez sur **Créer**

La création des ressources Azure et le déploiement des modules périphériques peuvent prendre quelques instants.

### <a name="verify-your-deployment"></a>Vérifier votre déploiement

Après avoir créé le déploiement, dans le portail Azure, accédez à la page de l’appareil IoT Edge dans votre hub IoT.

1. Sélectionnez le périphérique IoT Edge que vous avez ciblé avec le déploiement pour accéder à ses informations détaillées.
2. Dans les détails de l’appareil, vérifiez que les modules sont listés à la fois sous **Spécifié dans le déploiement et Signalé par l’appareil**.

Le démarrage des modules sur l’appareil et leur signalement au hub IoT peuvent prendre quelques instants. Actualisez la page pour afficher un état mis à jour.
Code d’état : 200 –OK signifie que [le runtime d’IoT Edge](../../../iot-edge/iot-edge-runtime.md) est sain et fonctionne correctement.

![La capture d’écran affiche une valeur d’état pour un runtime IoT Edge.](./media/deploy-iot-edge-device/status.png)

#### <a name="invoke-a-direct-method"></a>Appeler une méthode directe

Testons ensuite l’exemple en appelant une méthode directe. Lisez [Méthodes directes pour Azure Video Analyzer](direct-methods.md) pour comprendre toutes les méthodes directes fournies par notre module avaEdge.

1. En cliquant sur le module Edge que vous avez créé, vous êtes redirigé vers sa page de configuration.  

    ![La capture d’écran affiche la page de configuration d’un module Edge.](./media/deploy-iot-edge-device/modules.png)
1. Cliquez sur l’option de menu **Méthode directe**.

    > [!NOTE] 
    > Vous devrez ajouter une valeur dans les sections Chaîne de connexion, comme vous pouvez le voir sur la page actuelle. Vous n’avez pas besoin de masquer ou de modifier quoi que ce soit dans la section **Nom du paramètre**. Vous pouvez laisser ces informations publiques.

    ![Méthode directe](./media/deploy-iot-edge-device/module-details.png)
1. Ensuite, entrez « pipelineTopologyList » dans la zone `Method Name`.
1. Puis, copiez et collez la charge utile JSON ci-dessous dans la zone Charge utile.
    
   ```
   {
       "@apiVersion": "1.1"
   }
   ```
1. Cliquez sur l’option **Appeler la méthode** en haut de la page
1. Vous devez voir le message d’état 200 dans la zone `Result`

    ![Message d’état 200](./media/deploy-iot-edge-device/connection-timeout.png) 

## <a name="next-steps"></a>Étapes suivantes

Essayez [Démarrage rapide : Démarrer avec Azure Video Analyzer](get-started-detect-motion-emit-events.md)

> [!TIP]
> Si vous poursuivez avec le démarrage rapide ci-dessus, lors de l’appel des méthodes directes à l’aide de Visual Studio Code, vous utiliserez l’appareil qui a été ajouté au IoT Hub via cet article, au lieu de la `avasample-iot-edge-device` par défaut.
