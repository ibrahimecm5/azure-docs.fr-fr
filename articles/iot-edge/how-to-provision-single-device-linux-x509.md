---
title: Créer et approvisionner un appareil IoT Edge sur Linux avec des certificats X.509 – Azure IoT Edge | Microsoft Docs
description: Créer et approvisionner un appareil IoT Edge dans IoT Hub pour l’approvisionnement manuel avec des certificats X.509
author: v-tcassi
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/27/2021
ms.author: v-tcassi
ms.openlocfilehash: 5116774d0ea68f4d78dd0721a3546d9d82cbfdbd
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270706"
---
# <a name="create-and-provision-an-iot-edge-device-on-linux-using-x509-certificates"></a>Créer et approvisionner un appareil IoT Edge sur Linux avec des certificats X.509

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Cet article fournit des instructions de bout en bout pour l’inscription et l’approvisionnement d’un appareil IoT Edge Linux, incluant l’installation d’IoT Edge.

Chaque appareil qui se connecte à un hub IoT possède un ID d’appareil qui sert au suivi des communications cloud-à-appareil ou appareil-à-cloud. Vous configurez un appareil avec ses informations de connexion, qui comprennent le nom d’hôte du hub IoT, l’ID d’appareil ainsi que les informations que l’appareil utilise pour s’authentifier auprès d’IoT Hub.

Les étapes de cet article suivent un processus appelé approvisionnement manuel, qui consiste à connecter un seul appareil à son hub IoT. Avec le provisionnement manuel, vous avez le choix entre deux méthodes d’authentification des appareils IoT Edge :

* **Clés symétriques** : quand vous créez une identité d’appareil dans IoT Hub, le service crée deux clés. Vous placez l’une des clés sur l’appareil, lequel présente la clé à IoT Hub au moment de l’authentification.

  Cette méthode d’authentification est plus rapide pour commencer, mais moins sûre que l’autre.

* **Par certificat autosigné X.509** : vous créez deux certificats d’identité X.509, que vous placez sur l’appareil. Quand vous créez une identité d’appareil dans IoT Hub, vous fournissez les empreintes numériques des deux certificats. Quand l’appareil s’authentifie auprès de IoT Hub, il présente un certificat et IoT Hub vérifie que le certificat correspond à son empreinte numérique.

  Cette méthode d’authentification étant plus sécurisée, elle est recommandée dans les scénarios de production.

Cet article traite de l’utilisation de certificats X.509 comme méthode d’authentification. Si vous souhaitez utiliser des clés symétriques, consultez [Créer et provisionner un appareil IoT Edge sur Linux à l’aide de clés symétriques](how-to-provision-single-device-linux-symmetric.md).

> [!NOTE]
> Si vous avez de nombreux appareils à configurer et que vous ne souhaitez pas les approvisionner manuellement, consultez l’un des articles suivants pour découvrir comment IoT Edge fonctionne avec le service IoT Hub Device Provisioning :
>
> * [Créer et approvisionner des appareils IoT Edge à grande échelle à l’aide de certificats X.509](how-to-provision-devices-at-scale-linux-x509.md)
> * [Créer et approvisionner des appareils IoT Edge à grande échelle avec un module TPM](how-to-auto-provision-simulated-device-linux.md)
> * [Créer et approvisionner des appareils IoT Edge à grande échelle à l’aide de clés symétriques](how-to-provision-devices-at-scale-linux-symmetric.md)

## <a name="prerequisites"></a>Prérequis

Cet article traite de l’inscription de votre appareil IoT Edge et de l’installation d’IoT Edge sur l’appareil. Ces tâches ont plusieurs prérequis et s’effectuent avec différents utilitaires. Assurez-vous que toutes les conditions préalables sont remplies avant de continuer.

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

Un appareil Linux x64, ARM32 ou ARM64.

Microsoft fournit des packages d’installation pour les systèmes d’exploitation Ubuntu Server 18.04 et Raspberry Pi OS Stretch.

Pour obtenir les informations les plus récentes sur les systèmes d’exploitation actuellement pris en charge pour les scénarios de production, consultez [Systèmes pris en charge par Azure IoT Edge](support.md#operating-systems).

>[!NOTE]
>La prise en charge des appareils ARM64 est en [préversion publique](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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

   ![Ajouter un appareil IoT Edge à partir du portail Azure](./media/how-to-provision-single-device-linux-x509/portal-add-iot-edge-device.png)

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
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

Cette commande comprend plusieurs paramètres :

* `--device-id` ou `-d` : fournissez un nom descriptif unique à votre hub IoT. Notez cet ID d’appareil, car vous en aurez besoin dans la prochaine section.
* `hub-name` ou `-n` : indiquez le nom de votre hub IoT.
* `--edge-enabled` ou `--ee` : déclarez que l’appareil est un appareil IoT Edge.
* `--auth-method` ou `--am` : déclarez le type d’autorisation que l’appareil utilisera. Ici, nous utilisons des empreintes numériques de certificat X.509.
* `--primary-thumbprint` ou `--ptp` : fournissez une empreinte numérique de certificat X.509 à utiliser comme clé primaire.
* `--secondary-thumbprint` ou `--stp` : fournissez une empreinte numérique de certificat X.509 à utiliser comme clé secondaire.

---

Maintenant que vous avez un appareil inscrit dans IoT Hub, récupérez les informations à utiliser pour terminer l’installation et le provisionnement du runtime IoT Edge.

## <a name="view-registered-devices-and-retrieve-provisioning-information"></a>Afficher les appareils inscrits et récupérer les informations de provisionnement

Pour les appareils qui utilisent l’authentification par certificat X.509, vous devez récupérer le nom de leur hub IoT, leur nom d’appareil et leurs fichiers de certificat pour terminer l’installation et le provisionnement du runtime IoT Edge.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Tous les appareils compatibles avec Edge qui se connectent à votre hub IoT sont répertoriés dans la page **IoT Edge**.

![Utiliser le portail Azure pour voir tous les appareils IoT Edge dans votre hub IoT](./media/how-to-provision-single-device-linux-x509/portal-view-devices.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Vous ne pouvez pas inscrire des appareils avec des certificats X.509 par le biais de Visual Studio Code, mais vous pouvez toujours afficher vos appareils IoT Edge si nécessaire.

Tous les appareils qui se connectent à votre hub IoT sont listés dans la section **Azure IoT Hub** de l’Explorateur Visual Studio Code. Les appareils IoT Edge se distinguent des appareils non Edge par leur icône et par le fait que les modules **$edgeAgent** et **$edgeHub** sont développés sur chacun d’eux.

![Utiliser VS Code pour voir tous les appareils IoT Edge dans votre hub IoT](./media/how-to-provision-single-device-linux-x509/view-devices.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilisez la commande [az iot hub device-identity list](/cli/azure/iot/hub/device-identity) pour voir tous les appareils dans votre hub IoT. Par exemple :

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Tout appareil inscrit en tant qu’appareil IoT Edge a la propriété **capabilities.iotEdge** définie sur **true**.

---

## <a name="install-iot-edge"></a>Installer IoT Edge

Dans cette section, vous allez préparer votre machine virtuelle ou votre appareil physique Linux à IoT Edge. Ensuite, vous installez IoT Edge.

Vous devez effectuer deux étapes sur votre appareil pour qu’il soit prêt à installer le runtime IoT Edge. Votre appareil a besoin d’un accès aux packages d’installation Microsoft, et un moteur de conteneur doit être installé.

### <a name="prepare-your-device-to-access-the-microsoft-installation-packages"></a>Préparer votre appareil pour accéder aux packages d’installation de Microsoft

1. Installez la configuration du référentiel qui correspond au système d’exploitation de votre appareil.

   * **Ubuntu Server 18.04** :

      ```bash
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
      ```

   * **Raspberry Pi OS Stretch** :

      ```bash
      curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
      ```

1. Copiez la liste générée dans le répertoire sources.list.d.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

1. Installez la clé publique Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

Les packages logiciels Azure IoT Edge sont soumis aux termes du contrat de licence situés dans chaque package (`usr/share/doc/{package-name}` ou dans le répertoire `LICENSE`). Lisez les termes du contrat de licence avant d’utiliser un package. Le fait d’installer et d’utiliser un package revient à accepter ces termes. Si vous n’acceptez pas les termes du contrat de licence, n’utilisez pas le package en question.

### <a name="install-a-container-engine-on-your-device"></a>Installer un moteur de conteneur sur votre appareil

Azure IoT Edge s’appuie sur un runtime de conteneur compatible avec OCI. Dans les scénarios de production, nous vous recommandons d’utiliser le moteur Moby. Le moteur Moby est le seul moteur de conteneur officiellement pris en charge avec Azure IoT Edge. Les images conteneur Docker CE/EE sont compatibles avec le runtime Moby.

Mettez à jour les listes de packages sur votre appareil.

   ```bash
   sudo apt-get update
   ```

Installez le moteur Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

Si des erreurs surviennent lors de l’installation du moteur de conteneur Moby, vérifiez la compatibilité de votre noyau Linux avec Moby. Certains fabricants d’appareils embarqués livrent des images d’appareils qui contiennent des noyaux Linux personnalisés sans les fonctionnalités nécessaires à la compatibilité du moteur de conteneur. Exécutez la commande suivante, qui utilise le [script check-config](https://github.com/moby/moby/blob/master/contrib/check-config.sh) fourni par Moby pour vérifier la configuration de votre noyau :

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Dans la sortie du script, vérifiez que tous les éléments sous `Generally Necessary` et `Network Drivers` sont activés. Si vous ne disposez pas de certaines fonctionnalités, activez-les en régénérant votre noyau à partir des sources et en sélectionnant les modules associés à inclure dans le noyau .config approprié. De même, si vous utilisez un générateur de configuration du noyau comme `defconfig` ou `menuconfig`, trouvez et activez les fonctionnalités respectives et recompilez votre noyau en conséquence. Une fois que vous avez déployé votre nouveau noyau modifié, exécutez à nouveau le script check-config pour vérifier que toutes les fonctionnalités requises ont été activées avec succès.

### <a name="install-the-iot-edge-runtime"></a>Installer le runtime IoT Edge

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Le démon de sécurité IoT Edge fournit et gère les standards de sécurité sur l’appareil IoT Edge. Le démon se lance à chaque démarrage et amorce l’appareil en démarrant le reste du runtime IoT Edge.

La procédure de cette section représente le processus classique d’installation de la dernière version sur un appareil disposant d’une connexion Internet. Si vous devez installer une version spécifique, comme une préversion, ou si vous devez installer en mode hors connexion, suivez les étapes d’[installation d’une version hors connexion ou spécifique](#offline-or-specific-version-installation-optional) plus loin dans cet article.

Mettez à jour les listes de packages sur votre appareil.

   ```bash
   sudo apt-get update
   ```

Installez IoT Edge version 1.1.* avec le package **libiothsm-std** :

   ```bash
   sudo apt-get install iotedge
   ```

>[!NOTE]
>IoT Edge version 1.1 est la branche de support à long terme d’IoT Edge. Si vous exécutez une version antérieure, nous vous recommandons d’installer ou de mettre à jour vers le correctif le plus récent, car les versions antérieures ne sont plus prises en charge.

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Le service IoT Edge fournit et gère les standards de sécurité sur l’appareil IoT Edge. Le service se lance à chaque démarrage et amorce l’appareil en démarrant le reste du runtime IoT Edge.

Le service IoT Identity a été introduit avec la version 1.2 d’IoT Edge. Ce service gère l’approvisionnement et la gestion des identités pour IoT Edge et pour d’autres composants d’appareil qui doivent communiquer avec IoT Hub.

La procédure de cette section représente le processus classique d’installation de la dernière version sur un appareil disposant d’une connexion Internet. Si vous devez installer une version spécifique, comme une préversion, ou si vous devez installer en mode hors connexion, suivez les étapes d’[installation d’une version hors connexion ou spécifique](#offline-or-specific-version-installation-optional) plus loin dans cet article.

>[!NOTE]
>Les étapes décrites dans cette section vous indiquent la marche à suivre pour installer IoT Edge version 1.2.
>
>Si vous disposez déjà d’un appareil IoT Edge exécutant une version plus ancienne et que vous souhaitez effectuer une mise à niveau vers la version 1.2, suivez les étapes décrites dans [Mettre à jour le runtime et le démon de sécurité IoT Edge](how-to-update-iot-edge.md). La version 1.2 est si différente des versions précédentes d’IoT Edge que des étapes spécifiques sont nécessaires pour la mise à niveau.

Mettez à jour les listes de packages sur votre appareil.

   ```bash
   sudo apt-get update
   ```

Vérifiez les versions d’IoT Edge et le service d’identité IoT disponibles.

   ```bash
   apt list -a aziot-edge aziot-identity-service
   ```

Pour installer la dernière version d’IoT Edge et le package du service d’identité IoT, utilisez la commande suivante :

   ```bash
   sudo apt-get install aziot-edge
   ```

<!-- end 1.2 -->
::: moniker-end

## <a name="provision-the-device-with-its-cloud-identity"></a>Provisionnement de l’appareil avec son identité cloud

Maintenant que le moteur de conteneur et le runtime d’IoT Edge sont installés sur votre appareil, vous pouvez passer à l’étape suivante, à savoir configurer l’appareil avec ses informations d’identité et d’authentification cloud.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Sur l’appareil IoT Edge, ouvrez le fichier de configuration.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Recherchez la section des configurations de provisionnement du fichier et supprimez les marques de commentaire de la section **Manual provisioning configuration using an X.509 identity certificate**. Veillez à ajouter des marques de commentaire sur les autres sections de provisionnement. Assurez-vous que la ligne **d’approvisionnement :** n’est pas précédée d’une espace et que les éléments imbriqués sont en retrait de deux espaces.

   ```yml
   # Manual provisioning configuration using an x.509 identity certificate
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

Mettez à jour les champs suivants :

* **iothub_hostname** : nom d’hôte de l’hub IoT auquel l’appareil doit se connecter. Par exemple, `{IoT hub name}.azure-devices.net`.
* **device_id** : ID que vous avez indiqué lors de l’inscription de l’appareil.
* **identity_cert** : URI vers un certificat d’identité sur l’appareil. Par exemple, `file:///path/identity_certificate.pem`.
* **identity_pk** : URI vers le fichier de clé privée pour le certificat d’identité fourni. Par exemple, `file:///path/identity_key.pem`.

Enregistrez et fermez le fichier.

   `CTRL + X`, `Y`, `Enter`

Après avoir entré les informations de provisionnement dans le fichier de configuration, redémarrez le démon :

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Créez le fichier de configuration pour votre appareil à partir d’un fichier de modèle fourni dans le cadre de l’installation d’IoT Edge.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

Sur l’appareil IoT Edge, ouvrez le fichier de configuration.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

Recherchez la section **Provisioning** du fichier, et supprimez les marques de commentaire des lignes sur l’approvisionnement manuel avec un certificat d’identité X.509. Veillez à ajouter des marques de commentaire sur les autres sections de provisionnement.

   ```toml
   # Manual provisioning with x.509 certificates
   [provisioning]
   source = "manual"
   iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
   device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"

   [provisioning.authentication]
   method = "x509"

   identity_cert = "<REQUIRED URI OR POINTER TO DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

Mettez à jour les champs suivants :

* **iothub_hostname** : nom d’hôte de l’hub IoT auquel l’appareil doit se connecter. Par exemple, `{IoT hub name}.azure-devices.net`.
* **device_id** : ID que vous avez indiqué lors de l’inscription de l’appareil.
* **identity_cert** : URI d’un certificat d’identité sur l’appareil, par exemple : `file:///path/identity_certificate.pem`. Ou émettez le certificat de manière dynamique en utilisant EST ou une autorité de certification locale.
* **identity_pk** : URI du fichier de clé privée pour le certificat d’identité fourni. par exemple : `file:///path/identity_key.pem`. Ou fournissez un URI PKCS#11, puis fournissez vos informations de configuration dans la section **PKCS#11** ultérieurement dans le fichier config.

Enregistrez et fermez le fichier.

   `CTRL + X`, `Y`, `Enter`

Après avoir entré les informations d’approvisionnement dans le fichier config, appliquez vos modifications :

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

## <a name="verify-successful-configuration"></a>Vérification de la réussite de la configuration

Vérifiez que le runtime a été correctement installé et configuré sur votre appareil IoT Edge.

>[!TIP]
>Vous avez besoin de privilèges élevés pour exécuter les commandes `iotedge`. Une fois que vous vous déconnectez de votre machine et que vous vous reconnectez pour la première fois après avoir installé le runtime IoT Edge, vos autorisations sont automatiquement mises à jour. Dans l’intervalle, ajoutez `sudo` devant les commandes.

Vérifiez que le service système IoT Edge est en cours d’exécution.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   sudo systemctl status iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system status
   ```

Une réponse d’état réussie est `Ok`.

::: moniker-end

Si vous avez besoin de résoudre les problèmes du service, récupérez les journaux d’activité de ce dernier.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   journalctl -u iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system logs
   ```

::: moniker-end

Utilisez l’outil `check` pour vérifier l’état de la configuration et de la connexion de l’appareil.

   ```bash
   sudo iotedge check
   ```

>[!TIP]
>Utilisez toujours `sudo` pour exécuter l’outil de vérification, même après la mise à jour de vos autorisations. L’outil a besoin de privilèges élevés pour accéder au fichier config pour vérifier l’état de la configuration.

Affichez tous les modules s’exécutant sur votre appareil IoT Edge. Lors du premier démarrage du service, seul le module **edgeAgent** apparaît dans cette liste. Le module edgeAgent s’exécute par défaut et vous aide à installer et démarrer tous les modules supplémentaires que vous déployez sur votre appareil.

   ```bash
   sudo iotedge list
   ```

Lorsque vous créez un appareil IoT Edge, le code d’état `417 -- The device's deployment configuration is not set` s’affiche dans le portail Azure. Cet état est normal et signifie que l’appareil est prêt à recevoir un déploiement de module.

## <a name="offline-or-specific-version-installation-optional"></a>Installation d’une version spécifique hors connexion (facultatif)

Les étapes de cette section concernent les scénarios non couverts par les étapes d’installation standard. Cela peut inclure :

* Installer IoT Edge en mode hors connexion
* Installer une version Release candidate

Utilisez les étapes de cette section si vous souhaitez installer une version spécifique du runtime Azure IoT Edge, qui n’est pas disponible via `apt-get install`. La liste des packages Microsoft contient uniquement un ensemble limité de versions récentes et leurs sous-versions. Par conséquent, ces étapes sont destinées à toute personne souhaitant installer une version antérieure ou une version Release Candidate.

À l’aide de commandes curl, vous pouvez cibler les fichiers de composants directement à partir du référentiel GitHub IoT Edge.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

1. Accédez aux [versions d’Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)et recherchez celle que vous souhaitez cibler.

2. Développez la section **Ressources** pour cette version.

3. Chaque version devrait avoir de nouveaux fichiers pour le démon de sécurité IoT Edge et le hsmlib. Si vous envisagez d’installer IoT Edge sur un appareil hors connexion, téléchargez ces fichiers à l’avance. Autrement, utilisez les commandes suivantes pour mettre à jour ces composants.

   1. Trouvez le fichier **libiothsm-std** qui correspond à l’architecture de votre périphérique IoT Edge. Cliquez avec le bouton droit de la souris sur le lien de fichier et copiez l’adresse du lien.

   2. Utilisez le lien copié dans la commande suivante pour installer cette version du hsmlib :

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo apt-get install ./libiothsm-std.deb
      ```

   3. Trouvez le fichier **iotedge** qui correspond à l’architecture de votre périphérique IoT Edge. Cliquez avec le bouton droit de la souris sur le lien de fichier et copiez l’adresse du lien.

   4. Utilisez le lien copié dans la commande suivante pour installer cette version du démon de sécurité de l’IoT Edge.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo apt-get install ./iotedge.deb
      ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

>[!NOTE]
>Si votre appareil exécute actuellement IoT Edge version 1.1 ou une version antérieure, désinstallez les packages **iotedge** et **libiothsm-std** avant de suivre les étapes de cette section. Pour plus d’informations, consultez [Mise à jour de la version 1.0 ou 1.1 vers la version 1.2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12).

1. Accédez aux [versions d’Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)et recherchez celle que vous souhaitez cibler.

2. Développez la section **Ressources** pour cette version.

3. Chaque mise en production devrait avoir de nouveaux fichiers pour IoT Edge et le service d’identité. Si vous envisagez d’installer IoT Edge sur un appareil hors connexion, téléchargez ces fichiers à l’avance. Autrement, utilisez les commandes suivantes pour mettre à jour ces composants.

   1. Recherchez le fichier **aziot-identity-service** correspondant à l’architecture de votre appareil IoT Edge. Cliquez avec le bouton droit de la souris sur le lien de fichier et copiez l’adresse du lien.

   2. Utilisez le lien copié dans la commande suivante pour installer cette version du service d’identité :

      ```bash
      curl -L <identity service link> -o aziot-identity-service.deb && sudo apt-get install ./aziot-identity-service.deb
      ```

   3. Recherchez le fichier **aziot-edge** correspondant à l’architecture de votre appareil IoT Edge. Cliquez avec le bouton droit de la souris sur le lien de fichier et copiez l’adresse du lien.

   4. Utilisez le lien copié dans la commande suivante pour installer cette version d’IoT Edge.

      ```bash
      curl -L <iotedge link> -o aziot-edge.deb && sudo apt-get install ./aziot-edge.deb
      ```

<!-- end 1.2 -->
::: moniker-end

Maintenant que le moteur de conteneur et le runtime IoT Edge sont installés sur votre appareil, vous pouvez passer à l’étape suivante, [Provisionnement de l’appareil avec son identité cloud](#provision-the-device-with-its-cloud-identity).

## <a name="uninstall-iot-edge"></a>Désinstaller IoT Edge

Si vous souhaitez supprimer l’installation d’IoT Edge de votre appareil, utilisez les commandes suivantes.

Supprimez le runtime IoT Edge.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

```bash
sudo apt-get remove iotedge
```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

```bash
sudo apt-get remove aziot-edge
```

::: moniker-end

Utilisez l’indicateur `--purge` pour supprimer tous les fichiers associés à IoT Edge, dont vos fichiers de configuration. Omettez cet indicateur si vous souhaitez réinstaller IoT Edge et utiliser les mêmes informations de configuration à l’avenir.

Lors de la suppression du runtime IoT Edge, tous les conteneurs qu’il a créés sont arrêtés, mais continuent d’exister sur votre appareil. Affichez tous les conteneurs pour voir ceux qui restent.

```bash
sudo docker ps -a
```

Supprimez les conteneurs de votre appareil, dont les deux conteneurs de runtime.

```bash
sudo docker rm -f <container name>
```

Enfin, supprimez le runtime du conteneur de votre appareil.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Étapes suivantes

Passez à [Déployer des modules IoT Edge](how-to-deploy-modules-portal.md) pour savoir comment déployer des modules sur votre appareil.
