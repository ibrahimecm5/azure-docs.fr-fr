---
title: Créer et provisionner des appareils IoT Edge à grande échelle à l’aide de certificats X.509 sur Linux - Azure IoT Edge | Microsoft Docs
description: Utiliser des certificats X.509 pour tester l’approvisionnement d’appareils à grande échelle pour Azure IoT Edge avec le service d’approvisionnement d’appareils
author: kgremban
ms.author: kgremban
ms.date: 10/29/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
ms.openlocfilehash: 24e6b8943d87cf7735cc1baae7027557e3da2ad2
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505476"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-linux-using-x509-certificates"></a>Créer et provisionner des appareils IoT Edge à grande échelle sur Linux à l’aide de certificats X.509

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Cet article fournit des instructions complètes pour l’approvisionnement automatique d’un ou plusieurs appareils IoT Edge Linux à l’aide de certificats X.509. Les appareils Azure IoT Edge peuvent être approvisionnés automatiquement à l’aide du [Service IoT Hub Device Provisioning](../iot-dps/index.yml) (DPS). Si vous ne connaissez pas le processus d’approvisionnement automatique, consultez la [présentation de l’approvisionnement](../iot-dps/about-iot-dps.md#provisioning-process) avant de poursuivre.

Voici les tâches à effectuer :

1. Génération de certificats et de clés
1. Création d’une **inscription individuelle** pour un seul appareil ou d’une **inscription de groupe** pour un ensemble d’appareils
1. Installation du runtime IoT Edge et inscription de l’appareil auprès d’IoT Hub

Utiliser des certificats X.509 comme mécanisme d’attestation est un excellent moyen de mettre à l’échelle la production et de simplifier le provisionnement des appareils. Les certificats X.509 sont généralement organisés en une chaîne d’approbation de confiance. À partir d’un certificat racine auto-signé ou approuvé, chaque certificat de la chaîne signe le certificat inférieur suivant. Ce modèle crée une chaîne déléguée de confiance depuis le certificat racine jusqu’au certificat « feuille » final installé sur un appareil, en passant par chaque certificat intermédiaire.

## <a name="prerequisites"></a>Prérequis

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

### <a name="iot-edge-installation"></a>Installation d’IoT Edge

Un appareil Linux physique ou virtuel faisant office d’appareil IoT Edge.

## <a name="generate-device-identity-certificates"></a>Générer des certificats d’identité d’appareil

Le certificat d’identité d’appareil est un certificat feuille qui se connecte par le biais d’une chaîne de certificats de confiance au certificat d’autorité de certification X.509 supérieur. Le nom commun (CN) du certificat d’identité d’appareil doit être défini sur l’ID d’appareil que vous souhaitez que l’appareil ait dans votre hub IoT.

Les certificats d’identité d’appareil sont utilisés uniquement pour provisionner l’appareil IoT Edge et l’authentifier auprès d’Azure IoT Hub. Ils ne signent pas de certificats, contrairement aux certificats d’autorité de certification que l’appareil IoT Edge présente aux modules ou aux appareils de nœud terminal à des fins de vérification. Pour plus d’informations, consultez les [détails sur l’utilisation des certificats par Azure IoT Edge](iot-edge-certs.md).

Après avoir créé le certificat d’identité d’appareil, vous devez avoir deux fichiers : un fichier .cer ou .pem, qui contient la partie publique du certificat, et un fichier .cer ou .pem, qui comporte la clé privée du certificat. Si vous envisagez d’utiliser l’inscription de groupe dans le service Device Provisioning, vous avez également besoin de la partie publique d’un certificat d’autorité de certification racine ou intermédiaire dans la même chaîne de certificats de confiance.

Les fichiers suivants sont nécessaires pour configurer le provisionnement automatique avec X.509 :

* Le certificat d’identité d’appareil et son certificat de clé privée. Le certificat d’identité d’appareil est chargé dans DPS si vous créez une inscription individuelle. La clé privée est transmise au runtime IoT Edge.
* Un certificat de chaîne complète, qui doit comporter au moins l’identité d’appareil et les certificats intermédiaires qu’elle contient. Le certificat de chaîne complète est transmis au runtime IoT Edge.
* Un certificat d’autorité de certification racine ou intermédiaire issu de la chaîne d’approbation des certificats. Ce certificat est chargé dans DPS si vous créez une inscription de groupe.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> Il existe actuellement dans libiothsm une limitation empêchant l’utilisation de certificats qui expirent le 1er janvier 2038 ou après cette date.

:::moniker-end
<!-- end 1.1 -->

### <a name="use-test-certificates-optional"></a>Utiliser des certificats de test (facultatif)

Si vous n’avez pas d’autorité de certification disponible pour créer des certificats d’identité et que vous souhaitez tester ce scénario, le dépôt Git Azure IoT Edge contient des scripts que vous pouvez utiliser pour générer des certificats de test. Ces certificats, conçus uniquement pour les tests de développement, ne doivent pas être utilisés en production.

Pour créer des certificats de test, suivez les étapes décrites dans [Créer des certificats de démonstration pour tester les fonctionnalités de l’appareil IoT Edge](how-to-create-test-certificates.md). Effectuez les deux sections nécessaires pour configurer les scripts de génération de certificat et créer un certificat d’autorité de certification racine. Ensuite, suivez les étapes permettant de créer un certificat d’identité d’appareil. Quand vous avez terminé, vous devez disposer de la chaîne de certificats et de la paire de clés suivantes :

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Vous avez besoin de ces deux certificats sur l’appareil IoT Edge. Si vous envisagez d’utiliser l’inscription individuelle dans le service Device Provisioning, vous devez charger le fichier .cert.pem. Si vous envisagez d’utiliser l’inscription de groupe dans le service Device Provisioning, la même chaîne de certificats de confiance doit également comporter un certificat d’autorité de certification racine ou intermédiaire à charger. Si vous recourez à des certificats de démonstration, utilisez le certificat `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem` pour l’inscription de groupe.

<!-- Create a DPS enrollment using X.509 certificates H2 and content -->
[!INCLUDE [iot-edge-create-dps-enrollment-x509.md](../../includes/iot-edge-create-dps-enrollment-x509.md)]

<!-- Install IoT Edge on Linux H2 and content -->
[!INCLUDE [install-iot-edge-linux.md](../../includes/iot-edge-install-linux.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>Provisionnement de l’appareil avec son identité cloud

Une fois le runtime installé sur votre appareil, configurez ce dernier avec les informations qu’il utilise pour se connecter au service DPS et à IoT Hub.

Préparez les informations suivantes :

* La valeur **Étendue de l’ID** du service Device Provisioning. Vous pouvez récupérer cette valeur à partir de la page de présentation de votre instance du service Device Provisioning dans le portail Azure.
* Le fichier de chaîne de certificats d’identité d’appareil sur l’appareil
* Le fichier de clé d’identité d’appareil sur l’appareil

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Ouvrez le fichier de configuration sur l’appareil IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Recherchez la section des configurations de provisionnement dans le fichier. Supprimez les marques de commentaire des lignes pour l’approvisionnement de certificat DPS X.509, et assurez-vous que les autres lignes d’approvisionnement sont commentées.

   La ligne `provisioning:` ne doit pas être précédée d’un espace et les éléments imbriqués doivent être en retrait de deux espaces.

   ```yml
   # DPS X.509 provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "x509"
   #   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. Mettez à jour les valeurs de `scope_id`, `identity_cert` et `identity_pk` avec vos informations sur l’appareil et le service Device Provisioning.

   Quand vous ajoutez le certificat X.509 et les informations de clé au fichier config.yaml, les chemins doivent être fournis en tant qu’URI de fichier. Par exemple :

   `file:///<path>/identity_certificate_chain.pem`
   `file:///<path>/identity_key.pem`

1. Si vous le souhaitez, fournissez le `registration_id` de l'appareil, qui doit correspondre au nom commun du certificat d'identité. Si vous laissez cette ligne commentée, le nom commun est appliqué automatiquement.

1. Si vous le souhaitez, utilisez les lignes `always_reprovision_on_startup` ou `dynamic_reprovisioning` pour configurer le comportement de reprovisionnement de votre appareil. Si un appareil est configuré pour être reprovisionné au démarrage, le reprovisionnement est toujours tenté avec le service DPS dans un premier temps, puis, en cas d’échec, au moyen de la sauvegarde de provisionnement. Si un appareil est configuré pour se reprovisionner dynamiquement lui-même, IoT Edge redémarre et exécute le reprovisionnement si un événement de reprovisionnement est détecté. Pour plus d’informations, consultez [Concepts du reprovisionnement d’appareils IoT Hub](../iot-dps/concepts-device-reprovision.md).

1. Enregistrez et fermez le fichier config.yaml.

1. Redémarrez le runtime IoT Edge afin qu’il récupère toutes les modifications de configuration que vous avez effectuées sur l’appareil.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1. -->

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

1. Recherchez la section **Provisioning** du fichier. Supprimez les marques de commentaire des lignes pour l’approvisionnement de certificat X.509, et assurez-vous que les autres lignes d’approvisionnement sont commentées.

   ```toml
   # DPS provisioning with X.509 certificate
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "x509"
   registration_id = "<REGISTRATION ID>"

   identity_cert = "<DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<DEVICE IDENTITY PRIVATE KEY>"
   ```

1. Mettez à jour la valeur de `id_scope` avec l'ID d'étendue que vous avez copié à partir de votre instance de DPS.

1. Fournissez un `registration_id` pour l'appareil ; il s'agira de l'ID de l'appareil dans IoT Hub. L'ID d'inscription doit correspondre au nom commun du certificat d'identité.

1. Mettez à jour les valeurs d'`identity_cert` et `identity_pk` avec les informations de votre certificat et de votre clé.

   La valeur du certificat d’identité peut être fournie sous la forme d’un URI de fichier ou être émise de manière dynamique à l’aide de l’autorité de certification EST ou locale. Supprimez les marques de commentaire d’une seule ligne, selon le format que vous choisissez d’utiliser.

   La valeur de la clé privée d’identité peut être fournie sous la forme d’un URI de fichier ou d’un URI PKCS#11. Supprimez les marques de commentaire d’une seule ligne, selon le format que vous choisissez d’utiliser.

   Si vous utilisez des URI PKCS#11, recherchez la section **PKCS#11** dans le fichier config et fournissez des informations sur votre configuration de PKCS#11.

1. Enregistrez et fermez le fichier.

1. Appliquez les modifications de configuration que vous avez apportées à IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="verify-successful-installation"></a>Vérifier la réussite de l’installation

Si le runtime a démarré correctement, vous pouvez accéder à votre IoT Hub et commencer à déployer des modules IoT Edge sur votre appareil.

# <a name="individual-enrollment"></a>[Inscription individuelle](#tab/individual-enrollment)

Vous pouvez vérifier que l’inscription individuelle que vous avez créée dans le service d’approvisionnement des appareils a été utilisée. Accédez à l’instance du service d’approvisionnement des appareils dans le portail Azure. Ouvrez les détails de l’inscription pour l’inscription individuelle que vous avez créée. Notez que l’état de l’inscription est **Affecté** et que l’ID de l’appareil figure dans la liste.

# <a name="group-enrollment"></a>[Inscription de groupe](#tab/group-enrollment)

Vous pouvez vérifier que l’inscription de groupe que vous avez créée dans le service d’approvisionnement des appareils a été utilisée. Accédez à l’instance du service d’approvisionnement des appareils dans le portail Azure. Ouvrez les détails de l’inscription de groupe que vous avez créée. Accédez à l’onglet **Enregistrements d’inscription** pour afficher tous les appareils enregistrés dans ce groupe.

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

Le processus d’inscription auprès du service d’approvisionnement des appareils vous permet de définir l’ID d’appareil et les balises du jumeau d’appareil en même temps que vous approvisionnez le nouvel appareil. Vous pouvez utiliser ces valeurs pour cibler des appareils individuels ou des groupes d’appareils avec la gestion d’appareils automatique. En savoir plus sur [Déployer et surveiller des modules IoT Edge à grande échelle à l’aide du portail Azure](how-to-deploy-at-scale.md) ou [d’Azure CLI](how-to-deploy-cli-at-scale.md).
