---
title: Créer et provisionner un appareil IoT Edge sur Windows à l’aide de clés symétriques - Azure IoT Edge | Microsoft Docs
description: Créer et provisionner un appareil IoT Edge sur Windows dans IoT Hub en utilisant le provisionnement manuel avec des clés symétriques
author: v-tcassi
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/30/2021
ms.author: v-tcassi
monikerRange: iotedge-2018-06
ms.openlocfilehash: c9ae8eea18225fca6634d55b48b80ec29e713135
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270765"
---
# <a name="create-and-provision-an-iot-edge-device-on-windows-using-symmetric-keys"></a>Créer et provisionner un appareil IoT Edge sur Windows avec des clés symétriques

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Cet article fournit des instructions de bout en bout pour l’inscription et le provisionnement d’un appareil IoT Edge sur Windows.

>[!NOTE]
>Azure IoT Edge pour conteneurs Windows ne sera pas pris en charge à partir de la version 1.2 d’Azure IoT Edge.
>
>Envisagez d’utiliser la nouvelle méthode pour exécuter IoT Edge sur des appareils Windows, [Azure IoT Edge pour Linux sur Windows](iot-edge-for-linux-on-windows.md).

Chaque appareil qui se connecte à un hub IoT possède un ID d’appareil qui sert au suivi des communications cloud-à-appareil ou appareil-à-cloud. Vous configurez un appareil avec ses informations de connexion, qui comprennent le nom d’hôte du hub IoT, l’ID d’appareil ainsi que les informations que l’appareil utilise pour s’authentifier auprès d’IoT Hub.

Les étapes de cet article suivent un processus appelé approvisionnement manuel, qui consiste à connecter un seul appareil à son hub IoT. Avec le provisionnement manuel, vous avez le choix entre deux méthodes d’authentification des appareils IoT Edge :

* **Clés symétriques** : quand vous créez une identité d’appareil dans IoT Hub, le service crée deux clés. Vous placez l’une des clés sur l’appareil, lequel présente la clé à IoT Hub au moment de l’authentification.

  Cette méthode d’authentification est plus rapide pour commencer, mais moins sûre que l’autre.

* **Par certificat autosigné X.509** : vous créez deux certificats d’identité X.509, que vous placez sur l’appareil. Quand vous créez une identité d’appareil dans IoT Hub, vous fournissez les empreintes numériques des deux certificats. Quand l’appareil s’authentifie auprès de IoT Hub, il présente un certificat et IoT Hub vérifie que le certificat correspond à son empreinte numérique.

  Cette méthode d’authentification étant plus sécurisée, elle est recommandée dans les scénarios de production.

Cet article traite de l’utilisation de clés symétriques comme méthode d’authentification. Si vous souhaitez utiliser des certificats X.509, consultez [Créer et provisionner un appareil IoT Edge sur Windows à l’aide de certificats X.509](how-to-provision-single-device-windows-x509.md).

> [!NOTE]
> Si vous avez de nombreux appareils à configurer et que vous ne souhaitez pas les approvisionner manuellement, consultez l’un des articles suivants pour découvrir comment IoT Edge fonctionne avec le service IoT Hub Device Provisioning :
>
> * [Créer et provisionner des appareils IoT Edge à grande échelle à l’aide de certificats X.509](how-to-provision-devices-at-scale-windows-x509.md)
> * [Créer et provisionner des appareils IoT Edge à grande échelle avec un module TPM](how-to-auto-provision-simulated-device-windows.md)
> * [Créer et provisionner des appareils IoT Edge à grande échelle à l’aide de clés symétriques](how-to-provision-devices-at-scale-windows-symmetric.md)

## <a name="prerequisites"></a>Prérequis

Cet article traite de l’inscription de votre appareil IoT Edge et de l’installation d’IoT Edge sur l’appareil. Ces tâches ont plusieurs prérequis et s’effectuent avec différents utilitaires. Assurez-vous que tous les prérequis sont remplis avant de continuer.

### <a name="device-registration"></a>Enregistrement de l’appareil

Vous avez le choix d’utiliser le **portail Azure**, **Visual Studio Code** ou **Azure CLI** pour inscrire votre appareil. Chaque utilitaire possède sa propre configuration requise :

# <a name="portal"></a>[Portail](#tab/azure-portal)

Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) gratuit ou standard dans votre abonnement Azure.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) gratuit ou standard dans votre abonnement Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pour Visual Studio Code.

> [!NOTE]
> Actuellement, l’extension Azure IoT pour Visual Studio Code ne prend pas en charge l’inscription des appareils avec des certificats X.509.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* Un [hub IoT](../iot-hub/iot-hub-create-using-cli.md) gratuit ou standard dans votre abonnement Azure.
* [Azure CLI](/cli/azure/install-azure-cli) dans votre environnement. Vous devez utiliser Azure CLI version 2.0.70 ou ultérieure. Utilisez `az --version` pour valider. Cette version prend en charge les commandes d’extension az et introduit l’infrastructure de la commande Knack.

---

### <a name="iot-edge-installation"></a>Installation d’IoT Edge

Un appareil Windows

IoT Edge avec des conteneurs Windows requiert Windows version 1809/Build 17763, qui est la [build prise en charge à long terme de Windows](/windows/release-information/)la plus récente. Veillez à consulter la [liste de systèmes pris en charge](support.md#operating-systems) pour obtenir la liste des références SKU prises en charge.

## <a name="register-your-device"></a>Inscrire votre appareil

Vous avez le choix d’utiliser le **portail Azure**, **Visual Studio Code** ou **Azure CLI** pour inscrire votre appareil.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Dans votre hub IoT dans le portail Azure, les appareils IoT Edge sont créés et managés séparément des appareils IoT qui ne sont pas compatibles avec Edge.

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre IoT Hub.

1. Dans le volet de gauche, sélectionnez **IoT Edge** dans le menu, puis sélectionnez **Ajouter un appareil IoT Edge**.

   ![Ajouter un appareil IoT Edge à partir du portail Azure](./media/how-to-provision-single-device-windows-symmetric/portal-add-iot-edge-device.png)

1. Dans la page **Créer un appareil**, fournissez les informations suivantes :

   * Créez un ID d’appareil descriptif. Notez cet ID d’appareil, car vous en aurez besoin plus tard.
   * Sélectionnez **Clé symétrique** comme type d’authentification.
   * Utilisez les paramètres par défaut pour générer automatiquement les clés d’authentification et connecter le nouvel appareil à votre hub.

1. Sélectionnez **Enregistrer**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>Se connecter pour accéder à votre hub ioT

Vous pouvez utiliser les extensions Azure IoT pour Visual Studio Code afin d’effectuer des opérations avec votre hub IoT. Pour que ces opérations fonctionnent, vous devez vous connecter à votre compte Azure et sélectionner votre hub.

1. Dans Visual Studio Code, ouvrez la vue **Explorateur**.
1. Au bas de l’Explorateur, développez la section **Azure IoT Hub**.

   ![Développer la section Appareils Azure IoT Hub](./media/how-to-provision-single-device-windows-symmetric/azure-iot-hub-devices.png)

1. Cliquez sur **...** dans l’en-tête de section **Azure IoT Hub**. Si vous ne voyez pas les points de suspension, cliquez ou pointez sur l’en-tête.
1. Choisissez **Sélectionner un hub IoT**.
1. Si vous n’êtes pas connecté à votre compte Azure, suivez les invites de connexion.
1. Sélectionnez votre abonnement Azure.
1. Sélectionnez votre hub IoT.

### <a name="register-a-new-device-with-visual-studio-code"></a>Inscrire un nouvel appareil avec Visual Studio Code

1. Dans l’explorateur Visual Studio Code, développez la section **Azure IoT Hub**.
1. Cliquez sur **...** dans l’en-tête de section **Azure IoT Hub**. Si vous ne voyez pas les points de suspension, cliquez ou pointez sur l’en-tête.
1. Sélectionnez **Créer un appareil IoT Edge**.
1. Dans la zone de texte qui s’ouvre, donnez un ID à votre appareil.

Dans l’écran de sortie, vous voyez le résultat de la commande. Les informations de l’appareil apparaissent, notamment l’ID d’appareil (**deviceId**) que vous avez fourni et la chaîne de connexion (**connectionString**) qui vous permet de connecter votre appareil physique à votre hub IoT.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilisez la commande [az iot hub device-identity create](/cli/azure/iot/hub/device-identity) pour créer une identité d’appareil dans votre hub IoT. Par exemple :

   ```azurecli
   az iot hub device-identity create --device-id [device_id] --hub-name [hub_name] --edge-enabled
   ```

Cette commande inclut trois paramètres :

* `--device-id` ou `-d` : Fournissez un nom descriptif unique dans votre hub IoT.
* `--hub-name` ou `-n` : indiquez le nom de votre hub IoT.
* `--edge-enabled` ou `--ee` : déclarez que l’appareil est un appareil IoT Edge.

   ![az iot hub device-identity create output](./media/how-to-provision-single-device-windows-symmetric/create-edge-device-cli.png)

---

Maintenant que vous avez un appareil inscrit dans IoT Hub, récupérez les informations à utiliser pour terminer l’installation et le provisionnement du runtime IoT Edge.

## <a name="view-registered-devices-and-retrieve-provisioning-information"></a>Afficher les appareils inscrits et récupérer les informations de provisionnement

Les appareils qui utilisent l’authentification de clé symétrique ont besoin de leurs chaînes de connexion pour terminer l’installation et l’approvisionnement du runtime IoT Edge.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Tous les appareils compatibles avec Edge qui se connectent à votre hub IoT sont répertoriés dans la page **IoT Edge**.

![Utiliser le portail Azure pour voir tous les appareils IoT Edge dans votre hub IoT](./media/how-to-provision-single-device-windows-symmetric/portal-view-devices.png)

Pour configurer votre appareil, vous avez besoin de la chaîne de connexion qui établit un lien entre votre appareil physique et son identité dans le hub IoT.

Les appareils qui s’authentifient avec des clés symétriques disposent des chaînes de connexion pouvant être copiées dans le portail.

1. Dans la page **IoT Edge** du portail, cliquez sur l’ID de l’appareil dans la liste des appareils IoT Edge.
2. Copiez la valeur de **Chaîne de connexion principale** ou **Chaîne de connexion secondaire**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Tous les appareils qui se connectent à votre hub IoT sont listés dans la section **Azure IoT Hub** de l’Explorateur Visual Studio Code. Les appareils IoT Edge se distinguent des appareils non Edge par leur icône et par le fait que les modules **$edgeAgent** et **$edgeHub** sont développés sur chacun d’eux.

![Utiliser VS Code pour voir tous les appareils IoT Edge dans votre hub IoT](./media/how-to-provision-single-device-windows-symmetric/view-devices.png)

Pour configurer votre appareil, vous avez besoin de la chaîne de connexion qui établit un lien entre votre appareil physique et son identité dans le hub IoT.

1. Cliquez avec le bouton droit sur l’ID de votre appareil dans la section **Azure IoT Hub**.
1. Sélectionnez **Copier la chaîne de connexion de l’appareil**.

   La chaîne de connexion est copiée dans le Presse-papiers.

Vous pouvez également sélectionner **Obtenir les informations de l’appareil** dans le menu contextuel pour voir les informations de l’appareil, notamment la chaîne de connexion, dans la fenêtre de sortie.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilisez la commande [az iot hub device-identity list](/cli/azure/iot/hub/device-identity) pour voir tous les appareils dans votre hub IoT. Par exemple :

   ```azurecli
   az iot hub device-identity list --hub-name [hub_name]
   ```

Tout appareil inscrit en tant qu’appareil IoT Edge a la propriété **capabilities.iotEdge** définie sur **true**.

Pour configurer votre appareil, vous avez besoin de la chaîne de connexion qui établit un lien entre votre appareil physique et son identité dans le hub IoT. Utilisez la commande [az iot hub device-identity connection-string show](/cli/azure/iot/hub/device-identity/connection-string) pour retourner la chaîne de connexion d’un appareil unique :

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device_id] --hub-name [hub_name]
   ```

>[!TIP]
>La commande `connection-string show` a été introduite dans la version 0.9.8 de l’extension Azure IoT, remplaçant la commande `show-connection-string` déconseillée. Si une erreur survient lors de l’exécution de cette commande, assurez-vous que votre version de l’extension est mise à jour vers 0.9.8 ou une version ultérieure. Pour plus d’informations et pour connaître les dernières mises à jour, consultez [Extension Microsoft Azure IoT pour Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

La valeur du paramètre `device-id` respecte la casse.

Quand vous copiez la chaîne de connexion à utiliser sur un appareil, n’incluez pas les guillemets autour de la chaîne de connexion.

---

## <a name="install-iot-edge"></a>Installer IoT Edge

Dans cette section, vous préparez votre appareil Windows pour IoT Edge. Ensuite, vous installez IoT Edge sur l’appareil.

Azure IoT Edge s’appuie sur un runtime de conteneur compatible avec OCI. [Moby](https://github.com/moby/moby), un moteur basé sur Moby, est inclus dans le script d’installation. Vous n’avez donc pas d’étape supplémentaire à effectuer pour installer le moteur.

Pour installer le runtime IoT Edge :

1. Exécutez PowerShell ISE en tant qu’administrateur.

   Utilisez une session AMD64 de PowerShell, et non PowerShell (x86). Si vous ne savez pas quel type de session vous utilisez, exécutez la commande suivante :

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. Exécutez la commande [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) qui effectue les tâches suivantes :

   * Vérifie que votre ordinateur Windows est sous une version prise en charge.
   * Active la fonctionnalité des conteneurs.
   * Télécharge le moteur Moby et le runtime IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

3. Redémarrez votre appareil si vous y êtes invité.

Quand vous installez IoT Edge sur un appareil, vous pouvez utiliser des paramètres supplémentaires pour modifier le processus, à savoir :

* Diriger le trafic pour qu’il transite par un serveur proxy,
* Pointer le programme d’installation vers un répertoire local pour une installation hors connexion.

Pour plus d’informations sur ces paramètres supplémentaires, consultez [Scripts PowerShell pour IoT Edge sur conteneurs Windows](reference-windows-scripts.md).

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
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   La commande de déploiement utilise les composants trouvés dans le répertoire de fichiers local fourni. S’il manque le fichier .cab ou le programme d'installation de Visual C++, elle tente de les télécharger.

## <a name="uninstall-iot-edge"></a>Désinstaller IoT Edge

Si vous souhaitez supprimer l’installation d’IoT Edge de votre appareil, utilisez les commandes suivantes.

Si vous souhaitez supprimer l’installation d’IoT Edge de votre appareil Windows, utilisez la commande [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) à partir d’une fenêtre PowerShell d’administration. Cette commande supprime le runtime IoT Edge, ainsi que votre configuration existante et les données du moteur Moby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Pour plus d’informations sur les options de désinstallation, utilisez la commande `Get-Help Uninstall-IoTEdge -full`.

## <a name="next-steps"></a>Étapes suivantes

Passez à [Déployer des modules IoT Edge](how-to-deploy-modules-portal.md) pour savoir comment déployer des modules sur votre appareil.
