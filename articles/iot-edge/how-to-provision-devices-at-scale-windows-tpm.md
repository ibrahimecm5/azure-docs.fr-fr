---
title: Créer et provisionner des appareils avec un TPM virtuel sur Windows – Azure IoT Edge | Microsoft Docs
description: Utiliser un TPM simulé sur un appareil Windows afin de tester le service de provisionnement des appareils Azure pour Azure IoT Edge
author: kgremban
ms.author: kgremban
ms.date: 10/28/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 4211cd69cddfea77ccd3f6e2a095ced6ce5effc3
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853868"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-with-a-tpm-on-windows"></a>Créer et provisionner des appareils IoT Edge à grande échelle avec un module TPM sur Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Cet article fournit des instructions concernant le provisionnement automatique d’un appareil Azure IoT Edge pour Windows en utilisant un TPM (module de plateforme sécurisée). Les appareils IoT Edge peuvent être provisionnés automatiquement à l’aide du [service Azure IoT Hub Device Provisioning](../iot-dps/index.yml). Si vous ne connaissez pas le processus de provisionnement automatique, consultez la [présentation du provisionnement](../iot-dps/about-iot-dps.md#provisioning-process) avant de poursuivre.

>[!NOTE]
>Azure IoT Edge pour conteneurs Windows ne sera pas pris en charge à partir de la version 1.2 d’Azure IoT Edge.
>
>Envisagez d’utiliser la nouvelle méthode pour exécuter IoT Edge sur des appareils Windows, [Azure IoT Edge pour Linux sur Windows](iot-edge-for-linux-on-windows.md).
>
>Si vous souhaitez utiliser Azure IoT Edge pour Linux sur Windows, vous pouvez effectuer les étapes décrites dans le [guide pratique équivalent](how-to-provision-devices-at-scale-linux-on-windows-tpm.md).

Cet article décrit deux méthodologies. Sélectionnez votre préférence en fonction de l’architecture de votre solution :

- Provisionnement automatique d’un appareil Windows avec du matériel TPM physique.
- Provisionnement automatique d’un appareil Windows avec un TPM simulé. Nous vous recommandons cette méthodologie uniquement comme scénario de test. Un module de plateforme sécurisée simulé n’offre pas la même sécurité qu’un module de plateforme sécurisée physique.

Les instructions varient en fonction de la méthode choisie. Vérifiez que vous vous trouvez dans l’onglet approprié.

Voici les tâches à effectuer :

# <a name="physical-tpm"></a>[TPM physique](#tab/physical-tpm)

* Récupérez les informations d’approvisionnement de votre appareil.
* Création d’une inscription individuelle pour l’appareil.
* Installation du runtime IoT Edge et connexion de l’appareil à IoT Hub.

# <a name="simulated-tpm"></a>[TPM simulé](#tab/simulated-tpm)

* Configurez votre TPM simulé et récupérez ses informations de configuration.
* Création d’une inscription individuelle pour l’appareil.
* Installation du runtime IoT Edge et connexion de l’appareil à IoT Hub.

---

## <a name="prerequisites"></a>Prérequis

Les prérequis sont les mêmes pour les solutions de TPM physique et de TPM virtuel.

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

### <a name="iot-edge-installation"></a>Installation d’IoT Edge

Une machine de développement Windows. Cet article utilise Windows 10.

> [!NOTE]
> TPM 2.0 est requis lorsque vous utilisez l’attestation TPM avec le service de provisionnement des appareils.
>
> Vous pouvez uniquement créer des inscriptions de service de provisionnement des appareils, et non de groupe, lorsque vous utilisez un module de plateforme sécurisée.

## <a name="set-up-your-tpm"></a>Configurer votre TPM

# <a name="physical-tpm"></a>[TPM physique](#tab/physical-tpm)

Dans cette section, vous créez un outil permettant de récupérer l’ID d’inscription et la paire de clés de type EK (Endorsement key) de votre module TPM.

1. Suivez les étapes décrites dans [Configurer un environnement de développement Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) pour installer et générer l’Azure IoT device SDK pour C.

1. Exécutez les commandes suivantes dans une session PowerShell avec élévation de privilèges pour créer l’outil Kit de développement logiciel (SDK) qui récupère les informations d’approvisionnement de votre appareil pour votre TPM.

   ```powershell
   cd azure-iot-sdk-c\cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client\tools\tpm_device_provision
   make
   .\tpm_device_provision
   ```

1. La fenêtre Sortie affiche **l’ID d’inscription** et la **Paire de clés de type EK (Endorsement Key)** de l’appareil. Copiez ces valeurs. Vous vous en servirez plus tard, au moment de créer une inscription individuelle pour votre appareil dans le service de provisionnement des appareils.

> [!TIP]
> Si vous ne souhaitez pas utiliser l’outil SDK pour récupérer les informations, vous devez trouver un autre moyen d’obtenir les informations de provisionnement. La paire de clés de type EK, propre à chaque puce TPM, est fournie par le fabricant de la puce TPM associée. Vous pouvez dériver un ID d’inscription unique pour votre appareil TPM. Par exemple, vous pouvez créer un hachage SHA-256 de la paire de clés de type EK.

Une fois que vous avez votre ID d’inscription et votre paire de clés de type EK, vous pouvez continuer.

# <a name="simulated-tpm"></a>[TPM simulé](#tab/simulated-tpm)

Si vous n’avez pas de module de plateforme sécurisée (TPM) physique disponible et que vous souhaitez tester cette méthode d’approvisionnement, vous pouvez simuler un module de plateforme sécurisée sur votre appareil.

Le service de provisionnement des appareils IoT Hub fournit des exemples qui simulent un TPM et retournent pour vous la paire de clés de type EK (Endorsement Key) et l’ID d’inscription.

1. Choisissez l’un des exemples de la liste suivante, en fonction de votre langage par défaut.
1. Arrêtez de suivre les étapes de l’exemple de service de provisionnement des appareils après que le TPM est en cours d’exécution et que vous avez collecté la **paire de clés de type EK (Endorsement Key)** et l’**ID d’inscription**. N’appuyez pas sur **Entrée** pour exécuter l’inscription dans l’exemple d’application.
1. Laissez la fenêtre hébergeant le TPM en cours d’exécution jusqu’à ce que vous ayez terminé le test de ce scénario.
1. Revenez à cet article pour créer une inscription du service de provisionnement des appareils et configurer votre appareil.

Exemples de TPM simulés :

* [C](../iot-dps/quick-create-simulated-device-tpm.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm.md)
* [Node.JS](../iot-dps/quick-create-simulated-device-tpm.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm.md)

---

<!-- Create an enrollment for your device using TPM provisioning information H2 and content -->
[!INCLUDE [tpm-create-a-device-provision-service-enrollment.md](../../includes/tpm-create-a-device-provision-service-enrollment.md)]

<!-- Install IoT Edge on Windows H2 and content -->
[!INCLUDE [install-iot-edge-windows.md](../../includes/iot-edge-install-windows.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>Provisionnement de l’appareil avec son identité cloud

Une fois le runtime installé sur votre appareil, configurez ce dernier avec les informations qu’il utilise pour se connecter au service de provisionnement des appareils et à IoT Hub.

1. Récupérez les valeurs **Étendue de l’ID** du service de provisionnement des appareils et **ID d’inscription** de l’appareil qui ont été collectées dans les sections précédentes.

1. Ouvrez une fenêtre PowerShell en mode administrateur. Veillez à utiliser une session AMD64 de PowerShell, et pas PowerShell (x86), quand vous installez IoT Edge.

1. La commande `Initialize-IoTEdge` configure le runtime IoT Edge sur votre ordinateur. Par défaut, la commande est une commande d’approvisionnement manuel avec des conteneurs Windows. Ajoutez l’indicateur `-Dps` pour utiliser le service de provisionnement des appareils à la place du provisionnement manuel.

   Remplacez les valeurs d'espace réservé de `paste_scope_id_here` et `paste_registration_id_here` par les données que vous avez collectées précédemment.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId paste_scope_id_here -RegistrationId paste_registration_id_here
   ```

## <a name="verify-successful-installation"></a>Vérifier la réussite de l’installation

Si le runtime a démarré correctement, accédez à votre IoT Hub et commencez à déployer des modules IoT Edge sur votre appareil. Utilisez les commandes suivantes sur votre appareil pour vérifier que le runtime a été installé et démarré correctement.

1. Vérifiez l’état du service IoT Edge.

    ```powershell
    Get-Service iotedge
    ```

1. Examinez les journaux d’activité du service des 5 dernières minutes.

    ```powershell
    . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
    ```

1. Répertoriez les modules en cours d’exécution.

    ```powershell
    iotedge list
    ```

## <a name="next-steps"></a>Étapes suivantes

Le processus d’inscription auprès du service de provisionnement des appareils vous permet de définir l’ID d’appareil et les balises du jumeau d’appareil en même temps que vous provisionnez le nouvel appareil. Vous pouvez utiliser ces valeurs pour cibler des appareils individuels ou des groupes d’appareils avec la gestion d’appareils automatique.

Découvrez comment [déployer et surveiller des modules IoT Edge à grande échelle à l’aide du portail Azure](how-to-deploy-at-scale.md) ou [d’Azure CLI](how-to-deploy-cli-at-scale.md).
