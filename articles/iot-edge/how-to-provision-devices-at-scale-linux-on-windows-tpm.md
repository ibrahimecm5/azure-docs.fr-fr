---
title: Créer et provisionner un appareil IoT Edge pour Linux sur Windows à l’aide d’un TPM - Azure IoT Edge | Microsoft Docs
description: Utiliser un TPM simulé sur un appareil Linux sur Windows afin de tester le service de provisionnement des appareils Azure pour Azure IoT Edge.
author: kgremban
manager: lizross
ms.author: kgremban
ms.reviewer: fcabrera
ms.date: 10/28/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 1afc8bb1a8932fb808073cfdb9468c126c407b1e
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131579335"
---
# <a name="create-and-provision-an-iot-edge-for-linux-on-windows-device-at-scale-by-using-a-tpm"></a>Créer et provisionner un appareil IoT Edge pour Linux sur Windows à grande échelle à l’aide d’un TPM

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Cet article fournit des instructions concernant le provisionnement automatique d’un appareil IoT Edge pour Linux sur Windows avec un module de plateforme sécurisée (TPM). Les appareils Azure IoT Edge peuvent être provisionnés automatiquement à l’aide du [service Azure IoT Hub Device Provisioning](../iot-dps/index.yml). Si vous ne connaissez pas le processus de provisionnement automatique, consultez la [présentation du provisionnement](../iot-dps/about-iot-dps.md#provisioning-process) avant de poursuivre.

Cet article décrit deux méthodologies. Sélectionnez votre préférence en fonction de l’architecture de votre solution :

- Provisionnement automatique d’un appareil Linux sur Windows avec du matériel TPM physique.
- Provisionnement automatique d’un appareil Linux sur Windows à l’aide d’un TPM simulé. Nous vous recommandons cette méthodologie uniquement comme scénario de test. Un module de plateforme sécurisée simulé n’offre pas la même sécurité qu’un module de plateforme sécurisée physique.

Voici les tâches à effectuer :

# <a name="physical-tpm"></a>[TPM physique](#tab/physical-tpm)

* Installez IoT Edge pour Linux sur Windows.
* Récupérez les informations du module de plateforme sécurisée à partir de votre appareil.
* Création d’une inscription individuelle pour l’appareil.
* Approvisionnez votre appareil avec ses informations de module de plateforme sécurisée.

# <a name="simulated-tpm"></a>[TPM simulé](#tab/simulated-tpm)

* Installez IoT Edge pour Linux sur Windows.
* Configurez votre TPM simulé et récupérez ses informations de configuration.
* Création d’une inscription individuelle pour l’appareil.
* Approvisionnez votre appareil avec ses informations de module de plateforme sécurisée.

---

## <a name="prerequisites"></a>Prérequis

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

<!-- IoT Edge for Linux on Windows installation prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-linux-on-windows.md](../../includes/iot-edge-prerequisites-linux-on-windows.md)]

> [!NOTE]
> TPM 2.0 est requis lorsque vous utilisez l’attestation TPM avec le service de provisionnement des appareils.
>
> Vous pouvez uniquement créer des inscriptions de service de provisionnement des appareils, et non de groupe, lorsque vous utilisez un module de plateforme sécurisée.

<!-- Install IoT Edge for Linux on Windows H2 and content -->
[!INCLUDE [install-iot-edge-linux-on-windows.md](../../includes/iot-edge-install-linux-on-windows.md)]

Il existe quelques étapes pour préparer votre appareil pour le provisionnement avec un TPM. Laissez votre déploiement ouvert pendant que vous préparez votre appareil. Vous reviendrez à votre déploiement plus loin dans cet article.

## <a name="enable-tpm-passthrough"></a>Activer le relais TPM

La machine virtuelle IoT Edge pour Linux sur Windows dispose d’une fonctionnalité TPM qui peut être activée ou désactivée. Par défaut, elle est désactivée. Lorsque cette fonctionnalité est activée, la machine virtuelle peut accéder au module de plateforme sécurisée de l’ordinateur hôte.

1. Ouvrez une session PowerShell avec élévation de privilèges.

1. Si ce n’est encore fait, définissez la stratégie d’exécution de votre appareil sur `AllSigned` afin de pouvoir exécuter les fonctions PowerShell IoT Edge pour Linux sur Windows.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. Activez la fonctionnalité TPM.

   ```powershell
   Set-EflowVmFeature -feature 'DpsTpm' -enable
   ```

## <a name="retrieve-the-tpm-information-from-your-device"></a>Récupérer les informations du module de plateforme sécurisée à partir de votre appareil

# <a name="physical-tpm"></a>[TPM physique](#tab/physical-tpm)

Pour provisionner votre appareil, vous devez recueillir des informations de votre puce TPM et les fournir à votre instance du service ce provisionnement des appareils afin que le service puisse reconnaître votre appareil quand il tente de se connecter.

Tout d’abord, vous devez déterminer la paire de clés de type EK (Endorsement Key), propre à chaque puce TPM et fournie par le fabricant de la puce TPM associée. Vous devez ensuite fournir un ID d’inscription pour votre appareil. Vous pouvez dériver un ID d’inscription unique pour votre appareil TPM, en créant par exemple un code de hachage SHA-256 pour la paire de clés de type EK.

IoT Edge pour Linux sur Windows fournit un script PowerShell pour vous aider à récupérer ces informations à partir de votre TPM. Pour utiliser le script, procédez comme suit sur votre appareil :

1. Ouvrez une session PowerShell avec élévation de privilèges.

1. Exécutez la commande.

   ```powershell
   Get-EflowVmTpmProvisioningInfo
   ```

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

## <a name="provision-the-device-with-its-cloud-identity"></a>Provisionnement de l’appareil avec son identité cloud

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Ouvrez une session PowerShell avec élévation de privilèges sur l’appareil Windows.

1. Provisionnez votre appareil à l’aide de l’**ID d’étendue** que vous avez collecté à partir de votre instance du service de provisionnement des appareils.

   ```powershell
   Provision-EflowVM -provisioningType "DpsTpm" -scopeId "<scope id>"
   ```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. À l’étape **Connecter**, approvisionnez votre appareil.

   1. Sélectionnez la méthode d’approvisionnement **DpsTpm**.
   1. Indiquez l’**ID d’étendue** que vous récupérez à partir de votre instance du service de provisionnement des appareils.

      ![Capture d’écran montrant comment provisionner votre appareil avec le service de provisionnement des appareils et l’attestation TPM.](./media/how-to-auto-provision-tpm-linux-on-windows/tpm-provision.png)

1. Sélectionnez **Provisionnement avec la méthode sélectionnée**.

1. Une fois que IoT Edge a été correctement installé et configuré sur votre appareil, sélectionnez **Terminer** pour quitter l’Assistant déploiement.

---

## <a name="verify-successful-configuration"></a>Vérifier que la configuration a réussi

Vérifiez que IoT Edge pour Linux sur Windows a été correctement installé et configuré sur votre appareil IoT Edge.

Si le runtime a démarré correctement, vous pouvez accéder à votre IoT Hub et commencer à déployer des modules IoT Edge sur votre appareil.

Vous pouvez vérifier que l’inscription individuelle que vous avez créée dans le service de provisionnement des appareils a été utilisée. Accédez à l’instance du service de provisionnement des appareils dans le portail Azure. Ouvrez les détails de l’inscription pour l’inscription individuelle que vous avez créée. Notez que l’état de l’inscription est **Affecté** et que l’ID de l’appareil figure dans la liste.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Utilisez les commandes suivantes sur votre appareil pour vérifier que IoT Edge a été installé et démarré correctement.

1. Connectez-vous à votre machine virtuelle IoT Edge pour Linux sur Windows en utilisant la commande suivante dans votre session PowerShell :

   ```powershell
   Connect-EflowVm
   ```

   >[!NOTE]
   >Le seul compte autorisé à bénéficier d’un accès via SSH à la machine virtuelle est l’utilisateur qui l’a créée.

1. Une fois que vous êtes connecté, vous pouvez vérifier la liste des modules IoT Edge en cours d’exécution à l’aide de la commande Linux suivante :

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

   * La section **Liste des modules IoT Edge** affiche les modules en cours d’exécution sur l’appareil. Lors du premier démarrage du service IoT Edge, seul le module **edgeAgent** apparaît dans cette liste. Le module edgeAgent s’exécute par défaut et vous aide à installer et démarrer d’autres modules que vous déployez sur votre appareil.
   * La section **État IoT Edge** indique l’état du service, qui doit être **actif (en cours d’exécution)** .

---

## <a name="next-steps"></a>Étapes suivantes

Le processus d’inscription auprès du service de provisionnement des appareils vous permet de définir l’ID d’appareil et les balises du jumeau d’appareil en même temps que vous provisionnez le nouvel appareil. Vous pouvez utiliser ces valeurs pour cibler des appareils individuels ou des groupes d’appareils avec la gestion d’appareils automatique.

Découvrez comment [déployer et surveiller des modules IoT Edge à grande échelle à l’aide du portail Azure](how-to-deploy-at-scale.md) ou [d’Azure CLI](how-to-deploy-cli-at-scale.md).