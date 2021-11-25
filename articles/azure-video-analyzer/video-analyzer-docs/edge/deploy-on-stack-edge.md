---
title: Déployer Azure Video Analyzer sur Azure Stack Edge
description: Cet article explique la procédure de déploiement d’Azure Video Analyzer sur Azure Stack Edge.
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 023d6500a16d5b808bbb2e249076eae9b53741f8
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487628"
---
# <a name="deploy-azure-video-analyzer-on-azure-stack-edge"></a>Déployer Azure Video Analyzer sur Azure Stack Edge

Cet article fournit des instructions complètes sur le déploiement d’Azure Video Analyzer sur votre appareil Azure Stack Edge. Une fois que vous avez configuré et activé l’appareil, il est prêt pour le déploiement de Video Analyzer. 

Dans cet article, nous allons procéder à un déploiement par le biais d’Azure IoT Hub, bien que les ressources Azure Stack Edge exposent une API Kubernetes, qui permet au client de déployer des solutions supplémentaires sans prise en charge d’IoT Hub, qui peuvent interagir avec Video Analyzer. 

> [!TIP]
> L’utilisation de l’API Kubernetes pour le déploiement personnalisé est un cas particulier. Nous vous recommandons de créer des modules périphériques et de procéder au déploiement via IoT Hub sur chaque ressource Azure Stack Edge au lieu d’utiliser l’API Kubernetes. Cet article explique comment déployer le module Video Analyzer à l’aide d’IoT Hub.

## <a name="prerequisites"></a>Prérequis

* Un compte Azure Video Analyzer

    Ce [service cloud](../overview.md) permet d’inscrire le module périphérique Video Analyzer, et de lire des vidéos enregistrées et l’analytique vidéo.
* Une identité managée

    Il s’agit de l’[identité managée](../../../active-directory/managed-identities-azure-resources/overview.md) affectée par l’utilisateur qui permet de gérer l’accès à votre compte de stockage.
* Une ressource [Azure Stack Edge](../../../databox-online/azure-stack-edge-gpu-deploy-prep.md)
* Un [hub IoT](../../../iot-hub/iot-hub-create-through-portal.md)
* Un compte de stockage

    Nous vous recommandons d’utiliser un [compte de stockage v2 à usage général](../../../storage/common/storage-account-upgrade.md?tabs=azure-portal).  
* [Visual Studio Code](https://code.visualstudio.com/) installé sur votre machine de développement
*  L’[extension Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) installée dans Visual Studio Code
* Vérifiez que le réseau auquel votre machine de développement est connectée autorise le protocole AMQP (Advanced Message Queueing Protocol) sur le port 5671. Cette configuration permet à Azure IoT Tools de communiquer avec votre hub Azure IoT.

## <a name="configure-azure-stack-edge-to-use-video-analyzer"></a>Configurer Azure Stack Edge pour utiliser Video Analyzer

Azure Stack Edge est une solution matérielle en tant que service ainsi qu’un appareil de computing en périphérie basé sur l’intelligence artificielle. Il est doté de fonctionnalités de transfert de données via le réseau. Pour plus d’informations, consultez [Azure Stack Edge et instructions d’installation détaillées](../../../databox-online/azure-stack-edge-gpu-deploy-prep.md). 

Pour commencer, suivez les étapes ci-dessous :

1. [Créez une ressource Azure Stack Edge ou Azure Data Box Gateway](../../../databox-online/azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource).  
1. [Installez et configurez Azure Stack Edge Pro avec GPU](../../../databox-online/azure-stack-edge-gpu-deploy-install.md).  
1. Connectez et activez la ressource en procédant comme suit :

    a. [Connectez-vous à la configuration de l’interface utilisateur locale](../../../databox-online/azure-stack-edge-gpu-deploy-connect.md).  
    b. [Configurez le réseau](../../../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).  
    c. [Configurez l’appareil](../../../databox-online/azure-stack-edge-gpu-deploy-set-up-device-update-time.md).  
    d. [Configurez les certificats](../../../databox-online/azure-stack-edge-gpu-deploy-configure-certificates.md).  
    e. [Activez l’appareil](../../../databox-online/azure-stack-edge-gpu-deploy-activate.md).  

1. [Attachez un hub IoT à Azure Stack Edge](../../../databox-online/azure-stack-edge-gpu-deploy-configure-compute.md#configure-compute).

### <a name="meet-the-compute-prerequisites-on-the-azure-stack-edge-local-ui"></a>Répondre aux conditions préalables de calcul sur l’interface utilisateur locale Azure Stack Edge

Avant de continuer, assurez-vous que vous avez effectué les opérations suivantes :

* vous avez activé votre ressource Azure Stack Edge ;
* vous avez accès à un système client Windows exécutant PowerShell 5.0 ou une version ultérieure pour accéder à la ressource Azure Stack Edge.
* Pour déployer des clusters Kubernetes, vous avez configuré votre ressource Azure Stack Edge sur son [interface utilisateur locale](../../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md#connect-to-the-local-web-ui-setup). 

    1. Connectez-vous et configurez la ressource en effectuant les actions suivantes : a. [Connectez-vous à la configuration de l’interface utilisateur locale](../../../databox-online/azure-stack-edge-gpu-deploy-connect.md).  
        b. [Configurez le réseau](../../../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).  
        c. [Configurer l’appareil](../../../databox-online/azure-stack-edge-gpu-deploy-set-up-device-update-time.md)  
        d. [Configurez les certificats](../../../databox-online/azure-stack-edge-gpu-deploy-configure-certificates.md).  
        e. [Activez l’appareil](../../../databox-online/azure-stack-edge-gpu-deploy-activate.md).

    1. Pour activer le calcul, accédez à la page **Calcul** de l’interface utilisateur locale de votre appareil.
    
        a. Sélectionnez une interface réseau que vous souhaitez activer pour le calcul, puis sélectionnez **Activer**. L’activation du calcul crée un commutateur virtuel sur votre appareil sur cette interface réseau.  
        b. Laissez les adresses IP du nœud de test Kubernetes et les adresses IP des services externes Kubernetes vides.  
        c. Sélectionnez **Appliquer**. L’opération doit prendre environ deux minutes.
        
        > [!div class="mx-imgBorder"]

        > :::image type="content" source="../../../databox-online/media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png" alt-text="Capture d’écran des conditions préalables de calcul sur l’interface utilisateur locale Azure Stack Edge.":::

        Si Azure DNS n’est pas configuré pour l’API Kubernetes et la ressource Azure Stack Edge, vous pouvez mettre à jour votre fichier hôte Windows en procédant comme suit :
        
        a. Ouvrez un éditeur de texte en tant qu’administrateur.  
        b. Ouvrez le fichier *hosts* situé dans *C:\Windows\System32\drivers\etc\\* .  
        c. Ajoutez au fichier le nom d’hôte et le protocole Internet version 4 (IPv4) du nom de l’appareil de l’API Kubernetes. Ces informations sont disponibles sur le portail Azure Stack Edge, sous **Appareils**.  
        d. Enregistrez et fermez le fichier.

### <a name="deploy-video-analyzer-edge-modules-by-using-the-azure-portal"></a>Déployer des modules périphériques Video Analyzer à l’aide du portail Azure

Le portail Azure vous permet de créer un manifeste de déploiement et d’envoyer le déploiement vers un appareil IoT Edge.  

#### <a name="select-your-device-and-set-modules"></a>Sélectionner votre appareil et définir des modules

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com/), puis accédez à votre hub IoT.
1. Dans le volet gauche, sélectionnez **IoT Edge**.
1. Dans la liste des appareils, sélectionnez l’ID de l’appareil cible.
1. Sélectionnez **Définir modules**.

#### <a name="configure-a-deployment-manifest"></a>Configurer un manifeste de déploiement

Un manifeste de déploiement est un document JSON qui décrit les modules à déployer, le flux des données entre les modules et les propriétés souhaitées des jumeaux de module. Le portail Azure comprend un Assistant qui vous guide lors de la création du manifeste de déploiement. Ses trois étapes sont organisées sous les onglets **Modules**, **Itinéraires** et **Vérifier + créer**.

#### <a name="add-modules"></a>Ajouter des modules

1. Dans la section **Modules IoT Edge**, cliquez sur la liste déroulante **Ajouter**, puis sélectionnez **Module IoT Edge** pour afficher la page **Ajouter un module IoT Edge**.
1. Sélectionnez l’onglet **Paramètres du module**, fournissez un nom pour le module, puis spécifiez l’URI de l’image conteneur. Des exemples de valeurs sont affichés dans l'image suivante :     
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/add-module.png" alt-text="Capture d’écran du volet Paramètres du module sur la page Ajouter un module IoT Edge.":::
    
    > [!TIP]
    > Ne sélectionnez pas **Ajouter** tant que vous n’avez pas spécifié de valeur sous les onglets **Paramètres du module**, **Options de création de conteneur** et **Paramètres du jumeau de module**, comme décrit dans cette procédure.
    
    > [!IMPORTANT]
    > Les valeurs Azure IoT Edge respectent la casse lorsque vous appelez des modules. Notez la chaîne exacte que vous utilisez comme nom de module.
1. Sélectionnez l’onglet **Variables d’environnement**, puis entrez les valeurs, comme illustré dans l’image suivante :
   
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/environment-variables.png" alt-text="Capture d’écran du volet « Variables d’environnement » sur la page « Ajouter un module IoT Edge ».":::
1. Sélectionnez l’onglet **Options de création de conteneur**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/container-create-options.png" alt-text="Capture d’écran du volet Options de création de conteneur sur la page Ajouter un module IoT Edge.":::
 
    Dans la zone du volet **Options de création de conteneur**, collez le code JSON suivant. Cette action limite la taille des fichiers journaux générés par le module.
    
    ```    
    {
        "HostConfig": {
            "LogConfig": {
                "Type": "",
                "Config": {
                    "max-size": "10m",
                    "max-file": "10"
                }
            },
            "Binds": [
               "/var/lib/videoanalyzer/:/var/lib/videoanalyzer",
               "/var/media:/var/media"
            ],
            "IpcMode": "host",
            "ShmSize": 1536870912
        }
    }
    ````
   
   La section « Lier » du code JSON comporte deux entrées :
   * **"/var/lib/videoanalyzer:/var/lib/videoanalyzer"** permet de lier les données de configuration d’application persistantes du conteneur et de les stocker sur le périphérique.
   * **"/var/media:/var/"** lie les dossiers multimédias entre le périphérique et le conteneur. Elle permet de stocker les enregistrements vidéo quand vous exécutez une topologie de pipeline qui prend en charge le stockage des clips vidéo sur le périphérique.
   
1. Sélectionnez l’onglet **Paramètres du jumeau de module**.
 
   Pour s’exécuter, le module périphérique Video Analyzer nécessite un ensemble de propriétés de jumeau obligatoires, comme indiqué dans [Schéma de configuration du jumeau de module](module-twin-configuration-schema.md). 
1. Dans la zone du volet **Paramètres du jumeau de module**, collez le code JSON suivant :    
    ```
    {
        "applicationDataDirectory": "/var/lib/videoanalyzer",
        "ProvisioningToken": "{provisioning-token}",
        ...
    }
    ```
   
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/twin-settings.png" alt-text="Capture d’écran du volet « Paramètres du jumeau de module » sur la page « Ajouter un module IoT Edge ».":::   

    Pour faciliter la surveillance du module, vous pouvez ajouter les propriétés *recommandées* suivantes au code JSON. Pour plus d’informations, consultez [Surveillance et enregistrement](monitor-log-edge.md).
    
    ```
    "diagnosticsEventsOutputName": "diagnostics",
    "OperationalEventsOutputName": "operational",
    "logLevel": "Information",
    "logCategories": "Application,Events",
    "allowUnsecuredEndpoints": true,
    "telemetryOptOut": false
    ```
1. Sélectionnez **Ajouter**.  

#### <a name="add-the-real-time-streaming-protocol-rtsp-simulator-edge-module"></a>Ajoutez le module périphérique de simulateur RTSP (Real-Time Streaming Protocol)

1. Dans la section **Modules IoT Edge**, cliquez sur la liste déroulante **Ajouter**, puis sélectionnez **Module IoT Edge** pour afficher la page **Ajouter un module IoT Edge**.
1. Sélectionnez l’onglet **Paramètres du module**, fournissez un nom pour le module, puis spécifiez l’URI de l’image conteneur. Par exemple :   
    
    * **Nom du module IoT Edge** : rtspsim  
    * **URI d’image** : mcr.microsoft.com/ava-utilities/rtspsim-live555:1.2 

1. Sélectionnez l’onglet **Options de création de conteneur**, puis, dans la zone, collez le code JSON suivant :
    
    ```
    {
        "HostConfig": {
            "Binds": [
               "/home/localedgeuser/samples/input/:/live/mediaServer/media/"
            ],
            "PortBindings": {
                    "554/tcp": [
                        {
                        "HostPort": "554"
                        }
                    ]
            }
        }
    }
    ```
1. Sélectionnez **Ajouter**.  
1. Sélectionnez **Suivant : Itinéraires** pour passer à la section Itinéraires. 
1. Pour spécifier des itinéraires, sous **Nom**, entrez **AVAToHub**, puis, sous **Valeur**, entrez **FROM/messages/modules/avaedge/outputs/ INTO $upstream**.
1. Sélectionnez **Suivant : Vérifier + créer** pour passer à la section Vérifier.
1. Vérifiez les informations de votre déploiement, puis sélectionnez **Créer** pour déployer le module.

#### <a name="generate-the-provisioning-token"></a>Générer le jeton de provisionnement

1. Dans le portail Azure, accédez à Video Analyzer.
1. Dans le volet gauche, sélectionnez **Modules périphériques**.
1. Sélectionnez le module périphérique, puis sélectionnez **Générer un jeton**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/generate-provisioning-token.png" alt-text="Capture d’écran du volet « Ajouter des modules périphériques » pour la génération d’un jeton." lightbox="./media/deploy-on-stack-edge/generate-provisioning-token.png":::
1. Copiez le jeton d’approvisionnement, comme indiqué dans l’image suivante :

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/copy-provisioning-token.png" alt-text="Capture d’écran de la page « Copier le jeton d’approvisionnement ».":::



#### <a name="optional-set-up-docker-volume-mounts"></a>(Facultatif) Configurer les montages de volume Docker

Si vous souhaitez afficher les données dans les répertoires de travail, configurez les montages de volume Docker avant de le déployer. 

Cette section décrit les procédures de création d’un utilisateur de passerelle et de configuration de partages de fichiers pour voir le contenu du répertoire de travail Video Analyzer et du dossier multimédia Video Analyzer.

> [!NOTE]
> Les montages de liaison sont pris en charge, mais les montages de volume permettent d’afficher les données et, le cas échéant, de les copier à distance. Il est possible d’utiliser à la fois des montages de liaison et de volume, mais ils ne peuvent pas pointer vers le même chemin d’accès de conteneur.

1. Dans le portail Azure, accédez à la ressource Azure Stack Edge.
1. Pour créer un utilisateur de passerelle qui peut accéder aux partages, procédez comme suit :
    
    a. Dans le volet gauche, sélectionnez **Passerelle de stockage cloud**.  
    b. Dans le volet gauche, sélectionnez **Utilisateurs**.  
    c. Sélectionnez **Ajouter un utilisateur** pour définir le nom d’utilisateur (par exemple, nous vous recommandons *avauser*) et le mot de passe.  
    d. Sélectionnez **Ajouter**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/add-user.png" alt-text="Capture d’écran de la page « Ajouter un utilisateur » de la ressource Azure Stack Edge.":::
1. Pour créer un *partage local* pour la persistance de Video Analyzer, procédez comme suit :

    a. Sélectionnez **Passerelle de stockage cloud** > **Partages**.  
    b. Sélectionnez **Ajouter un partage**.  
    c. Définissez un nom de partage (par exemple, nous vous recommandons *ava*).  
    d. Conservez le type de partage **SMB**.  
    e. Assurez-vous que la case **Utiliser le partage avec le calcul Edge** est cochée.  
    f. Assurez-vous que la case **Configurer en tant que partage local Edge** est cochée.  
    g. Sous **Détails de l’utilisateur**, autorisez l’accès au partage à l’utilisateur que vous venez de créer en sélectionnant **Utilisez l’existant**.  
    h. Sélectionnez **Create** (Créer).
            
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/local-share.png" alt-text="Capture d’écran de la page « Ajouter un partage » pour la création d’un partage local.":::  
    
    > [!TIP]
    > Une fois votre client Windows connecté à votre appareil Azure Stack Edge, suivez les instructions de la section « Se connecter à un partage SMB » de l’article [Transférer des données avec Azure Stack Edge Pro FPGA](../../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share).    
1. Pour créer un *partage distant* pour le stockage File Sync, procédez comme suit :

    a. Commencez par créer un compte Stockage Blob Azure dans la même région en sélectionnant **Passerelle de stockage cloud** > **Comptes de stockage**.  
    b. Sélectionnez **Passerelle de stockage cloud** > **Partages**.  
    c. Sélectionnez **Ajouter des partages**.  
    d. Dans la zone **Nom**, entrez un nom de partage (par exemple, nous vous recommandons *média*).  
    e. Pour **Type**, conservez le type de partage **SMB**.  
    f. Assurez-vous que la case **Utiliser le partage avec le calcul Edge** est cochée.  
    g. Assurez-vous que la case **Configurer en tant que partage local Edge** est décochée.  
    h. Dans la liste déroulante **Compte de stockage**, sélectionnez le compte de stockage que vous venez de créer.  
    i. Dans la liste déroulante **Service de stockage**, sélectionnez **Objet blob de blocs**.  
    j. Dans la zone **Sélectionner le conteneur blob**, entrez le nom du conteneur.  
    k. Sous **Détails de l’utilisateur**, sélectionnez **Utilisez l’existant** pour autoriser l’accès au partage à l’utilisateur que vous venez de créer.  
    l. Sélectionnez **Create** (Créer).    
        
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/remote-share.png" alt-text="Capture d’écran de la page « Ajouter un partage » pour la création d’un partage distant.":::

1. Pour utiliser des montages de volume, mettez à jour les paramètres dans le volet **Options de création de conteneur** pour le module de simulateur RTSP en procédant comme suit :

    a. Sélectionnez le bouton **Définir des modules**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/set-modules.png" alt-text="Capture d’écran montrant le bouton « Définir les modules » dans le volet Paramètres du périphérique." lightbox="./media/deploy-on-stack-edge/set-modules.png":::  

    b. Dans la liste **Nom**, sélectionnez le module **rtspsim** :

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/select-module.png" alt-text="Capture d’écran du module « rtspsim » sous « Modules IoT Edge » dans le volet Paramètres du périphérique.":::  
    
    c. Dans le volet **Mettre à jour le module IoT Edge**, sélectionnez l’onglet **Options de création de conteneur**, puis ajoutez les montages comme indiqué dans le code JSON suivant :
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/update-module.png" alt-text="Capture d’écran du code de montage JSON dans le volet « Options de création de conteneur ».":::

    ```json
        "createOptions": 
        {
            "HostConfig": 
            {
                "Mounts": 
                [
                    {
                        "Target": "/live/mediaServer/media",
                        "Source": "media",
                        "Type": "volume"
                    }
                ],
                "PortBindings": {
                    "554/tcp": [
                        {
                        "HostPort": "554"
                        }
                    ]
                }
            }
        }
    ```  
    d. Sélectionnez **Mettre à jour**.  
    e. Pour mettre à jour le module, sélectionnez **Vérifier et créer**, puis sélectionnez **Créer**.
    
### <a name="verify-that-the-module-is-running"></a>Vérifiez que le module soit en cours d’exécution

Enfin, assurez-vous que votre module d’appareil IoT Edge est connecté et fonctionne comme prévu. Pour consulter l’état du runtime du module, procédez comme suit :

1. Dans le portail Azure, revenez à votre ressource Azure Stack Edge.
1. Dans le volet gauche, sélectionnez **Modules**. 
1. Dans le volet **Modules**, dans la liste **Nom**, sélectionnez le module que vous avez déployé. Dans la colonne **État du runtime**, l’état du module doit être *en cours d’exécution*.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/running-module.png" alt-text="Capture d’écran du volet « Module », qui indique l’état du runtime du module sélectionné « en cours d’exécution »." lightbox="./media/deploy-on-stack-edge/running-module.png":::

### <a name="configure-the-azure-iot-tools-extension"></a>Configurer l’extension Azure IoT Tools

Pour vous connecter à votre hub IoT à l’aide de l’extension Azure IoT Tools, procédez comme suit :

1. Dans Visual Studio Code, sélectionnez **Afficher** > **Explorateur**.
1. Dans le volet **Explorateur**, en bas à gauche, sélectionnez **Azure IoT Hub**.
1. Sélectionnez l’icône **Plus d’options** pour afficher le menu contextuel, puis sélectionnez **Définir la chaîne de connexion IoT Hub**.

   Une zone d’entrée s’affiche, dans laquelle vous allez entrer votre chaîne de connexion IoT Hub. Pour récupérer votre chaîne de connexion, procédez comme suit : 

   a. Accédez à votre IoT Hub dans le Portail Azure.  
   b. Dans le volet gauche, sélectionnez **Stratégies d’accès partagé**.  
   c. Sélectionnez **iothubowner pour obtenir les clés d’accès partagé**.  
   d. Copiez la clé primaire de la chaîne de connexion, puis collez-la dans la zone d’entrée.

   > [!NOTE]
   > La chaîne de connexion est écrite au format suivant :
   >
   > `HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
    
   Lorsque la connexion est établie, une liste des périphériques s’affiche, notamment votre appareil Azure Stack Edge. Vous pouvez désormais gérer vos appareils IoT Edge et interagir avec votre Azure IoT Hub par le biais du menu contextuel. 
   
   Pour afficher les modules déployés sur le périphérique, sous l’appareil Azure Stack, développez le nœud **Modules**.
    
## <a name="troubleshooting"></a>Dépannage

* **Accès à l’API Kubernetes (kubectl)**

    * Configurez votre ordinateur pour l’accès au cluster Kubernetes en suivant les instructions figurant dans [Créer et gérer un cluster Kubernetes sur un appareil GPU Azure Stack Edge Pro](../../../databox-online/azure-stack-edge-gpu-create-kubernetes-cluster.md).
    * Tous les modules IoT Edge déployés utilisent l’espace de noms *iotedge*. Veillez à inclure ce nom lorsque vous utilisez kubectl. 
* **Journaux de module**

    Si l’outil *iotedge* est inaccessible pour obtenir des journaux, utilisez les [journaux kubectl](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs) pour afficher les journaux ou le canal dans un fichier. Par exemple : <br/>  `kubectl logs deployments/mediaedge -n iotedge --all-containers`  
* **Métriques nœud et pod**

    Pour voir les métriques nœud et pod, utilisez [kubectl top](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top). Par exemple :   <br/>`kubectl top pods -n iotedge` 
* **Mise en réseau des modules**   

    Pour la découverte de module sur Azure Stack Edge, le module doit disposer de la liaison avec le port hôte dans createOptions. Le module sera ensuite adressable sur `moduleName:hostport`.
    
    ```json
    "createOptions": {
        "HostConfig": {
            "PortBindings": {
                "8554/tcp": [ { "HostPort": "8554" } ]
            }
        }
    }
    ```    
* **Montages de volume**

    Un module ne peut pas démarrer si le conteneur tente de monter un volume dans un répertoire existant qui n’est pas vide.
* **Mémoire partagée lors de l’utilisation de gRPC**

    La fonction de mémoire partagée sur les ressources Azure Stack Edge est prise en charge sur les pods dans tous les espaces de noms lorsque vous utilisez l’hôte IPC.
    
    Configurez la mémoire partagée sur un module périphérique pour le déploiement via IoT Hub à l’aide du code suivant :
    
    ```
    ...
    "createOptions": {
        "HostConfig": {
            "IpcMode": "host"
        }
    ...
        
    //(Advanced) Configuring shared memory on a Kubernetes pod or deployment manifest for deployment via the Kubernetes API spec:
        ...
        template:
        spec:
            hostIPC: true
        ...
    ```
* **(Avancé) Colocalisation de pods**

    Lorsque vous utilisez Kubernetes pour déployer des solutions d’inférence personnalisées qui communiquent avec Video Analyzer par le biais de gRPC, veillez à ce que les pods soient déployés sur les mêmes nœuds que les modules Video Analyzer.

    * **Option 1** : Utiliser l’*affinité de nœud* et les étiquettes de nœud intégrées pour la colocalisation.  

    Actuellement, la configuration personnalisée NodeSelector ne semble pas être possible, car les utilisateurs ne disposent pas des accès nécessaires pour définir des étiquettes sur les nœuds. Toutefois, en fonction de la topologie des utilisateurs et des conventions d’affectation de noms, ils peuvent être en mesure d’utiliser des [étiquettes de nœud intégrées](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels). Pour réussir la colocalisation, vous pouvez ajouter au manifeste de pod d’inférence une section nodeAffinity référençant des ressources Azure Stack Edge avec Video Analyzer.    
    * **Option 2** : (Recommandé) Utiliser l’*affinité de pod* pour la colocalisation.  

        Kubernetes prend en charge l’[affinité de pod](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity), qui peut planifier des pods sur le même nœud. Pour réussir la colocalisation, vous pouvez ajouter au manifeste de pod d’inférence une section podAffinity référençant le module Video Analyzer.

        ```yaml
        // Example Video Analyzer module deployment match labels
        selector:
          matchLabels:
            net.azure-devices.edge.deviceid: dev-ase-1-edge
            net.azure-devices.edge.module: mediaedge
        
        // Example inference deployment manifest pod affinity
        spec:
          affinity:
            podAntiAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
              - labelSelector:
                  matchExpressions:
                  - key: net.azure-devices.edge.module
                    operator: In
                    values:
                    - mediaedge
                topologyKey: "kubernetes.io/hostname"
        ```
* **Vous recevez un code d’erreur 404 lorsque vous utilisez le module *rtspsim***  
    
    Le conteneur lit les vidéos à partir d’un seul dossier au sein du conteneur. Si vous mappez/liez un dossier externe à un dossier qui existe déjà dans l’image conteneur, Docker masque les fichiers présents dans l’image conteneur.
 
    Par exemple, sans liaison, le conteneur peut avoir les fichiers suivants :  

    ```
    root@rtspsim# ls /live/mediaServer/media  
    /live/mediaServer/media/camera-300s.mkv  
    /live/mediaServer/media/win10.mkv  
    ```
     
    Et votre hôte peut avoir les fichiers suivants :

    ```    
    C:\MyTestVideos> dir
    Test1.mkv
    Test2.mkv
    ```
     
    Toutefois, lorsque la liaison suivante est ajoutée au fichier manifeste de déploiement, Docker remplace le contenu de /live/mediaServer/media afin qu’il corresponde à ce qui se trouve sur l’hôte.

    `C:\MyTestVideos:/live/mediaServer/media`
    
    ```
    root@rtspsim# ls /live/mediaServer/media
    /live/mediaServer/media/Test1.mkv
    /live/mediaServer/media/Test2.mkv
    ```

## <a name="next-steps"></a>Étapes suivantes

Analyser des vidéos avec Vision par ordinateur et Analyse spatiale [en utilisant Azure Stack Edge](computer-vision-for-spatial-analysis.md)
