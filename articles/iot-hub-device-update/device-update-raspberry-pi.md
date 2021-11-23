---
title: 'Tutoriel sur le service Device Update pour IoT Hub : Utilisation de l’image de référence Yocto Raspberry Pi 3 B+ | Microsoft Docs'
description: Démarrez avec le service Device Update pour IoT Hub en utilisant l’image de référence Yocto Raspberry Pi 3 B+.
author: ValOlson
ms.author: valls
ms.date: 2/11/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: ded90c34a3e43f202c815dc593b3420cf5d88e41
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518942"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-raspberry-pi-3-b-reference-image"></a>Tutoriel sur le service Device Update pour IoT Hub : Utilisation de l’image de référence Raspberry Pi 3 B+

Le service Device Update pour IoT Hub prend en charge deux formes de mise à jour : l’une basée sur une image et l’autre sur un package.

Les mises à jour basées sur une image offrent un niveau de confiance plus élevé dans l’état final de l’appareil. Il est généralement plus facile de répliquer les résultats d’une mise à jour basée sur une image entre un environnement de préproduction et un environnement de production, car cela ne présente pas les mêmes défis que les packages et leurs dépendances. En raison de leur nature atomique, il est également possible d’adopter aisément un modèle de basculement A/B.

Ce tutoriel vous guide tout au long des étapes de mise à jour de bout en bout basée sur une image à l’aide du service Device Update pour IoT Hub sur une carte Raspberry Pi 3 B+. 

Ce didacticiel vous apprendra à effectuer les opérations suivantes :
> [!div class="checklist"]
> * Télécharger une image
> * Ajouter une étiquette à votre appareil IoT
> * Importer une mise à jour
> * Créer un groupe d’appareils
> * Déployer une mise à jour basée sur une image
> * Superviser le déploiement de la mise à jour

Remarque : les mises à jour d’image dans ce tutoriel ont été validées sur la carte Raspberry Pi B3.

## <a name="prerequisites"></a>Prérequis
* Si vous ne l’avez pas déjà fait, créez un [compte et une instance Device Update](create-device-update-account.md), ce qui inclut la configuration d’un hub IoT.

## <a name="download-image"></a>Télécharger une image

Nous fournissons des exemples d’images dans les « ressources » figurant dans la [page des versions GitHub de mise à jour d’appareil](https://github.com/Azure/iot-hub-device-update/releases). Le fichier .gz est l’image de base que vous pouvez flasher sur une carte Raspberry Pi 3 B+ et le fichier swUpdate correspond à la mise à jour que vous importez par le biais de Device Update pour IoT Hub. 

## <a name="flash-sd-card-with-image"></a>Flasher la carte SD avec une image

À l’aide de l’outil de flashage du système d’exploitation de votre choix, installez l’image de base de Device Update (adu-base-image) sur la carte SD qui sera utilisée sur l’appareil Raspberry Pi 3 B+.

### <a name="using-bmaptool-to-flash-sd-card"></a>Utilisation de bmaptool pour flasher la carte SD

1. Si ce n’est déjà fait, installez l’utilitaire `bmaptool`.

   ```shell
   sudo apt-get install bmap-tools
   ```

2. Recherchez le chemin de la carte SD dans `/dev`. Le chemin doit être similaire à `/dev/sd*` ou `/dev/mmcblk*`. Vous pouvez recourir à l’utilitaire `dmesg` pour rechercher le chemin correct.

3. Vous devrez démonter toutes les partitions montées avant l’effectuer le flashage.

   ```shell
   sudo umount /dev/<device>
   ```

4. Veillez à disposer des autorisations d’accès en écriture sur l’appareil.

   ```shell
   sudo chmod a+rw /dev/<device>
   ```

5. facultatif. Pour accélérer le flashage, téléchargez le fichier bimap avec le fichier image et placez-les dans le même répertoire.

6. Flashez la carte SD.

   ```shell
   sudo bmaptool copy <path to image> /dev/<device>
   ```
   
Les logiciels de Device Update pour Azure IoT Hub sont soumis aux termes de contrat de licence suivants :
   * [Licence Device Update pour IoT Hub](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Licence cliente d’optimisation de la distribution](https://github.com/microsoft/do-client/blob/main/LICENSE)
   
Lisez les termes de contrat de licence avant d’utiliser l’agent. Le fait de procéder à l’installation et à l’utilisation revient à accepter ces termes. Si vous n’acceptez pas les termes du contrat de licence, n’utilisez pas l’agent de mise à jour de l’appareil pour IoT Hub.

## <a name="create-device-or-module-in-iot-hub-and-get-connection-string"></a>Créer un appareil ou module dans IoT Hub et récupérer la chaîne de connexion

À présent, l’appareil doit être ajouté à Azure IoT Hub.  Dans Azure IoT Hub, une chaîne de connexion est générée pour l’appareil.

1. À partir du portail Azure, lancez le hub Azure IoT.
2. Créez un appareil.
3. Sur le côté gauche de la page, accédez à « Appareils IoT », puis sélectionnez « Nouveau ».
4. Donnez un nom à l’appareil sous « ID de l’appareil » ; vérifiez que la case « Générer automatiquement les clés » est cochée.
5. Sélectionnez « Enregistrer ».
6. À présent, vous êtes redirigé vers la page « Appareils » et l’appareil que vous avez créé doit figurer dans la liste. 
7. Obtenez la chaîne de connexion de l’appareil :
    - Option 1 utilisant l’agent Device Update avec une identité de module : À partir de la même page « Appareils », cliquez sur « + Ajouter une identité de module » en haut. Créez un module Device Update portant le nom « IoTHubDeviceUpdate », choisissez d’autres options en fonction de votre cas d’utilisation, puis cliquez sur « Enregistrer ». Cliquez sur le « module » nouvellement créé et, dans la vue du module, sélectionnez l’icône « Copier » en regard de « Chaîne de connexion principale ».
    - Option 2 utilisant l’agent Device Update avec l’identité de l’appareil : Dans la vue de l’appareil, sélectionnez l’icône « Copier » en regard de « Chaîne de connexion principale ».
8. Collez les caractères copiés quelque part en vue d’une utilisation ultérieure dans les étapes ci-dessous.
   **Cette chaîne copiée est la chaîne de connexion de votre appareil**.

## <a name="provision-connection-string-on-sd-card"></a>Provisionner la chaîne de connexion sur la carte SD

1. Vérifiez que l’appareil Raspberry Pi3 est connecté au réseau.
2. Dans PowerShell, utilisez la commande ci-dessous pour vous connecter à l’appareil en mode SSH.
   ```markdown
   ssh raspberrypi3 -l root
      ```
4. Entrez l’identifiant « root » et laissez le mot de passe vide.
5. Une fois correctement connecté à l’appareil en mode SSH, exécutez les commandes ci-dessous.
 
Remplacez `<device connection string>` par votre chaîne de connexion.
 ```markdown
    echo "connection_string=<device connection string>" > /adu/adu-conf.txt  
    echo "aduc_manufacturer=ADUTeam" >> /adu/adu-conf.txt
    echo "aduc_model=RefDevice" >> /adu/adu-conf.txt
   ```

## <a name="connect-the-device-in-device-update-iot-hub"></a>Connecter l’appareil dans le hub IoT de Device Update

1. Sur le côté gauche de la page, sélectionnez « Appareils IoT ».
2. Sélectionnez le lien comportant le nom de votre appareil.
3. En haut de la page, sélectionnez « Jumeau d’appareil » si vous vous connectez directement à Device Update à l’aide de l’identité de l’appareil IoT. Dans le cas contraire, sélectionnez le module que vous avez créé ci-dessus, puis cliquez sur son « jumeau de module ».
4. Dans la section « Signalé » des propriétés de jumeau d’appareil, recherchez la version du noyau Linux.
Pour un nouvel appareil qui n’a pas reçu de mise à jour du service Device Update, la valeur [DeviceManagement:DeviceInformation:1.swVersion](device-update-plug-and-play.md) représente la version du microprogramme qui s’exécute sur l’appareil.  Une fois qu’une mise à jour a été appliquée à un appareil, le service Device Update utilise la valeur de la propriété [AzureDeviceUpdateCore:ClientMetadata:4.installedUpdateId](device-update-plug-and-play.md) pour représenter la version du microprogramme qui s’exécute sur l’appareil.
5. Les noms des fichiers image de base et de mise à jour comportent un numéro de version.

   ```markdown
   adu-<image type>-image-<machine>-<version number>.<extension>
   ```

Utilisez ce numéro de version dans l’étape « Importer la mise à jour » ci-dessous.

## <a name="add-a-tag-to-your-device"></a>Ajouter une étiquette à votre appareil

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez au hub IoT.

2. Sous « Appareils IoT » ou « IoT Edge » dans le volet de navigation de gauche, recherchez votre appareil IoT, puis accédez au Jumeau d’appareil ou au Jumeau de module.

3. Dans le jumeau de module du module d’agent Device Update, supprimez toutes les valeurs d’étiquette Device Update existantes en leur affectant la valeur null. Si vous utilisez l’identité d’appareil avec l’agent Device Update, effectuez ces changements sur le jumeau d’appareil.

4. Ajoutez une nouvelle valeur d’étiquette Device Update comme indiqué ci-dessous.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="import-update"></a>Importer la mise à jour

1. Téléchargez l’[exemple de manifeste d’importation](https://github.com/Azure/iot-hub-device-update/releases/download/0.7.0/TutorialImportManifest_Pi.json) et l’[exemple de mise à jour d’image](https://github.com/Azure/iot-hub-device-update/releases/download/0.7.0-rc1/adu-update-image-raspberrypi3-0.6.5073.1.swu).
2. Connectez-vous au [portail Azure](https://portal.azure.com/) et accédez à votre hub IoT avec Device Update. Ensuite, sélectionnez l’option Mises à jour de l’appareil sous Gestion automatique des appareils dans la barre de navigation de gauche.
3. Sélectionnez l’onglet Mises à jour.
4. Sélectionnez « + Importer une nouvelle mise à jour ».
5. Sélectionnez l’icône de dossier ou la zone de texte sous « Sélectionner un fichier manifeste d’importation ». Vous verrez une boîte de dialogue de sélection de fichiers. Sélectionnez l’_exemple de manifeste d’importation_ que vous avez téléchargé à l’étape 1 ci-dessus.  Ensuite, sélectionnez l’icône de dossier ou la zone de texte sous « Sélectionner un ou plusieurs fichiers de mise à jour ». Vous verrez une boîte de dialogue de sélection de fichiers. Sélectionnez l’_exemple de fichier de mise à jour_ que vous avez téléchargé à l’étape 1 ci-dessus.
   
   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Capture d’écran illustrant la sélection du fichier de mise à jour." lightbox="media/import-update/select-update-files.png":::

5. Sélectionnez l’icône de dossier ou la zone de texte sous « Sélectionner un conteneur de stockage ». Sélectionnez ensuite le compte de stockage approprié.

6. Si vous avez déjà créé un conteneur, vous pouvez le réutiliser. (Dans le cas contraire, sélectionnez « + Conteneur » pour créer un conteneur de stockage pour les mises à jour.)  Sélectionnez le conteneur à utiliser et cliquez sur « Sélectionner ».
  
  :::image type="content" source="media/import-update/container.png" alt-text="Capture d’écran illustrant la sélection du conteneur." lightbox="media/import-update/container.png":::

7. Sélectionnez « Soumettre » pour démarrer le processus d’importation.

8. Le processus d’importation commence et l’écran passe à la section « Historique d’importation ». Sélectionnez « Actualiser » pour voir la progression jusqu’à la fin du processus d’importation. Selon la taille de la mise à jour, cette opération peut prendre quelques minutes ou durer plus longtemps.
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Capture d’écran illustrant la séquence d’importation de mise à jour." lightbox="media/import-update/update-publishing-sequence-2.png":::

9. Quand la colonne État indique que l’importation a réussi, sélectionnez l’en-tête « Prêt pour le déploiement ». Vous devez maintenant voir votre mise à jour importée dans la liste.

[Apprenez-en davantage](import-update.md) sur l’importation de mises à jour.

## <a name="create-update-group"></a>Créer un groupe de mises à jour

1. Accédez au hub IoT que vous avez précédemment connecté à votre instance Device Update.

2. Sélectionnez l’option Mises à jour sous Gestion des appareils dans la barre de navigation de gauche.

3. Sélectionnez l’onglet Groupes en haut de la page. 

4. Sélectionnez le bouton Ajouter pour créer un groupe.

5. Sélectionnez l’étiquette IoT Hub que vous avez créée à l’étape précédente à partir de la liste. Sélectionnez Créer un groupe.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Capture d’écran illustrant la sélection d’une étiquette" lightbox="media/create-update-group/select-tag.PNG":::

[En savoir plus](create-update-group.md) sur l’ajout d’étiquettes et la création de groupes de mises à jour


## <a name="deploy-update"></a>Déployer la mise à jour

1. Une fois le groupe créé, vous devriez voir une nouvelle mise à jour disponible pour votre groupe d’appareils, avec un lien vers cette mise à jour sous Mises à jour en attente. Vous devrez peut-être actualiser une fois. 

2. Cliquez sur la mise à jour disponible.

3. Vérifiez que le groupe approprié est sélectionné en tant que groupe cible. Planifiez votre déploiement, puis sélectionnez Déployer la mise à jour.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Sélectionner la mise à jour" lightbox="media/deploy-update/select-update.png":::

4. Visualisez le graphique de conformité. Vous devez voir que la mise à jour est maintenant en cours. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Mise à jour en cours" lightbox="media/deploy-update/update-in-progress.png":::

5. Une fois votre appareil correctement mis à jour, vous devez voir votre graphique de conformité et les détails du déploiement refléter la même chose. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Mise à jour réussie" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Superviser le déploiement d’une mise à jour

1. Sélectionnez l’onglet Déploiements en haut de la page.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Onglet Déploiements" lightbox="media/deploy-update/deployments-tab.png":::

2. Sélectionnez le déploiement que vous avez créé pour en examiner les détails.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Détails du déploiement" lightbox="media/deploy-update/deployment-details.png":::

3. Sélectionnez Actualiser pour voir les détails d’état les plus récents. Poursuivez ce processus jusqu’à ce que l’état passe à Réussi.

Vous avez maintenant réussi une mise à jour d’image de bout en bout à l’aide du service Device Update pour IoT Hub sur un appareil Raspberry Pi 3 B+. 

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’en avez plus besoin, nettoyez vos compte, instance, hub IoT et appareil IoT Device Update. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Agent de référence du simulateur](device-update-simulator.md)
