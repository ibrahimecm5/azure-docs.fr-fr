---
title: Créer et provisionner des appareils IoT Edge avec des certificats X.509 sur Linux sur Windows - Azure IoT Edge | Microsoft Docs
description: Utiliser l’attestation de certificat X.509 pour tester le provisionnement d’appareils à grande échelle pour Azure IoT Edge avec le service de provisionnement des appareils
author: kgremban
ms.author: kgremban
ms.reviewer: v-tcassi
ms.date: 10/28/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 42cdee3f9b4354e2396f14e17f5f146c27ff29c5
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131506127"
---
# <a name="create-and-provision-iot-edge-for-linux-on-windows-devices-at-scale-using-x509-certificates"></a>Créer et provisionner IoT Edge pour Linux sur des appareils Windows à grande échelle avec des certificats X.509

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Cet article fournit des instructions complètes pour le provisionnement automatique d’un ou de plusieurs appareils [IoT Edge pour Linux sur Windows](iot-edge-for-linux-on-windows.md) à l’aide de certificats X.509. Les appareils Azure IoT Edge peuvent être provisionnés automatiquement à l’aide du [service Azure IoT Hub Device Provisioning](../iot-dps/index.yml) (DPS). Si vous ne connaissez pas le processus de provisionnement automatique, consultez la [présentation du provisionnement](../iot-dps/about-iot-dps.md#provisioning-process) avant de poursuivre.

Voici les tâches à effectuer :

1. Génération de certificats et de clés
1. Création d’une **inscription individuelle** pour un appareil ou d’une **inscription de groupe** pour un ensemble d’appareils
1. Déploiement d’une machine virtuelle Linux avec le runtime IoT Edge installé, et connexion de cette machine à IoT Hub

Utiliser des certificats X.509 comme mécanisme d’attestation est un excellent moyen de mettre à l’échelle la production et de simplifier le provisionnement des appareils. Les certificats X.509 sont généralement organisés en une chaîne d’approbation de confiance. À partir d’un certificat racine auto-signé ou approuvé, chaque certificat de la chaîne signe le certificat inférieur suivant. Ce modèle crée une chaîne déléguée de confiance depuis le certificat racine jusqu’au certificat « feuille » final installé sur un appareil, en passant par chaque certificat intermédiaire.

## <a name="prerequisites"></a>Prérequis

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

<!-- IoT Edge for Linux on Windows installation prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-linux-on-windows.md](../../includes/iot-edge-prerequisites-linux-on-windows.md)]

## <a name="generate-device-identity-certificates"></a>Générer des certificats d’identité d’appareil

Le certificat d’identité d’appareil est un certificat feuille qui se connecte par le biais d’une chaîne de certificats de confiance au certificat d’autorité de certification X.509 supérieur. Le nom commun (CN) du certificat d’identité d’appareil doit être défini sur l’ID d’appareil que vous souhaitez que l’appareil ait dans votre hub IoT.

Les certificats d’identité d’appareil sont utilisés uniquement pour provisionner l’appareil IoT Edge et l’authentifier auprès d’Azure IoT Hub. Ils ne signent pas de certificats, contrairement aux certificats d’autorité de certification que l’appareil IoT Edge présente aux modules ou aux appareils de nœud terminal à des fins de vérification. Pour plus d’informations, consultez les [détails sur l’utilisation des certificats par Azure IoT Edge](iot-edge-certs.md).

Après avoir créé le certificat d’identité d’appareil, vous devez avoir deux fichiers : un fichier .cer ou .pem, qui contient la partie publique du certificat, et un fichier .cer ou .pem, qui comporte la clé privée du certificat. Si vous envisagez d’utiliser l’inscription de groupe dans le service Device Provisioning, vous avez également besoin de la partie publique d’un certificat d’autorité de certification racine ou intermédiaire dans la même chaîne de certificats de confiance.

Les fichiers suivants sont nécessaires pour configurer le provisionnement automatique avec X.509 :

* Le certificat d’identité d’appareil et son certificat de clé privée. Le certificat d’identité d’appareil est chargé dans DPS si vous créez une inscription individuelle. La clé privée est transmise au runtime IoT Edge.
* Un certificat de chaîne complète, qui doit comporter au moins l’identité d’appareil et les certificats intermédiaires qu’elle contient. Le certificat de chaîne complète est transmis au runtime IoT Edge.
* Un certificat d’autorité de certification racine ou intermédiaire issu de la chaîne d’approbation des certificats. Ce certificat est chargé dans DPS si vous créez une inscription de groupe.

> [!NOTE]
> Il existe actuellement dans libiothsm une limitation empêchant l’utilisation de certificats qui expirent le 1er janvier 2038 ou après cette date.

### <a name="use-test-certificates-optional"></a>Utiliser des certificats de test (facultatif)

Si vous n’avez pas d’autorité de certification disponible pour créer des certificats d’identité et que vous souhaitez tester ce scénario, le dépôt Git Azure IoT Edge contient des scripts que vous pouvez utiliser pour générer des certificats de test. Ces certificats, conçus uniquement pour les tests de développement, ne doivent pas être utilisés en production.

Pour créer des certificats de test, suivez les étapes décrites dans [Créer des certificats de démonstration pour tester les fonctionnalités de l’appareil IoT Edge](how-to-create-test-certificates.md). Effectuez les deux sections nécessaires pour configurer les scripts de génération de certificat et créer un certificat d’autorité de certification racine. Ensuite, suivez les étapes permettant de créer un certificat d’identité d’appareil. Quand vous avez terminé, vous devez disposer de la chaîne de certificats et de la paire de clés suivantes :

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

Vous avez besoin de ces deux certificats sur l’appareil IoT Edge. Si vous envisagez d’utiliser l’inscription individuelle dans le service Device Provisioning, vous devez charger le fichier .cert.pem. Si vous envisagez d’utiliser l’inscription de groupe dans le service Device Provisioning, la même chaîne de certificats de confiance doit également comporter un certificat d’autorité de certification racine ou intermédiaire à charger. Si vous recourez à des certificats de démonstration, utilisez le certificat `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` pour l’inscription de groupe.

<!-- Create a DPS enrollment using X.509 certificates H2 and content -->
[!INCLUDE [iot-edge-create-dps-enrollment-x509.md](../../includes/iot-edge-create-dps-enrollment-x509.md)]

<!-- Install IoT Edge for Linux on Windows H2 and content -->
[!INCLUDE [install-iot-edge-linux-on-windows.md](../../includes/iot-edge-install-linux-on-windows.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>Provisionnement de l’appareil avec son identité cloud

Une fois le runtime installé sur votre appareil, configurez ce dernier avec les informations qu’il utilise pour se connecter au service DPS et à IoT Hub.

Préparez les informations suivantes :

* La valeur **Étendue de l’ID** du service Device Provisioning. Vous pouvez récupérer cette valeur à partir de la page de présentation de votre instance du service Device Provisioning dans le portail Azure.
* Le fichier de chaîne de certificats d’identité d’appareil sur l’appareil
* Le fichier de clé d’identité d’appareil sur l’appareil

Vous pouvez utiliser PowerShell ou Windows Admin Center pour provisionner votre appareil IoT Edge.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour PowerShell, exécutez la commande suivante en mettant à jour les valeurs d’espace réservé avec vos propres valeurs :

```powershell
Provision-EflowVm -provisioningType DpsX509 -scopeId PASTE_YOUR_ID_SCOPE_HERE -registrationId PASTE_YOUR_REGISTRATION_ID_HERE -identityCertPath PASTE_ABSOLUTE_PATH_TO_IDENTITY_CERTIFICATE_HERE -identityPrivateKey PASTE_ABSOLUTE_PATH_TO_IDENTITY_PRIVATE_KEY_HERE
```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Pour Windows Admin Center, procédez comme suit :

1. Dans le volet **Provisionnement de l’appareil Azure IoT Edge**, sélectionnez **Certificat X.509 (DPS)** dans le menu déroulant des méthodes de provisionnement.

1. Indiquez votre ID d’étendue DPS, l’ID d’inscription de l’appareil et la clé primaire ou la clé dérivée de l’inscription dans les champs Windows Admin Center.

1. Sélectionnez **Approvisionnement avec la méthode sélectionnée**.

   ![Sélectionnez Provisionnement avec la méthode sélectionnée après avoir renseigné les champs requis pour le provisionnement avec clé symétrique.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/provisioning-with-selected-method-x509-certs.png)

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

    1. Si vous avez besoin de résoudre les problèmes du service, récupérez les journaux d’activité de ce dernier.

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
