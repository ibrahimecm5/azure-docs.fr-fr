---
title: Création d’un appareil IoT Edge – Azure IoT Edge | Microsoft Docs
description: Découvrez la plateforme et les options d’approvisionnement pour la création d’un appareil IoT Edge.
author: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/11/2021
ms.author: kgremban
ms.openlocfilehash: 6fdfbd937e767cde118ed80476e73d6207c657e6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495405"
---
# <a name="create-an-iot-edge-device"></a>Créer un appareil IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Cet article donne une vue d’ensemble des options disponibles pour l’installation et l’approvisionnement d’IoT Edge sur vos appareils.

Il présente toutes les options de votre solution IoT Edge pour vous aider dans vos choix :

* [Choisir une plateforme](#choose-a-platform)
* [Choisir comment approvisionner les appareils](#choose-how-to-provision-your-devices)
* [Choisir une méthode d’authentification](#choose-an-authentication-method)

À la fin de cet article, vous aurez une idée claire de la plateforme, de l’approvisionnement et des options d’authentification que vous souhaitez pour votre solution IoT Edge.

## <a name="get-started"></a>Bien démarrer

Si vous connaissez le type de plateforme, l’approvisionnement et les options d’authentification que vous souhaitez utiliser pour créer un appareil IoT Edge, suivez les liens qui figurent dans le tableau ci-dessous pour commencer.

Si vous souhaitez savoir plus en détail comment choisir l’option la plus adaptée, poursuivez la lecture de cet article.

<!--1.1-->
:::moniker range="iotedge-2018-06"

|    | Conteneurs Linux sur des hôtes Linux | Conteneurs Linux sur des hôtes Windows | Conteneurs Windows sur des hôtes Windows |
|--| ----- | ---------------- | ------- |
| **Approvisionnement manuel (un seul appareil)** | [Certificats X.509](how-to-provision-single-device-linux-x509.md)<br><br>[Clés symétriques](how-to-provision-single-device-linux-symmetric.md) | [Certificats X.509](how-to-provision-single-device-linux-on-windows-x509.md)<br><br>[Clés symétriques](how-to-provision-single-device-linux-on-windows-symmetric.md) | [Certificats X.509](how-to-provision-single-device-windows-x509.md)<br><br>[Clés symétriques](how-to-provision-single-device-windows-symmetric.md) |
| **Approvisionnement automatique (appareils à grande échelle)** | [Certificats X.509](how-to-provision-devices-at-scale-linux-x509.md)<br><br>[Module de plateforme sécurisée](how-to-provision-devices-at-scale-linux-tpm.md)<br><br>[Clés symétriques](how-to-provision-devices-at-scale-linux-symmetric.md) | [Certificats X.509](how-to-provision-devices-at-scale-linux-on-windows-x509.md)<br><br>[Module de plateforme sécurisée](how-to-provision-devices-at-scale-linux-on-windows-tpm.md)<br><br>[Clés symétriques](how-to-provision-devices-at-scale-linux-on-windows-symmetric.md) | [Certificats X.509](how-to-provision-devices-at-scale-windows-x509.md)<br><br>[Module de plateforme sécurisée](how-to-provision-devices-at-scale-windows-tpm.md)<br><br>[Clés symétriques](how-to-provision-devices-at-scale-windows-symmetric.md) |

:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>Le tableau suivant reflète les scénarios pris en charge pour IoT Edge version 1.2. Pour voir du contenu portant sur les appareils Windows, passez à la version [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true) de cet article.

|    | Conteneurs Linux sur des hôtes Linux |
|--| ----- |
| **Approvisionnement manuel (un seul appareil)** | [Certificats X.509](how-to-provision-single-device-linux-x509.md)<br><br>[Clés symétriques](how-to-provision-single-device-linux-symmetric.md) |
| **Approvisionnement automatique (appareils à grande échelle)** | [Certificats X.509](how-to-provision-devices-at-scale-linux-x509.md)<br><br>[Module de plateforme sécurisée](how-to-provision-devices-at-scale-linux-tpm.md)<br><br>[Clés symétriques](how-to-provision-devices-at-scale-linux-symmetric.md) |

:::moniker-end

## <a name="terms-and-concepts"></a>Termes et concepts

Si vous ne connaissez pas déjà la terminologie IoT Edge, passez en revue quelques concepts clés :

**Runtime IoT Edge** : le [runtime IoT Edge](iot-edge-runtime.md) est une collection de programmes qui transforment un appareil en appareil IoT Edge. Pris collectivement, ses composants permettent à des appareils IoT Edge d’exécuter vos modules IoT Edge.

**Approvisionnement** : chaque appareil IoT Edge doit être approvisionné. Le processus d’approvisionnement comprend deux étapes. La première consiste à inscrire l’appareil dans un hub IoT, ce qui crée une identité cloud permettant à l’appareil d’établir la connexion à son hub. La deuxième correspond à la configuration de l’appareil avec son identité cloud. L’approvisionnement peut être effectué manuellement appareil par appareil, ou à grande échelle avec le [Service IoT Hub Device Provisioning](../iot-dps/about-iot-dps.md).

**Authentification** : l’appareil IoT Edge doit vérifier son identité lorsqu’il se connecte à IoT Hub. Vous pouvez choisir la méthode d’authentification à utiliser, par exemple les mots de passe de clé symétrique, les empreintes de certificat ou les Modules de plateforme sécurisée (TPM, Trusted Platform Module).

## <a name="choose-a-platform"></a>Choisir une plateforme

Les options de plateforme sont désignées par les termes « système d’exploitation du conteneur » et « système d’exploitation hôte ». Le système d’exploitation du conteneur correspond au système d’exploitation utilisé à l’intérieur des conteneurs du runtime IoT Edge et des modules. Le système d’exploitation hôte, lui, est le système d’exploitation de l’appareil sur lequel s’exécutent les conteneurs et les modules du runtime IoT Edge.

Il existe trois options de plateforme pour les appareils IoT Edge.

* **Conteneurs Linux sur des hôtes Linux** : exécutez des conteneurs IoT Edge Linux directement sur un hôte Linux. Dans les documents IoT Edge, cette option est également appelée **Linux** et **Conteneurs Linux** pour des raisons de simplicité.

* **Conteneurs Linux sur des hôtes Windows** : exécutez des conteneurs IoT Edge Linux dans une machine virtuelle Linux sur un hôte Windows. Dans les documents IoT Edge, cette option est également appelée **Linux sur Windows**, **IoT Edge pour Linux sur Windows** et **EFLOW**.

* **Conteneurs Windows sur des hôtes Windows** : exécuter des conteneurs IoT Edge Windows directement sur un hôte Windows. Dans les documents IoT Edge, cette option est également appelée **Windows** et **Conteneurs Windows** pour des raisons de simplicité.

Pour les dernières informations sur les systèmes d’exploitation actuellement pris en charge pour les scénarios de production, consultez [Systèmes pris en charge par Azure IoT Edge](support.md#operating-systems).

### <a name="linux-containers-on-linux"></a>Conteneurs Linux sur Linux

Pour les appareils Linux, le runtime IoT Edge est installé directement sur l’appareil hôte.

IoT Edge prend en charge les appareils Linux x64, ARM32 et ARM64. Microsoft fournit des packages d’installation pour les systèmes d’exploitation Ubuntu Server 18.04 et Raspberry Pi OS Stretch.

La prise en charge des appareils ARM64 est en [préversion publique](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="linux-containers-on-windows"></a>Conteneurs Linux sur Windows

<!--1.1-->
:::moniker range="iotedge-2018-06"
IoT Edge pour Linux sur Windows héberge une machine virtuelle Linux sur un appareil Windows. La machine virtuelle est fournie avec le runtime IoT Edge, et les mises à jour sont gérées avec Microsoft Update.

IoT Edge pour Linux sous Windows est la méthode recommandée pour exécuter IoT Edge sur les appareils Windows. Pour plus d’informations, consultez [Présentation d’Azure IoT Edge pour Linux sur Windows](iot-edge-for-linux-on-windows.md).

Actuellement, IoT Edge pour Linux sur Windows ne prend pas en charge la version 1.2 d’Azure IoT Edge.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
Actuellement, la version 1.2 d’IoT Edge ne prend pas en charge IoT Edge pour Linux sur Windows. Pour plus d’informations sur IoT Edge pour Linux sur Windows, consultez la version [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true) de cet article.
:::moniker-end

### <a name="windows-containers-on-windows"></a>Conteneurs Windows sur Windows

<!--1.1-->
:::moniker range="iotedge-2018-06"
Pour les appareils Windows, le runtime IoT Edge est installé directement sur l’appareil hôte. Cette plateforme permet de créer, de déployer et d’exécuter des modules IoT Edge en tant que conteneurs Windows.

   > [!NOTE]
   > Les conteneurs Windows ne constituent pas la méthode recommandée pour exécuter IoT Edge sur des appareils Windows, car ils ne sont pas pris en charge au-delà de la version 1.1 d’Azure IoT Edge.
   >
   > Envisagez d’utiliser IoT Edge pour Linux sur Windows, qui fonctionnera dans les versions ultérieures.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
La version 1.2 d’IoT Edge n’accepte pas les conteneurs Windows, qui ne sont pas pris en charge au-delà de la version 1.1. Pour plus d’informations sur IoT Edge avec des conteneurs Windows, consultez la version [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true) de cet article.
:::moniker-end

## <a name="choose-how-to-provision-your-devices"></a>Choix du mode d’approvisionnement des appareils

Vous pouvez approvisionner un seul appareil ou plusieurs à la fois, en fonction des besoins de votre solution IoT Edge.

Les options disponibles pour authentifier les communications entre les appareils IoT Edge et vos hubs IoT dépendent de la méthode d’approvisionnement choisie. Pour plus d’informations sur ces options, consultez la section [Choix d’une méthode d’authentification](#choose-an-authentication-method).

### <a name="single-device"></a>Un seul appareil

L’approvisionnement d’un seul appareil fait référence à l’approvisionnement d’un appareil IoT Edge sans l’aide du [Service IoT Hub Device Provisioning](../iot-dps/about-iot-dps.md) (DPS, Device Provisioning Service). Comme vous pourrez le constater, il est également appelé **approvisionnement manuel**.

Avec l’approvisionnement d’un seul appareil, vous devez entrer manuellement les informations d’approvisionnement, par exemple une chaîne de connexion, sur vos appareils. L’approvisionnement manuel est rapide et facile à configurer pour quelques appareils seulement, mais la charge de travail augmente avec le nombre d’appareils. Gardez cela à l’esprit lorsque vous envisagez la scalabilité de votre solution.

La **clé symétrique** et les méthodes d’authentification **X.509 auto-signées** sont disponibles pour l’approvisionnement manuel. Pour plus d’informations sur ces options, consultez la section [Choix d’une méthode d’authentification](#choose-an-authentication-method).

### <a name="devices-at-scale"></a>Appareils à grande échelle

L’approvisionnement d’appareils à grande échelle fait référence à l’approvisionnement d’un ou plusieurs appareils IoT Edge avec l’aide du [Service IoT Hub Device Provisioning](../iot-dps/about-iot-dps.md). Comme vous pourrez le constater, il est également appelé **approvisionnement automatique**.

Si votre solution IoT Edge exige plusieurs appareils, l’approvisionnement automatique avec le service DPS vous évite de saisir manuellement les informations d’approvisionnement dans les fichiers de configuration de chacun des appareils que vous souhaitez utiliser. Ce modèle automatisé peut être appliqué à des millions d’appareils IoT Edge. Le workflow d’approvisionnement automatique apparaît dans la section [En coulisses de la page de présentation du service DPS d’IoT Hub](../iot-dps/about-iot-dps.md#behind-the-scenes).

Vous pouvez sécuriser votre solution IoT Edge avec la méthode d’authentification de votre choix. Les méthodes d’authentification par **clé symétrique**, **certificats X.509** et **attestation de Module de plateforme sécurisée (TPM, Trusted Platform Module)** sont disponibles pour approvisionner les appareils à grande échelle. Pour plus d’informations sur ces options, consultez la section [Choix d’une méthode d’authentification](#choose-an-authentication-method).

Pour plus d’informations sur les fonctionnalités du service DPS, consultez la [section Fonctionnalités de la page de présentation](../iot-dps/about-iot-dps.md#features-of-the-device-provisioning-service).

## <a name="choose-an-authentication-method"></a>Choisir une méthode d’authentification

### <a name="symmetric-keys-attestation"></a>Attestation de clé symétrique

L’attestation de clé symétrique constitue une approche simple pour authentifier un appareil. Cette méthode d’attestation représente une expérience « Hello world » pour les développeurs qui découvrent le provisionnement d’appareils ou n’ont pas d’exigences de sécurité strictes.

quand vous créez une identité d’appareil dans IoT Hub, le service crée deux clés. Vous placez l’une des clés sur l’appareil, lequel présente la clé à IoT Hub au moment de l’authentification.

Cette méthode d’authentification est plus rapide pour commencer, mais moins sécurisée que l’approvisionnement des appareils avec un module TPM ou des certificats X.509, qui doit être utilisé pour les solutions présentant des exigences de sécurité sont plus strictes.

### <a name="x509-certificate-attestation"></a>Attestation de certificat X.509

Utiliser des certificats X.509 comme mécanisme d’attestation est un excellent moyen de mettre à l’échelle la production et de simplifier le provisionnement des appareils. Les certificats X.509 sont généralement organisés en une chaîne d’approbation de confiance. À partir d’un certificat racine auto-signé ou approuvé, chaque certificat de la chaîne signe le certificat inférieur suivant. Ce modèle crée une chaîne déléguée de confiance depuis le certificat racine jusqu’au certificat « feuille » final installé sur un appareil, en passant par chaque certificat intermédiaire.

vous créez deux certificats d’identité X.509, que vous placez sur l’appareil. Quand vous créez une identité d’appareil dans IoT Hub, vous fournissez les empreintes numériques des deux certificats. Quand l’appareil s’authentifie auprès de IoT Hub, il présente un certificat et IoT Hub vérifie que le certificat correspond à son empreinte numérique.

Cette méthode d’authentification étant plus sécurisée que les clés symétriques, elle est recommandée dans les scénarios de production.

### <a name="trusted-platform-module-tpm-attestation"></a>Attestation de module de plateforme sécurisée

L’attestation TPM constitue la méthode la plus sûre pour l’approvisionnement des appareils, car elle fournit des fonctionnalités d’authentification au niveau logiciel et matériel. Chaque puce TPM utilise une clé de type EK (Endorsement Key) unique pour vérifier son authenticité.

L’attestation TPM est uniquement disponible pour l’approvisionnement à grande échelle avec le service DPS. Elle ne prend en charge que les inscriptions individuelles, et non les inscriptions de groupe en raison de la nature propre à l’appareil du module TPM.

TPM 2.0 est requis lorsque vous utilisez l’attestation TPM avec le service de provisionnement des appareils.

Cette méthode d’authentification étant plus sécurisée que les clés symétriques, elle est recommandée dans les scénarios de production.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez utiliser la table des matières pour accéder au guide de création d’un appareil IoT Edge de bout en bout adapté aux exigences de plateforme, d’approvisionnement et d’authentification de votre solution IoT Edge.

Vous pouvez également suivre les liens ci-dessous pour accéder à l’article correspondant.

### <a name="linux-containers-on-linux-hosts"></a>Conteneurs Linux sur des hôtes Linux

**Approvisionnement manuel d’un seul appareil** :

* [Approvisionnement d’un seul appareil Linux avec des certificats X.509](how-to-provision-single-device-linux-x509.md)
* [Approvisionnement d’un seul appareil Linux avec des clés symétriques](how-to-provision-single-device-linux-symmetric.md)

**Approvisionnement de plusieurs appareils à grande échelle** :

* [Approvisionnement d’appareils Linux à grande échelle avec des certificats X.509](how-to-provision-devices-at-scale-linux-x509.md)
* [Approvisionnement d’appareils Linux à grande échelle avec l’attestation TPM](how-to-provision-devices-at-scale-linux-tpm.md)
* [Approvisionnement d’appareils Linux à grande échelle avec des clés symétriques](how-to-provision-devices-at-scale-linux-symmetric.md)

<!--1.1-->
:::moniker range="iotedge-2018-06"
### <a name="linux-containers-on-windows-hosts"></a>Conteneurs Linux sur des hôtes Windows

**Approvisionnement manuel d’un seul appareil** :

* [Approvisionnement d’un seul appareil Linux sur Windows avec des certificats X.509](how-to-provision-single-device-linux-on-windows-x509.md)
* [Approvisionnement d’un seul appareil Linux sur Windows avec des clés symétriques](how-to-provision-single-device-linux-on-windows-symmetric.md)

**Approvisionnement de plusieurs appareils à grande échelle** :

* [Approvisionnement d’appareils Linux sur Windows à grande échelle avec des certificats X.509](how-to-provision-devices-at-scale-linux-on-windows-x509.md)
* [Approvisionnement d’appareils Linux sur Windows à grande échelle avec l’attestation TPM](how-to-provision-devices-at-scale-linux-on-windows-tpm.md)
* [Approvisionnement d’appareils Linux sur Windows à grande échelle avec des clés symétriques](how-to-provision-devices-at-scale-linux-on-windows-symmetric.md)

### <a name="windows-containers-on-windows-hosts"></a>Conteneurs Windows sur des hôtes Windows

**Approvisionnement manuel d’un seul appareil** :

* [Approvisionnement d’un seul appareil Windows avec des certificats X.509](how-to-provision-single-device-windows-x509.md)
* [Approvisionnement d’un seul appareil Windows avec des clés symétriques](how-to-provision-single-device-windows-symmetric.md)

**Approvisionnement de plusieurs appareils à grande échelle** :

* [Approvisionnement d’appareils Windows à grande échelle avec des certificats X.509](how-to-provision-devices-at-scale-windows-x509.md)
* [Approvisionnement d’appareils Windows à grande échelle avec l’attestation TPM](how-to-provision-devices-at-scale-windows-tpm.md)
* [Approvisionnement d’appareils Windows à grande échelle avec des clés symétriques](how-to-provision-devices-at-scale-windows-symmetric.md)
:::moniker-end
