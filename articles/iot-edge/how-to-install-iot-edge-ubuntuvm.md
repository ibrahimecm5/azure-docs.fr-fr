---
title: Exécuter Azure IoT Edge sur des Machines virtuelles Ubuntu | Microsoft Docs
description: Instructions de configuration d’Azure IoT Edge pour des machines virtuelles Ubuntu 18.04 LTS
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/27/2021
ms.author: pdecarlo
ms.openlocfilehash: 6865a89eae0df708d73d9062dd07c18c46ece4f8
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130263759"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Exécuter Azure IoT Edge sur des Machines virtuelles Ubuntu

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Le runtime Azure IoT Edge est ce qui transforme un appareil en appareil IoT Edge. Le runtime peut être déployé sur un appareil de petite taille comme un Raspberry Pi ou de grande taille comme un serveur industriel. Une fois qu’un appareil est configuré avec le runtime IoT Edge, vous pouvez commencer à déployer une logique métier sur celui-ci à partir du cloud.

Pour en savoir plus sur le fonctionnement du runtime IoT Edge et les composants inclus, consultez [Présentation du runtime Azure IoT Edge et de son architecture](iot-edge-runtime.md).

Cet article décrit les étapes de déploiement d’une machine virtuelle Ubuntu 18.04 LTS avec le runtime Azure IoT Edge installé et configuré à l’aide d’une chaîne de connexion d’appareil fournie. Le déploiement s’effectue à l’aide d’un [modèle Resource Manager](../azure-resource-manager/templates/overview.md) basé sur [cloud-init](../virtual-machines/linux/using-cloud-init.md) géré dans le dépôt de projet [iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy).

Au premier démarrage, la machine virtuelle Ubuntu 18.04 LTS [installe la version la plus récente du runtime Azure IoT Edge via cloud-init](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt). Elle définit également une chaîne de connexion fournie avant le démarrage du runtime, ce qui vous permet de configurer et de connecter facilement l’appareil IoT Edge sans avoir à démarrer une session SSH ou de bureau à distance.

## <a name="deploy-using-deploy-to-azure-button"></a>Déployer à l’aide du Déployer sur Azure

Le bouton [Déployer sur Azure](../azure-resource-manager/templates/deploy-to-azure-button.md) permet d’effectuer un déploiement simplifié des [modèles Resource Manager](../azure-resource-manager/templates/overview.md) conservés sur GitHub.  Cette section montre l’utilisation du bouton Déployer sur Azure contenu dans le dépôt de projet [iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy).  

1. Nous allons déployer une machine virtuelle Linux prenant en charge Azure IoT Edge à l’aide du modèle Resource Manager iotedge-vm-deploy.  Pour commencer, cliquez sur le bouton ci-dessous :
   :::moniker range="iotedge-2018-06"
   [![Bouton déployer sur Azure pour iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)
   :::moniker-end
   :::moniker range="iotedge-2020-11"
   [![Bouton déployer sur Azure pour iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2F1.2.0%2FedgeDeploy.json)
   :::moniker-end

1. Dans la fenêtre qui vient de s’ouvrir, renseignez les champs de formulaire disponibles :

    > [!div class="mx-imgBorder"]
    > [![Capture d’écran montrant le modèle iotedge-vm-deploy](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Abonnement**: Abonnement Azure actif dans lequel déployer la machine virtuelle.

    **Groupe de ressources** : Groupe de ressources existant ou nouvellement créé pour contenir la machine virtuelle et ses ressources associées.

    **Préfixe d’étiquette DNS** : Valeur obligatoire de votre choix, utilisée pour préfixer le nom d’hôte de la machine virtuelle.

    **Nom d’utilisateur administrateur** : Nom de l’utilisateur qui sera doté de privilèges root sur le déploiement.

    **Chaîne de connexion de l’appareil** : [Chaîne de connexion d’appareil](./how-to-provision-single-device-linux-symmetric.md#view-registered-devices-and-retrieve-provisioning-information) pour un appareil créé dans votre [IoT Hub](../iot-hub/about-iot-hub.md) prévu.

    **Taille de la machine virtuelle** : [Taille](../cloud-services/cloud-services-sizes-specs.md) de la machine virtuelle à déployer

    **Version du système d’exploitation Ubuntu**  : Version du système d’exploitation Ubuntu à installer sur la machine virtuelle de base.

    **Emplacement** : [Région géographique](https://azure.microsoft.com/global-infrastructure/locations/) dans laquelle déployer la machine virtuelle. Par défaut, il s’agit de l’emplacement du groupe de ressources sélectionné.

    **Type d’authentification** : Choisissez **sshPublicKey** ou **mot de passe** selon votre préférence.

    **Mot de passe ou clé d’administrateur** : Valeur de la clé publique SSH ou du mot de passe en fonction du choix du type d’authentification.

    Lorsque tous les champs sont renseignés, activez la case à cocher en bas de la page pour accepter les conditions d’utilisation, puis sélectionnez **Achat** pour commencer le déploiement.

1. Vérifiez que le déploiement a complètement abouti.  Une ressource de machine virtuelle doit avoir été déployée dans le groupe de ressources sélectionné.  Notez le nom de la machine, qui doit être au format `vm-0000000000000`. Notez également le **Nom DNS** associé, qui doit être au format `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com.

    Vous pouvez trouver le **Nom DNS** dans la section **Vue d’ensemble** de la machine virtuelle nouvellement déployée dans le portail Azure.

    > [!div class="mx-imgBorder"]
    > [![Capture d’écran montrant le nom dns de la machine virtuelle iotedge](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Si vous souhaitez utiliser SSH dans cette machine virtuelle après configuration, utilisez le **Nom DNS** associé avec la commande : `ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>Effectuer un déploiement sur Azure CLI

1. Assurez-vous que vous avez installé l’extension iot Azure CLI IOT avec :

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

1. Ensuite, si vous utilisez Azure CLI sur votre ordinateur de bureau, commencez par vous connecter :

   ```azurecli-interactive
   az login
   ```

1. Si vous avez plusieurs abonnements, sélectionnez celui que vous souhaitez utiliser :
   1. Répertoriez vos abonnements :

      ```azurecli-interactive
      az account list --output table
      ```

   1. Copiez le champ SubscriptionID de l’abonnement que vous souhaitez utiliser.

   1. Définissez votre abonnement de travail avec l’ID que vous venez de copier :

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. Créez un groupe de ressources (ou spécifiez un groupe existant aux étapes suivantes) :

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Créez une machine virtuelle :
   :::moniker range="iotedge-2018-06"
   Pour utiliser l’**authenticationType** `password`, consultez l’exemple ci-dessous :

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

   Pour vous authentifier avec une clé SSH, vous pouvez spécifier l’**authenticationType** `sshPublicKey`, puis fournir la valeur de la clé SSH dans le paramètre **adminPasswordOrKey**.  Voici un exemple.

   ```azurecli-interactive
   #Generate the SSH Key
   ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  
    
   #Create a VM using the iotedge-vm-deploy script
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='sshPublicKey' \
   --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
   ```
   :::moniker-end
   :::moniker range="iotedge-2020-11"
   Pour utiliser l’**authenticationType** `password`, consultez l’exemple ci-dessous :

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

   Pour vous authentifier avec une clé SSH, vous pouvez spécifier l’**authenticationType** `sshPublicKey`, puis fournir la valeur de la clé SSH dans le paramètre **adminPasswordOrKey**.  Voici un exemple.

   ```azurecli-interactive
   #Generate the SSH Key
   ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  
    
   #Create a VM using the iotedge-vm-deploy script
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='sshPublicKey' \
   --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
   ```
   :::moniker-end

1. Vérifiez que le déploiement a complètement abouti.  Une ressource de machine virtuelle doit avoir été déployée dans le groupe de ressources sélectionné.  Notez le nom de la machine, qui doit être au format `vm-0000000000000`. Notez également le **Nom DNS** associé, qui doit être au format `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com.

    Vous pouvez trouver le **Nom DNS** dans la sortie au format JSON de l’étape précédente, à l’intérieur de la section **outputs** qui fait partie de l’entrée **SSH publique**.  La valeur de cette entrée peut être utilisée pour la connexion SSH à la machine nouvellement déployée.

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    Vous pouvez également trouver le **Nom DNS** dans la section **Vue d’ensemble** de la machine virtuelle nouvellement déployée dans le portail Azure.

    > [!div class="mx-imgBorder"]
    > [![Capture d’écran montrant le nom dns de la machine virtuelle iotedge](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Si vous souhaitez utiliser SSH dans cette machine virtuelle après configuration, utilisez le **Nom DNS** associé avec la commande : `ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’un appareil IoT Edge approvisionné avec le runtime installé, vous pouvez [déployer des modules IoT Edge](how-to-deploy-modules-portal.md).

Si vous ne parvenez pas à installer correctement le runtime IoT Edge, consultez la page de [résolution des problèmes](troubleshoot.md).

Pour mettre à jour une installation existante vers la dernière version d’IoT Edge, voir [Mettre à jour le runtime et le démon de sécurité IoT Edge](how-to-update-iot-edge.md).

Si vous souhaitez ouvrir des ports pour accéder à la machine virtuelle via SSH ou d’autres connexions entrantes, reportez-vous à la documentation de des machines virtuelles Azure sur l’[ouverture de ports et de points de terminaison sur une machine virtuelle Linux](../virtual-machines/linux/nsg-quickstart.md)