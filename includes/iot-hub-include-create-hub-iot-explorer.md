---
title: Fichier include
description: Fichier include
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 09/17/2021
ms.openlocfilehash: 9e0b90c4d9ec306e462cef63f27e7a306a8289df
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131077478"
---
## <a name="create-an-iot-hub"></a>Créer un hub IoT
Dans cette section, vous utilisez Azure CLI pour créer un hub IoT et un groupe de ressources.  Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Un hub IoT agit en tant que hub de messages central pour la communication bidirectionnelle entre votre application IoT et les appareils.

Pour créer un hub IoT et un groupe de ressources :

1. Lancez l’interface Azure CLI : 
    - Si vous utilisez Cloud Shell, sélectionnez le bouton **Essayer** dans les commandes CLI pour lancer Cloud Shell dans une fenêtre de navigateur partagée. Ou vous pouvez ouvrir [Cloud Shell](https://shell.azure.com/bash) dans un onglet de navigateur distinct.
    - Si vous utilisez l’interface Azure CLI localement, ouvrez une console telle que Windows CMD, PowerShell ou Bash et [connectez-vous à Azure CLI](/cli/azure/authenticate-azure-cli).
    
    Pour exécuter les commandes CLI dans le reste de ce guide de démarrage rapide : copiez la syntaxe de la commande, collez-la dans votre fenêtre Cloud Shell ou votre console CLI, modifiez les valeurs des variables et appuyez sur Entrée.

1. Exécutez la commande [az extension add](/cli/azure/extension#az_extension_add) pour installer ou mettre à niveau l’extension *azure-iot* vers la version actuelle.

    ```azurecli-interactive
    az extension add --upgrade --name azure-iot
    ```

1. Exécutez la commande [az group create](/cli/azure/group#az_group_create) pour créer un groupe de ressources. La commande suivante crée un groupe de ressources nommé *MyResourceGroup* à l’emplacement *eastus* : 
    >[!NOTE]
    > Vous pouvez éventuellement définir une autre région. Pour voir les régions disponibles, exécutez `az account list-locations`. Ce tutoriel utilise *eastus* comme indiqué dans l’exemple de commande. 

    ```azurecli-interactive
    az group create --name MyResourceGroup --location eastus
    ```

1. Exécutez la commande [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) pour créer un hub IoT. La création de votre hub IoT peut prendre plusieurs minutes. 

    *YourIotHubName*. Remplacez cet espace réservé et les accolades qui l’entourent dans la commande suivante, en utilisant le nom que vous avez choisi pour votre hub IoT. Le nom du hub IoT doit être globalement unique dans Azure. Utilisez le nom de votre hub IoT dans le reste de ce guide de démarrage rapide là où vous voyez l’espace réservé.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```
    > [!TIP]
    > Après avoir créé un hub IoT, vous utiliserez Azure IoT Explorer pour interagir avec votre hub IoT dans le reste de ce démarrage rapide. IoT Explorer est une application GUI qui vous permet de vous connecter à un hub IoT existant et d’ajouter, de gérer et de surveiller des appareils. Pour plus d’informations, consultez [Installer et utiliser l’explorateur Azure IoT](../articles/iot-fundamentals/howto-use-iot-explorer.md). Si vous le souhaitez, vous pouvez continuer à utiliser les commandes CLI.

### <a name="configure-iot-explorer"></a>Configurer IoT Explorer

Dans le reste de ce démarrage rapide, vous allez utiliser IoT Explorer pour inscrire un appareil sur votre hub IoT et afficher la télémétrie de l’appareil. Dans cette section, vous allez configurer IoT Explorer pour qu’il se connecte au hub IoT que vous venez de créer et lise les modèles Plug-and-Play à partir du référentiel de modèle public. 

> [!NOTE]
> Vous pouvez également utiliser l’interface Azure CLI pour inscrire un appareil. Utilisez la commande *[az iot hub device-identity create](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_create) --device-id mydevice --hub-name {YourIoTHubName}* pour inscrire un nouvel appareil et la commande *[az iot hub device-identity connection-string show](/cli/azure/iot/hub/device-identity/connection-string#az_iot_hub_device_identity_connection_string_show) --device-id mydevice --hub-name {YourIoTHubName}* pour obtenir la chaîne de connexion principale pour l’appareil. Une fois que vous avez noté la chaîne de connexion de l’appareil, vous pouvez passer directement à l’étape [Exécuter un appareil](#run-a-device).

Pour ajouter une connexion à votre hub IoT :

1. Exécutez la commande [az iot hub connection-string show](/cli/azure/iot/hub/connection-string#az_iot_hub_connection_string_show) pour obtenir la chaîne de connexion pour votre hub IoT.

    ```azurecli
    az iot hub connection-string  show --hub-name {YourIoTHubName}
    ```

1. Copiez la chaîne de connexion sans les guillemets qui l’entourent.
1. Dans Azure IoT Explorer, sélectionnez **Hubs IoT** dans le menu de gauche, puis **+ Ajouter une connexion**.
1. Collez la chaîne de connexion dans la zone **Chaîne de connexion**.
1. Sélectionnez **Enregistrer**.

    :::image type="content" source="media/iot-hub-include-create-hub-iot-explorer/iot-explorer-add-connection.png" alt-text="Capture d’écran de l’ajout d’une connexion dans IoT Explorer":::

1. Si la connexion aboutit, IoT Explorer bascule vers l’affichage **Appareils**.

Pour ajouter le référentiel de modèle public :

1. Dans IoT Explorer, sélectionnez **Accueil** pour revenir à la vue d’accueil.
1. Dans le menu de gauche, sélectionnez **Paramètres IoT Plug-and-Play**, puis **+ Ajouter** et **Référentiel public** dans le menu déroulant.
1. Une entrée s’affiche pour le référentiel de modèle public à l’emplacement `https://devicemodels.azure.com`.

    :::image type="content" source="media/iot-hub-include-create-hub-iot-explorer/iot-explorer-add-public-repository.png" alt-text="Capture d’écran de l’ajout du référentiel de modèle public dans IoT Explorer":::

1. Sélectionnez **Enregistrer**.

### <a name="register-a-device"></a>Inscrire un appareil

Dans cette section, vous allez créer une instance d’appareil et l’inscrire auprès du hub IoT que vous avez créé. Vous allez utiliser les informations de connexion de l’appareil que vous venez d’inscrire pour connecter votre appareil en toute sécurité dans une section ultérieure.

Pour inscrire un appareil :

1. À partir de la vue d’accueil dans IoT Explorer, sélectionnez **Hubs IoT**.
1. La connexion que vous avez ajoutée précédemment devrait apparaître. Sous les propriétés de connexion, sélectionnez **Afficher les appareils dans ce hub**.
1. Sélectionnez **+ Nouveau**, puis entrez un ID d’appareil pour votre appareil, par exemple, *mydevice*. Conservez toutes les autres propriétés en l’état.
1. Sélectionnez **Create** (Créer).

    :::image type="content" source="media/iot-hub-include-create-hub-iot-explorer/iot-explorer-device-created.png" alt-text="Capture d’écran d’identité d’appareil Azure IoT Explorer":::

1. Utilisez les boutons de copie pour copier et noter le champ **Chaîne de connexion principale**. Vous aurez besoin de cette chaîne de connexion ultérieurement.
