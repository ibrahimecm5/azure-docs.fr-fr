---
title: Créer et approvisionner des appareils IoT Edge à l’aide de clés symétriques pour Linux sur Windows - Azure IoT Edge | Microsoft Docs
description: Utiliser l’attestation de clés symétriques pour tester le provisionnement des appareils Linux sur Windows à grande échelle pour Azure IoT Edge avec le service de provisionnement des appareils
author: kgremban
ms.author: kgremban
ms.reviewer: v-tcassi
ms.date: 10/29/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 250a11c7f79bc4c19327523075b369b9dd85a3de
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131506236"
---
# <a name="create-and-provision-iot-edge-for-linux-on-windows-devices-at-scale-using-symmetric-keys"></a>Créer et approvisionner des IoT Edge pour Linux sur des appareils Windows à grande échelle à l’aide de clés symétriques 

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Cet article fournit des instructions complètes pour le provisionnement automatique d’un ou de plusieurs appareils [IoT Edge pour Linux sur Windows](iot-edge-for-linux-on-windows.md) à l’aide de clés symétriques. Les appareils Azure IoT Edge peuvent être provisionnés automatiquement à l’aide du [service Azure IoT Hub Device Provisioning](../iot-dps/index.yml) (DPS). Si vous ne connaissez pas le processus de provisionnement automatique, consultez la [présentation du provisionnement](../iot-dps/about-iot-dps.md#provisioning-process) avant de poursuivre.

Voici les tâches à effectuer :

1. Créer une **inscription individuelle** pour un seul appareil ou une **inscription de groupe** pour un ensemble d’appareils.
1. Déploiement d’une machine virtuelle Linux avec le runtime IoT Edge installé, et connexion de cette machine à IoT Hub

L’attestation de clés symétriques est une approche simple pour authentifier un appareil avec une instance du service de provisionnement des appareils. Cette méthode d’attestation représente une expérience « Hello world » pour les développeurs qui découvrent le provisionnement d’appareils ou n’ont pas d’exigences de sécurité strictes. L’attestation d’appareil avec un [Module de plateforme sécurisée (TPM)](../iot-dps/concepts-tpm-attestation.md) ou un [certificat X.509](../iot-dps/concepts-x509-attestation.md) est plus sécurisée, et doit être utilisée lorsque les exigences de sécurité sont plus strictes.

## <a name="prerequisites"></a>Prérequis

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

<!-- IoT Edge for Linux on Windows installation prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-linux-on-windows.md](../../includes/iot-edge-prerequisites-linux-on-windows.md)]

<!-- Create a DPS enrollment using symmetric keys H2 and content -->
[!INCLUDE [iot-edge-create-dps-enrollment-symmetric.md](../../includes/iot-edge-create-dps-enrollment-symmetric.md)]

<!-- Install IoT Edge for Linux on Windows H2 and content -->
[!INCLUDE [install-iot-edge-linux-on-windows.md](../../includes/iot-edge-install-linux-on-windows.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>Provisionnement de l’appareil avec son identité cloud

Une fois le runtime installé sur votre appareil, configurez ce dernier avec les informations qu’il utilise pour se connecter au service DPS et à IoT Hub.

Préparez les informations suivantes :

* Valeur **Étendue de l’ID** du service Device Provisioning
* L’**ID d’inscription** de l’appareil que vous avez créé
* Entrez la **clé primaire** d’une inscription spécifique ou une [clé dérivée](#derive-a-device-key) pour les appareils utilisant une inscription de groupe.

Vous pouvez utiliser PowerShell ou Windows Admin Center pour provisionner votre appareil IoT Edge.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour PowerShell, exécutez la commande suivante en mettant à jour les valeurs d’espace réservé avec vos propres valeurs :

```powershell
Provision-EflowVm -provisioningType DpsSymmetricKey -scopeId PASTE_YOUR_ID_SCOPE_HERE -registrationId PASTE_YOUR_REGISTRATION_ID_HERE -symmKey PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE
```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Pour Windows Admin Center, procédez comme suit :

1. Dans le volet **Approvisionnement de l’appareil Azure IoT Edge**, sélectionnez **Clés symétriques (DPS)** dans le menu déroulant des méthodes d’approvisionnement.

1. Dans le [portail Azure](https://ms.portal.azure.com/), accédez à votre instance DPS.

1. Indiquez votre ID d’étendue DPS, l’ID d’inscription de l’appareil et la clé primaire ou la clé dérivée de l’inscription dans les champs Windows Admin Center.

1. Sélectionnez **Approvisionnement avec la méthode sélectionnée**.

   ![Sélectionnez Provisionnement avec la méthode sélectionnée après avoir renseigné les champs requis pour le provisionnement avec clé symétrique.](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/provisioning-with-selected-method-symmetric-key.png)

1. Une fois l’approvisionnement terminé, sélectionnez **Terminer**. Vous êtes redirigé vers le tableau de bord principal. Un nouvel appareil, dont le type est `IoT Edge Devices`, doit maintenant apparaître dans la liste. Vous pouvez sélectionner l’appareil IoT Edge pour vous y connecter. Une fois sur la page de **vue d’ensemble**, vous pouvez consulter la **liste des modules IoT Edge** et l’**état IoT Edge** de votre appareil.

---

## <a name="verify-successful-configuration"></a>Vérifier que la configuration a réussi

Vérifiez que IoT Edge pour Linux sur Windows a été correctement installé et configuré sur votre appareil IoT Edge.

# <a name="individual-enrollment"></a>[Inscription individuelle](#tab/individual-enrollment)

Vous pouvez vérifier que l’inscription individuelle que vous avez créée dans le service de provisionnement des appareils a été utilisée. Accédez à l’instance du service de provisionnement des appareils dans le portail Azure. Ouvrez les détails de l’inscription pour l’inscription individuelle que vous avez créée. Notez que l’état de l’inscription est **Affecté** et que l’ID de l’appareil figure dans la liste.

# <a name="group-enrollment"></a>[Inscription de groupe](#tab/group-enrollment)

Vous pouvez vérifier que l’inscription de groupe que vous avez créée dans le service de provisionnement des appareils a été utilisée. Accédez à l’instance du service de provisionnement des appareils dans le portail Azure. Ouvrez les détails de l’inscription de groupe que vous avez créée. Accédez à l’onglet **Enregistrements d’inscription** pour afficher tous les appareils enregistrés dans ce groupe.

---

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Connectez-vous à votre IoT Edge pour Linux sur machine virtuelle Windows en utilisant la commande suivante dans votre session PowerShell :

   ```powershell
   Connect-EflowVm
   ```

   >[!NOTE]
   >Le seul compte autorisé à accéder via SSH à la machine virtuelle est celui de l’utilisateur qui l’a créée.

1. Une fois connecté, vous pouvez vérifier la liste des modules IoT Edge en cours d’exécution à l’aide de la commande Linux suivante :

   ```bash
   sudo iotedge list
   ```

1. Si vous devez dépanner le service IoT Edge, utilisez les commandes Linux suivantes.

    1. Récupérez les journaux de service.

       ```bash
       sudo journalctl -u iotedge
       ```

    2. Utilisez l’outil `check` pour vérifier l’état de la configuration et de la connexion de l’appareil.

       ```bash
       sudo iotedge check
       ```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Sélectionnez votre appareil IoT Edge dans la liste des appareils connectés dans Windows Admin Center pour vous y connecter.

1. La page de vue d’ensemble de l’appareil affiche des informations sur l’appareil :

   * La section **Liste des modules IoT Edge** affiche les modules en cours d’exécution sur l’appareil. Lors du premier démarrage du service IoT Edge, seul le module **edgeAgent** apparaît dans cette liste. Le module edgeAgent s’exécute par défaut et vous aide à installer et démarrer tous les modules supplémentaires que vous déployez sur votre appareil.

   * La section **État IoT Edge** indique l’état du service, qui doit être **actif (en cours d’exécution)** .

---

Lorsque vous créez un appareil IoT Edge, il affichera le code d’état `417 -- The device's deployment configuration is not set` dans le portail Azure. Cet état est normal et signifie que l’appareil est prêt à recevoir un déploiement de module.

## <a name="next-steps"></a>Étapes suivantes

Le processus d’inscription dans le service de provisionnement des appareils vous permet de définir les ID d’appareil et les étiquettes de jumeau d’appareil en même temps que vous provisionnez le nouvel appareil. Vous pouvez utiliser ces valeurs pour cibler des appareils individuels ou des groupes d’appareils avec la gestion d’appareils automatique. En savoir plus sur [Déployer et surveiller des modules IoT Edge à grande échelle à l’aide du portail Azure](how-to-deploy-at-scale.md) ou [d’Azure CLI](how-to-deploy-cli-at-scale.md).

Vous pouvez également :

* Passez à [Déployer des modules IoT Edge](how-to-deploy-modules-portal.md) pour savoir comment déployer des modules sur votre appareil.
* Découvrez comment [gérer les certificats sur votre IoT Edge pour Linux sur une machine virtuelle Windows](how-to-manage-device-certificates.md) et à transférer des fichiers du système d’exploitation hôte vers votre machine virtuelle Linux.
* Découvrez comment [configurer vos appareils IoT Edge pour qu’ils communiquent par le biais d’un serveur proxy](how-to-configure-proxy-support.md).
