---
title: Créer et approvisionner un appareil IoT Edge sur Linux à l’aide de clés symétriques – Azure IoT Edge | Microsoft Docs
description: Créer et approvisionner un appareil IoT Edge dans IoT Hub en utilisant le approvisionnement manuel avec des clés symétriques
author: kgremban
ms.reviewer: v-tcassi
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: kgremban
ms.openlocfilehash: 8d0db2b4aa516be4da48a6d80904cfd485e8462e
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490559"
---
# <a name="create-and-provision-an-iot-edge-device-on-linux-using-symmetric-keys"></a>Créer et provisionner un appareil IoT Edge sur Linux avec des clés symétriques

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Cet article fournit des instructions de bout en bout pour l’inscription et l’approvisionnement d’un appareil IoT Edge Linux, incluant l’installation d’IoT Edge.

Chaque appareil qui se connecte à un hub IoT possède un ID d’appareil qui sert au suivi des communications cloud-à-appareil ou appareil-à-cloud. Vous configurez un appareil avec ses informations de connexion, qui comprennent le nom d’hôte du hub IoT, l’ID d’appareil ainsi que les informations que l’appareil utilise pour s’authentifier auprès d’IoT Hub.

Les étapes de cet article suivent un processus appelé approvisionnement manuel, qui consiste à connecter un seul appareil à son hub IoT. Avec le provisionnement manuel, vous avez le choix entre deux méthodes d’authentification des appareils IoT Edge :

* **Clés symétriques** : quand vous créez une identité d’appareil dans IoT Hub, le service crée deux clés. Vous placez l’une des clés sur l’appareil, lequel présente la clé à IoT Hub au moment de l’authentification.

  Cette méthode d’authentification est plus rapide pour commencer, mais moins sûre que l’autre.

* **Par certificat autosigné X.509** : vous créez deux certificats d’identité X.509, que vous placez sur l’appareil. Quand vous créez une identité d’appareil dans IoT Hub, vous fournissez les empreintes numériques des deux certificats. Quand l’appareil s’authentifie auprès de IoT Hub, il présente un certificat et IoT Hub vérifie que le certificat correspond à son empreinte numérique.

  Cette méthode d’authentification étant plus sécurisée, elle est recommandée dans les scénarios de production.

Cet article traite de l’utilisation de clés symétriques comme méthode d’authentification. Si vous souhaitez utiliser des certificats X.509, consultez [Créer et approvisionner un appareil IoT Edge sur Linux à l’aide de certificats X.509](how-to-provision-single-device-linux-x509.md).

> [!NOTE]
> Si vous avez de nombreux appareils à configurer et que vous ne souhaitez pas les provisionner manuellement, consultez l’un des articles suivants pour découvrir comment IoT Edge fonctionne avec le service IoT Hub Device Provisioning :
>
> * [Créer et provisionner des appareils IoT Edge à grande échelle à l’aide de certificats X.509](how-to-provision-devices-at-scale-linux-x509.md)
> * [Créer et provisionner des appareils IoT Edge à grande échelle avec un module TPM](how-to-provision-devices-at-scale-linux-tpm.md)
> * [Créer et provisionner des appareils IoT Edge à grande échelle à l’aide de clés symétriques](how-to-provision-devices-at-scale-linux-symmetric.md)

## <a name="prerequisites"></a>Prérequis

Cet article traite de l’inscription de votre appareil IoT Edge et de l’installation d’IoT Edge sur l’appareil. Ces tâches ont des conditions préalables et différents utilitaires sont utilisés pour les accomplir. Assurez-vous que toutes les conditions préalables sont remplies avant de continuer.

<!-- Device registration prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-register-device.md](../../includes/iot-edge-prerequisites-register-device.md)]

### <a name="iot-edge-installation"></a>Installation d’IoT Edge

Un appareil Linux x64, ARM32 ou ARM64.

Microsoft fournit des packages d’installation pour les systèmes d’exploitation Ubuntu Server 18.04 et Raspberry Pi OS Stretch.

Pour les dernières informations sur les systèmes d’exploitation actuellement pris en charge pour les scénarios de production, consultez [Systèmes pris en charge par Azure IoT Edge](support.md#operating-systems).

>[!NOTE]
>La prise en charge des appareils ARM64 est en [préversion publique](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

<!-- Register your device and View provisioning information H2s and content -->
[!INCLUDE [iot-edge-register-device-symmetric.md](../../includes/iot-edge-register-device-symmetric.md)]

<!-- Install IoT Edge on Linux H2 and content -->
[!INCLUDE [install-iot-edge-linux.md](../../includes/iot-edge-install-linux.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>Provisionnement de l’appareil avec son identité cloud

Maintenant que le moteur de conteneur et le runtime d’IoT Edge sont installés sur votre appareil, vous pouvez passer à l’étape suivante, à savoir configurer l’appareil avec ses informations d’identité et d’authentification cloud.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Sur l’appareil IoT Edge, ouvrez le fichier de configuration.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Recherchez les configurations d’approvisionnement du fichier et supprimez les marques de commentaire de la section **Manual provisioning configuration using a connection string** si ce n’est déjà fait.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "ADD_DEVICE_CONNECTION_STRING_HERE"
   ```

Mettez à jour la valeur de **device_connection_string** avec la chaîne de connexion à partir de votre appareil IoT Edge. Veillez à ajouter des marques de commentaire sur les autres sections de provisionnement. Assurez-vous que la ligne **d’approvisionnement :** n’est pas précédée d’une espace et que les éléments imbriqués sont en retrait de deux espaces.

Pour coller le contenu du Presse-papiers dans Nano, appuyez sur `Shift+Right Click` ou sur `Shift+Insert`.

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

Vous pouvez rapidement configurer votre appareil IoT Edge avec une authentification par clé symétrique en utilisant la commande suivante :

   ```bash
   sudo iotedge config mp --connection-string 'PASTE_DEVICE_CONNECTION_STRING_HERE'
   ```

   La commande `iotedge config mp` crée un fichier de configuration sur l’appareil et entre votre chaîne de connexion dans le fichier.

Appliquez les modifications de la configuration.

   ```bash
   sudo iotedge config apply
   ```

Si vous souhaitez afficher le fichier de configuration, vous pouvez l’ouvrir :

   ```bash
   sudo nano /etc/aziot/config.toml
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
      curl -L libiothsm-std_link_here -o libiothsm-std.deb && sudo apt-get install ./libiothsm-std.deb
      ```

   3. Trouvez le fichier **iotedge** qui correspond à l’architecture de votre périphérique IoT Edge. Cliquez avec le bouton droit de la souris sur le lien de fichier et copiez l’adresse du lien.

   4. Utilisez le lien copié dans la commande suivante pour installer cette version du démon de sécurité de l’IoT Edge.

      ```bash
      curl -L iotedge_link_here -o iotedge.deb && sudo apt-get install ./iotedge.deb
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
