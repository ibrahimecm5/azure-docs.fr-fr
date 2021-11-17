---
title: Créer et provisionner un appareil IoT Edge sur Windows à l’aide de clés symétriques - Azure IoT Edge | Microsoft Docs
description: Créer et provisionner un appareil IoT Edge sur Windows dans IoT Hub en utilisant le provisionnement manuel avec des clés symétriques
author: kgremban
ms.reviewer: v-tcassi
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: kgremban
monikerRange: iotedge-2018-06
ms.openlocfilehash: 4c831e12c7660727c966d1fdc9d0079d8b236fa5
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853355"
---
# <a name="create-and-provision-an-iot-edge-device-on-windows-using-symmetric-keys"></a>Créer et provisionner un appareil IoT Edge sur Windows avec des clés symétriques

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Cet article fournit des instructions de bout en bout pour l’inscription et le provisionnement d’un appareil IoT Edge sur Windows.

>[!NOTE]
>Azure IoT Edge pour conteneurs Windows ne sera pas pris en charge à partir de la version 1.2 d’Azure IoT Edge.
>
>Envisagez d’utiliser la nouvelle méthode pour exécuter IoT Edge sur des appareils Windows, [Azure IoT Edge pour Linux sur Windows](iot-edge-for-linux-on-windows.md).
>
>Si vous souhaitez utiliser Azure IoT Edge pour Linux sur Windows, vous pouvez effectuer les étapes décrites dans le [guide pratique équivalent](how-to-provision-single-device-linux-on-windows-symmetric.md).

Chaque appareil qui se connecte à un hub IoT possède un ID d’appareil qui sert au suivi des communications cloud-à-appareil ou appareil-à-cloud. Vous configurez un appareil avec ses informations de connexion, qui comprennent le nom d’hôte du hub IoT, l’ID d’appareil ainsi que les informations que l’appareil utilise pour s’authentifier auprès d’IoT Hub.

Les étapes de cet article suivent un processus appelé approvisionnement manuel, qui consiste à connecter un seul appareil à son hub IoT. Avec le provisionnement manuel, vous avez le choix entre deux méthodes d’authentification des appareils IoT Edge :

* **Clés symétriques** : quand vous créez une identité d’appareil dans IoT Hub, le service crée deux clés. Vous placez l’une des clés sur l’appareil, lequel présente la clé à IoT Hub au moment de l’authentification.

  Cette méthode d’authentification est plus rapide pour commencer, mais moins sûre que l’autre.

* **Par certificat autosigné X.509** : vous créez deux certificats d’identité X.509, que vous placez sur l’appareil. Quand vous créez une identité d’appareil dans IoT Hub, vous fournissez les empreintes numériques des deux certificats. Quand l’appareil s’authentifie auprès de IoT Hub, il présente un certificat et IoT Hub vérifie que le certificat correspond à son empreinte numérique.

  Cette méthode d’authentification étant plus sécurisée, elle est recommandée dans les scénarios de production.

Cet article traite de l’utilisation de clés symétriques comme méthode d’authentification. Si vous souhaitez utiliser des certificats X.509, consultez [Créer et provisionner un appareil IoT Edge sur Windows à l’aide de certificats X.509](how-to-provision-single-device-windows-x509.md).

> [!NOTE]
> Si vous avez de nombreux appareils à configurer et que vous ne souhaitez pas les provisionner manuellement, consultez l’un des articles suivants pour découvrir comment IoT Edge fonctionne avec le service IoT Hub Device Provisioning :
>
> * [Créer et provisionner des appareils IoT Edge à grande échelle à l’aide de certificats X.509](how-to-provision-devices-at-scale-windows-x509.md)
> * [Créer et provisionner des appareils IoT Edge à grande échelle avec un module TPM](how-to-provision-devices-at-scale-windows-tpm.md)
> * [Créer et provisionner des appareils IoT Edge à grande échelle à l’aide de clés symétriques](how-to-provision-devices-at-scale-windows-symmetric.md)

## <a name="prerequisites"></a>Prérequis

Cet article traite de l’inscription de votre appareil IoT Edge et de l’installation d’IoT Edge sur l’appareil. Ces tâches ont des conditions préalables et différents utilitaires sont utilisés pour les accomplir. Assurez-vous que toutes les conditions préalables sont remplies avant de continuer.

<!-- Device registration prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-register-device.md](../../includes/iot-edge-prerequisites-register-device.md)]

### <a name="iot-edge-installation"></a>Installation d’IoT Edge

Un appareil Windows.

IoT Edge avec des conteneurs Windows requiert Windows version 1809/Build 17763, qui est la [build prise en charge à long terme de Windows](/windows/release-information/)la plus récente. Veillez à consulter la [liste de systèmes pris en charge](support.md#operating-systems) pour obtenir la liste des références SKU prises en charge.

<!-- Register your device and View provisioning information H2s and content -->
[!INCLUDE [iot-edge-register-device-symmetric.md](../../includes/iot-edge-register-device-symmetric.md)]

<!-- Install IoT Edge on Windows H2 and content -->
[!INCLUDE [install-iot-edge-windows.md](../../includes/iot-edge-install-windows.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>Provisionnement de l’appareil avec son identité cloud

Maintenant que le moteur de conteneur et le runtime d’IoT Edge sont installés sur votre appareil, vous pouvez passer à l’étape suivante, à savoir configurer l’appareil avec ses informations d’identité et d’authentification cloud.

1. Sur l’appareil IoT Edge, exécutez PowerShell en tant qu’administrateur.

2. Utilisez la commande [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) pour configurer le runtime IoT Edge sur votre ordinateur. Par défaut, la commande est une commande d’approvisionnement manuel avec des conteneurs Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * Si vous avez téléchargé le script IoTEdgeSecurityDaemon.ps1 sur votre appareil pour une installation hors connexion ou l’installation d’une version spécifique, veillez à référencer la copie locale du script.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
      ```

3. Lorsque vous y êtes invité, spécifiez la chaîne de connexion de l’appareil que vous avez récupérée dans la section précédente. La chaîne de connexion de l’appareil associe l’appareil physique à un ID d’appareil dans IoT Hub et fournit des informations d’authentification.

   La chaîne de connexion se présente au format suivant et ne doit pas contenir de guillemets : `HostName={IoT_hub_name}.azure-devices.net;DeviceId={device_name};SharedAccessKey={key}`

Quand vous provisionnez un appareil manuellement, vous pouvez utiliser des paramètres supplémentaires pour changer le processus, à savoir :

* Diriger le trafic pour qu’il transite par un serveur proxy,
* Déclarer une image conteneur d’agent Edge spécifique, et fournir des informations d’identification si l’image se trouve dans un registre privé

Pour plus d’informations sur ces paramètres supplémentaires, consultez [Scripts PowerShell pour IoT Edge sur conteneurs Windows](reference-windows-scripts.md).

## <a name="verify-successful-configuration"></a>Vérification de la réussite de la configuration

Vérifiez que le runtime a été correctement installé et configuré sur votre appareil IoT Edge.

Vérifiez l’état du service IoT Edge.

```powershell
Get-Service iotedge
```

Consultez les journaux d’activité de service.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Répertoriez les modules en cours d’exécution.

```powershell
iotedge list
```

## <a name="offline-or-specific-version-installation-optional"></a>Installation d’une version spécifique hors connexion (facultatif)

Les étapes de cette section concernent les scénarios non couverts par les étapes d’installation standard. Cela peut inclure :

* Installer IoT Edge en mode hors connexion
* Installer une version Release candidate
* Installer une version autre que la version la plus récente

Lors de l’installation, trois fichiers sont téléchargés :

* Un script PowerShell, qui contient les instructions d’installation
* Le fichier cab Microsoft Azure IoT Edge, contenant le démon de sécurité IoT Edge (iotedged), le moteur du conteneur Moby et Moby CLI
* Programme d’installation du package Visual C++ Redistribuable (Runtime VC)

Si votre appareil est hors connexion pendant l’installation, ou si vous souhaitez installer une version spécifique d’IoT Edge, vous pouvez télécharger à l’avance ces fichiers sur l’appareil. Quand il est temps d’effectuer l’installation, pointez le script d’installation sur le répertoire contenant les fichiers téléchargés. Le programme d’installation commence par vérifier le répertoire, puis télécharge uniquement les composants qui ne s’y trouvent pas. Si tous les fichiers sont disponibles hors connexion, vous pouvez procéder à l’installation sans connexion Internet.

1. Pour accéder aux derniers fichiers d’installation IoT Edge ainsi qu’aux versions précédentes, voir les [publications d’Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

2. Recherchez la version que vous souhaitez installer, puis téléchargez les fichiers suivants de la section **Ressources** des notes de publication vers votre appareil IoT :

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab du canal de distribution 1.1.

   Il est important d’utiliser le script PowerShell de la même version que le fichier .cab que vous utilisez, car les fonctionnalités changent pour prendre en charge les fonctionnalités de chaque version.

3. Si le fichier .cab que vous avez téléchargé est doté d’un suffixe d’architecture, renommez le fichier uniquement **Microsoft-Azure-IoTEdge.cab**.

4. Éventuellement, téléchargez un programme d’installation pour Visual C++ Redistributable. Par exemple, le script PowerShell utilise cette version : [vc_redist.x64. exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Enregistrez le programme d’installation dans le même dossier que les fichiers IoT Edge sur votre appareil.

5. Pour installer avec des composants hors connexion, [effectuez un appel de source de type « dot source »](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) de la copie locale du script PowerShell.

6. Exécutez la commande [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) avec le paramètre `-OfflineInstallationPath`. Indiquez le chemin d’accès absolu au répertoire de fichiers. Par exemple,

   ```powershell
   . path_to_powershell_module_here\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath path_to_file_directory_here
   ```

   La commande de déploiement utilise les composants trouvés dans le répertoire de fichiers local fourni. S’il manque le fichier .cab ou le programme d'installation de Visual C++, elle tente de les télécharger.

## <a name="uninstall-iot-edge"></a>Désinstaller IoT Edge

Si vous souhaitez supprimer l’installation d’IoT Edge de votre appareil Windows, utilisez la commande [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) à partir d’une fenêtre PowerShell d’administration. Cette commande supprime le runtime IoT Edge, ainsi que votre configuration existante et les données du moteur Moby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Pour plus d’informations sur les options de désinstallation, utilisez la commande `Get-Help Uninstall-IoTEdge -full`.

## <a name="next-steps"></a>Étapes suivantes

Passez à [Déployer des modules IoT Edge](how-to-deploy-modules-portal.md) pour savoir comment déployer des modules sur votre appareil.
