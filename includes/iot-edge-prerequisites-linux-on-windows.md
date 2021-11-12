---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3fae7fc37b010a754b7fe1dd9b6e7b092ae534e8
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131576849"
---
### <a name="iot-edge-for-linux-on-windows-installation"></a>Installation de IoT Edge pour Linux sur Windows

Un appareil Windows avec la configuration minimale requise suivante :

* Configuration requise
   * Windows 10¹/11 (Pro, Entreprise, IoT Entreprise)
   * Windows Server 2019¹/2022  
   <sub>¹ Windows 10 et Windows Server 2019 version 17763 ou ultérieure, avec toutes les mises à jour cumulatives actuelles installées.</sub>

* Configuration matérielle requise
  * Mémoire disponible minimale : 1 Go
  * Espace disque disponible minimal : 10 Go

* Prise en charge de la virtualisation
  * Sur Windows 10, activez Hyper-V. Pour plus d’informations, consultez [Installation de Hyper-V sur Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).
  * Sur Windows Server, installez le rôle Hyper-V et créez un commutateur réseau par défaut. Pour plus d’informations, consultez [Virtualisation imbriquée pour Azure IoT Edge pour Linux sur Windows](../articles/iot-edge/nested-virtualization.md).
  * Sur une machine virtuelle, configurez la virtualisation imbriquée. Pour plus d’informations, consultez [Virtualisation imbriquée](../articles/iot-edge/nested-virtualization.md).

* Prise en charge du réseau
  * Windows Server n’est pas fourni avec un commutateur par défaut. Pour pouvoir déployer EFLOW sur un appareil Windows Server, vous devez créer un commutateur virtuel.  Pour plus d’informations, consultez [Création d’un commutateur virtuel pour Linux sur Windows](../articles/iot-edge/how-to-create-virtual-switch.md).
  * Les versions Windows Desktop sont fournies avec un commutateur par défaut utilisable pour l’installation d’EFLOW. Si nécessaire, vous pouvez créer votre propre commutateur virtuel personnalisé.

> [!TIP]
> Si vous souhaitez utiliser des **modules Linux avec accélération GPU** dans votre déploiement Azure IoT Edge pour Linux sur Windows, il existe plusieurs options de configuration à prendre en compte.
>
> Vous devrez installer les pilotes adaptés à votre architecture GPU. Par ailleurs, vous aurez peut-être besoin d’un accès à une build du Programme Windows Insider. Pour déterminer vos besoins en matière de configuration et satisfaire à ces prérequis, consultez [Accélération GPU pour Azure IoT Edge pour Linux sur Windows](../articles/iot-edge/gpu-acceleration.md).
>
> Veillez à prendre le temps de répondre aux conditions préalables à l’accélération du GPU. Vous devrez redémarrer le processus d’installation si vous décidez d’accélérer le GPU pendant l’installation.

Vous pouvez utiliser **PowerShell** ou **Windows Admin Center** pour gérer vos appareils IoT Edge. Chaque utilitaire possède sa propre configuration requise :

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Si vous souhaitez utiliser PowerShell, suivez les étapes ci-dessous pour préparer votre appareil cible à l’installation d’Azure IoT Edge pour Linux sur Windows et au déploiement de la machine virtuelle Linux :

1. Définissez la stratégie d’exécution sur l’appareil cible sur `AllSigned`. Vous pouvez vérifier la stratégie d’exécution actuelle dans une invite PowerShell avec élévation de privilèges à l’aide de la commande suivante :

   ```powershell
   Get-ExecutionPolicy -List
   ```

   Si la stratégie d’exécution de `local machine` n’est pas `AllSigned`, vous pouvez la définir à l’aide de :

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

Pour en savoir plus sur le module PowerShell d’Azure IoT Edge pour Linux sur Windows, consultez la [documentation de référence sur les fonctions PowerShell](../articles/iot-edge/reference-iot-edge-for-linux-on-windows-functions.md).

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Si vous souhaitez utiliser Windows Admin Center, suivez les étapes ci-dessous pour télécharger et installer Windows Admin Center et installer l’extension Azure IoT Edge de Windows Admin Center :

   1. Téléchargez et exécutez le [programme d’installation de Windows Admin Center](https://aka.ms/wacdownload). Suivez les invites de l’Assistant Installation pour installer Windows Admin Center.

   1. Une fois l’installation terminée, utilisez un navigateur pris en charge pour ouvrir le Windows Admin Center. Les navigateurs pris en charge sont Microsoft Edge (Windows 10, version 1709 ou ultérieure), Google Chrome et Microsoft Edge Insider.

   1. Lors de la première utilisation de Windows Admin Center, vous êtes invité à sélectionner le certificat à utiliser. Sélectionnez **Client Windows Admin Center** comme certificat.

   1. Installez l’extension Azure IoT Edge. Sélectionnez l’icône d’engrenage en haut à droite du tableau de bord de Windows Admin Center.

      ![Sélectionnez l’icône d’engrenage en haut à droite du tableau de bord pour accéder aux paramètres.](../articles/iot-edge/media/how-to-provision-devices-at-scale-linux-on-windows-x509/select-gear-icon.png)

   1. Dans le menu **Paramètres**, sous **Passerelle**, sélectionnez **Extensions**.

   1. Dans l’onglet **Extensions disponibles**, recherchez **Azure IoT Edge** dans la liste des extensions. Sélectionnez-le et cliquez sur l’invite **Installer** au-dessus de la liste des extensions.

   1. Une fois l’installation terminée, Azure IoT Edge doit apparaître dans la liste des extensions installées dans l’onglet **Extensions installées**.

---
