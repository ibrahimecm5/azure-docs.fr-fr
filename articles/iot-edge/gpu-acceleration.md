---
title: Accélération GPU pour Azure IoT Edge pour Linux sur Windows | Microsoft Docs
description: Découvrez comment configurer vos machines virtuelles Azure IoT Edge pour Linux sur Windows afin d’utiliser des processeurs graphiques (GPU) d’appareils hôtes.
author: v-tcassi
manager: kgremban
ms.author: v-tcassi
ms.date: 06/22/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: c85659a6c9e89145494d4683da37bd2f13cbd379
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234862"
---
# <a name="gpu-acceleration-for-azure-iot-edge-for-linux-on-windows-preview"></a>Accélération GPU pour Azure IoT Edge pour Linux sur Windows (préversion)

Les processeurs graphiques (GPU) sont un choix courant pour les calculs d’intelligence artificielle, car ils offrent des capacités de traitement parallèle et peuvent souvent exécuter l’inférence reposant sur la vision plus rapidement que les processeurs. Pour mieux prendre en charge les applications d’intelligence artificielle et de machine learning, Azure IoT Edge pour Linux sur Windows peut exposer un processeur graphique (GPU) au module Linux de la machine virtuelle.

> [!NOTE]
> Les fonctionnalités d’accélération GPU détaillées ci-dessous sont en préversion et sont susceptibles d’être modifiées.

Azure IoT Edge pour Linux sur Windows prend en charge plusieurs technologies de relais GPU, notamment :

* **DDA (Discrete Device Assignment)**  : les cœurs GPU sont alloués à la machine virtuelle Linux ou à l’hôte.

* **GPU-PV (GPU-Paravirtualization)**  : le processeur graphique (GPU) est partagé entre la machine virtuelle Linux et l’hôte.

Le déploiement Azure IoT Edge pour Linux sur Windows sélectionne automatiquement la méthode de relais appropriée pour qu’elle corresponde aux capacités prises en charge par le matériel GPU de votre appareil.

> [!IMPORTANT]
> Ces fonctionnalités peuvent inclure des composants développés et détenus par NVIDIA Corporation ou ses concédants. L’utilisation des composants est régie par le contrat de Licence Utilisateur Final se trouvant [sur le site web de NVIDIA](https://www.nvidia.com/content/DriverDownload-March2009/licence.php?lang=us).
>
> En utilisant les fonctionnalités d’accélération GPU, vous acceptez les termes du contrat de licence Utilisateur final NVIDIA.

## <a name="prerequisites"></a>Configuration requise

Les fonctionnalités d’accélération GPU d’Azure IoT Edge pour Linux sur Windows prennent actuellement en charge un ensemble sélectionné de matériel GPU. De plus, l’utilisation de ces fonctionnalités peut nécessiter la dernière version de Windows Insider Dev Channel, en fonction de votre configuration.

Les processeurs graphiques (GPU) pris en charge et les versions Windows requises sont listées ci-dessous :

* NVIDIA T4 (prend en charge DDA)

  * Windows Server, build 17763 ou ultérieure
  * Windows Entreprise ou Professionnel, build 21318 ou ultérieure (build Windows Insider)

* NVIDIA GeForce/Quadro (prend en charge GPU-PV)

  * Windows Entreprise ou Professionnel, build 20145 ou ultérieure (build Windows Insider)

### <a name="windows-insider-builds"></a>Builds Windows Insider

Pour les utilisateurs Windows Entreprise ou Professionnel, vous devez vous [inscrire au programme Windows Insider](https://insider.windows.com/getting-started#register).

Une fois que vous êtes inscrit, suivez les instructions fournies sous l’onglet **2. Vol** pour accéder à la build Windows Insider appropriée. Quand vous sélectionnez le canal que vous souhaitez utiliser, sélectionnez le [canal de développement](/windows-insider/flight-hub/#active-development-builds-of-windows-10). Après l’installation, vous pouvez vérifier votre numéro de version de la build en exécutant `winver` par le biais de l’invite de commandes.

### <a name="t4-gpus"></a>GPU T4

Pour les **GPU T4**, Microsoft recommande d’installer un pilote d’atténuation des appareils que vous pouvez obtenir auprès du fournisseur de votre processeur graphique (GPU). Bien que facultative, l’installation d’un pilote d’atténuation peut améliorer la sécurité de votre déploiement. Pour plus d’informations, consultez [Déployer des appareils graphiques à l’aide de la technologie DDA (Discrete Device Assignment)](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda#optional---install-the-partitioning-driver).

> [!WARNING]
> L’activation du relais d’appareils matériels peut augmenter les risques liés à la sécurité. Microsoft vous recommande de recourir à un pilote d’atténuation des appareils du fournisseur de votre GPU, le cas échéant. Pour plus d’informations, consultez [Déploiement d’appareils graphiques avec la technologie DDA (Discrete Device Assignment)](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda).

### <a name="geforcequadro-gpus"></a>GPU GeForce/Quadro

Pour les **GPU GeForce/Quadro**, téléchargez et installez le [pilote compatible NVIDIA CUDA pour le sous-système Windows pour Linux (WSL)](https://developer.nvidia.com/cuda/wsl) à utiliser avec vos workflows ML CUDA existants. Initialement développé pour WSL, les pilotes CUDA pour WSL sont également utilisés pour Azure IoT Edge pour Linux sur Windows.

## <a name="using-gpu-acceleration-for-your-linux-on-windows-deployment"></a>Utilisation de l’accélération GPU pour votre déploiement Linux sur Windows

Vous êtes maintenant prêt à déployer et à exécuter des modules Linux avec accélération GPU dans votre environnement de Windows par le biais d’Azure IoT Edge pour Linux sur Windows. Pour plus d’informations sur le processus de déploiement, consultez le [guide pour provisionner un appareil IoT Edge pour Linux sur Windows à l’aide de clés symétriques](how-to-provision-single-device-linux-on-windows-symmetric.md) ou [à l’aide de certificats X.509](how-to-provision-single-device-linux-on-windows-x509.md).

## <a name="next-steps"></a>Étapes suivantes

* [Créer votre déploiement d’Azure IoT Edge pour Linux sur Windows](how-to-install-iot-edge-on-windows.md)

* Essayez notre [exemple avec processeur graphique (GPU) comprenant la vision sur Edge](https://github.com/Azure-Samples/azure-intelligent-edge-patterns/blob/master/factory-ai-vision/Tutorial/Eflow.md), un modèle de solution illustrant la façon de générer votre propre application de machine learning reposant sur la vision.

* Découvrez-en plus sur les technologies de relais GPU en consultant la [documentation DDA](/windows-server/virtualization/hyper-v/plan/plan-for-gpu-acceleration-in-windows-server#discrete-device-assignment-dda) et le [billet de blog sur GPU-PV](https://devblogs.microsoft.com/directx/directx-heart-linux/#gpu-virtualization).
