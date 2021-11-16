---
title: Créer et provisionner des appareils avec un TPM virtuel sur Linux - Azure IoT Edge
description: Utilisez un TPM simulé sur un appareil Linux afin de tester le service Azure IoT Hub Device Provisioning pour Azure IoT Edge.
author: kgremban
manager: lizross
ms.author: kgremban
ms.date: 10/28/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b353f683a64444a0ad89f062d0b826484260681f
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852044"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-with-a-tpm-on-linux"></a>Créer et provisionner des appareils IoT Edge à grande échelle avec un module TPM sur Linux

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Cet article fournit des instructions concernant le provisionnement automatique d’un appareil Azure IoT Edge pour Linux en utilisant un TPM (module de plateforme sécurisée). Les appareils IoT Edge peuvent être provisionnés automatiquement à l’aide du [service Azure IoT Hub Device Provisioning](../iot-dps/index.yml). Si vous ne connaissez pas le processus de provisionnement automatique, consultez la [présentation du provisionnement](../iot-dps/about-iot-dps.md#provisioning-process) avant de poursuivre.

Cet article décrit deux méthodologies. Sélectionnez votre préférence en fonction de l’architecture de votre solution :

- Provisionnement automatique d’un appareil Linux avec du matériel TPM physique. Le TPM [Infineon OPTIGA&trade; SLB 9670](https://devicecatalog.azure.com/devices/3f52cdee-bbc4-d74e-6c79-a2546f73df4e) est un exemple de TPM physique.
- Provisionnement automatique d’une machine virtuelle Linux avec un module TPM simulé exécuté sur un ordinateur de développement Windows sur lequel Hyper-V est activé. Nous vous recommandons d’utiliser cette méthode uniquement dans un scénario de test. Un module de plateforme sécurisée simulé n’offre pas la même sécurité qu’un module de plateforme sécurisée physique.

Les instructions varient en fonction de la méthode choisie. Vérifiez que vous vous trouvez dans l’onglet approprié.

# <a name="physical-device"></a>[Appareil physique](#tab/physical-device)

Voici les tâches à effectuer :

1. Récupérez les informations de provisionnement de votre module TPM.
1. Créez une inscription individuelle pour votre appareil dans une instance du service IoT Hub Device Provisioning.
1. Installez le runtime IoT Edge et connecte l’appareil au hub IoT.

# <a name="virtual-machine"></a>[Machine virtuelle](#tab/virtual-machine)

Voici les tâches à effectuer :

1. Créez une machine virtuelle Linux dans Hyper-V avec un module TPM simulé pour la sécurité matérielle.
1. Récupérez les informations de provisionnement de votre module TPM.
1. Créez une inscription individuelle pour votre appareil dans une instance du service IoT Hub Device Provisioning.
1. Installez le runtime IoT Edge et connecte l’appareil au hub IoT.

---

## <a name="prerequisites"></a>Prérequis

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

### <a name="iot-edge-installation"></a>Installation d’IoT Edge

# <a name="physical-device"></a>[Appareil physique](#tab/physical-device)

Un appareil Linux physique faisant office d’appareil IoT Edge.

# <a name="virtual-machine"></a>[Machine virtuelle](#tab/virtual-machine)

Une machine de développement Windows avec [Hyper-V activé](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Cet article utilise Windows 10 exécutant une machine virtuelle Ubuntu Server.

---

> [!NOTE]
> TPM 2.0 est requis lorsque vous utilisez l’attestation TPM avec le service de provisionnement des appareils.
>
> Vous pouvez uniquement créer des inscriptions de service de provisionnement des appareils, et non de groupe, lorsque vous utilisez un module de plateforme sécurisée.

## <a name="set-up-your-device"></a>Configurer votre appareil

# <a name="physical-device"></a>[Appareil physique](#tab/physical-device)

Si vous utilisez un appareil Linux physique avec un module TPM, il n’y a pas d’étapes supplémentaires à suivre pour configurer votre appareil.

Vous pouvez continuer.

# <a name="virtual-machine"></a>[Machine virtuelle](#tab/virtual-machine)

Dans cette section, vous créez une machine virtuelle Linux sur Hyper-V. Vous configurez cette machine avec un module TPM simulé permettant de tester le fonctionnement du provisionnement automatique avec IoT Edge.

> [!TIP]
> Si vous utilisez une machine virtuelle, vous allez y effectuer de nombreux copier-coller tout au long de cet article. Ces opérations ne sont pas faciles dans l’application de connexion Gestionnaire Hyper-V. Vous pouvez vous connecter une fois à la machine virtuelle, par le biais du Gestionnaire Hyper-V, pour récupérer son adresse IP. Exécutez d’abord `sudo apt install net-tools`, puis exécutez `hostname -I`. Vous pouvez ensuite utiliser l’adresse IP pour vous connecter via SSH : `ssh <username>@<ipaddress>`.

### <a name="create-a-virtual-switch"></a>Créer un commutateur virtuel

Un commutateur virtuel permet à votre machine virtuelle de se connecter à un réseau physique.

1. Ouvrez Hyper-V Manager sur votre ordinateur Windows.

1. Dans le menu **Actions**, sélectionnez **Gestionnaire de commutateur virtuel**.

1. Sélectionnez un commutateur virtuel **externe**, puis sélectionnez **Créer un commutateur virtuel**.

1. Donnez un nom à votre nouveau commutateur virtuel, Par exemple, utilisez **EdgeSwitch**. Vérifiez que le type de connexion est défini sur **Réseau externe**, puis sélectionnez **OK**.

1. Une fenêtre contextuelle vous avertit que la connectivité réseau pourra être interrompue. Cliquez sur **Oui** pour continuer.

> [!TIP]
> Si vous constatez des erreurs quand vous créez le commutateur virtuel, vérifiez qu’aucun autre commutateur n’utilise l’adaptateur Ethernet ou ne porte le même nom.

### <a name="create-the-virtual-machine"></a>Créer la machine virtuelle

Créez une machine virtuelle à partir d’un fichier image démarrable.

1. Téléchargez un fichier image de disque à utiliser pour votre machine virtuelle et enregistrez-le localement. Par exemple, [Ubuntu Server 18.04](http://releases.ubuntu.com/18.04/). Pour plus d’informations sur les systèmes d’exploitation pris en charge pour les appareils IoT Edge, consultez [Systèmes pris en charge Azure IoT Edge](./support.md).

1. Dans le Gestionnaire Hyper-V, sélectionnez **Action** > **Créer** > **Machine virtuelle** dans le menu **Actions**.

1. Exécutez l’**Assistant Nouvel ordinateur virtuel** avec les configurations spécifiques suivantes :

   1. **Spécifier la génération** : sélectionnez **Génération 2**. La virtualisation imbriquée est activée sur les machines virtuelles de génération 2 : celle-ci est nécessaire pour exécuter IoT Edge sur une machine virtuelle.
   1. **Configurer la mise en réseau** : définissez la valeur de **Connexion** sur le commutateur virtuel que vous avez créé à la section précédente.
   1. **Options d’installation** : sélectionnez **Installer un système d’exploitation à partir d’un fichier image de démarrage** et accédez au fichier image de disque que vous avez enregistré localement.

1. Sélectionnez **Terminer** dans l’Assistant pour créer la machine virtuelle.

La création de la nouvelle machine virtuelle peut prendre plusieurs minutes.

### <a name="enable-virtual-tpm"></a>Activer le TPM virtuel

Une fois votre machine virtuelle créée, ouvrez ses paramètres pour activer le module TPM virtuel qui vous permet de provisionner automatiquement l’appareil.

1. Dans le Gestionnaire Hyper-V, cliquez avec le bouton droit sur la machine virtuelle, puis sélectionnez **Paramètres**.

1. Accédez à **Sécurité**.

1. Décochez **Activer le démarrage sécurisé**.

1. Sélectionnez **Activer le module de plateforme sécurisée**.

1. Sélectionnez **OK**.

### <a name="start-the-virtual-machine"></a>Démarrage de la machine virtuelle

Démarrez votre machine virtuelle pour terminer le processus d’installation.

1. Dans le Gestionnaire Hyper-V, démarrez votre machine virtuelle et connectez-vous-y.

1. Suivez les invites sur la machine virtuelle pour terminer le processus d’installation, puis redémarrez la machine.

Une fois que vous avez terminé l’installation et que vous vous êtes reconnecté à votre machine virtuelle, vous pouvez continuer.

---

## <a name="retrieve-provisioning-information-for-your-tpm"></a>Récupération des informations de provisionnement de votre module TPM

Dans cette section, vous créez un outil permettant de récupérer l’ID d’inscription et la paire de clés de type EK (Endorsement key) de votre module TPM.

1. Connectez-vous à votre appareil, puis suivez les étapes décrites dans [Configurer un environnement de développement Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) pour installer et générer Azure IoT device SDK pour C.

1. Exécutez les commandes suivantes pour créer l’outil SDK permettant de récupérer les informations de provisionnement des appareils de votre module TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. La fenêtre Sortie affiche **l’ID d’inscription** et la **Paire de clés de type EK (Endorsement Key)** de l’appareil. Copiez ces valeurs. Vous vous en servirez plus tard, au moment de créer une inscription individuelle pour votre appareil dans le service de provisionnement des appareils.

> [!TIP]
> Si vous ne souhaitez pas utiliser l’outil SDK pour récupérer les informations, vous devez trouver un autre moyen d’obtenir les informations de provisionnement. La paire de clés de type EK, propre à chaque puce TPM, est fournie par le fabricant de la puce TPM associée. Vous pouvez dériver un ID d’inscription unique pour votre appareil TPM. Par exemple, vous pouvez créer un hachage SHA-256 de la paire de clés de type EK.

Une fois que vous avez votre ID d’inscription et votre paire de clés de type EK, vous pouvez continuer.

<!-- Create an enrollment for your device using TPM provisioning information H2 and content -->
[!INCLUDE [tpm-create-a-device-provision-service-enrollment.md](../../includes/tpm-create-a-device-provision-service-enrollment.md)]

<!-- Install IoT Edge on Linux H2 and content -->
[!INCLUDE [install-iot-edge-linux.md](../../includes/iot-edge-install-linux.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>Provisionnement de l’appareil avec son identité cloud

Une fois le runtime installé sur votre appareil, configurez ce dernier avec les informations qu’il utilise pour se connecter au service de provisionnement des appareils et à IoT Hub.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Récupérez les valeurs **Étendue de l’ID** du service de provisionnement des appareils et **ID d’inscription** de l’appareil qui ont été collectées précédemment.

1. Ouvrez le fichier de configuration sur votre appareil IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Recherchez la section de configuration du provisionnement dans le fichier. Supprimez les marques de commentaire des lignes du provisionnement TPM et de toutes les autres lignes de provisionnement.

   La ligne `provisioning:` ne doit pas être précédée d’un espace. Par ailleurs, les éléments imbriqués doivent être en retrait de deux espaces.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "SCOPE_ID_HERE"
     attestation:
       method: "tpm"
       registration_id: "REGISTRATION_ID_HERE"
   # always_reprovision_on_startup: true
   # dynamic_reprovisioning: false
   ```

1. Remplacez les valeurs `scope_id` et `registration_id` par les informations propres à votre appareil et à votre service de provisionnement des appareils. La valeur `scope_id` correspond à la valeur **Étendue de l’ID** indiquée dans la page de présentation de votre instance du service de provisionnement des appareils.

1. Si vous le souhaitez, utilisez les lignes `always_reprovision_on_startup` ou `dynamic_reprovisioning` pour configurer le comportement de reprovisionnement de votre appareil. Si un appareil est configuré pour être reprovisionné au démarrage, le provisionnement est toujours tenté avec le service de provisionnement des appareils dans un premier temps. En cas d’échec, il bascule vers la sauvegarde de provisionnement. Si un appareil est configuré pour se provisionner dynamiquement lui-même, IoT Edge redémarre et effectue le reprovisionnement dès qu’un événement de reprovisionnement est détecté. Pour plus d’informations, consultez [Concepts du reprovisionnement d’appareils IoT Hub](../iot-dps/concepts-device-reprovision.md).

1. Enregistrez et fermez le fichier.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Récupérez les valeurs **Étendue de l’ID** du service de provisionnement des appareils et **ID d’inscription** de l’appareil qui ont été collectées précédemment.

1. Créez un fichier de configuration pour votre appareil à partir d’un fichier de modèle fourni dans le cadre de l’installation d’IoT Edge.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. Ouvrez le fichier de configuration sur l’appareil IoT Edge.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Recherchez la section des configurations de provisionnement dans le fichier. Supprimez les marques de commentaire des lignes du provisionnement TPM et de toutes les autres lignes de provisionnement.

   ```toml
   # DPS provisioning with TPM
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "SCOPE_ID_HERE"
   
   [provisioning.attestation]
   method = "tpm"
   registration_id = "REGISTRATION_ID_HERE"
   ```

1. Remplacez les valeurs `id_scope` et `registration_id` par les informations propres à votre appareil et à votre service de provisionnement des appareils. La valeur `scope_id` correspond à la valeur **Étendue de l’ID** indiquée dans la page de présentation de votre instance du service de provisionnement des appareils.

1. Si vous le souhaitez, recherchez la section auto-reprovisioning mode du fichier. Utilisez le paramètre `auto_reprovisioning_mode` pour configurer le comportement de reprovisionnement de votre appareil sur `Dynamic`, `AlwaysOnStartup` ou `OnErrorOnly`. Pour plus d’informations, consultez [Concepts du reprovisionnement d’appareils IoT Hub](../iot-dps/concepts-device-reprovision.md).

1. Enregistrez et fermez le fichier.

:::moniker-end
<!-- end 1.2 -->

## <a name="give-iot-edge-access-to-the-tpm"></a>Donner à IoT Edge l’accès au TPM

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Le runtime IoT Edge a besoin d’accéder au module TPM pour provisionner automatiquement votre appareil.

Vous pouvez accorder au TPM un accès au runtime IoT Edge en substituant les paramètres système afin que le service `iotedge`dispose des privilèges root. Si vous ne souhaitez pas élever les privilèges de service, vous pouvez également utiliser les étapes suivantes pour fournir manuellement un accès au TPM.

1. Créez une règle qui donne au runtime IoT Edge l’accès à `tpm0` et à `tpmrm0`.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

1. Ouvrez le fichier de règles.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

1. Copiez les informations d’accès suivantes dans le fichier de règles. `tpmrm0` n’est pas toujours présent sur les appareils qui utilisent un noyau antérieur à la version 4.12. Les appareils qui n’ont pas de `tpmrm0` ignorent cette règle en toute sécurité.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
   KERNEL=="tpmrm0", SUBSYSTEM=="tpmrm", OWNER="iotedge", MODE="0600"
   ```

1. Enregistrez et fermez le fichier.

1. Déclenchez le système `udev` pour évaluer la nouvelle règle.

   ```bash
   /bin/udevadm trigger --subsystem-match=tpm --subsystem-match=tpmrm
   ```

1. Vérifiez que la règle a bien été appliquée.

   ```bash
   ls -l /dev/tpm*
   ```

   En cas de réussite, la sortie se présente ainsi :

   ```output
   crw------- 1 iotedge root 10, 224 Jul 20 16:27 /dev/tpm0
   crw------- 1 iotedge root 10, 224 Jul 20 16:27 /dev/tpmrm0
   ```

   Si vous constatez que les autorisations appropriées n’ont pas été appliquées, essayez de redémarrer votre ordinateur pour actualiser `udev`.

1. Redémarrez le runtime IoT Edge afin qu’il récupère toutes les modifications de configuration que vous avez effectuées sur l’appareil.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Le runtime IoT Edge s’appuie sur un service de module de plateforme sécurisée (TPM) qui donne accès au module TPM d’un appareil. Le service a besoin d’accéder au module TPM pour provisionner automatiquement votre appareil.

Vous pouvez accorder au TPM en substituant les paramètres système afin que le service `aziottpm`dispose des privilèges root. Si vous ne souhaitez pas élever les privilèges de service, vous pouvez également utiliser les étapes suivantes pour fournir manuellement un accès au TPM.

1. Créez une règle qui donne au runtime IoT Edge l’accès à `tpm0` et à `tpmrm0`.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

1. Ouvrez le fichier de règles.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

1. Copiez les informations d’accès suivantes dans le fichier de règles. `tpmrm0` n’est pas toujours présent sur les appareils qui utilisent un noyau antérieur à la version 4.12. Les appareils qui n’ont pas de `tpmrm0` ignorent cette règle en toute sécurité.

   ```input
   # allow aziottpm access to tpm0 and tpmrm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="aziottpm", MODE="0660"
   KERNEL=="tpmrm0", SUBSYSTEM=="tpmrm", OWNER="aziottpm", MODE="0660"
   ```

1. Enregistrez et fermez le fichier.

1. Déclenchez le système `udev` pour évaluer la nouvelle règle.

   ```bash
   /bin/udevadm trigger --subsystem-match=tpm --subsystem-match=tpmrm
   ```

1. Vérifiez que la règle a bien été appliquée.

   ```bash
   ls -l /dev/tpm*
   ```

   En cas de réussite, la sortie se présente ainsi :

   ```output
   crw-rw---- 1 root aziottpm 10, 224 Jul 20 16:27 /dev/tpm0
   crw-rw---- 1 root aziottpm 10, 224 Jul 20 16:27 /dev/tpmrm0
   ```

   Si vous constatez que les autorisations appropriées n’ont pas été appliquées, essayez de redémarrer votre ordinateur pour actualiser `udev`.

1. Appliquez les modifications de configuration que vous avez apportées à l’appareil.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="verify-successful-installation"></a>Vérifier la réussite de l’installation

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Si vous ne l’avez pas déjà fait, redémarrez le runtime IoT Edge pour qu’il récupère toutes les modifications de configuration que vous avez effectuées sur l’appareil.

   ```bash
   sudo systemctl restart iotedge
   ```

Vérifiez que le runtime IoT Edge est en cours d’exécution.

   ```bash
   sudo systemctl status iotedge
   ```

Examinez les journaux d’activité du démon.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Si vous constatez des erreurs de provisionnement, il est possible que les modifications de configuration ne soient pas encore prises en compte. Essayez de redémarrer le démon IoT Edge.

   ```bash
   sudo systemctl daemon-reload
   ```

Ou bien, essayez de redémarrer votre machine virtuelle pour voir si les modifications sont prises en compte après un redémarrage à zéro.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Si vous ne l’avez pas déjà fait, appliquez les modifications de configuration que vous avez effectuées sur l’appareil.

   ```bash
   sudo iotedge config apply
   ```

Vérifiez que le runtime IoT Edge est en cours d’exécution.

   ```bash
   sudo iotedge system status
   ```

Examinez les journaux d’activité du démon.

   ```cmd/sh
   sudo iotedge system logs
   ```

Si vous constatez des erreurs de provisionnement, il est possible que les modifications de configuration ne soient pas encore prises en compte. Essayez de redémarrer le démon IoT Edge.

   ```bash
   sudo systemctl daemon-reload
   ```

Ou bien, essayez de redémarrer votre machine virtuelle pour voir si les modifications sont prises en compte après un redémarrage à zéro.
:::moniker-end
<!-- end 1.2 -->

Si le runtime a été démarré correctement, vous pouvez accéder à votre hub IoT et voir que votre nouvel appareil a été automatiquement provisionné. Votre appareil est maintenant prêt à exécuter des modules IoT Edge.

Répertoriez les modules en cours d’exécution.

```cmd/sh
iotedge list
```

Vous pouvez vérifier que l’inscription individuelle que vous avez créée dans le service de provisionnement des appareils a été utilisée. Accédez à l’instance du service de provisionnement des appareils dans le portail Azure. Ouvrez les détails de l’inscription pour l’inscription individuelle que vous avez créée. Notez que l’état de l’inscription est **Affecté** et que l’ID de l’appareil figure dans la liste.

## <a name="next-steps"></a>Étapes suivantes

Le processus d’inscription auprès du service de provisionnement des appareils vous permet de définir l’ID d’appareil et les balises du jumeau d’appareil en même temps que vous provisionnez le nouvel appareil. Vous pouvez utiliser ces valeurs pour cibler des appareils individuels ou des groupes d’appareils avec la gestion d’appareils automatique.

Découvrez comment [déployer et surveiller des modules IoT Edge à grande échelle à l’aide du portail Azure](how-to-deploy-at-scale.md) ou [d’Azure CLI](how-to-deploy-cli-at-scale.md).
