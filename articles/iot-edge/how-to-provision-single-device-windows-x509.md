---
title: Créer et provisionner un appareil IoT Edge sur Windows avec des certificats X.509 - Azure IoT Edge | Microsoft Docs
description: Créer et provisionner un appareil IoT Edge sur Windows dans IoT Hub en utilisant le provisionnement manuel avec des certificats X.509
author: v-tcassi
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/30/2021
ms.author: v-tcassi
monikerRange: iotedge-2018-06
ms.openlocfilehash: b9b8a1e3c17ce441dd7469547da83791815dc330
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270745"
---
# <a name="create-and-provision-an-iot-edge-device-on-windows-using-x509-certificates"></a>Créer et provisionner un appareil IoT Edge sur Windows avec des certificats X.509

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

Cet article traite de l’utilisation de certificats X.509 comme méthode d’authentification. Si vous souhaitez utiliser des clés symétriques, consultez [Créer et provisionner un appareil IoT Edge sur Windows à l’aide de clés symétriques](how-to-provision-single-device-windows-symmetric.md).

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

## <a name="generate-device-identity-certificates"></a>Générer des certificats d’identité d’appareil

L’approvisionnement manuel avec des certificats X.509 requiert IoT Edge version 1.0.10 ou ultérieure.

Lorsque vous configurez un appareil IoT Edge avec des certificats X.509, vous utilisez ce que l’on appelle un *certificat d’identité d’appareil*. Ce certificat est utilisé uniquement pour approvisionner un appareil IoT Edge et l’authentifier avec Azure IoT Hub. Il s’agit d’un certificat feuille qui ne signe pas d’autres certificats. Le certificat d’identité d’appareil est distinct des certificats d’autorité de certification que l’appareil IoT Edge présente aux modules ou aux appareils en aval à des fins de vérification.

Pour l’authentification par certificat X.509, les informations d’authentification de chaque appareil sont fournies sous la forme d’*empreintes numériques* extraites de vos certificats d’identité d’appareil. Ces empreintes numériques, fournies à IoT Hub au moment de l’inscription de l’appareil, permettent au service de reconnaître l’appareil qui se connecte.

Pour plus d’informations sur l’utilisation des certificats d’autorité de certification dans les appareils IoT Edge, consultez [Comprendre comment Azure IoT Edge utilise les certificats](iot-edge-certs.md).

Les fichiers suivants sont nécessaires pour le provisionnement manuel avec X.509 :

* Deux des certificats d’identité d’appareil avec leurs certificats de clé privée correspondants au format .cer ou .pem.

  Un ensemble de fichiers de certificat/clé est fourni au runtime IoT Edge. Quand vous créez des certificats d’identité d’appareil, définissez le nom commun du certificat (CN) avec l’ID d’appareil que l’appareil doit avoir dans votre hub IoT.

* Empreintes numériques tirées des deux certificats d’identité d’appareil.

  Les valeurs d’empreinte numérique sont constituées de 40 caractères hexadécimaux pour les hachages SHA-1 ou de 64 caractères hexadécimaux pour les hachages SHA-256. Les deux empreintes numériques sont fournies pour IoT Hub lors de l’enregistrement de l’appareil.

Si vous n’avez pas de certificats disponibles, vous pouvez [créer des certificats de démonstration pour tester les fonctionnalités de l’appareil IoT Edge](how-to-create-test-certificates.md). Suivez les instructions de cet article pour configurer des scripts de création de certificat, créer un certificat d’autorité de certification racine, puis créer deux certificats d’identité d’appareil IoT Edge.

L’une des méthodes permettant de récupérer l’empreinte numérique d’un certificat consiste à utiliser la commande openssl suivante :

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

## <a name="register-your-device"></a>Inscrire votre appareil

Vous avez le choix d’utiliser le **portail Azure**, **Visual Studio Code** ou **Azure CLI** pour inscrire votre appareil.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Dans votre hub IoT dans le portail Azure, les appareils IoT Edge sont créés et managés séparément des appareils IoT qui ne sont pas compatibles avec Edge.

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre IoT Hub.

1. Dans le volet de gauche, sélectionnez **IoT Edge** dans le menu, puis sélectionnez **Ajouter un appareil IoT Edge**.

   ![Ajouter un appareil IoT Edge à partir du portail Azure](./media/how-to-provision-single-device-windows-x509/portal-add-iot-edge-device.png)

1. Dans la page **Créer un appareil**, fournissez les informations suivantes :

   * Créez un ID d’appareil descriptif. Notez cet ID d’appareil, car vous en aurez besoin plus tard.
   * Sélectionnez **X.509 autosigné** comme type d’authentification.
   * Fournissez les empreintes numériques des certificats d’identité principal et secondaire. Les valeurs d’empreinte numérique sont constituées de 40 caractères hexadécimaux pour les hachages SHA-1 ou de 64 caractères hexadécimaux pour les hachages SHA-256.

1. Sélectionnez **Enregistrer**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Actuellement, l’extension Azure IoT pour Visual Studio Code ne prend pas en charge l’inscription des appareils avec des certificats X.509.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilisez la commande [az iot hub device-identity create](/cli/azure/iot/hub/device-identity) pour créer une identité d’appareil dans votre hub IoT. Par exemple :

   ```azurecli
   az iot hub device-identity create --device-id [device_id] --hub-name [hub_name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA_thumbprint] --secondary-thumbprint [SHA_thumbprint]
   ```

Cette commande comprend plusieurs paramètres :

* `--device-id` ou `-d` : fournissez un nom descriptif unique à votre hub IoT. Notez cet ID d’appareil, car vous en aurez besoin dans la prochaine section.
* `hub-name` ou `-n` : indiquez le nom de votre hub IoT.
* `--edge-enabled` ou `--ee` : déclarez que l’appareil est un appareil IoT Edge.
* `--auth-method` ou `--am` : déclarez le type d’autorisation que l’appareil utilisera. Ici, nous utilisons des empreintes numériques de certificat X.509.
* `--primary-thumbprint` ou `--ptp` : fournissez une empreinte numérique de certificat X.509 à utiliser comme clé primaire.
* `--secondary-thumbprint` ou `--stp` : fournissez une empreinte numérique de certificat X.509 à utiliser comme clé secondaire.

---

Maintenant que vous avez un appareil inscrit dans IoT Hub, récupérez les informations à utiliser pour terminer l’installation et le provisionnement du runtime IoT Edge.

## <a name="view-registered-devices-and-retrieve-provisioning-information"></a>Afficher les appareils inscrits et récupérer les informations de provisionnement

Pour les appareils qui utilisent l’authentification par certificat X.509, vous devez récupérer le nom de leur hub IoT, leur nom d’appareil et leurs fichiers de certificat pour terminer l’installation et le provisionnement du runtime IoT Edge.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Tous les appareils compatibles avec Edge qui se connectent à votre hub IoT sont répertoriés dans la page **IoT Edge**.

![Utiliser le portail Azure pour voir tous les appareils IoT Edge dans votre hub IoT](./media/how-to-provision-single-device-windows-x509/portal-view-devices.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Vous ne pouvez pas inscrire des appareils avec des certificats X.509 par le biais de Visual Studio Code, mais vous pouvez toujours afficher vos appareils IoT Edge si nécessaire.

Tous les appareils qui se connectent à votre hub IoT sont listés dans la section **Azure IoT Hub** de l’Explorateur Visual Studio Code. Les appareils IoT Edge se distinguent des appareils non Edge par leur icône et par le fait que les modules **$edgeAgent** et **$edgeHub** sont développés sur chacun d’eux.

![Utiliser VS Code pour voir tous les appareils IoT Edge dans votre hub IoT](./media/how-to-provision-single-device-windows-x509/view-devices.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilisez la commande [az iot hub device-identity list](/cli/azure/iot/hub/device-identity) pour voir tous les appareils dans votre hub IoT. Par exemple :

   ```azurecli
   az iot hub device-identity list --hub-name [hub_name]
   ```

Tout appareil inscrit en tant qu’appareil IoT Edge a la propriété **capabilities.iotEdge** définie sur **true**.

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

2. Utilisez la commande [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) pour configurer le runtime IoT Edge sur votre ordinateur.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * Si vous avez téléchargé le script IoTEdgeSecurityDaemon.ps1 sur votre appareil pour une installation hors connexion ou l’installation d’une version spécifique, veillez à référencer la copie locale du script.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. À l’invite, fournissez les informations suivantes :

   * **IotHubHostName** : nom d’hôte de l’hub IoT auquel l’appareil doit se connecter. Par exemple, `{IoT_hub_name}.azure-devices.net`.
   * **DeviceId** : ID que vous avez indiqué lors de l’inscription de l’appareil.
   * **X509IdentityCertificate** : chemin absolu d’un certificat d’identité sur l’appareil. Par exemple, `C:\path\identity_certificate.pem`.
   * **X509IdentityPrivateKey** : chemin absolu du fichier de clé privée pour le certificat d’identité fourni. Par exemple, `C:\path\identity_key.pem`.

Quand vous provisionnez un appareil manuellement, vous pouvez utiliser des paramètres supplémentaires pour changer le processus, à savoir :

* Diriger le trafic pour qu’il transite par un serveur proxy,
* Déclarer une image conteneur d’agent Edge spécifique, et fournir des informations d’identification si l’image se trouve dans un registre privé

Pour plus d’informations sur ces paramètres supplémentaires, consultez [Scripts PowerShell pour IoT Edge sur conteneurs Windows](reference-windows-scripts.md).

---

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
