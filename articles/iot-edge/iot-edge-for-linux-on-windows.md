---
title: Qu’est-ce qu’Azure IoT Edge pour Linux sur Windows | Microsoft Docs
description: Vue d’ensemble de la manière d’exécuter des modules IoT Edge Linux sur des appareils Windows 10
author: kgremban
ms.reviewer: twarwick
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/18/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 881d0fe19339976342db866072dcf86009b1bbd5
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578858"
---
# <a name="what-is-azure-iot-edge-for-linux-on-windows"></a>Qu’est-ce qu’Azure IoT Edge pour Linux sur Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge pour Linux sur Windows vous permet d’exécuter des charges de travail Linux en conteneur avec des applications Windows dans des déploiements IoT Windows. Les entreprises qui s’appuient sur l’IoT Windows pour alimenter leurs appareils périphériques peuvent désormais tirer parti des solutions d’analytique natives Cloud créées dans Linux.

IoT Edge pour Linux sur Windows fonctionne en exécutant une machine virtuelle Linux sur un appareil Windows. La machine virtuelle Linux est préinstallée avec le runtime IoT Edge. Tout module IoT Edge déployé sur l’appareil s’exécute à l’intérieur de la machine virtuelle. Pendant ce temps, les applications Windows s’exécutant sur l’appareil hôte Windows peuvent communiquer avec les modules s’exécutant sur la machine virtuelle Linux.

[Commencez dès](how-to-provision-single-device-linux-on-windows-symmetric.md) aujourd'hui.

## <a name="components"></a>Components

IoT Edge pour Linux sur Windows utilise les composants suivants pour permettre que des charges de travail Linux et Windows s’exécutent les unes à côté des autres et communiquent en toute transparence :

* **Machine virtuelle Linux exécutant Azure IoT Edge** : une machine virtuelle Linux basée sur le système d’exploitation [CBL-Mariner](https://github.com/microsoft/CBL-Mariner) de Microsoft est créée avec le runtime IoT Edge et validée en tant qu’environnement compatible de niveau 1 pour des charges de travail IoT Edge.

* **Windows Admin Center** : une extension IoT Edge pour Windows Admin Center facilite l’installation, la configuration et les diagnostics de IoT Edge sur la machine virtuelle Linux. Windows Admin Center peut déployer IoT Edge pour Linux sur Windows sur l’appareil local, ou peut se connecter à des appareils cibles et les gérer à distance.

* **Microsoft Update** : l’intégration à Microsoft Update permet de tenir à jour les composants Windows Runtime, la machine virtuelle Linux CBL-Mariner et le service IoT Edge.

![Windows et la machine virtuelle Linux s’exécutent en parallèle, tandis que Windows Admin Center contrôle les deux composants](./media/iot-edge-for-linux-on-windows/architecture-and-communication.png)

Une communication bidirectionnelle entre le processus Windows et la machine virtuelle Linux signifie que les processus Windows peuvent fournir des interfaces utilisateur ou des proxys matériels pour des charges de travail exécutées dans les conteneurs Linux.


## <a name="prerequisites"></a>Prérequis

Un appareil Windows avec la configuration minimale requise suivante :

* Configuration requise
   * Windows 10¹/11 (Pro, Entreprise, IoT Entreprise)
   * Windows Server 2019¹/2022  
   <sub>¹ Windows 10 et Windows Server 2019 build 17763 ou ultérieure, avec et toutes les mises à jour cumulatives actuelles installées.</sub>

* Configuration matérielle requise
  * Mémoire disponible minimale : 1 Go
  * Espace disque disponible minimal : 10 Go


## <a name="samples"></a>Exemples

IoT Edge pour Linux sur Windows met l’accent sur l’interopérabilité entre les composants Linux et Windows.

Pour obtenir des exemples qui illustrent la communication entre les applications Windows et les modules IoT Edge, consultez [Exemples EFLOW et Windows 10 IoT](https://aka.ms/AzEFLOW-Samples).

## <a name="support"></a>Support

Utilisez les canaux de support et de commentaires d’IoT Edge pour obtenir de l’aide concernant IoT Edge pour Linux sur Windows.

**Signalement des bogues** : vous pouvez signaler les bogues relatifs à Azure IoT Edge pour Linux sur Windows sur la [page iotedge-eflow issues](https://aka.ms/AzEFLOW-Issues). Des bogues liés à IoT Edge peuvent être signalés dans la [page des problèmes](https://github.com/azure/iotedge/issues) du projet open source IoT Edge.

**Équipe de support technique Microsoft** : les utilisateurs qui ont un [plan de support](https://azure.microsoft.com/support/plans/) peuvent solliciter l’équipe de support technique Microsoft en créant un ticket de support directement à partir du [portail Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Demandes de fonctionnalités** : le produit Azure IoT Edge effectue le suivi des demandes de fonctionnalités par le biais de sa [page User Voice](https://feedback.azure.com/d365community/forum/0e2fff5d-f524-ec11-b6e6-000d3a4f0da0).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations et un exemple en action, regardez [IoT Edge pour Linux sur Windows 10 IoT Enterprise](https://aka.ms/azeflow-show).

Pour configurer un appareil avec IoT Edge pour Linux sur Windows, suivez les étapes décrites dans [Approvisionner manuellement Azure IoT Edge pour Linux sur un appareil Windows](how-to-provision-single-device-linux-on-windows-symmetric.md).
