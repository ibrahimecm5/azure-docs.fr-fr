---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 890ec787bfc7a4316df9d27271460a90d5047175
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842679"
---
## <a name="install-iot-edge"></a>Installer IoT Edge

Déployez Azure IoT Edge pour Linux sur Windows sur votre appareil cible.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Installez IoT Edge pour Linux sur Windows sur votre appareil cible.

> [!NOTE]
> Le processus PowerShell suivant explique comment déployer IoT Edge pour Linux sur Windows sur l’appareil local. Pour effectuer un déploiement sur un appareil cible distant avec PowerShell, vous pouvez utiliser [PowerShell distant](/powershell/module/microsoft.powershell.core/about/about_remote) afin d’établir une connexion à un appareil distant et d’exécuter ces commandes à distance sur cet appareil.

1. Dans une session PowerShell avec élévation de privilèges, exécutez chacune des commandes suivantes pour télécharger IoT Edge pour Linux sur Windows.

   ```powershell
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. Installez IoT Edge pour Linux sur Windows sur votre appareil.

   ```powershell
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   Vous pouvez spécifier des répertoires personnalisés d’installation d’IoT Edge pour Linux sur Windows et VHDX en ajoutant les paramètres `INSTALLDIR="<FULLY_QUALIFIED_PATH>"` et `VHDXDIR="<FULLY_QUALIFIED_PATH>"` à la commande d’installation.

1. Définissez la stratégie d’exécution sur l’appareil cible sur `AllSigned` si ce n’est pas déjà fait. Consultez la configuration requise des commandes PowerShell pour vérifier la stratégie d’exécution actuelle et définir la stratégie d’exécution sur `AllSigned`.

1. Créer le déploiement d’IoT Edge pour Linux sur Windows. Le déploiement crée votre machine virtuelle Linux et installe le runtime IoT Edge pour vous.

   ```powershell
   Deploy-Eflow
   ```

   >[!TIP]
   >Par défaut, la commande `Deploy-Eflow` crée votre machine virtuelle Linux avec 1 Go de RAM, 1 noyau de processeur virtuel et 16 Go d’espace disque. Toutefois, les ressources dont votre machine virtuelle a besoin dépendent fortement des charges de travail que vous déployez. Si votre machine virtuelle ne dispose pas de suffisamment de mémoire pour prendre en charge vos charges de travail, elle ne démarrera pas.
   >
   >Vous pouvez personnaliser les ressources disponibles de la machine virtuelle à l’aide des paramètres facultatifs de la commande `Deploy-Eflow`.
   >
   >Par exemple, la commande ci-dessous crée une machine virtuelle avec 4 cœurs de processeur virtuel, 4 Go de RAM (quantité représentée en Mo) et 20 Go d’espace disque :
   >
   >   ```powershell
   >   Deploy-Eflow -cpuCount 4 -memoryInMB 4096 -vmDiskSize 20
   >   ```
   >
   >Pour plus d’informations sur tous les paramètres facultatifs disponibles, consultez [Fonctions PowerShell pour IoT Edge pour Linux sur Windows](../articles/iot-edge/reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow).

   Vous pouvez affecter un GPU à votre déploiement pour permettre l’utilisation de modules Linux avec accélération GPU. Pour accéder à ces fonctionnalités, vous devez installer les composants requis détaillés dans [Accélération GPU pour Azure IoT Edge pour Linux sur Windows](../articles/iot-edge/gpu-acceleration.md).

   Pour utiliser un relais GPU, ajoutez les paramètres **gpuName**, **gpuPassthroughType** et **gpuCount** à votre commande `Deploy-Eflow`. Pour plus d’informations sur tous les paramètres facultatifs disponibles, consultez [Fonctions PowerShell pour IoT Edge pour Linux sur Windows](../articles/iot-edge/reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow).

   >[!WARNING]
   >L’activation du relais d’appareils matériels peut augmenter les risques liés à la sécurité. Microsoft vous recommande de recourir à un pilote d’atténuation des appareils du fournisseur de votre GPU, le cas échéant. Pour plus d’informations, consultez [Déploiement d’appareils graphiques avec la technologie DDA (Discrete Device Assignment)](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda).

1. Entrez « Y » pour accepter les termes du contrat de licence.

1. Entrez « O » ou « R » pour activer ou désactiver les **données de diagnostic facultatives** à votre convenance.

1. Une fois le déploiement terminé, la fenêtre PowerShell signale **Déploiement réussi**.

   ![Un déploiement réussi indique « Déploiement réussi » à la fin des messages.](./media/iot-edge-install-linux-on-windows/successful-powershell-deployment.png)

   Une fois le déploiement réussi, vous êtes prêt à provisionner votre appareil.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

>[!NOTE]
>L’extension Azure IoT Edge pour Windows Admin Center est actuellement en [préversion publique](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Les processus d’installation et de gestion peuvent différer de ceux des fonctionnalités généralement disponibles.

Installez Azure IoT Edge pour Linux sur Windows sur votre appareil.

1. Téléchargez le [programme d’installation Azure IoT Edge pour Linux sur Windows](https://aka.ms/AzEflowMSI).

1. Après avoir installé Azure IoT Edge pour Linux sur Windows, ouvrez Windows Admin Center.

   Sur la page d’accueil de Windows Admin Center, sous la liste des connexions figure une connexion d’hôte local qui représente le PC sur lequel Windows Admin Center est exécuté. Les autres serveurs, ordinateurs ou clusters que vous gérez s’affichent également ici.

   Vous pouvez utiliser Windows Admin Center pour installer et gérer Azure IoT Edge pour Linux sur Windows sur votre appareil local ou des appareils gérés distants. Dans ce guide, la connexion de l’hôte local servira d’appareil cible pour le déploiement d’Azure IoT Edge pour Linux sur Windows.

1. Vérifiez que votre appareil local est répertorié sous **Toutes les connexions**, comme indiqué ci-dessous.

   ![Tableau de bord initial Windows Admin Center avec l’appareil cible figurant dans la liste, PNG.](./media/iot-edge-install-linux-on-windows/windows-admin-center-initial-dashboard.png)

   Si vous préférez effectuer un déploiement sur un appareil cible distant plutôt que sur votre appareil local, et que l’appareil cible souhaité ne figure pas dans la liste, suivez les [instructions pour ajouter votre appareil](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters).

1. Sélectionnez **+ Ajouter** pour commencer à créer votre déploiement. Le déploiement crée votre machine virtuelle Linux et installe le runtime IoT Edge pour vous.

1. Dans le volet **Ajouter ou créer des ressources**, localisez la vignette **Azure IoT Edge**. Sélectionnez **Créer** pour installer une nouvelle instance d’Azure IoT Edge pour Linux sur un appareil.

   Si IoT Edge pour Linux sur Windows est déjà exécuté sur un appareil, vous pouvez sélectionner **Ajouter** pour vous connecter à cet appareil IoT Edge existant et le gérer avec Windows Admin Center.

   ![Sélectionnez Créer sur la vignette Azure IoT Edge dans Windows Admin Center.](./media/iot-edge-install-linux-on-windows/resource-creation-tiles.png)

1. Le volet **Créer un déploiement d’Azure IoT Edge pour Linux sur Windows** s’ouvre. Sur l’onglet **1. Prise en main**, vérifiez la configuration minimale requise, puis sélectionnez **Suivant**.

1. Passez en revue les termes du contrat de licence, cochez la case **J’accepte** et sélectionnez **Suivant**.

1. Vous pouvez activer ou désactiver les **données de diagnostic facultatives** à votre convenance.

1. Sélectionnez **Suivant : Déployer**.

   ![Sélectionnez le bouton Suivant : Déployer après avoir activé ou désactivé les données de diagnostic facultatives à votre convenance.](./media/iot-edge-install-linux-on-windows/select-next-deploy.png)

1. Dans l’onglet **2. Déployer**, sous **Sélectionner un appareil cible**, cliquez sur votre appareil dans la liste pour vérifier qu’il respecte la configuration minimale requise. Lorsque son état indique qu’il est pris en charge, sélectionnez **Suivant**.

   ![Sélectionnez votre appareil pour vérifier qu’il est pris en charge.](./media/iot-edge-install-linux-on-windows/evaluate-supported-device.png)

1. Sous l’onglet **Paramètres 2.2**, vérifiez les paramètres de configuration de votre déploiement. Le tableau ci-dessous présente les paramètres et leurs valeurs par défaut.

   | Paramètre | Valeur par défaut |
   | ------- | ------------- |
   | Taille du disque virtuel en Go | 16 |
   | Mémoire en Mo | 1 024 |
   | Nombre de mémoires à tores magnétiques | 2 |
   | Version Azure IoT Edge | 1.1 (LTS) |

   >[!NOTE]
   >IoT Edge pour Linux sur Windows utilise un commutateur par défaut qui attribue une adresse IP interne à la machine virtuelle Linux. Cette adresse IP interne ne peut pas être atteinte en dehors de l’ordinateur Windows. Vous pouvez vous connecter en local à la machine virtuelle tout en ayant une session ouverte sur l’ordinateur Windows.
   >
   >Si vous utilisez Windows Server, [configurez un commutateur par défaut](../articles/iot-edge/how-to-create-virtual-switch.md) avant de déployer IoT Edge pour Linux sur Windows.

   Vous pouvez affecter un GPU à votre déploiement pour permettre l’utilisation de modules Linux avec accélération GPU. Pour accéder à ces fonctionnalités, vous devez installer les composants requis détaillés dans [Accélération GPU pour Azure IoT Edge pour Linux sur Windows](../articles/iot-edge/gpu-acceleration.md). Si vous n’installez ces composants requis qu’à ce stade du processus de déploiement, vous devez recommencer du début.

   Deux options sont disponibles pour le relais GPU disponible : **Direct Device Assignment (DDA)** et **GPU Paravirtualization (GPU-PV)** , en fonction de l’adaptateur GPU attribué au déploiement.

   Pour la méthode DDA, sélectionnez le nombre de processeurs GPU à allouer à votre machine virtuelle Linux.

   Pour la méthode PV, aucun paramètre supplémentaire n’est nécessaire.

   >[!WARNING]
   >L’activation du relais d’appareils matériels peut augmenter les risques liés à la sécurité. Microsoft vous recommande de recourir à un pilote d’atténuation des appareils du fournisseur de votre GPU, le cas échéant. Pour plus d’informations, consultez [Déploiement d’appareils graphiques avec la technologie DDA (Discrete Device Assignment)](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda).

   Une fois que vous êtes satisfait des paramètres, sélectionnez **Suivant**.

1. Dans l’onglet **2.3. Déploiement**, vous pouvez suivre la progression du déploiement. Le processus complet comprend le téléchargement du package Azure IoT Edge pour Linux sur Windows, l’installation du package, la configuration de l’appareil hôte et la configuration de la machine virtuelle Linux. Ce processus peut prendre plusieurs minutes. Voici l’illustration d’un déploiement réussi.

   ![Dans un déploiement réussi, chaque étape comporte une coche verte et la mention « Terminé ».](./media/iot-edge-install-linux-on-windows/successful-deployment.png)

1. Une fois votre déploiement terminé, vous êtes prêt à approvisionner votre appareil. Sélectionnez **Suivant : Connecter** pour passer à l’onglet **3. Connecter**, qui gère l’approvisionnement de l’appareil Azure IoT Edge.

---
