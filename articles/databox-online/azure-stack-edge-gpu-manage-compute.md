---
title: Gestion du calcul sur Azure Stack Edge Pro avec GPU | Microsoft Docs
description: Décrit comment gérer les paramètres de calcul Edge (déclencheur, modules, affichage de la configuration du calcul, suppression de configuration) via le portail Azure sur votre appareil Azure Stack Edge Pro avec GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/04/2021
ms.author: alkohli
ms.openlocfilehash: eac9154d4308700eaa6ea8faf0bf94fabc80bce4
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111982517"
---
# <a name="manage-compute-on-your-azure-stack-edge-pro-gpu"></a>Gérer le calcul sur votre appareil Azure Stack Edge Pro avec GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article explique comment gérer le calcul par l’intermédiaire du service IoT Edge sur un appareil GPU Azure Stack Edge Pro. Vous pouvez gérer le calcul via le portail Azure ou l’interface utilisateur web locale. Le Portail Azure permet de gérer les modules, les déclencheurs, la configuration IoT Edge et l’interface utilisateur locale pour l’administration des paramètres réseau de calcul.



## <a name="manage-triggers"></a>Gérer les déclencheurs

Les événements sont des faits qui se produisent au sein de votre environnement cloud ou sur votre appareil, sur lesquels vous pouvez agir. Par exemple, quand un fichier est créé dans un partage, il s’agit d’un événement. Les déclencheurs activent les événements. Dans Azure Stack Edge Pro, des déclencheurs peuvent répondre à des événements de fichier ou intervenir en vertu d’une planification.

- **Fichier** : Ces déclencheurs répondent à des événements tels que la création ou la modification d’un fichier.
- **Scheduled** : Ces déclencheurs résultent d’une planification que vous pouvez définir avec une date de début, une heure de début et un intervalle de répétition.


### <a name="add-a-trigger"></a>Ajouter un déclencheur

Pour créer un déclencher, procédez comme suit sur le portail Azure.

1. Sur le Portail Azure, accédez à votre ressource Azure Stack Edge, puis à **IoT Edge**. Accédez à **Déclencheurs**, puis sélectionnez **+ Ajouter un déclencheur** dans la barre de commandes.

    ![Sélectionner Ajouter un déclencheur](media/azure-stack-edge-gpu-manage-compute/add-trigger-1-m.png)

2. Dans le panneau **Ajouter un déclencheur**, entrez un nom unique pour votre déclencheur.
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. Sélectionnez un **Type** pour le déclencheur. Choisissez **Fichier** lorsque le déclencheur répond à un événement de fichier. Sélectionnez **Planifié** si vous souhaitez que le déclencheur démarre à une heure définie et se répète à un intervalle spécifié. L’ensemble d’options présenté varie en fonction de votre sélection.

    - **Déclencheur de fichier** : choisissez un partage monté dans la liste déroulante. Lors du déclenchement d’un événement de fichier dans ce partage, le déclencheur appelle une fonction Azure.

        ![Ajouter un partage SMB](media/azure-stack-edge-gpu-manage-compute/add-file-trigger.png)

    - **Déclencheur planifié** : spécifiez les date et heure de démarrage, ainsi que l’intervalle de répétition en heures, minutes ou secondes. Entrez le nom d’une rubrique. Une rubrique vous donne la possibilité de router le déclencheur vers un module déployé sur l’appareil.

        Voici un exemple de chaîne de routage : `"route3&quot;: &quot;FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint(&quot;modules/modulename/inputs/input1")"`.

        ![Ajouter un partage NFS](media/azure-stack-edge-gpu-manage-compute/add-scheduled-trigger.png)

4. Sélectionnez **Ajouter** pour créer le déclencheur. Une notification indique que la création du déclencheur est en cours. Une fois le déclencheur créé, le panneau s’actualise pour afficher le nouveau déclencheur.
 
    ![Liste des déclencheurs mise à jour](media/azure-stack-edge-gpu-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>Supprimer un déclencheur

Pour supprimer un déclencheur, procédez comme suit sur le portail Azure.

1. Dans la liste des déclencheurs, sélectionnez le déclencheur à supprimer.

    ![Sélectionner le déclencheur](media/azure-stack-edge-gpu-manage-compute/delete-trigger-1.png)

2. Cliquez avec le bouton droit, puis sélectionnez **Supprimer**.

    ![Sélectionner Supprimer](media/azure-stack-edge-gpu-manage-compute/delete-trigger-2.png)

3. Cliquez sur **Oui** lorsque vous êtes invité à confirmer l’opération.

    ![Confirmation de suppression](media/azure-stack-edge-gpu-manage-compute/delete-trigger-3.png)

La liste des déclencheurs est actualisée afin de refléter la suppression.

## <a name="manage-iot-edge-configuration"></a>Gestion de la configuration IoT Edge

Utilisez le portail Azure pour afficher la configuration de calcul, supprimer une configuration de calcul existante, ou actualiser la configuration de calcul afin de synchroniser les clés d’accès de l’appareil IoT et de l’appareil IoT Edge pour Azure Stack Edge Pro.

### <a name="view-iot-edge-configuration"></a>Affichage de la configuration IoT Edge

Pour afficher la configuration IoT Edge de votre appareil, suivez la procédure ci-dessous sur le Portail Azure.

1. Sur le Portail Azure, accédez à votre ressource Azure Stack Edge, puis à **IoT Edge**. Une fois le service IoT Edge activé sur l’appareil, la page Vue d’ensemble indique qu’il s’exécute bien.

    ![Sélectionner Voir le computing](media/azure-stack-edge-gpu-manage-compute/view-compute-1.png)

2. Accédez à **Propriétés** pour afficher la configuration IoT Edge sur votre appareil. Lorsque vous avez configuré le calcul, vous avez créé une ressource IoT Hub. Sous cette ressource IoT Hub, un appareil IoT et un appareil IoT Edge sont configurés. Seuls les modules Linux sont pris en charge pour s’exécuter sur l’appareil IoT Edge.

    ![Afficher la configuration](media/azure-stack-edge-gpu-manage-compute/view-compute-2.png)


### <a name="remove-iot-edge-service"></a>Suppression du service IoT Edge

Pour supprimer la configuration IoT Edge existante de votre appareil, suivez la procédure ci-dessous sur le Portail Azure.

1. Sur le Portail Azure, accédez à votre ressource Azure Stack Edge, puis à **IoT Edge**. Accédez à **Vue d’ensemble** et sélectionnez **Supprimer** dans la barre de commandes.

    ![Sélectionner Supprimer le computing](media/azure-stack-edge-gpu-manage-compute/remove-compute-1.png)

2. Si vous supprimez le service IoT Edge, l’action est irréversible et ne peut pas être annulée. Les modules et les déclencheurs que vous avez créés seront également supprimés. Il vous faudra reconfigurer votre appareil au cas où vous devriez utiliser de nouveau IoT Edge. Quand il vous est demandé de confirmer l’opération, sélectionnez **OK**.

    ![Sélectionner Supprimer le computing 2](media/azure-stack-edge-gpu-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>Synchroniser les clés d’accès de l’appareil IoT et de l’appareil IoT Edge

Lorsque vous configurez le calcul sur votre ressource Azure Stack Edge Pro, un appareil IoT et un appareil IoT Edge sont créés. Des clés d’accès symétriques sont affectées automatiquement à ces appareils. Conformément aux meilleures pratiques de sécurité, le service IoT Hub veille à une rotation régulière de ces clés.

Pour définir la rotation de ces clés, vous pouvez accéder au service IoT Hub que vous avez créé, puis sélectionnez l’appareil IoT ou l’appareil IoT Edge. Chaque appareil possède une clé d’accès primaire et une clé d’accès secondaire. Affectez la clé d’accès primaire à la clé d’accès secondaire, puis régénérez la clé d’accès primaire.

Si une rotation des clés de vos appareils IoT et IoT Edge a eu lieu, vous devez actualiser la configuration sur votre ressource Azure Stack Edge Pro pour obtenir les dernières clés d’accès. La synchronisation aide l’appareil à obtenir les dernières clés de vos appareils IoT et IoT Edge. Azure Stack Edge Pro utilise uniquement les clés d’accès primaires.

Pour synchroniser les clés d’accès pour votre appareil, procédez comme suit sur le portail Azure.

1. Sur le Portail Azure, accédez à votre ressource Azure Stack Edge, puis à **Computing IoT Edge**. Accédez à **Vue d’ensemble** et sélectionnez **Actualiser la configuration** dans la barre de commandes.

    ![Sélectionner Actualiser la configuration](media/azure-stack-edge-gpu-manage-compute/refresh-configuration-1.png)

2. Lorsque vous êtes invité à confirmer l’opération, sélectionnez **Oui**.

    ![Sélectionner Oui à l’invite](media/azure-stack-edge-gpu-manage-compute/refresh-configuration-2.png)

3. Fermez la boîte de dialogue une fois la synchronisation terminée.

## <a name="change-external-service-ips-for-containers"></a>Modifier les adresses IP du service externe pour des conteneurs

Les adresses IP du service externe Kubernetes sont utilisées pour accéder aux services exposés en dehors du cluster Kubernetes. Une fois votre appareil activé, vous pouvez définir ou modifier les adresses IP du service externe pour les charges de travail conteneurisées pour votre appareil en accédant à l’interface utilisateur locale.


1. Dans l’interface utilisateur locale de l’appareil, accédez à **Compute**.
1. Sélectionnez le port dont le réseau est configuré pour le calcul. Dans le panneau qui s’ouvre, spécifiez ou modifiez les adresses IP (respectivement nouvelles ou existantes) du service externe Kubernetes. Ces adresses IP sont utilisées pour tous les services qui doivent être exposés en dehors du cluster Kubernetes. 
    - Vous avez besoin au minimum d’une adresse IP de service pour le service `edgehub` qui s’exécute sur votre appareil et que les modules IoT Edge utilisent. 
    - Vous aurez besoin d’une adresse IP pour chaque module ou conteneur IoT Edge supplémentaire que vous souhaitez déployer. 
    - Il s’agit d’adresses IP statiques contiguës.

    ![Modifier les adresses IP du service Kubernetes](media/azure-stack-edge-gpu-manage-compute/change-service-ips-1.png)

1. Sélectionnez **Appliquer**. Une fois les adresses IP appliquées, votre appareil n’a pas besoin d’être redémarré ou relancé. Les nouvelles adresses IP prennent effet immédiatement.


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Résoudre les problèmes avec IoT Edge survenant sur votre appareil Azure Stack Edge Pro avec GPU](azure-stack-edge-gpu-troubleshoot-iot-edge.md).
