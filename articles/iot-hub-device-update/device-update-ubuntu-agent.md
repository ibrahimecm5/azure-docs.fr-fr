---
title: Tutoriel Device Update pour Azure IoT Hub avec l’agent de package Ubuntu Server 18.04 x64 | Microsoft Docs
description: Commencez à utiliser Device Update pour Azure IoT Hub avec l’agent de package Ubuntu Server 18.04 x64.
author: vimeht
ms.author: vimeht
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 1cd4ea0d01299b8e361e65a1f8152c4222df3bf0
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372167"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-package-agent-on-ubuntu-server-1804-x64"></a>Tutoriel Device Update pour Azure IoT Hub avec l’agent de package sur Ubuntu Server 18.04 x64

Device Update pour IoT Hub prend en charge deux formes de mise à jour : l’une basée sur une image et l’autre sur un package.

Les mises à jour basées sur un package sont des mises à jour ciblées qui modifient uniquement un composant ou une application spécifique sur l’appareil. Elles réduisent la consommation de bande passante et permettent de réduire le temps de téléchargement et d’installation des mises à jour. Les mises à jour basées sur un package permettent généralement de réduire les temps d’arrêt des appareils lors de l’application d’une mise à jour et d’éviter une surcharge liée à la création d’images. Elles utilisent un [manifeste apt](device-update-apt-manifest.md) qui fournit à l’agent Device Update les informations dont il a besoin pour télécharger et installer les packages spécifiés dans le fichier manifeste APT (ainsi que leurs dépendances) à partir d’un dépôt désigné.

Ce tutoriel de bout en bout vous guide tout au long de la mise à jour d’Azure IoT Edge sur Ubuntu Server 18.04 x64 à l’aide de l’agent de package Device Update. Ce tutoriel décrit la mise à jour d’IoT Edge, mais vous pouvez procéder de manière similaire pour mettre à jour d’autres packages, comme le moteur de conteneur qu’il utilise.

Les outils et les concepts de ce tutoriel s’appliquent toujours, même si vous envisagez d’utiliser une autre configuration de plateforme de système d’exploitation. Suivez cette présentation d’un processus de mise à jour de bout en bout, puis choisissez votre forme préférée de mise à jour et votre plateforme de système d’exploitation, avant d’entrer dans les détails.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :
> [!div class="checklist"]
> * Télécharger et installer l’agent Device Update et ses dépendances
> * Ajouter une étiquette à votre appareil
> * Importer une mise à jour
> * Créer un groupe d’appareils
> * Déployer une mise à jour de package
> * Surveiller le déploiement de la mise à jour

## <a name="prerequisites"></a>Prérequis

* Si vous ne l’avez pas déjà fait, créez un [compte et une instance Device Update](create-device-update-account.md), ce qui inclut la configuration d’un hub IoT.
* La [chaîne de connexion pour un appareil IoT Edge](../iot-edge/how-to-provision-single-device-linux-symmetric.md?view=iotedge-2020-11&preserve-view=true#view-registered-devices-and-retrieve-provisioning-information).

## <a name="prepare-a-device"></a>Préparer un appareil
### <a name="using-the-automated-deploy-to-azure-button"></a>Utilisation du bouton de déploiement automatique sur Azure

Pour des raisons pratiques, ce tutoriel utilise un [modèle Azure Resource Manager](../azure-resource-manager/templates/overview.md) basé sur [cloud-init](../virtual-machines/linux/using-cloud-init.md) pour vous aider à configurer rapidement une machine virtuelle Ubuntu 18.04 LTS. Il installe à la fois le runtime Azure IoT Edge et l’agent de package Device Update, puis configure automatiquement l’appareil avec des informations de provisionnement en utilisant la chaîne de connexion d’appareil pour un appareil IoT Edge (prérequis) que vous fournissez. Le modèle Azure Resource Manager évite également d’avoir à démarrer une session SSH pour effectuer l’installation.

1. Pour commencer, cliquez sur le bouton ci-dessous :

   [![Bouton déployer sur Azure pour iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fdevice-update-tutorial%2FedgeDeploy.json)

1. Dans la fenêtre qui vient de s’ouvrir, renseignez les champs de formulaire disponibles :

    > [!div class="mx-imgBorder"]
    > [![Capture d’écran montrant le modèle iotedge-vm-deploy](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Abonnement**: Abonnement Azure actif dans lequel déployer la machine virtuelle.

    **Groupe de ressources** : Groupe de ressources existant ou nouvellement créé pour contenir la machine virtuelle et ses ressources associées.

    **Préfixe d’étiquette DNS** : Valeur obligatoire de votre choix, utilisée pour préfixer le nom d’hôte de la machine virtuelle.

    **Nom d’utilisateur administrateur** : Nom de l’utilisateur qui sera doté de privilèges root sur le déploiement.

    **Chaîne de connexion de l’appareil** : [Chaîne de connexion d’appareil](../iot-edge/how-to-provision-single-device-linux-symmetric.md#view-registered-devices-and-retrieve-provisioning-information) pour un appareil créé dans votre [IoT Hub](../iot-hub/about-iot-hub.md) prévu.

    **Taille de la machine virtuelle** : [Taille](../cloud-services/cloud-services-sizes-specs.md) de la machine virtuelle à déployer

    **Version du système d’exploitation Ubuntu**  : Version du système d’exploitation Ubuntu à installer sur la machine virtuelle de base. Laissez la valeur par défaut inchangée, car elle est déjà définie sur Ubuntu 18.04-LTS.

    **Emplacement** : [Région géographique](https://azure.microsoft.com/global-infrastructure/locations/) dans laquelle déployer la machine virtuelle. Par défaut, il s’agit de l’emplacement du groupe de ressources sélectionné.

    **Type d’authentification** : Choisissez **sshPublicKey** ou **mot de passe** selon votre préférence.

    **Mot de passe ou clé d’administrateur** : Valeur de la clé publique SSH ou du mot de passe en fonction du choix du type d’authentification.

    Lorsque tous les champs sont renseignés, activez la case à cocher en bas de la page pour accepter les conditions d’utilisation, puis sélectionnez **Achat** pour commencer le déploiement.

1. Vérifiez que le déploiement a complètement abouti. Patientez quelques minutes après la fin du déploiement, le temps que la post-installation et la configuration terminent l’installation d’IoT Edge et de l’agent de package Device Update.

   Une ressource de machine virtuelle doit avoir été déployée dans le groupe de ressources sélectionné.  Notez le nom de la machine, qui doit être au format `vm-0000000000000`. Notez également le **Nom DNS** associé, qui doit être au format `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com.

    Vous pouvez trouver le **Nom DNS** dans la section **Vue d’ensemble** de la machine virtuelle nouvellement déployée dans le portail Azure.

    > [!div class="mx-imgBorder"]
    > [![Capture d’écran montrant le nom dns de la machine virtuelle iotedge](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

   > [!TIP]
   > Si vous souhaitez établir une connexion SSH vers cette machine virtuelle après la configuration, utilisez le **Nom DNS** associé avec la commande : `ssh <adminUsername>@<DNS_Name>`

### <a name="optional-manually-prepare-a-device"></a>(Facultatif) Préparer manuellement un appareil
Comme pour les étapes automatisées par le [script cloud-init](https://github.com/Azure/iotedge-vm-deploy/blob/1.2.0-rc4/cloud-init.txt), les étapes manuelles permettant d’installer et de configurer l’appareil sont les suivantes. Ces étapes peuvent être utilisées pour préparer un appareil physique.

1. Suivez les instructions indiquant comment [installer le runtime Azure IoT Edge](../iot-edge/how-to-provision-single-device-linux-symmetric.md?view=iotedge-2020-11&preserve-view=true).
   > [!NOTE]
   > L’agent de package Device Update ne dépend pas d’IoT Edge. Toutefois, il s’appuie sur le démon de service d’identité IoT installé avec IoT Edge (versions 1.2.0 et ultérieures) pour obtenir une identité et se connecter à IoT Hub.
   >
   > Bien que ce point ne soit pas abordé dans ce tutoriel, le [démon de service d’identité IoT peut être installé en mode autonome sur des appareils IoT basés sur Linux](https://azure.github.io/iot-identity-service/installation.html). La séquence d’installation est importante. L’agent de package Device Update doit être installé _après_ le service d’identité IoT. Sinon, l’agent de package n’est pas inscrit en tant que composant autorisé pour établir une connexion à IoT Hub.

1. Ensuite, installez les packages .deb de l’agent Device Update.

   ```bash
   sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt 
   ```

Les packages logiciels de Device Update pour Azure IoT Hub sont soumis aux termes de contrat de licence suivants :
  * [Licence Device Update pour IoT Hub](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
  * [Licence cliente d’optimisation de la distribution](https://github.com/microsoft/do-client/blob/main/LICENSE)

Lisez les termes du contrat de licence avant d’utiliser un package. Le fait d’installer et d’utiliser un package revient à accepter ces termes. Si vous n’acceptez pas les termes du contrat de licence, n’utilisez pas le package en question.

## <a name="add-a-tag-to-your-device"></a>Ajouter une étiquette à votre appareil

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez au hub IoT.

2. Sous « IoT Edge » dans le volet de navigation de gauche, recherchez votre appareil IoT Edge, puis accédez au jumeau d’appareil ou au jumeau de module.

3. Dans le jumeau de module du module d’agent Device Update, supprimez toutes les valeurs d’étiquette Device Update existantes en leur affectant la valeur null. Si vous utilisez l’identité d’appareil avec l’agent Device Update, effectuez ces changements sur le jumeau d’appareil.

4. Ajoutez une nouvelle valeur d’étiquette Device Update comme indiqué ci-dessous.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            },
```

## <a name="import-update"></a>Importer la mise à jour

1. Accédez à [Device Update releases](https://github.com/Azure/iot-hub-device-update/releases) dans GitHub, puis cliquez sur la liste déroulante « Assets ».

3. Téléchargez le fichier `Edge.package.update.samples.zip` en cliquant dessus.

5. Extrayez le contenu du dossier pour découvrir un exemple de [manifeste APT](device-update-apt-manifest.md) et son [manifeste d’importation](import-concepts.md) correspondant. 

2. Dans le portail Azure, sélectionnez l’option Mises à jour de l’appareil sous Gestion automatique des appareils dans la barre de navigation de gauche de votre hub IoT.

3. Sélectionnez l’onglet Mises à jour.

4. Sélectionnez « + Importer une nouvelle mise à jour ».

5. Sélectionnez l’icône de dossier ou la zone de texte sous « Sélectionner un fichier manifeste d’importation ». Vous verrez une boîte de dialogue de sélection de fichiers. Sélectionnez le manifeste d’importation `sample-1.0.1-aziot-edge-importManifest.json` à partir du dossier que vous avez téléchargé. Ensuite, sélectionnez l’icône de dossier ou la zone de texte sous « Sélectionner un ou plusieurs fichiers de mise à jour ». Vous verrez une boîte de dialogue de sélection de fichiers. Sélectionnez le fichier de mise à jour de manifeste apt `sample-1.0.1-aziot-edge-apt-manifest.json` à partir du dossier que vous avez téléchargé.
Cette mise à jour met à jour les packages `aziot-identity-service` et `aziot-edge` vers la version 1.2.0~rc4-1 sur votre appareil.

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Capture d’écran illustrant la sélection du fichier de mise à jour." lightbox="media/import-update/select-update-files.png":::

6. Sélectionnez l’icône de dossier ou la zone de texte sous « Sélectionner un conteneur de stockage ». Sélectionnez ensuite le compte de stockage approprié.

7. Si vous avez déjà créé un conteneur, vous pouvez le réutiliser. (Dans le cas contraire, sélectionnez « + Conteneur » pour créer un conteneur de stockage pour les mises à jour.)  Sélectionnez le conteneur à utiliser et cliquez sur « Sélectionner ».

   :::image type="content" source="media/import-update/container.png" alt-text="Capture d’écran illustrant la sélection du conteneur." lightbox="media/import-update/container.png":::

8. Sélectionnez « Soumettre » pour démarrer le processus d’importation.

9. Le processus d’importation commence et l’écran passe à la section « Historique d’importation ». Sélectionnez « Actualiser » pour voir la progression jusqu’à la fin du processus d’importation. Selon la taille de la mise à jour, le processus d’importation peut prendre quelques minutes ou durer plus longtemps.

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Capture d’écran illustrant la séquence d’importation de mise à jour." lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Quand la colonne État indique que l’importation a réussi, sélectionnez l’en-tête « Prêt pour le déploiement ». Vous devez maintenant voir votre mise à jour importée dans la liste.

[Apprenez-en davantage](import-update.md) sur l’importation de mises à jour.

## <a name="create-update-group"></a>Créer un groupe de mises à jour

1. Accédez au hub IoT que vous avez précédemment connecté à votre instance Device Update.

1. Sélectionnez l’option Mises à jour de l’appareil sous Gestion automatique des appareils dans la barre de navigation de gauche.

1. Sélectionnez l’onglet Groupes en haut de la page.

1. Sélectionnez le bouton Ajouter pour créer un groupe.

1. Sélectionnez l’étiquette IoT Hub que vous avez créée à l’étape précédente à partir de la liste. Sélectionnez Créer un groupe de mises à jour.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Capture d’écran illustrant la sélection d’une étiquette" lightbox="media/create-update-group/select-tag.PNG":::

[En savoir plus](create-update-group.md) sur l’ajout d’étiquettes et la création de groupes de mises à jour

## <a name="deploy-update"></a>Déployer la mise à jour

1. Une fois le groupe créé, vous devriez voir une nouvelle mise à jour disponible pour votre groupe d’appareils, avec un lien vers cette mise à jour dans la colonne _Mises à jour disponibles_. Vous devrez peut-être actualiser une fois.

1. Cliquez sur le lien vers la mise à jour disponible.

1. Vérifiez que le groupe approprié est sélectionné comme groupe cible et planifiez votre déploiement.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Sélectionner la mise à jour" lightbox="media/deploy-update/select-update.png":::

   > [!TIP]
   > Par défaut, la date/heure de début est 24 heures à partir de votre heure actuelle. Veillez à sélectionner une autre date/heure si vous souhaitez que le déploiement commence plus tôt.

1. Sélectionnez Déployer la mise à jour.

1. Visualisez le graphique de conformité. Vous devez voir que la mise à jour est maintenant en cours. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Mise à jour en cours" lightbox="media/deploy-update/update-in-progress.png":::

1. Une fois votre appareil correctement mis à jour, vous devez voir votre graphique de conformité et les détails du déploiement refléter la même chose. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Mise à jour réussie" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Superviser le déploiement d’une mise à jour

1. Sélectionnez l’onglet Déploiements en haut de la page.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Onglet Déploiements" lightbox="media/deploy-update/deployments-tab.png":::

1. Sélectionnez le déploiement que vous avez créé pour en examiner les détails.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Détails du déploiement" lightbox="media/deploy-update/deployment-details.png":::

1. Sélectionnez Actualiser pour voir les détails d’état les plus récents. Poursuivez ce processus jusqu’à ce que l’état passe à Réussi.

Vous avez maintenant réussi une mise à jour de package de bout en bout avec Device Update pour IoT Hub sur un appareil Ubuntu Server 18.04 x64. 

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’en avez plus besoin, nettoyez votre compte Device Update, votre instance, votre hub IoT et l’appareil IoT Edge (si vous avez créé la machine virtuelle par le biais du bouton Déployer sur Azure). Pour ce faire, accédez à chaque ressource individuelle et sélectionnez « Supprimer ». Vous devez nettoyer une instance Device Update avant de nettoyer le compte Device Update.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel de mise à jour d’image sur Raspberry Pi 3 B+](device-update-raspberry-pi.md)
