---
title: 'Comment installer et exécuter le conteneur d’analyse spatiale : vision par ordinateur'
titleSuffix: Azure Cognitive Services
description: Le conteneur d’analyse spatiale vous permet de détecter des personnes et des distances.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: pafarley
ms.custom: ignite-fall-2021
ms.openlocfilehash: f866db833381b8dc6c75538265eefdc057445b3a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057891"
---
# <a name="install-and-run-the-spatial-analysis-container-preview"></a>Installer et exécuter le conteneur d’analyse spatiale (préversion)

Le conteneur d’analyse spatiale vous permet d’analyser le flux vidéo en temps réel afin de comprendre les relations spatiales entre les personnes, leurs déplacements et les interactions avec des objets dans des environnements physiques. Les conteneurs conviennent particulièrement bien à certaines exigences de sécurité et de gouvernance des données.

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* [!INCLUDE [contributor-requirement](../includes/quickstarts/contributor-requirement.md)]
* Une fois que vous avez votre abonnement Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Créer une ressource Vision par ordinateur"  target="_blank">créez une ressource Vision par ordinateur </a> pour le niveau Standard S1 dans le portail Azure afin d’obtenir votre clé et votre point de terminaison. Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour exécuter le conteneur d’analyse spatiale. Vous utiliserez votre clé et votre point de terminaison ultérieurement.

### <a name="spatial-analysis-container-requirements"></a>Exigences du conteneur d’analyse spatiale

Pour exécuter le conteneur d’analyse spatiale, vous avez besoin d’un appareil de calcul avec une [GPU NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/). Nous vous recommandons d’utiliser [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) avec l’accélération GPU, bien que le conteneur s’exécute sur n’importe quel autre ordinateur de bureau présentant la configuration minimale requise. Nous ferons référence à cet appareil en tant qu’ordinateur hôte.

#### <a name="azure-stack-edge-device"></a>[Appareil Azure Stack Edge](#tab/azure-stack-edge)

Azure Stack Edge est une solution matérielle en tant que service ainsi qu’un appareil de computing en périphérie basé sur l’intelligence artificielle. Il est doté de fonctionnalités de transfert de données via le réseau. Pour obtenir des instructions détaillées sur la préparation et la configuration, consultez la [documentation Azure Stack Edge](../../databox-online/azure-stack-edge-deploy-prep.md).

#### <a name="desktop-machine"></a>[Ordinateur de bureau](#tab/desktop-machine)

#### <a name="minimum-hardware-requirements"></a>Conditions matérielles minimales requises

* RAM système de 4 Go
* 4 Go de RAM GPU
* Processeur 8 cœurs
* 1 GPU NVIDIA Tesla T4
* 20 GB d’espace HDD

#### <a name="recommended-hardware"></a>Matériel recommandé

* RAM système de 32 Go
* 16 Go de RAM GPU
* Processeur 8 cœurs
* 2 GPU NVIDIA Tesla T4
* 50 Go d’espace SSD

Dans cet article, vous allez télécharger et installer les packages logiciels suivants. L’ordinateur hôte doit être en mesure d’exécuter les opérations suivantes (voir les instructions ci-dessous) :

* [Pilotes graphiques NVIDIA](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html) et [NVIDIA CUDA Toolkit](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)
* Configurations pour [NVIDIA MPS](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf) (service multiprocessus).
* [Docker CE](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-engine---community-1) et [NVIDIA-Docker2](https://github.com/NVIDIA/nvidia-docker) 
* Runtime [Azure IoT Edge](../../iot-edge/how-to-provision-single-device-linux-symmetric.md).

#### <a name="azure-vm-with-gpu"></a>[Machine virtuelle Azure avec GPU](#tab/virtual-machine)
Dans notre exemple, nous utilisons une [machine virtuelle de série NC](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) dotée d’un GPU K80.

---

| Condition requise | Description |
|--|--|
| Appareil photo | Le conteneur d’analyse spatiale n’est pas lié à une marque de caméra spécifique. La caméra doit : prendre en charge l’encodage RTSP (Real-Time Streaming Protocol) et H. 264, être accessible à l’ordinateur hôte et pouvoir assurer une diffusion en continu à 15FPS et avec une résolution de 1080p. |
| Système d’exploitation Linux | [Ubuntu Desktop 18.04 LTS](http://releases.ubuntu.com/18.04/) doit être installé sur l’ordinateur hôte.  |

## <a name="set-up-the-host-computer"></a>Configurer l’ordinateur hôte

Il est recommandé d’utiliser un appareil Azure Stack Edge pour votre ordinateur hôte. Cliquez sur **Ordinateur de bureau** si vous configurez un autre appareil ou sur **Machine virtuelle** si vous utilisez une machine virtuelle.

#### <a name="azure-stack-edge-device"></a>[Appareil Azure Stack Edge](#tab/azure-stack-edge)

### <a name="configure-compute-on-the-azure-stack-edge-portal"></a>Configurer le calcul sur le portail Azure Stack Edge 
 
L’analyse spatiale utilise les fonctionnalités de calcul d’Azure Stack Edge pour exécuter une solution d’intelligence artificielle. Pour activer les fonctionnalités de calcul, assurez-vous que les conditions suivantes sont remplies : 

* Vous avez [connecté et activé](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md) votre appareil Azure Stack Edge. 
* Vous avez un système client Windows exécutant PowerShell 5.0 ou une version ultérieure pour accéder à l’appareil.  
* Pour déployer un cluster Kubernetes, vous devez configurer votre appareil Azure Stack Edge via l’**interface utilisateur locale** dans le [portail Azure](https://portal.azure.com/) : 
  1. Configurer la fonctionnalité de calcul sur votre appareil Azure Stack Edge. Pour activer le calcul, accédez à la page **Calcul** dans l’interface web de votre appareil. 
  2. Sélectionnez une interface réseau que vous souhaitez activer pour le calcul, puis cliquez sur **Activer**. Un commutateur virtuel est créé sur votre appareil sur cette interface réseau.
  3. Laissez les adresses IP du nœud de test Kubernetes et les adresses IP des services externes Kubernetes vides.
  4. Cliquez sur **Appliquer**. Cette opération peut prendre environ deux minutes. 

![Configurer le calcul](media/spatial-analysis/configure-compute.png)

### <a name="set-up-an-edge-compute-role-and-create-an-iot-hub-resource"></a>Configurer un rôle de calcul Edge et créer une ressource IoT Hub

Dans le [portail Azure](https://portal.azure.com/), accédez à votre ressource Azure Stack Edge. Sur la page **Vue d’ensemble** ou dans la liste de navigation, cliquez sur le bouton de calcul Edge **Démarrer**. Dans la vignette  **Configurer le calcul Edge** , cliquez sur **Configurer**. 

![Lien](media/spatial-analysis/configure-edge-compute-tile.png)

Dans la page  **Configurer le calcul Edge** , choisissez une ressource IoT Hub existante, ou choisissez d’en créer une nouvelle. Par défaut, un niveau tarifaire Standard (S1) est utilisé pour créer une ressource IoT Hub. Pour utiliser une ressource IoT Hub de niveau Gratuit, créez-en une, puis sélectionnez-la. La ressource IoT Hub utilise les mêmes abonnement et groupe de ressources que la ressource Azure Stack Edge 

Cliquez sur **Créer**. La création de ressources IoT Hub peut prendre quelques minutes. Une fois la ressource IoT Hub créée, la vignette  **Configurer le computing en périphérie** sera mise à jour pour afficher la nouvelle configuration. Pour vérifier que le rôle de computing Edge a été configuré, sélectionnez  **Configuration de la vue** sur la vignette  **Configurer le calcul** .

Quand le rôle de calcul Edge est configuré sur l’appareil Edge, il crée deux appareils : un appareil IoT et un appareil IoT Edge. Ces deux appareils peuvent être visualisés dans la ressource IoT Hub. Le runtime Azure IoT Edge sera déjà en cours d’exécution sur l’appareil IoT Edge.

> [!NOTE]
> * Actuellement, seule la plateforme Linux est prise en charge pour les appareils IoT Edge. Pour obtenir de l’aide sur le dépannage de l’appareil Azure Stack Edge, consultez l’article [Journalisation et résolution des problèmes](spatial-analysis-logging.md).
> * Pour en savoir plus sur la configuration d’un appareil IoT Edge pour communiquer via un serveur proxy, consultez [Configurer un appareil IoT Edge pour communiquer via un serveur proxy](../../iot-edge/how-to-configure-proxy-support.md#azure-portal)

###  <a name="enable-mps-on-azure-stack-edge"></a>Activer MPS sur Azure Stack Edge 

Suivez cette procédure pour effectuer une connexion distante depuis un client Windows.

1. Démarrez une session Windows PowerShell en tant qu’administrateur.
2. Assurez-vous que le service Windows Remote Management est en cours d’exécution sur votre client. À l’invite de commandes, tapez :

    ```powershell
    winrm quickconfig
    ```

    Pour plus d’informations, consultez [Installation et configuration de Windows Remote Management](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management#quick-default-configuration).

3. Assignez une variable à la chaîne de connexion utilisée dans le fichier `hosts`.

    ```powershell
    $Name = "<Node serial number>.<DNS domain of the device>"
    ``` 

    Remplacez `<Node serial number>` et `<DNS domain of the device>` par le numéro de série du nœud et le domaine DNS de votre appareil. Vous pouvez obtenir la valeur du numéro de série du nœud à partir de la page **Certificats** et celle du domaine DNS à partir de la page **Appareil** dans l’interface utilisateur web locale de votre appareil.

4. Pour ajouter cette chaîne de connexion pour votre appareil à la liste des hôtes approuvés du client, saisissez la commande suivante :

    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $Name -Concatenate -Force
    ```

5. Démarrez une session Windows PowerShell sur l’appareil :

    ```powershell
    Enter-PSSession -ComputerName $Name -Credential ~\EdgeUser -ConfigurationName Minishell -UseSSL
    ```

    Si vous voyez une erreur liée à une relation de confiance, vérifiez si la chaîne de signature du certificat de nœud chargé sur votre appareil est également installée sur le client qui accède à votre appareil.

6. Indiquez le mot de passe lorsque vous y êtes invité. Utilisez le mot de passe vous permettant de vous connecter à l’interface utilisateur web locale. Le mot de passe par défaut de cette interface est *Password1*. Lorsque vous êtes connecté à l’appareil à l’aide de PowerShell à distance, vous pouvez voir l’exemple de sortie suivant :  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $Name = "1HXQG13.wdshcsso.com"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $Name -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $Name -Credential ~\EdgeUser -ConfigurationName Minishell -UseSSL

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [1HXQG13.wdshcsso.com]: PS>
    ```

#### <a name="desktop-machine"></a>[Ordinateur de bureau](#tab/desktop-machine)

Suivez ces instructions si votre ordinateur hôte n’est pas un appareil Azure Stack Edge.

#### <a name="install-nvidia-cuda-toolkit-and-nvidia-graphics-drivers-on-the-host-computer"></a>Installez NVIDIA CUDA Toolkit et les pilotes graphiques Nvidia sur l’ordinateur hôte

Utilisez le script Bash suivant pour installer les pilotes graphiques NVIDIA requis et CUDA Toolkit.

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo add-apt-repository "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/ /"
sudo apt-get update
sudo apt-get -y install cuda
```

Redémarrez l’ordinateur et exécutez la commande suivante :

```bash
nvidia-smi
```

Vous devez voir la sortie suivante.

![Sortie du pilote NVIDIA](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-host-computer"></a>Installez Docker CE et nvidia-docker2 sur l’ordinateur hôte

Installez Docker CE sur l’ordinateur hôte.

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```

Installez le package logiciel *nvidia-docker-2*.

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update
sudo apt-get install -y docker-ce nvidia-docker2
sudo systemctl restart docker
```

## <a name="enable-nvidia-mps-on-the-host-computer"></a>Activez NVIDIA MPS sur l’ordinateur hôte

> [!TIP]
> * N’installez pas de pack d’administration si la capacité de calcul de votre GPU est inférieure à 7.x (pre Volta). Pour obtenir des informations de référence, consultez [Compatibilité CUDA](https://docs.nvidia.com/deploy/cuda-compatibility/index.html#support-title). 
> * Exécutez les instructions de MPS à partir d’une fenêtre de terminal sur l’ordinateur hôte. Pas à l’intérieur de votre instance de conteneur Docker.

Pour des performances et une utilisation optimales, configurez le ou les GPU de l’ordinateur hôte pour le [service de multitraitement (MPS) NVIDIA](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf). Exécutez les instructions de MPS à partir d’une fenêtre de terminal sur l’ordinateur hôte.

```bash
# Set GPU(s) compute mode to EXCLUSIVE_PROCESS
sudo nvidia-smi --compute-mode=EXCLUSIVE_PROCESS

# Cronjob for setting GPU(s) compute mode to EXCLUSIVE_PROCESS on boot
echo "SHELL=/bin/bash" > /tmp/nvidia-mps-cronjob
echo "PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin" >> /tmp/nvidia-mps-cronjob
echo "@reboot   root    nvidia-smi --compute-mode=EXCLUSIVE_PROCESS" >> /tmp/nvidia-mps-cronjob

sudo chown root:root /tmp/nvidia-mps-cronjob
sudo mv /tmp/nvidia-mps-cronjob /etc/cron.d/

# Service entry for automatically starting MPS control daemon
echo "[Unit]" > /tmp/nvidia-mps.service
echo "Description=NVIDIA MPS control service" >> /tmp/nvidia-mps.service
echo "After=cron.service" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Service]" >> /tmp/nvidia-mps.service
echo "Restart=on-failure" >> /tmp/nvidia-mps.service
echo "ExecStart=/usr/bin/nvidia-cuda-mps-control -f" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Install]" >> /tmp/nvidia-mps.service
echo "WantedBy=multi-user.target" >> /tmp/nvidia-mps.service

sudo chown root:root /tmp/nvidia-mps.service
sudo mv /tmp/nvidia-mps.service /etc/systemd/system/

sudo systemctl --now enable nvidia-mps.service
```

## <a name="configure-azure-iot-edge-on-the-host-computer"></a>Configurer Azure IoT Edge sur l’ordinateur hôte

Pour déployer le conteneur d’analyse spatiale sur l’ordinateur hôte, créez une instance d’un service [Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md) à l’aide du niveau tarifaire Standard (S1) ou Gratuit (F0). 

Utiliser l’interface de ligne de commande Azure pour créer un service Azure IoT Hub. Remplacez les paramètres le cas échéant. Vous pouvez également créer le service Azure IoT Hub sur le [portail Azure](https://portal.azure.com/).

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```
```bash
sudo az login
```
```bash
sudo az account set --subscription "<name or ID of Azure Subscription>"
```
```bash
sudo az group create --name "<resource-group-name>" --location "<your-region>"
```
Consultez [Prise en charge des régions](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) pour les régions disponibles.
```bash
sudo az iot hub create --name "<iothub-group-name>" --sku S1 --resource-group "<resource-group-name>"
```
```bash
sudo az iot hub device-identity create --hub-name "<iothub-name>" --device-id "<device-name>" --edge-enabled
```

Vous devez installer la version 1.0.9 d’[Azure IoT Edge](../../iot-edge/how-to-provision-single-device-linux-symmetric.md). Pour télécharger la version correcte, procédez comme suit :

Ubuntu Server 18.04 :
```bash
curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
```

Copiez la liste générée.
```bash
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
```

Installez la clé publique Microsoft GPG.

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
```
```bash
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

Mettez à jour les listes de packages sur votre appareil.

```bash
sudo apt-get update
```

Installez la version 1.0.9 :

```bash
sudo apt-get install iotedge=1.0.9* libiothsm-std=1.0.9*
```

Ensuite, inscrivez l’ordinateur hôte en tant qu’appareil IoT Edge dans votre instance IoT Hub à l’aide d’une [chaîne de connexion](../../iot-edge/how-to-provision-single-device-linux-symmetric.md#register-your-device).

Vous devez connecter l’appareil IoT Edge à votre service IoT Hub Azure. Vous devez copier la chaîne de connexion à partir de l’appareil IoT Edge que vous avez créé précédemment. Vous pouvez aussi exécuter la commande Azure CLI ci-dessous.

```bash
sudo az iot hub device-identity connection-string show --device-id my-edge-device --hub-name test-iot-hub-123
```

Sur l’ordinateur hôte, ouvrez `/etc/iotedge/config.yaml` pour le modifier. Remplacez `ADD DEVICE CONNECTION STRING HERE` par la chaîne de connexion. Enregistrez et fermez le fichier. Exécutez cette commande pour redémarrer le service IoT Edge sur l’ordinateur hôte.

```bash
sudo systemctl restart iotedge
```

Déployez le conteneur d’analyse spatiale comme un module IoT sur l’ordinateur hôte à partir du [portail Azure](../../iot-edge/how-to-deploy-modules-portal.md) ou de l’[interface de ligne de commande Azure](../cognitive-services-apis-create-account-cli.md?tabs=windows). Si vous utilisez le portail, définissez l’URI de l’image sur l’emplacement de votre service Azure Container Registry. 

Procédez comme suit pour déployer le conteneur à l’aide de l’interface de ligne de commande Azure.

#### <a name="azure-vm-with-gpu"></a>[Machine virtuelle Azure avec GPU](#tab/virtual-machine)

Une machine virtuelle Azure avec un GPU peut également être utilisée pour exécuter une analyse spatiale. L’exemple ci-dessous utilisera une [machine virtuelle de série NC](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) dotée d’un GPU K80.

#### <a name="create-the-vm"></a>Création de la machine virtuelle

Ouvrez l’Assistant [Création d’une machine virtuelle](https://ms.portal.azure.com/#create/Microsoft.VirtualMachine) dans le portail Azure.

Donnez un nom à votre machine virtuelle et sélectionnez (USA) USA Ouest 2 comme région. 

> [!IMPORTANT]
> Veillez à définir `Availability Options` sur « Aucune redondance d’infrastructure requise ». Reportez-vous à la figure ci-dessous pour obtenir la configuration complète et à l’étape suivante pour trouver la bonne taille de machine virtuelle. 

:::image type="content" source="media/spatial-analysis/virtual-machine-instance-details.jpg" alt-text="Détails de la configuration de la machine virtuelle" lightbox="media/spatial-analysis/virtual-machine-instance-details.jpg":::

Pour trouver la taille de machine virtuelle, sélectionnez « Voir toutes les tailles », puis affichez la liste des « tailles de machine virtuelle de stockage non Premium », indiquée ci-dessous.

:::image type="content" source="media/spatial-analysis/virtual-machine-sizes.png" alt-text="Tailles de machines virtuelles" lightbox="media/spatial-analysis/virtual-machine-sizes.png":::

Sélectionnez ensuite **NC6** ou **NC6_Promo**.

:::image type="content" source="media/spatial-analysis/promotional-selection.png" alt-text="Sélection promotionnelle" lightbox="media/spatial-analysis/promotional-selection.png":::

Ensuite, créez la machine virtuelle. Une fois créée, accédez à la ressource de machine virtuelle dans le portail Azure et sélectionnez `Extensions` dans le volet gauche. Cliquez sur « Ajouter » pour afficher la fenêtre des extensions avec toutes les extensions disponibles. Recherchez et sélectionnez `NVIDIA GPU Driver Extension`, cliquez sur Créer, puis suivez l’Assistant.

Une fois l’extension correctement appliquée, accédez à la page principale de la machine virtuelle dans le portail Azure, puis cliquez sur `Connect`. La machine virtuelle est accessible via SSH ou RDP. RDP est utile, car il permet d’afficher la fenêtre de visualiseur (expliquée plus loin). Configurez l’accès RDP en suivant [ces étapes](../../virtual-machines/linux/use-remote-desktop.md) et en ouvrant une connexion Bureau à distance à la machine virtuelle.

### <a name="verify-graphics-drivers-are-installed"></a>Vérifier que les pilotes graphiques sont installés

Exécutez la commande suivante pour vérifier que les pilotes graphiques ont été correctement installés. 

```bash
nvidia-smi
```

Vous devez voir la sortie suivante.

![Sortie du pilote NVIDIA](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-vm"></a>Installer Docker CE et nvidia-docker2 sur la machine virtuelle

Exécutez les commandes suivantes une par une pour installer Docker CE et nvidia-docker2 sur la machine virtuelle.

Installez Docker CE sur l’ordinateur hôte.

```bash
sudo apt-get update
```
```bash
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
```
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
```bash
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```
```bash
sudo apt-get update
```
```bash
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```


Installez le package logiciel *nvidia-docker-2*.

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
```
```bash
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
```
```bash
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
```
```bash
sudo apt-get update
```
```bash
sudo apt-get install -y docker-ce nvidia-docker2
```
```bash
sudo systemctl restart docker
```

Votre machine virtuelle étant configurée, suivez les étapes ci-dessous pour configurer Azure IoT Edge. 

## <a name="configure-azure-iot-edge-on-the-vm"></a>Configurer Azure IoT Edge sur la machine virtuelle

Pour déployer le conteneur d’analyse spatiale sur la machine virtuelle, créez une instance d’un service [Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md) à l’aide du niveau tarifaire Standard (S1) ou Gratuit (F0).

Utiliser l’interface de ligne de commande Azure pour créer un service Azure IoT Hub. Remplacez les paramètres le cas échéant. Vous pouvez également créer le service Azure IoT Hub sur le [portail Azure](https://portal.azure.com/).

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```
```bash
sudo az login
```
```bash
sudo az account set --subscription "<name or ID of Azure Subscription>"
```
```bash
sudo az group create --name "<resource-group-name>" --location "<your-region>"
```
Consultez [Prise en charge des régions](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) pour les régions disponibles.
```bash
sudo az iot hub create --name "<iothub-group-name>" --sku S1 --resource-group "<resource-group-name>"
```
```bash
sudo az iot hub device-identity create --hub-name "<iothub-name>" --device-id "<device-name>" --edge-enabled
```

Vous devez installer la version 1.0.9 d’[Azure IoT Edge](../../iot-edge/how-to-provision-single-device-linux-symmetric.md). Pour télécharger la version correcte, procédez comme suit :

Ubuntu Server 18.04 :
```bash
curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
```

Copiez la liste générée.
```bash
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
```

Installez la clé publique Microsoft GPG.

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
```
```bash
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

Mettez à jour les listes de packages sur votre appareil.

```bash
sudo apt-get update
```

Installez la version 1.0.9 :

```bash
sudo apt-get install iotedge=1.0.9* libiothsm-std=1.0.9*
```

Ensuite, inscrivez la machine virtuelle en tant que périphérique IoT Edge dans votre instance IoT Hub à l’aide d’une [chaîne de connexion](../../iot-edge/how-to-provision-single-device-linux-symmetric.md#register-your-device).

Vous devez connecter l’appareil IoT Edge à votre service IoT Hub Azure. Vous devez copier la chaîne de connexion à partir de l’appareil IoT Edge que vous avez créé précédemment. Vous pouvez aussi exécuter la commande Azure CLI ci-dessous.

```bash
sudo az iot hub device-identity connection-string show --device-id my-edge-device --hub-name test-iot-hub-123
```

Ouvrez `/etc/iotedge/config.yaml` sur la machine virtuelle pour les modifications. Remplacez `ADD DEVICE CONNECTION STRING HERE` par la chaîne de connexion. Enregistrez et fermez le fichier. Exécutez cette commande pour redémarrer le service IoT Edge sur la machine virtuelle.

```bash
sudo systemctl restart iotedge
```

Déployez le conteneur d’analyse spatiale en tant que module IoT sur la machine virtuelle, depuis le [portail Azure](../../iot-edge/how-to-deploy-modules-portal.md) ou [Azure CLI](../cognitive-services-apis-create-account-cli.md?tabs=windows). Si vous utilisez le portail, définissez l’URI de l’image sur l’emplacement de votre service Azure Container Registry. 

Procédez comme suit pour déployer le conteneur à l’aide de l’interface de ligne de commande Azure.

---

### <a name="iot-deployment-manifest"></a>Manifeste de déploiement IoT

Pour simplifier le déploiement de conteneurs sur plusieurs ordinateurs hôtes, vous pouvez créer un fichier de manifeste de déploiement pour spécifier les options de création de conteneurs et les variables d’environnement. Vous trouverez un exemple de manifeste de déploiement [pour Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179), d’[autres ordinateurs de bureau](https://go.microsoft.com/fwlink/?linkid=2152270) et une [machine virtuelle Azure avec GPU](https://go.microsoft.com/fwlink/?linkid=2152189) sur GitHub.

Le tableau suivant présente les différentes variables d’environnement utilisées par le module IoT Edge. Vous pouvez également les définir dans le manifeste de déploiement lié ci-dessus, à l’aide de l’attribut `env` dans `spatialanalysis` :

| Nom du paramètre | Valeur | Description|
|---------|---------|---------|
| ARCHON_LOG_LEVEL | Info; Verbose | Niveau de journalisation, sélectionnez l’une des deux valeurs suivantes :|
| ARCHON_SHARED_BUFFER_LIMIT | 377487360 | Ne modifiez pas|
| ARCHON_PERF_MARKER| false| Affectez la valeur true à la journalisation des performances ; sinon, la valeur doit être false| 
| ARCHON_NODES_LOG_LEVEL | Info; Verbose | Niveau de journalisation, sélectionnez l’une des deux valeurs suivantes :|
| OMP_WAIT_POLICY | PASSIVE | Ne modifiez pas|
| QT_X11_NO_MITSHM | 1 | Ne modifiez pas|
| APIKEY | votre clé API| Récupérez cette valeur dans le portail Azure à partir de votre ressource Vision par ordinateur. Vous la trouverez dans la section **Clé et point de terminaison** de vos ressources. |
| FACTURATION | votre URI de point de terminaison| Récupérez cette valeur dans le portail Azure à partir de votre ressource Vision par ordinateur. Vous la trouverez dans la section **Clé et point de terminaison** de vos ressources.|
| CLUF | accepter | Cette valeur doit être définie sur *accepter* pour que le conteneur s’exécute |
| DISPLAY | :1 | Cette valeur doit être identique à la sortie de `echo $DISPLAY` sur l’ordinateur hôte. Les appareils Azure Stack Edge n’ont pas d’affichage. Ce paramètre n'est pas applicable|
| ARCHON_GRAPH_READY_TIMEOUT | 600 | Ajoutez cette variable d’environnement si votre GPU n’est **pas** T4 or NVIDIA 2080 Ti.|
| ORT_TENSORRT_ENGINE_CACHE_ENABLE | 0 | Ajoutez cette variable d’environnement si votre GPU n’est **pas** T4 or NVIDIA 2080 Ti.|
| KEY_ENV | Clé de chiffrement ASE | Ajoutez cette variable d’environnement si Video_URL est une chaîne masquée. |
| IV_ENV | Vecteur d’initialisation | Ajoutez cette variable d’environnement si Video_URL est une chaîne masquée.|

> [!IMPORTANT]
> Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas.  Pour plus d'informations, consultez [Facturation](#billing).

Une fois que vous avez mis à jour le manifeste de déploiement pour des [appareils Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179), un [appareil de bureau](https://go.microsoft.com/fwlink/?linkid=2152270) ou une [machine virtuelle Azure avec GPU](https://go.microsoft.com/fwlink/?linkid=2152189) avec vos propres paramètres et sélection d’opérations, vous pouvez utiliser la commande [Azure CLI](../cognitive-services-apis-create-account-cli.md?tabs=windows) ci-dessous pour déployer le conteneur en tant que module IoT Edge sur l’ordinateur hôte.

```azurecli
sudo az login
sudo az extension add --name azure-iot
sudo az iot edge set-modules --hub-name "<iothub-name>" --device-id "<device-name>" --content DeploymentManifest.json --subscription "<name or ID of Azure Subscription>"
```

|Paramètre  |Description  |
|---------|---------|
| `--hub-name` | Nom de votre Azure IoT Hub. |
| `--content` | Nom du fichier de déploiement. |
| `--target-condition` | Nom de votre appareil IoT Edge pour l’ordinateur hôte. |
| `-–subscription` | ID ou nom de l’abonnement. |

Cette commande démarre le déploiement. Accédez à la page de votre instance Azure IoT Hub dans le portail Azure pour afficher l’état du déploiement. L’état peut se présenter sous la forme *417 : la configuration de déploiement de l’appareil n’est pas définie* jusqu’à ce que l’appareil termine de télécharger les images du conteneur et démarre l’exécution.

## <a name="validate-that-the-deployment-is-successful"></a>Vérifiez que déploiement est réussi

Il existe plusieurs façons de confirmer que le conteneur s’exécute. Localisez la section *État du runtime* dans les **paramètres du module IoT Edge** pour le module d’analyse spatiale de votre instance Azure IoT Hub sur le portail Azure. Vérifiez que les champs **Valeur souhaitée** et **Valeur signalée** de la section *État du runtime* indiquent *En cours d’exécution*.

![Exemple de vérification du déploiement](./media/spatial-analysis/deployment-verification.png)

Une fois le déploiement terminé et le conteneur en cours d’exécution, l’**ordinateur hôte** commence à envoyer des événements à Azure IoT Hub. Si vous avez utilisé la version `.debug` des opérations, vous verrez une fenêtre de visualiseur pour chaque caméra que vous avez configurée dans le manifeste de déploiement. Vous pouvez maintenant définir les lignes et les zones que vous souhaitez analyser dans le manifeste de déploiement et suivre les instructions pour effectuer un nouveau déploiement. 

## <a name="configure-the-operations-performed-by-spatial-analysis"></a>Configurer les opérations effectuées par l’analyse spatiale

Vous devez utiliser [opérations d’analyse spatiale](spatial-analysis-operations.md) pour configurer le conteneur afin d’utiliser des caméras connectées et de configurer les opérations, entre autres. Pour chaque caméra que vous configurez, les opérations d’analyse spatiale génèrent un flux de sortie de messages JSON envoyés à votre instance Azure IoT Hub.

## <a name="use-the-output-generated-by-the-container"></a>Utiliser la sortie générée par le conteneur

Si vous souhaitez commencer à consommer la sortie générée par le conteneur, consultez les articles suivants :

*    Utilisez le kit de développement logiciel (SDK) Azure Event Hub pour le langage de programmation que vous avez choisi pour vous connecter au point de terminaison Azure IoT Hub et recevoir les événements. Pour plus d’informations, consultez [Lire des messages appareil-à-cloud à partir du point de terminaison intégré](../../iot-hub/iot-hub-devguide-messages-read-builtin.md). 
*    Configurez le routage des messages sur votre instance IoT Hub Azure pour envoyer les événements à d’autres points de terminaison ou enregistrer les événements dans votre stockage Blob Azure, etc. Pour plus d’informations, consultez [Routage des messages IoT Hub](../../iot-hub/iot-hub-devguide-messages-d2c.md). 

## <a name="running-spatial-analysis-with-a-recorded-video-file"></a>Exécution de l’analyse spatiale avec un fichier vidéo enregistré

Vous pouvez utiliser l’analyse spatiale avec des vidéos enregistrées ou en direct. Pour utiliser l’analyse spatiale pour une vidéo enregistrée, essayez d’enregistrer un fichier vidéo et de l’enregistrer en tant que fichier MP4. Créez un compte de stockage d’objets blob existant ou créez-en un nouveau. Ensuite, mettez à jour les paramètres de stockage d’objets blob suivants dans le Portail Azure :
    1. Modifiez **Transfert sécurisé requis** en **Désactivé**
    2. Modifiez **Autoriser l’accès public aux objets blob** en **Activé**

Accédez à la section **Conteneur**, puis créez un nouveau conteneur ou utilisez un conteneur existant. Téléchargez ensuite le fichier vidéo dans le conteneur. Développez les paramètres de fichier pour le fichier chargé, puis sélectionnez **Générer une SAS**. Veillez à définir une **Date d’expiration** suffisamment longue pour couvrir la période de tests. Définissez les **protocoles autorisés** sur *HTTP* (*HTTPS* n’est pas pris en charge).

Cliquez sur **Générer un jeton SAS et une URL** et copiez l’URL SAS de l’objet blob. Remplacez le `https` de départ par `http` et testez l’URL dans un navigateur qui prend en charge la lecture vidéo.

Remplacez `VIDEO_URL` dans le manifeste de déploiement pour votre [appareil Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179), [ordinateur de bureau](https://go.microsoft.com/fwlink/?linkid=2152270) ou [machine virtuelle Azure avec GPU](https://go.microsoft.com/fwlink/?linkid=2152189) par l’URL que vous avez créée, pour tous les graphiques. Définissez `VIDEO_IS_LIVE` sur `false`et redéployez le conteneur d’analyse spatiale avec le manifeste mis à jour. Reportez-vous à l’exemple ci-dessous.

Le module d’analyse spatiale va commencer à consommer un fichier vidéo et le relira automatiquement en permanence.


```json
"zonecrossing": {
    "operationId" : "cognitiveservices.vision.spatialanalysis-personcrossingpolygon",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "Replace http url here",
        "VIDEO_SOURCE_ID": "personcountgraph",
        "VIDEO_IS_LIVE": false,
      "VIDEO_DECODE_GPU_INDEX": 0,
        "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"do_calibration\": true }",
        "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0.3,0.3],[0.3,0.9],[0.6,0.9],[0.6,0.3],[0.3,0.3]], \"events\": [{\"type\": \"zonecrossing\", \"config\": {\"threshold\": 16.0, \"focus\": \"footprint\"}}]}]}"
    }
   },

```

## <a name="troubleshooting"></a>Dépannage

Si vous rencontrez des problèmes lors du démarrage ou de l’exécution du conteneur, consultez [Télémétrie et résolution des problèmes](spatial-analysis-logging.md) pour connaître les étapes de problèmes courants. Cet article contient également des informations sur la génération et la collecte de journaux et sur la collecte de l’intégrité du système.

[!INCLUDE [Diagnostic container](../containers/includes/diagnostics-container.md)]

## <a name="billing"></a>Facturation

Le conteneur d’analyse spatiale envoie des informations de facturation à Azure à l’aide d’une ressource Vision par ordinateur sur votre compte Azure. L’utilisation de l’analyse spatiale en préversion publique est actuellement gratuite. 

Les conteneurs Azure Cognitive Services ne sont pas concédés sous licence dans le but de s’exécuter sans être connectés au point de terminaison de mesure/facturation. Vous devez configurer les conteneurs de manière à ce qu’ils communiquent les informations de facturation au point de terminaison de facturation à tout moment. Les conteneurs Cognitive Services n’envoient pas de données client, telles que la vidéo ou l’image analysées, à Microsoft.


## <a name="summary"></a>Résumé

Dans cet article, vous avez découvert des concepts et le flux de travail pour le téléchargement, l’installation et l’exécution du conteneur d’analyse spatiale. En résumé :

* L’analyse spatiale est un conteneur Linux pour Docker.
* Les images conteneurs sont téléchargées à partir de Microsoft Container Registry.
* Les images de conteneur s’exécutent en tant que modules IoT dans Azure IoT Edge.
* Comment configurer le conteneur et le déployer sur un ordinateur hôte.

## <a name="next-steps"></a>Étapes suivantes

* [Déployer une application web de comptage de personnes](spatial-analysis-web-app.md)
* [Configurer les opérations d’analyse spatiale](spatial-analysis-operations.md)
* [Journalisation et résolution des problèmes](spatial-analysis-logging.md)
* [Guide de positionnement de la caméra](spatial-analysis-camera-placement.md)
* [Guide de positionnement de zone et de ligne](spatial-analysis-zone-line-placement.md)
