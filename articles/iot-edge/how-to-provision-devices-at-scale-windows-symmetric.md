---
title: Créer et approvisionner des appareils IoT Edge à l’aide de clés symétriques sur Windows - Azure IoT Edge | Microsoft Docs
description: Utiliser l’attestation de clés symétriques pour tester l’approvisionnement des appareils Windows à grande échelle pour Azure IoT Edge avec le service d’approvisionnement des appareils
author: kgremban
ms.author: kgremban
ms.reviewer: v-tcassi
ms.date: 10/27/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: a1abccf4a039a36f2969e6b32b8eca2478d07427
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845935"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-windows-using-symmetric-keys"></a>Créer et approvisionner des appareils IoT Edge à grande échelle sur Windows à l’aide de clés symétriques

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Cet article fournit des instructions de bout en bout pour l’approvisionnement automatique d’un ou plusieurs appareils IoT Edge Windows à l’aide de clés symétriques. Les appareils Azure IoT Edge peuvent être approvisionnés automatiquement à l’aide du [Service IoT Hub Device Provisioning](../iot-dps/index.yml) (DPS). Si vous ne connaissez pas le processus d’approvisionnement automatique, consultez la [présentation de l’approvisionnement](../iot-dps/about-iot-dps.md#provisioning-process) avant de poursuivre.

>[!NOTE]
>Azure IoT Edge pour conteneurs Windows ne sera pas pris en charge à partir de la version 1.2 d’Azure IoT Edge.
>
>Envisagez d’utiliser la nouvelle méthode pour exécuter IoT Edge sur des appareils Windows, [Azure IoT Edge pour Linux sur Windows](iot-edge-for-linux-on-windows.md).
>
>Si vous souhaitez utiliser Azure IoT Edge pour Linux sur Windows, vous pouvez effectuer les étapes décrites dans le [guide pratique équivalent](how-to-provision-devices-at-scale-linux-on-windows-symmetric.md).

Voici les tâches à effectuer :

1. Création d’une **inscription individuelle** pour un appareil unique ou d’une **inscription de groupe** pour un ensemble d’appareils
1. Installation du runtime IoT Edge et de la connexion à IoT Hub.

L’attestation de clé symétrique est une approche simple pour authentifier un appareil avec une instance du service d’approvisionnement des appareils. Cette méthode d’attestation représente une expérience « Hello world » pour les développeurs qui découvrent le provisionnement d’appareils ou n’ont pas d’exigences de sécurité strictes. L’attestation d’appareil avec un [Module de plateforme sécurisée (TPM)](../iot-dps/concepts-tpm-attestation.md) ou un [certificat X.509](../iot-dps/concepts-x509-attestation.md) est plus sécurisée, et doit être utilisée lorsque les exigences de sécurité sont plus strictes. <!-- note links here; they will break -->

## <a name="prerequisites"></a>Prérequis

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

### <a name="iot-edge-installation"></a>Installation d’IoT Edge

Un appareil Windows physique ou virtuel faisant office d’appareil IoT Edge.

Vous devrez définir un **ID d’inscription***unique* pour identifier chaque appareil. Vous pouvez utiliser l’adresse MAC, le numéro de série ou toute information unique provenant de l’appareil. Dans cet exemple, vous pouvez utiliser une combinaison d’une adresse MAC et d’un numéro de série, forment la chaîne suivante pour un ID d’inscription : `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`. Les caractères valides sont les caractères alphanumériques minuscules et les tirets (`-`).

<!-- Create a DPS enrollment using symmetric keys H2 and content -->
[!INCLUDE [iot-edge-create-dps-enrollment-symmetric.md](../../includes/iot-edge-create-dps-enrollment-symmetric.md)]

<!-- Install IoT Edge on Windows H2 and content -->
[!INCLUDE [install-iot-edge-windows.md](../../includes/iot-edge-install-windows.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>Provisionnement de l’appareil avec son identité cloud

Une fois le runtime installé sur votre appareil, configurez ce dernier avec les informations qu’il utilise pour se connecter au service DPS et à IoT Hub.

Préparez les informations suivantes :

* Valeur **Étendue de l’ID** du service Device Provisioning
* L’**ID d’inscription** de l’appareil que vous avez créé
* Entrez la **clé primaire** d’une inscription spécifique ou une [clé dérivée](#derive-a-device-key) pour les appareils utilisant une inscription de groupe.

1. Ouvrez une fenêtre PowerShell en mode administrateur. Veillez à utiliser une session AMD64 de PowerShell lors de l'installation d'IoT Edge, plutôt que PowerShell (x86).

1. La commande **Initialize-IoTEdge** configure le runtime IoT Edge sur votre ordinateur. La commande opère par défaut un approvisionnement manuel avec les conteneurs Windows. Vous devez donc ajouter l’indicateur `-DpsSymmetricKey` pour utiliser l’approvisionnement automatique avec l’authentification par clé symétrique.

   Remplacez les valeurs d’espace réservé pour `paste_scope_id_here`, `paste_registration_id_here` et `paste_symmetric_key_here` par les données que vous avez collectées précédemment.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsSymmetricKey -ScopeId paste_scope_id_here -RegistrationId paste_registration_id_here -SymmetricKey paste_symmetric key_here
   ```

## <a name="verify-successful-installation"></a>Vérifier la réussite de l’installation

Si le runtime a démarré correctement, vous pouvez accéder à votre IoT Hub et commencer à déployer des modules IoT Edge sur votre appareil.

# <a name="individual-enrollment"></a>[Inscription individuelle](#tab/individual-enrollment)

Vous pouvez vérifier que l’inscription individuelle que vous avez créée dans le service d’approvisionnement des appareils a été utilisée. Accédez à l’instance du service d’approvisionnement des appareils dans le portail Azure. Ouvrez les détails de l’inscription pour l’inscription individuelle que vous avez créée. Notez que l’état de l’inscription est **Affecté** et que l’ID de l’appareil figure dans la liste.

# <a name="group-enrollment"></a>[Inscription de groupe](#tab/group-enrollment)

Vous pouvez vérifier que l’inscription de groupe que vous avez créée dans le service d’approvisionnement des appareils a été utilisée. Accédez à l’instance du service d’approvisionnement des appareils dans le portail Azure. Ouvrez les détails de l’inscription de groupe que vous avez créée. Accédez à l’onglet **Enregistrements d’inscription** pour afficher tous les appareils enregistrés dans ce groupe.

---

Utilisez les commandes suivantes sur votre appareil pour vérifier que IoT Edge a été installé et démarré correctement.

Vérifiez l’état du service IoT Edge.

```powershell
Get-Service iotedge
```

Consultez les journaux d’activité de service.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Répertoriez les modules en cours d’exécution.

```powershell
iotedge list
```

## <a name="next-steps"></a>Étapes suivantes

Le processus d’inscription auprès du service de provisionnement des appareils vous permet de définir l’ID d’appareil et les balises du jumeau d’appareil en même temps que vous provisionnez le nouvel appareil. Vous pouvez utiliser ces valeurs pour cibler des appareils individuels ou des groupes d’appareils avec la gestion d’appareils automatique. En savoir plus sur [Déployer et surveiller des modules IoT Edge à grande échelle à l’aide du portail Azure](how-to-deploy-at-scale.md) ou [d’Azure CLI](how-to-deploy-cli-at-scale.md).
