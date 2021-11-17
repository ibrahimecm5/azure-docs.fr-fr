---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 94b115654d50fcc7924d86afcc9b78ddab05688d
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845081"
---
## <a name="install-iot-edge"></a>Installer IoT Edge

Dans cette section, vous préparez votre machine virtuelle ou votre appareil physique Windows pour IoT Edge. Ensuite, vous installez IoT Edge.

Azure IoT Edge s’appuie sur un runtime de conteneur compatible avec OCI. [Moby](https://github.com/moby/moby), un moteur basé sur Moby, est inclus dans le script d’installation, ce qui signifie qu’il n’y a aucune étape supplémentaire pour installer le moteur.

Pour installer le runtime IoT Edge :

1. Exécutez PowerShell ISE en tant qu’administrateur.

   Utilisez une session AMD64 de PowerShell, et non PowerShell (x86). Si vous ne savez pas quel type de session vous utilisez, exécutez la commande suivante :

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. Exécutez la commande [Deploy-IoTEdge](../articles/iot-edge/reference-windows-scripts.md#deploy-iotedge) qui effectue les tâches suivantes :

   * Vérifie que votre ordinateur Windows est sur une version prise en charge
   * Active la fonctionnalité des conteneurs
   * Télécharge le moteur moby et le runtime IoT Edge

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

3. Redémarrez votre appareil si vous y êtes invité.

Quand vous installez IoT Edge sur un appareil, vous pouvez utiliser des paramètres supplémentaires pour modifier le processus, à savoir :

* Diriger le trafic pour qu’il transite par un serveur proxy,
* Pointer le programme d’installation vers un répertoire local pour une installation hors connexion

Pour plus d’informations sur ces paramètres supplémentaires, consultez [Scripts PowerShell pour IoT Edge sur conteneurs Windows](../articles/iot-edge/reference-windows-scripts.md).
