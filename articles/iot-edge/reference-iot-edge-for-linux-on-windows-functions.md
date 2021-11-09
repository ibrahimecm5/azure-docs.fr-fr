---
title: Fonctions PowerShell pour Azure IoT Edge pour Linux sur Windows | Microsoft Docs
description: Informations de référence sur les fonctions PowerShell pour Azure IoT Edge pour Linux sur Windows pour déployer, approvisionner et obtenir l’état IoT Edge pour Linux sur des machines virtuelles Windows.
author: v-tcassi
ms.author: fcabrera
ms.date: 10/15/2021
ms.topic: reference
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 9038f8b99728b2808cb1d4cf6b23a7673fa5d92e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131085902"
---
# <a name="powershell-functions-for-iot-edge-for-linux-on-windows"></a>Fonctions PowerShell pour IoT Edge pour Linux sur Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Découvrez les fonctions PowerShell qui déploient, approvisionnent et obtiennent l’état de votre IoT Edge pour Linux sur une machine virtuelle Windows (EFLOW).

## <a name="prerequisites"></a>Prérequis

Les commandes décrites dans cet article proviennent du fichier `AzureEFLOW.psm1`, qui se trouve sur votre système dans votre répertoire `WindowsPowerShell` sous `C:\Program Files\WindowsPowerShell\Modules\AzureEFLOW`.

Si vous n’avez pas le dossier **AzureEflow** dans votre répertoire PowerShell, suivez les étapes ci-dessous pour télécharger et installer Azure IoT Edge pour Linux sur Windows : 

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

   Vous pouvez spécifier des répertoires d’installation et VHDX personnalisés en ajoutant les paramètres `INSTALLDIR="<FULLY_QUALIFIED_PATH>"` and `VHDXDIR="<FULLY_QUALIFIED_PATH>"`à la commande d’installation.

1. Définissez la stratégie d’exécution sur l’appareil cible sur `AllSigned` si ce n’est pas déjà fait.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

## <a name="connect-eflowvm"></a>Connect-EflowVm

La commande **Connect-EflowVm** se connecte à la machine virtuelle via SSH. Le seul compte autorisé à accéder via SSH à la machine virtuelle est celui de l’utilisateur qui l’a créée.

Cette commande fonctionne uniquement sur une session PowerShell en cours d’exécution sur l’appareil hôte. Elle ne fonctionnera pas lors de l’utilisation du centre d’administration Windows ou de PowerShell ISE.

Pour plus d'informations, utilisez la commande `Get-Help Connect-EflowVm -full`.

## <a name="copy-eflowvmfile"></a>Copy-EflowVmFile

La commande **Copy-EflowVmFile** copie le fichier vers ou à partir de la machine virtuelle à l’aide de SCP. Utilisez les paramètres facultatifs pour spécifier les chemins d’accès aux fichiers source et de destination, ainsi que la direction de la copie.

L’utilisateur **iotedge-user** doit disposer de l’autorisation de lecture sur tous les répertoires d’origine ou d’une autorisation d’écriture sur les répertoires de destination de l’ordinateur virtuel.

| Paramètre | Valeurs acceptées | Commentaires |
| --------- | --------------- | -------- |
| fromFile | Chaîne représentant le chemin d’accès au fichier | Définit le fichier à partir duquel effectuer la lecture. |
| toFile | Chaîne représentant le chemin d’accès au fichier |  Définit le fichier dans lequel écrire. |
| pushFile | Aucun | Cet indicateur indique la direction de la copie. Si présent, la commande transmet le fichier à la machine virtuelle. Si absent, la commande extrait le fichier à partir de la machine virtuelle. |

Pour plus d'informations, utilisez la commande `Get-Help Copy-EflowVMFile -full`.

## <a name="deploy-eflow"></a>Deploy-Eflow

La commande **Deploy-Eflow** est la principale méthode de déploiement. La commande de déploiement crée la machine virtuelle, approvisionne des fichiers et déploie le module d’agent IoT Edge. Bien qu’aucun des paramètres ne soit obligatoire, ils peuvent être utilisés pour modifier les paramètres de la machine virtuelle pendant sa création.

| Paramètre | Valeurs acceptées | Commentaires |
| --------- | --------------- | -------- |
| acceptEula | **Oui** ou **Non** | Raccourci pour accepter/refuser le CLUF et contourner l’invite du CLUF. |
| acceptOptionalTelemetry | **Oui** ou **Non** |  Raccourci permettant d’accepter/de refuser la télémétrie facultative et de contourner l’invite de télémétrie. |
| cpuCount | Valeur entière comprise entre 1 et les cœurs d’UC de l’appareil |  Nombre de cœurs de processeur pour la machine virtuelle.<br><br>**Valeur par défaut**: 1 vCore. |
| memoryInMB | Valeur entière comprise entre 1 024 et la quantité maximale de mémoire disponible de l’appareil |Mémoire allouée pour la machine virtuelle.<br><br>**DValeur par défaut**  : 1 024 Mo. |
| vmDiskSize | Entre 8 Go et 256 Go | Taille de disque maximale du disque dur virtuel de taille dynamique.<br><br>**Valeur par défaut** : 10 Go. |
| vswitchName | Nom du commutateur virtuel |  Nom du commutateur virtuel affecté à la machine virtuelle EFLOW. |
| vswitchType | **Interne** ou **externe** | Nom du commutateur virtuel affecté à la machine virtuelle EFLOW. |
| ip4Address | Adresse IPv4 dans la plage de l’étendue du serveur DCHP | Adresse IPv4 statique de la machine virtuelle EFLOW. |
| ip4PrefixLength | Longueur du préfixe IPv4 du sous-réseau | Longueur de préfixe de sous-réseau IPv4 uniquement valide lorsque l’adresse IPv4 statique est spécifiée. |
| ip4GatewayAddress | Adresse IPv4 de la passerelle de sous-réseau | Adresse IPv4 de la passerelle, valide uniquement lorsque l’adresse IPv4 statique est spécifiée. |
| gpuName | Nom du Périphérique GPU |  Nom du périphérique GPU à utiliser pour le relais. |
| gpuPassthroughType | **DirectDeviceAssignment**, **ParaVirtualization**, ou aucun (CPU uniquement) |  Type de relais GPU |
| gpuCount | Valeur entière comprise entre 1 et le nombre de cœurs GPU de l’appareil | Nombre de périphériques GPU pour la machine virtuelle. <br><br>**Remarque** : Si vous utilisez Paravirtualization, veillez à définir gpuCount = 1 |

Pour plus d'informations, utilisez la commande `Get-Help Deploy-Eflow -full`.  

## <a name="get-eflowhostconfiguration"></a>Get-EflowHostConfiguration

La commande **Get-EflowHostConfiguration** renvoie la configuration de l’hôte. Cette commande n’accepte aucun paramètre. Elle retourne un objet qui contient quatre propriétés :

* FreePhysicalMemoryInMB
* NumberOfLogicalProcessors
* DiskInfo
* GpuInfo

Pour plus d'informations, utilisez la commande `Get-Help Get-EflowHostConfiguration -full`.

## <a name="get-eflowlogs"></a>Get-EflowLogs

La commande **Get-EflowLogs** collecte et regroupe les journaux et les packs ed’IoT Edge pour le déploiement et l’installation de Linux sur Windows. Elle génère les journaux regroupés sous la forme d’un dossier `.zip`.

Pour plus d'informations, utilisez la commande `Get-Help Get-EflowLogs -full`.

## <a name="get-eflowvm"></a>Get-EflowVm

La commande **Get-EflowVm** renvoie la configuration actuelle de la machine virtuelle. Cette commande n’accepte aucun paramètre. Elle retourne un objet qui contient quatre propriétés :

* VmConfiguration
* EdgeRuntimeVersion
* EdgeRuntimeStatus
* SystemStatistics

Pour afficher une propriété spécifique dans une liste lisible, exécutez la commande `Get-EflowVM` avec la propriété développée. Par exemple :

```powershell
Get-EflowVM | Select -ExpandProperty VmConfiguration | Format-List
```

Pour plus d'informations, utilisez la commande `Get-Help Get-EflowVm -full`.

## <a name="get-eflowvmaddr"></a>Get-EflowVmAddr

La commande **Get-EflowVmAddr** est utilisée pour interroger les adresses IP et Mac de la machine virtuelle. Cette commande existe afin de tenir compte du fait que les adresses IP et Mac peuvent changer dans le temps.

Pour toute information supplémentaire, utilisez la commande `Get-Help Get-EflowVmAddr -full`.

## <a name="get-eflowvmfeature"></a>Get-EflowVmFeature

La commande **Get-EflowVmFeature** renvoie l’état de l’activation d’IoT Edge pour les fonctionnalités Linux sur Windows.

| Paramètre | Valeurs acceptées | Commentaires |
| --------- | --------------- | -------- |
| fonctionnalité | **DpsTpm** | Nom de la fonctionnalité à interroger. |

Pour plus d'informations, utilisez la commande `Get-Help Get-EflowVmFeature -full`.

## <a name="get-eflowvmname"></a>Get-EflowVmName

La commande **Get-EflowVmName** renvoie le nom d’hôte actuel de la machine virtuelle. Cette commande permet de tenir compte du fait que le nom d’hôte Windows peut changer dans le temps.

Pour plus d'informations, utilisez la commande `Get-Help Get-EflowVmName -full`.

## <a name="get-eflowvmtelemetryoption"></a>Get-EflowVmTelemetryOption

La commande **Get-EflowVmTelemetryOption** affiche l’état de la télémétrie (soit **Facultatif** soit **Requis**) au sein de la machine virtuelle.

Pour plus d'informations, utilisez la commande `Get-Help Get-EflowVmTelemetryOption -full`.

## <a name="get-eflowvmtpmprovisioninginfo"></a>Get-EflowVmTpmProvisioningInfo

La commande **Get-EflowVmTpmProvisioningInfo** renvoie les informations d’approvisionnement du Module de plateforme sécurisée (TPM). Cette commande n’accepte aucun paramètre. Elle retourne un objet qui contient quatre propriétés :

* EK (Endorsement Key)
* ID d’inscription

Pour plus d'informations, utilisez la commande `Get-Help Get-EflowVmTpmProvisioningInfo -full`.

## <a name="invoke-eflowvmcommand"></a>Invoke-EflowVmCommand

La commande **Invoke-EflowVMCommand** exécute une commande Linux dans la machine virtuelle et renvoie le résultat. Cette commande fonctionne uniquement pour les commandes Linux qui retournent une sortie finie. Elle ne peut pas être utilisée pour les commandes Linux qui nécessitent une interaction de l’utilisateur ou qui s’exécutent indéfiniment.

Les paramètres facultatifs suivants peuvent être utilisés pour spécifier la commande à l’avance.

| Paramètre | Valeurs acceptées | Commentaires |
| --------- | --------------- | -------- |
| commande | String | Commande à exécuter sur la machine virtuelle. |
| ignoreError | Aucun |  Si cet indicateur est présent, ignorez les erreurs de la commande. |

Pour plus d'informations, utilisez la commande `Get-Help Invoke-EflowVmCommand -full`.

## <a name="provision-eflowvm"></a>Provision-EflowVm

La commande **Provision-EflowVm** ajoute les informations d’approvisionnement de votre appareil IoT Edge au fichier `config.yaml` IoT Edge de la machine virtuelle.

| Paramètre | Valeurs acceptées | Commentaires |
| --------- | --------------- | -------- |
| provisioningType | **ManualConnectionString**, **ManualX509**, **DpsTPM**, **DpsX509** ou **DpsSymmetricKey** |  Définit le type d’approvisionnement que vous souhaitez utiliser pour votre appareil IoT Edge. |
| devConnString | Chaîne de connexion d’un appareil IoT Edge existant | Chaîne de connexion d’appareil pour l’approvisionnement manuel d’un appareil IoT Edge (**ManualConnectionString**). |
| iotHubHostname | Nom d’hôte d’un IoT hub existant | Nom d’hôte Azure IoT Hub pour l’approvisionnement d’un appareil IoT Edge (**ManualX509**). |
| deviceId | ID d’appareil d’un appareil IoT Edge existant | ID d’appareil pour l’approvisionnement d’un appareil IoT Edge (**ManualX509**). |
| scopeId | ID d’étendue pour une instance DPS existante. | ID d’étendue pour l’approvisionnement d’un appareil IoT Edge (**DpsTPM**, **DpsX509**, ou **DpsSymmetricKey**). |
| symmKey | Clé primaire pour une inscription DPS existante ou clé primaire d’un appareil IoT Edge existant inscrit à l’aide de clés symétriques | Clé symétrique pour l’approvisionnement d’un appareil IoT Edge (**DpsSymmetricKey**). |
| registrationId | ID d’inscription d’un appareil IoT Edge existant | ID d’inscription pour l’approvisionnement d’un appareil IoT Edge (**DpsSymmetricKey**). |
| identityCertPath | Chemin du répertoire | Chemin d’accès de la destination absolue du certificat d’identité sur votre machine hôte Windows (**ManualX509**, **DpsX509**). |
| identityPrivKeyPath | Chemin du répertoire | Chemin d’accès de la destination absolue de la clé privée d’identité sur votre machine hôte Windows (**ManualX509**, **DpsX509**). |

Pour plus d'informations, utilisez la commande `Get-Help Provision-EflowVm -full`.

## <a name="set-eflowvm"></a>Set-EflowVM

La commande **Set-EflowVM** actualise la configuration de la machine virtuelle avec les propriétés requises. Utilisez les paramètres facultatifs pour définir une configuration spécifique pour la machine virtuelle.

| Paramètre | Valeurs acceptées | Commentaires |
| --------- | --------------- | -------- |
| cpuCount | Valeur entière comprise entre 1 et les cœurs d’UC de l’appareil | Nombre de cœurs de processeur pour la machine virtuelle. |
| memoryInMB | Valeur entière comprise entre 1 024 et la quantité maximale de mémoire disponible de l’appareil | Mémoire allouée pour la machine virtuelle. |
| gpuName | Nom du Périphérique GPU |  Nom du périphérique GPU à utiliser pour le relais. |
| gpuPassthroughType | **DirectDeviceAssignment**, **ParaVirtualization**, ou aucun (pas de relais) |  Type de relais GPU |
| gpuCount | Valeur entière comprise entre 1 et les cœurs GPU de l’appareil | Nombre d’appareils GPU pour la machine virtuelle. **Remarque** : uniquement valide en cas d’utilisation de DirectDeviceAssignment |
| sans affichage | Aucun | Si cet indicateur est présent, il détermine si l’utilisateur doit confirmer en cas d’émission d’un avertissement de sécurité. |

Pour plus d'informations, utilisez la commande `Get-Help Set-EflowVM -full`.

## <a name="set-eflowvmfeature"></a>Set-EflowVmFeature

La commande **Set-EflowVmFeature** active ou désactive l’état d’IoT Edge pour les fonctionnalités Linux sur Windows.

| Paramètre | Valeurs acceptées | Commentaires |
| --------- | --------------- | -------- |
| fonctionnalité | **DpsTpm** | Nom de la fonctionnalité à activer/désactiver. |
| enable | Aucun | Si cet indicateur est présent, la commande active la fonctionnalité. |

Pour plus d'informations, utilisez la commande `Get-Help Set-EflowVmFeature -full`.

## <a name="set-eflowvmtelemetryoption"></a>Set-EflowVmTelemetryOption

La commande **Set-EflowVmTelemetryOption** active ou désactive la télémétrie facultative au sein de la machine virtuelle.

| Paramètre | Valeurs acceptées | Commentaires |
| --------- | --------------- | -------- |
| optionalTelemetry | **True** ou **False** | Si la télémétrie facultative est sélectionnée. |

Pour plus d'informations, utilisez la commande `Get-Help Set-EflowVmTelemetryOption -full`.

## <a name="start-eflowvm"></a>Start-EflowVm

La commande **Start-EflowVm** démarre la machine virtuelle. Si la machine virtuelle est déjà démarrée, aucune action n’est effectuée.

Pour plus d'informations, utilisez la commande `Get-Help Start-EflowVm -full`.

## <a name="stop-eflowvm"></a>Stop-EflowVm

La commande **Stop-EflowVm** arrête la machine virtuelle. Si la machine virtuelle est déjà arrêtée, aucune action n’est effectuée.

Pour plus d'informations, utilisez la commande `Get-Help Stop-EflowVm -full`.

## <a name="verify-eflowvm"></a>Verify-EflowVm

La commande **Verify-EflowVm** est une fonction exposée pour vérifier si la machine virtuelle IoT Edge pour Linux sur Windows a été créée. Elle accepte uniquement des paramètres communs et retourne la valeur **True** si la machine virtuelle a été créée et **False** dans le cas contraire.

Pour plus d'informations, utilisez la commande `Get-Help Verify-EflowVm -full`.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment utiliser ces commandes pour installer et configurer IoT Edge pour Linux sur Windows dans l’article suivant :

* [Installer Azure IoT Edge pour Linux sur Windows](./how-to-provision-single-device-linux-on-windows-symmetric.md)
