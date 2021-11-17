---
title: Créer et provisionner des appareils IoT Edge à l’aide de clés symétriques sur Linux - Azure IoT Edge | Microsoft Docs
description: Utilisez l’attestation de clé symétrique afin de tester le provisionnement des appareils Linux à grande échelle pour Azure IoT Edge avec le service Device Provisioning.
author: kgremban
ms.author: kgremban
ms.reviewer: v-tcassi
ms.date: 10/29/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6a0b1a73c45c5c08eaf3c172900ade531c6fedda
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505938"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-linux-using-symmetric-key"></a>Créer et provisionner des appareils IoT Edge à grande échelle sur Linux à l’aide de clés symétriques

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Cet article donne des instructions complètes pour le provisionnement automatique d’un ou plusieurs appareils IoT Edge Linux avec des clés symétriques. Les appareils Azure IoT Edge peuvent être provisionnés automatiquement avec le [Service Azure IoT Hub Device Provisioning](../iot-dps/index.yml) (DPS, Device Provisioning Service). Si vous ne connaissez pas le processus de provisionnement automatique, consultez la [vue d’ensemble du provisionnement](../iot-dps/about-iot-dps.md#provisioning-process) avant de poursuivre.

Voici les tâches à effectuer :

1. Création d’une **inscription individuelle** pour un appareil unique ou d’une **inscription de groupe** pour un ensemble d’appareils
1. Installation du runtime IoT Edge et de la connexion à IoT Hub.

:::moniker range=">=iotedge-2020-11"
>[!TIP]
>Pour une expérience simplifiée, essayez l[outil de configuration Azure IoT Edge](https://github.com/azure/iot-edge-config). Cet outil en ligne de commande, actuellement disponible en préversion publique, installe IoT Edge sur votre appareil et le provisionne à l’aide du service IoT Hub Device Provisioning et de l’attestation de clé symétrique.
:::moniker-end

L’attestation de clé symétrique constitue une approche simple pour authentifier un appareil avec une instance DPS. Cette méthode d’attestation représente une expérience « Hello world » pour les développeurs qui découvrent le provisionnement d’appareils ou n’ont pas d’exigences de sécurité strictes. L’attestation d’appareil avec un [Module de plateforme sécurisée (TPM)](../iot-dps/concepts-tpm-attestation.md) ou un [certificat X.509](../iot-dps/concepts-x509-attestation.md) est plus sécurisée, et doit être utilisée lorsque les exigences de sécurité sont plus strictes.

## <a name="prerequisites"></a>Prérequis

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

### <a name="iot-edge-installation"></a>Installation d’IoT Edge

Un appareil Linux physique ou virtuel faisant office d’appareil IoT Edge.

Vous devrez définir un **ID d’inscription** *unique* pour identifier chaque appareil. Vous pouvez utiliser l’adresse MAC, le numéro de série ou toute information unique provenant de l’appareil. Dans cet exemple, vous pouvez utiliser une combinaison d’une adresse MAC et d’un numéro de série, forment la chaîne suivante pour un ID d’inscription : `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`. Les caractères valides sont les caractères alphanumériques minuscules et les tirets (`-`).

<!-- Create a DPS enrollment using symmetric keys H2 and content -->
[!INCLUDE [iot-edge-create-dps-enrollment-symmetric.md](../../includes/iot-edge-create-dps-enrollment-symmetric.md)]

<!-- Install IoT Edge on Linux H2 and content -->
[!INCLUDE [install-iot-edge-linux.md](../../includes/iot-edge-install-linux.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>Provisionnement de l’appareil avec son identité cloud

Une fois le runtime installé sur votre appareil, configurez ce dernier avec les informations qu’il utilise pour se connecter au service DPS et à IoT Hub.

Préparez les informations suivantes :

* Valeur **Étendue de l’ID** du service Device Provisioning
* L’**ID d’inscription** de l’appareil que vous avez créé
* Entrez la **clé primaire** d’une inscription spécifique ou une [clé dérivée](#derive-a-device-key) pour les appareils utilisant une inscription de groupe.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Ouvrez le fichier de configuration sur l’appareil IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Recherchez la section des configurations de provisionnement dans le fichier. Décommentez les lignes du provisionnement avec les clés symétriques du service Device Provisioning et assurez-vous que les autres lignes de provisionnement sont commentées.

   La ligne `provisioning:` ne doit pas être précédée d’un espace et les éléments imbriqués doivent être en retrait de deux espaces.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "PASTE_YOUR_SCOPE_ID_HERE"
     attestation:
       method: "symmetric_key"
       registration_id: "PASTE_YOUR_REGISTRATION_ID_HERE"
       symmetric_key: "PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. Mettez à jour les valeurs de `scope_id`, `registration_id` et `symmetric_key` avec vos informations relatives à l’appareil et à DPS.

1. Si vous le souhaitez, utilisez les lignes `always_reprovision_on_startup` ou `dynamic_reprovisioning` pour configurer le comportement de reprovisionnement de votre appareil. Si un appareil est configuré pour être reprovisionné au démarrage, le reprovisionnement est toujours tenté avec le service DPS dans un premier temps, puis, en cas d’échec, au moyen de la sauvegarde de provisionnement. Si un appareil est configuré pour se reprovisionner dynamiquement lui-même, IoT Edge redémarre et exécute le reprovisionnement si un événement de reprovisionnement est détecté. Pour plus d’informations, consultez [Concepts du reprovisionnement d’appareils IoT Hub](../iot-dps/concepts-device-reprovision.md).

1. Redémarrez le runtime IoT Edge afin qu’il récupère toutes les modifications de configuration que vous avez effectuées sur l’appareil.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Créez un fichier de configuration pour votre appareil à partir d’un fichier de modèle fourni dans le cadre de l’installation d’IoT Edge.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. Ouvrez le fichier de configuration sur l’appareil IoT Edge.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Recherchez la section **Provisioning** du fichier. Supprimez les marques de commentaire des lignes pour l’approvisionnement par clé symétrique de DPS, et assurez-vous que les autres lignes d’approvisionnement sont commentées.

   ```toml
   # DPS provisioning with symmetric key
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "PASTE_YOUR_SCOPE_ID_HERE"
   
   [provisioning.attestation]
   method = "symmetric_key"
   registration_id = "PASTE_YOUR_REGISTRATION_ID_HERE"

   symmetric_key = "PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE"
   ```

1. Mettez à jour les valeurs de `id_scope`, `registration_id` et `symmetric_key` avec vos informations relatives à l’appareil et à DPS.

   Le paramètre de clé symétrique peut accepter la valeur d’une clé incluse, d’un URI de fichier ou d’un URI de PKCS#11. Supprimez les marques de commentaire d’une ligne de clé symétrique, en fonction du format que vous utilisez.

   Si vous utilisez des URI PKCS#11, recherchez la section **PKCS#11** dans le fichier config et fournissez des informations sur votre configuration de PKCS#11.

1. Enregistrez et fermez le fichier config.toml.

1. Appliquez les modifications de configuration que vous avez apportées à IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="verify-successful-installation"></a>Vérifier la réussite de l’installation

Si le runtime a démarré correctement, vous pouvez accéder à votre IoT Hub et commencer à déployer des modules IoT Edge sur votre appareil.

# <a name="individual-enrollment"></a>[Inscription individuelle](#tab/individual-enrollment)

Vous pouvez vérifier que l’inscription individuelle que vous avez créée dans le service DPS a bien été utilisée. Accédez à l’instance DPS sur le Portail Azure. Ouvrez les détails de l’inscription pour l’inscription individuelle que vous avez créée. Notez que l’état de l’inscription est **Affecté** et que l’ID de l’appareil figure dans la liste.

# <a name="group-enrollment"></a>[Inscription de groupe](#tab/group-enrollment)

Vous pouvez vérifier que l’inscription de groupe que vous avez créée dans le service DPS a bien été utilisée. Accédez à l’instance DPS sur le Portail Azure. Ouvrez les détails de l’inscription de groupe que vous avez créée. Accédez à l’onglet **Enregistrements d’inscription** pour afficher tous les appareils enregistrés dans ce groupe.

---

Utilisez les commandes suivantes sur votre appareil pour vérifier que IoT Edge a été installé et démarré correctement.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Vérifiez l’état du service IoT Edge.

```cmd/sh
systemctl status iotedge
```

Consultez les journaux d’activité de service.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Répertoriez les modules en cours d’exécution.

```cmd/sh
iotedge list
```

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Vérifiez l’état du service IoT Edge.

```cmd/sh
sudo iotedge system status
```

Consultez les journaux d’activité de service.

```cmd/sh
sudo iotedge system logs
```

Répertoriez les modules en cours d’exécution.

```cmd/sh
sudo iotedge list
```

:::moniker-end

## <a name="next-steps"></a>Étapes suivantes

Le processus d’inscription DPS vous permet de définir l’ID d’appareil et les balises du jumeau d’appareil tandis que vous provisionnez le nouvel appareil. Vous pouvez utiliser ces valeurs pour cibler des appareils individuels ou des groupes d’appareils avec la gestion d’appareils automatique. En savoir plus sur [Déployer et surveiller des modules IoT Edge à grande échelle à l’aide du portail Azure](how-to-deploy-at-scale.md) ou [d’Azure CLI](how-to-deploy-cli-at-scale.md).
