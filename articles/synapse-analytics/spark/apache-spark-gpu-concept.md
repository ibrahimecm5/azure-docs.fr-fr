---
title: Pools accélérés par GPU
description: Présentation des GPU à l’intérieur de Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 11/10/2021
ms.author: midesa
ms.openlocfilehash: d4a4c38b61dcce01ec7fb73dc70605ce795b6412
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495419"
---
# <a name="gpu-accelerated-apache-spark-pools-in-azure-synapse-analytics"></a>Pools Apache Spark accélérés par GPU dans Azure Synapse Analytics
Azure Synapse Analytics prend désormais en charge les pools Apache Spark accélérés par des GPU (Graphics Processing Units, unités de traitement graphique). 

Grâce aux GPU NVIDIA, les scientifiques des données et les ingénieurs Données peuvent réduire le temps nécessaire pour exécuter des pipelines d’intégration de données, scorer des modèles Machine Learning, etc. Cet article décrit comment créer et utiliser des pools accélérés par GPU avec Azure Synapse Analytics. Cet article détaille également les bibliothèques et pilotes GPU préinstallés dans le cadre du runtime accéléré par GPU.

> [!NOTE]
> Les pools compatibles GPU Azure Synapse sont actuellement en préversion publique.

## <a name="create-a-gpu-accelerated-pool"></a>Créer un pool accéléré par GPU
Pour simplifier le processus de création et de gestion des pools, Azure Synapse s’occupe de la préinstallation des bibliothèques de bas niveau et de la configuration de toutes les exigences réseau complexes entre les nœuds de calcul. Cette intégration permet aux utilisateurs de bien démarrer avec les pools accélérés par GPU en quelques minutes. Pour en savoir plus sur la création d’un pool accéléré par GPU, vous pouvez consulter le guide de démarrage rapide sur la manière de [créer un pool accéléré par GPU](../quickstart-create-apache-gpu-pool-portal.md).

> [!NOTE]
>  - Vous pouvez créer des pools accélérés par GPU dans des espaces de travail situés dans les régions USA Est, Australie Est et Europe Nord.
>  - Les pools accélérés par GPU sont disponibles uniquement avec le runtime Apache Spark 3.
>  - Vous pouvez avoir besoin de demander [une augmentation de limite](./apache-spark-rapids-gpu.md#quotas-and-resource-constraints-in-azure-synapse-gpu-enabled-pools) afin de créer des clusters compatibles GPU. 
 
## <a name="gpu-accelerated-runtime"></a>Runtime accéléré par GPU

### <a name="nvidia-gpu-driver-cuda-and-cudnn"></a>Pilote GPU NVIDIA, CUDA et cuDNN
Azure Synapse Analytics offre désormais des pools Apache Spark accélérés par GPU, qui incluent diverses bibliothèques et configurations NVIDIA. Par défaut, Azure Synapse Analytics installe le pilote et les bibliothèques NVIDIA nécessaires à l’utilisation des GPU sur le pilote et les instances Worker de Spark :
 - CUDA 11.2
 - libnccl2=2.8.4
 - libnccl-dev=2.8.4
 - libcudnn8=8.1.1 
 - libcudnn8-dev=8.1.1

> [!NOTE]
> Ce logiciel contient le code source fourni par NVIDIA Corporation. Plus précisément, pour prendre en charge les pools accélérés par GPU, les pools Azure Synapse Apache Spark incluent le code des [exemples CUDA](https://docs.nvidia.com/cuda/eula/#nvidia-cuda-samples-preface).

### <a name="nvidia-end-user-license-agreement-eula"></a>Contrat de Licence Utilisateur Final (CLUF) NVIDIA
Quand vous sélectionnez une option de matériel accéléré par GPU dans Synapse Spark, vous acceptez implicitement les conditions générales indiquées dans le CLUF NVIDIA concernant :
  - CUDA 11.2 : [EULA :: CUDA Toolkit Documentation (nvidia.com)](https://docs.nvidia.com/cuda/eula/index.html)
  - libnccl2=2.8.4 : [nccl/LICENSE.txt at master · NVIDIA/nccl (github.com)](https://github.com/NVIDIA/nccl/blob/master/LICENSE.txt)
  - libnccl-dev=2.8.4 : [nccl/LICENSE.txt at master · NVIDIA/nccl (github.com)](https://github.com/NVIDIA/nccl/blob/master/LICENSE.txt)
  - libcudnn8=8.1.1 : [Software License Agreement :: NVIDIA Deep Learning cuDNN Documentation](https://docs.nvidia.com/deeplearning/cudnn/sla/index.html)
  - libcudnn8-dev=8.1.1 : [Software License Agreement :: NVIDIA Deep Learning cuDNN Documentation](https://docs.nvidia.com/deeplearning/cudnn/sla/index.html)
  - Les bibliothèques CUDA, NCCL et cuDNN et le [Contrat de Licence Utilisateur Final NVIDIA (avec supplément NCCL)](https://docs.nvidia.com/deeplearning/nccl/sla/index.html#overview) pour la bibliothèque NCCL

## <a name="accelerate-etl-workloads"></a>Accélérer des charges de travail ETL
Grâce à la prise en charge intégrée de l’[Accélérateur RAPIDS pour Apache Spark](https://nvidia.github.io/spark-rapids/), les pools Spark accélérés par GPU dans Azure Synapse peuvent apporter des améliorations significatives en matière de performances par rapport aux points de référence analytiques standard sans nécessiter de modifications de code. S’appuyant sur NVIDIA CUDA et UCX, NVIDIA RAPIDS permet les opérations DataFrame SQL accélérées par GPU, ainsi que les lectures aléatoires Spark. Dans la mesure où aucune modification de code n’est nécessaire pour bénéficier de ces accélérations, les utilisateurs peuvent également accélérer leurs pipelines de données qui s’appuient sur Delta Lake de Linux Foundation ou l’indexation Hyperspace de Microsoft. 

Pour en savoir plus sur la façon dont vous pouvez utiliser l’Accélérateur NVIDIA RAPIDS avec votre pool accéléré par GPU dans Azure Synapse Analytics, consultez ce guide sur l’[amélioration des performances avec RAPIDS](apache-spark-rapids-gpu.md).

## <a name="improve-machine-learning-scoring-workloads"></a>Améliorer les charges de travail de scoring Machine Learning
De nombreuses organisations s’appuient sur des travaux de scoring par lot volumineux pour s’exécuter souvent pendant des fenêtres de temps restreintes. Pour accomplir des travaux de scoring par lot améliorées, vous pouvez également utiliser des pools Spark accélérés par GPU avec la [bibliothèque Hummingbird](https://github.com/Microsoft/hummingbird) de Microsoft. Avec Hummingbird, les utilisateurs peuvent prendre leurs modèles ML traditionnels basés sur une arborescence pour les compiler dans des calculs tensoriels. Hummingbird permet aux utilisateurs de bénéficier facilement d’une accélération matérielle native et de frameworks de réseaux neuronaux pour accélérer le scoring de leurs modèles ML sans avoir besoin de réécrire leurs modèles.  

## <a name="next-steps"></a>Étapes suivantes
- [Azure Synapse Analytics](../overview-what-is.md)
