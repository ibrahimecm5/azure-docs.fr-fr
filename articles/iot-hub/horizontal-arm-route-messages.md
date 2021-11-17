---
title: Utiliser un modèle ARM pour publier un hub Azure IoT, un compte de stockage, des messages de routage
description: Utiliser un modèle ARM pour publier un hub Azure IoT, un compte de stockage, des messages de routage
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: robinsh
ms.custom: mvc, subject-armqs
ms.openlocfilehash: e40b714709aedbb643b2b2d65878a73618a7682f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131445864"
---
# <a name="quickstart-deploy-an-azure-iot-hub-and-a-storage-account-using-an-arm-template"></a>Démarrage rapide : Déployer un hub Azure IoT et un compte de stockage en utilisant un modèle ARM

Dans ce guide de démarrage rapide, vous utilisez un modèle Azure Resource Manager (modèle ARM) pour créer un hub IoT qui route les messages vers Stockage Azure et un compte de stockage pour stocker les messages. Après avoir ajouté manuellement un appareil IoT virtuel au hub pour envoyer les messages, vous configurez ces informations de connexion dans une application appelée *arm-read-write* pour envoyer des messages de l’appareil vers le hub. Le hub est configuré de sorte que les messages qui lui sont envoyés sont automatiquement routés vers le compte de stockage. À la fin de ce guide de démarrage rapide, vous pouvez ouvrir le compte de stockage et voir les messages envoyés.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Si votre environnement remplit les prérequis et que vous êtes déjà familiarisé avec l’utilisation des modèles ARM, sélectionnez le bouton **Déployer sur Azure**. Le modèle s’ouvre dans le portail Azure.

[![Déployer sur Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.devices%2Fiothub-auto-route-messages%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans ce guide de démarrage rapide est appelé `101-iothub-auto-route-messages` et provient des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/iothub-auto-route-messages).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.devices/iothub-auto-route-messages/azuredeploy.json":::

Deux ressources Azure sont définies dans le modèle :

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Devices/IotHubs](/azure/templates/microsoft.devices/iothubs)

## <a name="deploy-the-template-and-run-the-sample-app"></a>Déployer le modèle et exécuter l’exemple d’application

Cette section décrit les étapes de déploiement du modèle, de création d’un appareil virtuel et d’exécution de l’application arm-read-write pour envoyer les messages.

1. Créez les ressources en déployant le modèle ARM.

    > [!TIP]
    > Sélectionnez le bouton ci-dessous pour démarrer le déploiement du modèle. Pendant qu’il s’exécute, configurez l’application arm-read-write pour l’exécution.

    [![Déployer sur Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.devices%2Fiothub-auto-route-messages%2Fazuredeploy.json)

1. Téléchargez et décompressez les [exemples C# IoT](/samples/azure-samples/azure-iot-samples-csharp/azure-iot-samples-for-csharp-net/).

1. Ouvrez une fenêtre de commande et accédez au dossier où vous avez décompressé les exemples C# IoT. Recherchez le dossier contenant le fichier arm-read-write.csproj. Vous créez les variables d’environnement dans cette fenêtre de commande. Connectez-vous au [portail Azure](https://portal.azure.com) pour obtenir les clés. Sélectionnez **Groupes de ressources**, puis sélectionnez le groupe de ressources utilisé pour ce guide de démarrage rapide.

   ![Sélectionner le groupe de ressources](./media/horizontal-arm-route-messages/01-select-resource-group.png)

1. Vous voyez le hub IoT et le compte de stockage qui ont été créés quand vous avez déployé le modèle ARM. Attendez que le modèle soit entièrement déployé avant de continuer. Sélectionnez ensuite votre groupe de ressources pour voir vos ressources.

   ![Visualiser les ressources dans le groupe de ressources](./media/horizontal-arm-route-messages/02-view-resources-in-group.png)

1. Vous avez besoin du **nom du hub**. Sélectionnez le hub dans la liste des ressources. Copiez le nom du hub en haut de la section IoT Hub dans le Presse-papiers Windows.

    Remplacez le nom du hub dans cette commande là où c’est indiqué, puis exécutez cette commande dans la fenêtre de commande :

    ```cmd
    SET IOT_HUB_URI=<hub name goes here>.azure-devices-net;
    ```

   qui va se présenter comme cet exemple :

   ```cmd
   SET IOT_HUB_URI=ContosoTestHubdlxlud5h.azure-devices-net;
   ```

1. La variable d’environnement suivante est la clé de l’appareil IoT. Ajoutez un nouvel appareil au hub en sélectionnant **Appareils** dans le menu IoT Hub du hub.

   :::image type="content" source="./media/horizontal-arm-route-messages/04-select-iot-devices.png" alt-text="Capture d’écran montrant les appareils dans le volet gauche." border="true":::

1. Sur le côté droit de l’écran, sélectionnez **+ Ajouter un appareil** pour ajouter un nouvel appareil.

   Renseignez le nom du nouvel appareil. Ce guide de démarrage rapide utilise un nom commençant par **Contoso-Test-Device**. Enregistrez l’appareil, puis rouvrez cet écran pour récupérer la clé de l’appareil. (La clé est générée pour vous quand vous fermez le volet.) Sélectionnez la clé principale ou secondaire, puis copiez-la dans le Presse-papiers Windows. Dans la fenêtre de commande, définissez la commande à exécuter, puis appuyez sur **Entrée**. La commande doit se présenter comme celle-ci, mais avec la clé de l’appareil collée :

   ```cmd
   SET IOT_DEVICE_KEY=<device-key-goes-here>
   ```

1. La dernière variable d’environnement est l’**ID d’appareil**. Dans la fenêtre de commande, configurez la commande et exécutez-la.

   ```cmd
   SET IOT_DEVICE_ID=<device-id-goes-here>
   ```

   qui va se présenter comme cet exemple :

   ```cmd
   SET IOT_DEVICE_ID=Contoso-Test-Device
   ```

1. Pour voir les variables d’environnement que vous avez définies, tapez SET sur la ligne de commande et appuyez sur **Entrée**, puis recherchez celles qui commencent par **IoT**.

   ![Voir les variables d’environnement](./media/horizontal-arm-route-messages/06-environment-variables.png)

    À présent que les variables d’environnement sont définies, exécutez l’application à partir de la même fenêtre de commande. Comme vous utilisez la même fenêtre, les variables sont accessibles en mémoire lors de l’exécution de l’application.

1. Pour exécuter l’application, tapez la commande suivante dans la fenêtre de commande, puis appuyez sur **Entrée**.

    `dotnet run arm-read-write`

   L’application génère et affiche des messages sur la console à chaque envoi de message au hub IoT. Le hub a été configuré dans le modèle ARM avec un routage automatisé. Les messages contenant le texte `level = storage` sont automatiquement routés vers le compte de stockage. Laissez l’application s’exécuter pendant 10 à 15 minutes, puis appuyez sur **Entrée** une ou deux fois jusqu’à ce qu’elle cesse de s’exécuter.

## <a name="review-deployed-resources"></a>Vérifier les ressources déployées

1. Connectez-vous au [portail Azure](https://portal.azure.com) et sélectionnez le groupe de ressources, puis sélectionnez le compte de stockage.

1. Explorez le compte de stockage jusqu’à trouver les fichiers.

   ![Examiner les fichiers du compte de stockage](./media/horizontal-arm-route-messages/07-see-storage.png)

1. Sélectionnez un des fichiers et sélectionnez **Télécharger**, puis téléchargez le fichier à un emplacement que vous pouvez retrouver ultérieurement. Son nom est un nombre, par exemple 47. Ajoutez _.txt_ à la fin, puis double-cliquez sur le fichier pour l’ouvrir.

1. Quand vous ouvrez le fichier, chaque ligne correspond à un message différent ; le corps de chaque message est chiffré. Il doit vous permettre d’effectuer des requêtes sur le corps du message.

   ![Voir les messages envoyés](./media/horizontal-arm-route-messages/08-messages.png)

   > [!NOTE]
   > Ces messages sont encodés au format UTF-32 et base64. Si vous relisez le message, vous devez le décoder depuis base64 et UTF-32 pour pouvoir lire au format ASCII. Si vous êtes intéressé, vous pouvez utiliser la méthode ReadOneRowFromFile du tutoriel Routage pour en lire un depuis un de ces fichiers de messages et le décoder en ASCII. ReadOneRowFromFile se trouve dans le référentiel d’exemples C# IoT que vous avez décompressé pour ce guide de démarrage rapide. Voici le chemin d’accès à partir du haut de ce dossier : *./iot-hub/Tutorials/Routing/SimulatedDevice/Program.cs.* Définissez le booléen `readTheFile` sur true et codez en dur le chemin du fichier sur le disque : cela ouvrira et traduira la première ligne du fichier.

Vous avez déployé un modèle ARM pour créer un hub IoT et un compte de stockage, et vous avez exécuté un programme pour envoyer des messages au hub. Les messages sont ensuite automatiquement stockés dans le compte de stockage où ils peuvent être consultés.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour supprimer les ressources ajoutées au cours de ce démarrage rapide, connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Groupes de ressources**, puis recherchez le groupe de ressources utilisé pour ce guide de démarrage rapide. Sélectionnez le nom du groupe de ressources, puis sélectionnez *Supprimer*. Ceci va supprimer toutes les ressources dans le groupe.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Créer et déployer votre premier modèle Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
