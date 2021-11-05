---
title: Bien démarrer avec les pipelines en direct d’Azure Video Analyzer - Portail Azure
description: Ce guide de démarrage rapide vous montre les différentes étapes nécessaires pour capturer et enregistrer des vidéos à partir d’une caméra RTSP en utilisant des pipelines en direct dans le service Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: quickstart
ms.date: 10/16/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f032eea5c54ac4c0d0200d9063b7f499838f2465
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096753"
---
# <a name="quickstart-get-started-with-video-analyzer-live-pipelines-in-the-azure-portal"></a>Démarrage rapide : Bien démarrer avec les pipelines en direct de Video Analyzer dans le portail Azure

[!INCLUDE [header](includes/cloud-env.md)]

Ce guide de démarrage rapide vous montre les différentes étapes nécessaires pour capturer et enregistrer des vidéos à partir d’une caméra RTSP (Real Time Streaming Protocol) en utilisant des pipelines en direct dans le service Azure Video Analyzer.
Vous créez un compte Video Analyzer et les ressources qui l’accompagnent en utilisant le portail Azure. Vous déployez un simulateur de caméra RTSP, si vous n’avez pas accès à une caméra RTSP réelle (qui peut être rendue accessible sur Internet). Vous déployez ensuite les ressources Video Analyzer pertinentes pour enregistrer la vidéo sur votre compte Video Analyzer.

Les étapes décrites dans ce document s’appliquent aux caméras accessibles sur Internet qui ne sont pas protégées derrière un pare-feu. Le diagramme suivant représente graphiquement le [pipeline](../pipeline.md) en direct que vous déployez sur votre compte Video Analyzer.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/live-pipeline.svg" alt-text="Représentation d’un pipeline en direct sur le cloud":::

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure actif. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/).

  [!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](../includes/note-account-storage-same-subscription.md)]
- Une caméra RTSP accessible sur Internet ou une machine virtuelle Linux Azure (avec des privilèges d’administrateur) pour héberger un simulateur de caméra RTSP

## <a name="sample-architecture---recording-video-from-a-camera-over-the-internet"></a>Exemple d’architecture - Enregistrement de vidéos à partir d’une caméra sur Internet
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/public-camera-to-cloud-live-pipeline-arch.png" alt-text="Diagramme d’un exemple d’architecture de flux vidéo de caméra publique intégrant le pipeline en direct de Video Analyzer qui capture les vidéos sur le cloud.":::

## <a name="rtsp-camera"></a>Caméra RTSP

Vous avez besoin d’un accès à une caméra compatible RTSP. [Consultez les caméras prises en charge](../quotas-limitations.md). La caméra doit être configurée pour encoder la vidéo avec un débit maximal de 3 Mbits/s. Prenez note de ce paramètre de débit maximal. Par ailleurs, le serveur RTSP sur cette caméra doit être accessible sur l’Internet public. Si vous avez la possibilité d’utiliser ce type de caméra, vous pouvez passer à la section Créer des ressources Azure. Vous pouvez aussi déployer un simulateur de caméra RTSP, comme décrit dans la section ci-dessous.

## <a name="deploy-rtsp-camera-simulator"></a>Déployer un simulateur de caméra RTSP

Cette section vous montre comment déployer un simulateur de caméra RTSP sur une machine virtuelle Linux Azure, exécutant le système d’exploitation « Ubuntu Server 18.04 ». Ce simulateur utilise le [serveur multimédia Live555](http://www.live555.com/mediaServer/).

> [!NOTE]
> Les références aux logiciels tiers sont fournies uniquement à titre d’information et de commodité. Microsoft ne garantit pas et ne concède pas de droits sur des logiciels tiers. Pour plus d’informations, consultez [Serveur multimédia Live555](http://www.live555.com/mediaServer/).

> [!WARNING]
> Notez que ce point de terminaison de simulateur de caméra RTSP est exposé sur Internet et donc accessible à toute personne connaissant l’URL RTSP.

**Étapes du déploiement :**
1. Déployez une machine virtuelle Linux Azure de série standard_D2s_v3 exécutant le système d’exploitation « Ubuntu Server 18.04 ». [Cliquez ici](../../../virtual-machines/linux/quick-create-portal.md) pour voir les étapes de création de la machine virtuelle, vous n’avez pas besoin d’installer le serveur web décrit dans l’article associé. Autorisez également le port SSH dans l’Assistant Déploiement pour pouvoir vous connecter à la machine virtuelle avec une connexion SSH.
1. Activez les connexions entrantes pour le protocole RTSP. Dans le portail Azure, ouvrez le volet de gestion de la machine virtuelle Linux que vous avez créée ci-dessus.

    1. Cliquez sur Réseau : le panneau s’ouvre sur les règles de port d’entrée pour le groupe de sécurité réseau (NSG) qui a été créé afin de prendre en charge les connexions SSH entrantes.
    1. Cliquez sur Ajouter une règle de port d’entrée pour en ajouter une
    1. Dans le volet qui s’ouvre, définissez les plages de port de destination sur 554. Choisissez un nom pour la règle, par exemple, « RTSP ». Conservez toutes les autres valeurs par défaut. Consultez [ce document](../../../virtual-machines/windows/nsg-quickstart-portal.md) pour plus d’informations.
1. Installez Docker sur la machine virtuelle en utilisant les instructions [ici](https://docs.docker.com/engine/install/ubuntu/), suivez uniquement les étapes jusqu’à la vérification de l’installation de Docker en exécutant l’image « hello-world ».
1. Connectez-vous à votre machine virtuelle, par exemple, en utilisant SSH. Dans la fenêtre de terminal, créez un dossier local, par exemple, « localmedia », pour héberger les fichiers multimédias. Ce dossier VM local est utilisé pour le mappage au conteneur RTSP du serveur multimédia.
1. Copiez un fichier MKV utilisé pour simuler le flux de la caméra de la façon suivante :

    ```
    cd localmedia
    wget https://lvamedia.blob.core.windows.net/public/camera-1800s.mkv
    ```
1. À partir du répertoire racine, démarrez le serveur RTSP sur la machine virtuelle en utilisant l’image conteneur préconfigurée de la façon suivante

    ```    
    sudo docker run -d -p 554:554 -v ${PWD}/localmedia:/live/mediaServer/media mcr.microsoft.com/ava-utilities/rtspsim-live555:1.2
    ```
1. Une fois le serveur RTSP en cours d’exécution, les clients peuvent s’y connecter via une URL RTSP :

    - Accédez à la page « Vue d’ensemble » de votre machine virtuelle dans le portail Azure et notez la valeur de l’« adresse IP publique »
    
        - L’URL RTSP est rtsp://{adresse IP publique}:554/media/camera-1800s.mkv et peut être testée avec un lecteur de bureau, par exemple, VLC

## <a name="create-azure-resources"></a>Créer des ressources Azure

La prochaine étape consiste à créer les ressources Azure nécessaires (compte Video Analyzer, compte de stockage et identité managée affectée par l’utilisateur).

### <a name="create-a-video-analyzer-account-in-the-azure-portal"></a>Créer un compte Video Analyzer dans le portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Dans la barre de recherche située en haut, entrez **Video Analyzer**.
1. Sélectionnez **Analyseurs vidéo** sous **Services**.
1. Sélectionnez **Ajouter**.
1. Dans la section **Créer un compte Video Analyzer**, entrez les valeurs requises :

   - **Abonnement** : choisissez l’abonnement sous lequel vous souhaitez créer le compte Video Analyzer.
   - **Groupe de ressources** : choisissez un groupe de ressources pour créer le compte Video Analyzer ou sélectionnez **Créer nouveau** pour créer un groupe de ressources.
   - **Nom du compte Video Analyzer** : entrez un nom de votre compte Video Analyzer. Le nom doit se composer de lettres en minuscules ou de chiffres, sans espaces, et comprendre entre 3 et 24 caractères.
   - **Localisation** : choisissez une région pour déployer votre compte Video Analyzer (par exemple **USA Ouest 2**).
   - **compte de stockage** : créez un compte de stockage. Nous vous recommandons de sélectionner un compte de stockage [standard v2 à usage général](../../../storage/common/storage-account-overview.md#types-of-storage-accounts).
   - **Identité de l’utilisateur** : Créez et nommez une nouvelle identité managée affectée par l’utilisateur.
1. Sélectionnez **Vérifier + créer** en bas du formulaire.

### <a name="deploy-a-live-pipeline"></a>Déployer un pipeline en direct

Une fois le compte Video Analyzer créé, vous pouvez accéder aux étapes suivantes pour créer une topologie de pipeline en direct et un pipeline en direct.
1. Accédez au compte Video Analyzer et recherchez l’élément de menu **En direct** en bas à gauche, puis sélectionnez-le. 
1. Dans le plan Topologies, sélectionnez l’option **Créer une topologie** en haut, pour créer une topologie en direct. Suivez les étapes de l’Assistant du portail pour créer une topologie de pipeline en direct

    - L’Assistant **Créer une topologie de pipeline** s’affiche sur le portail
    - Sélectionnez **Essayer des exemples de topologie**-> sélectionnez la topologie **Capture en direct, enregistrement et flux à partir d’une caméra RTSP**-> sélectionnez « Continuer » dans la boîte de dialogue **Charger l’exemple de topologie**.
    - L’Assistant permettant de modifier la topologie de pipeline en direct « source RTSP vers récepteur vidéo » s’affiche.
    - Entrez les champs nécessaires pour créer la topologie : 
    
        - **Nom de la topologie** : entrez le nom de la topologie 
        - **Description** (facultatif) : brève description de la topologie 
        - **Type** (prérempli avec « En direct »)
        - Pour le nœud **Source RTSP** : définissez la propriété **Transport** sur la valeur TCP
        - Sélectionnez **Enregistrer** avec la configuration par défaut pour le reste des propriétés
1. L’étape suivante consiste à créer un pipeline en direct en utilisant la topologie créée à l’étape précédente. 

    - Sélectionnez **Pipelines**-> sélectionnez **Créer un pipeline**-> puis sélectionnez la topologie de pipeline en direct créée à l’étape précédente pour créer un pipeline. Après avoir sélectionné la topologie, cliquez sur **Créer**
    - L’Assistant **Créer un pipeline en direct** s’affiche sur le portail. Renseignez les champs obligatoires : 
    
        - **Nom du pipeline en direct** : utilisez un nom unique, qui autorise les chiffres et les tirets
        - **Débit** : il s’agit de la capacité maximale, en Kbits/s, réservée au pipeline en direct. La plage autorisée va de 500 Kbits/s à 3000 Kbit/s. Utilisez la valeur par défaut 1000 pour le fichier camera-1800s.mkv du simulateur de caméra RTSP (cette valeur doit correspondre à l’exemple de fichier vidéo utilisé). 
        - **rtspUserNameParameter**, **rtspPasswordParameter** : définissez des valeurs factices pour ces champs si vous utilisez un simulateur de caméra RTSP, sinon, entrez les informations d’identification d’authentification du flux de caméra RTSP réel
        - **rtspUrlParameter** : utilisez `rtsp://<VMpublicIP>:554/media/camera-1800s.mkv` (pour le simulateur de caméra RTSP), ou l’URL du flux de caméra RTSP réel
        - **videoNameParameter** : nom unique de la ressource vidéo cible à enregistrer. Remarque : Utilisez une ressource vidéo unique pour chaque caméra (ou fichier MKV)
    - Sélectionnez **Créer**. Vous voyez un pipeline créé dans la grille de pipelines sur le portail.
    - Sélectionnez le pipeline en direct créé dans la grille, sélectionnez l’option **Activer** disponible à droite du volet pour activer le pipeline en direct. Cette action démarre votre pipeline en direct ainsi que l’enregistrement de la vidéo
1. À présent, vous pouvez voir la ressource vidéo sous le compte Video Analyzer-> volet **Vidéos** dans le portail. Son état indique **Est en cours d’utilisation**, car le pipeline est actif et en train d’enregistrer.
1. Au bout de quelques secondes, sélectionnez la vidéo pour voir le [flux à faible latence](../playback-recordings-how-to.md).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/camera-1800s-mkv.png" alt-text="Diagramme de la vidéo enregistrée capturée par le pipeline en direct sur le cloud.":::

    > [!NOTE]
    > Si vous utilisez un simulateur de caméra RTSP, vous ne pouvez pas déterminer avec justesse la latence de bout en bout. Par ailleurs, dès que le simulateur de caméra RTSP atteint la fin du fichier MKV, il s’arrête. Le pipeline en direct tente alors de se reconnecter et, au bout d’un certain temps, le simulateur redémarre le flux à partir du début du fichier. Si vous laissez ce pipeline en direct s’exécuter pendant de nombreuses heures, vous voyez des trous dans l’enregistrement vidéo chaque fois que le simulateur s’arrête et redémarre.
* Si nécessaire, consultez le journal d’activité pour vérifier rapidement vos opérations de déploiement. Accédez [ici](./monitor-log-cloud.md) pour voir les journaux d’événements et de supervision.
* Pour désactiver l’enregistrement du pipeline, accédez à votre compte Video Analyzer. Dans le volet gauche, sélectionnez **En direct**-> **Pipelines**-> sélectionnez le pipeline à désactiver, puis **Désactiver** dans la grille de pipelines pour arrêter l’enregistrement. 
* Vous pouvez également supprimer le pipeline et la topologie s’ils ne sont pas nécessaires.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous voulez essayer d’autres guides de démarrage rapide ou tutoriels, conservez les ressources que vous avez créées. Dans le cas contraire, accédez au portail Azure, à vos groupes de ressources, sélectionnez le groupe de ressources où vous avez exécuté ce guide de démarrage rapide et supprimez toutes les ressources.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la gestion de la [stratégie de conservation](../manage-retention-policy.md) de la vidéo
- Essayez différents exemples de fichiers MKV pour le simulateur multimédia en cliquant [ici](https://github.com/Azure/video-analyzer/tree/main/media). Le débit de l’exemple de fichier doit correspondre à la configuration du pipeline.
- En savoir plus sur la [Supervision et la journalisation des pipelines cloud](./monitor-log-cloud.md).
