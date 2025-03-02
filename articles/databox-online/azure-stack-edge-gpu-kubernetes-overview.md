---
title: Vue d’ensemble d’un cluster Kubernetes sur un appareil Microsoft Azure Stack Edge Pro | Microsoft Docs
description: Décrit comment Kubernetes est implémenté sur votre appareil Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 22a66ca16d8d2cb7ade82cb665f50cc9aae4b27b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563100"
---
# <a name="kubernetes-on-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes sur votre appareil Azure Stack Edge Pro avec GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Kubernetes est une plateforme open source populaire qui permet d’orchestrer des applications en conteneur. Cet article fournit une vue d’ensemble de Kubernetes, puis décrit le fonctionnement de Kubernetes sur votre appareil Azure Stack Edge Pro. 

## <a name="about-kubernetes"></a>À propos de Kubernetes 

Kubernetes fournit une plateforme simple et fiable pour gérer les applications basées sur un conteneur et leurs composants de stockage et de mise en réseau associés. Kubernetes vous permet de créer, livrer et mettre à l’échelle rapidement des applications en conteneur.

En tant que plateforme ouverte, Kubernetes vous permet de créer des applications avec vos langage de programmation, système d’exploitation, bibliothèques ou bus de messagerie préférés. Pour planifier et déployer des mises en production, Kubernetes peut s’intégrer avec des outils d’intégration et de livraison continues existants.

Pour plus d’informations, consultez [fonctionnement de Kubernetes](https://www.youtube.com/watch?v=q1PcAawa4Bg&list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&index=2&t=0s).

## <a name="kubernetes-on-azure-stack-edge-pro"></a>Kubernetes sur Azure Stack Edge Pro

Sur votre appareil Azure Stack Edge Pro, vous pouvez créer un cluster Kubernetes en configurant le calcul. Lorsque le rôle de calcul est configuré, le cluster Kubernetes, y compris les nœuds principal et Worker, sont déployés et configurés pour vous. Ce cluster est ensuite utilisé pour le déploiement de la charge de travail via `kubectl`, IoT Edge ou Azure Arc.

L’appareil Azure Stack Edge Pro est disponible en tant que configuration à un nœud constituant le cluster d’infrastructure. Le cluster Kubernetes est séparé du cluster d’infrastructure, et déployé par-dessus le cluster d’infrastructure. Le cluster d’infrastructure fournit le stockage persistant pour votre appareil Azure Stack Edge Pro tandis que le cluster Kubernetes est uniquement responsable de l’orchestration de l’application. 

Dans ce cas, le cluster Kubernetes a un nœud principal et un nœud Worker. Les nœuds Kubernetes dans un cluster sont des machines virtuelles qui exécutent vos applications et flux de travail cloud. 

Le nœud principal Kubernetes est chargé de maintenir l’état souhaité pour votre cluster. Le nœud principal contrôle également le nœud Worker qui, à son tour, exécute les applications en conteneur. 

Le diagramme suivant illustre l’implémentation de Kubernetes sur un appareil Azure Stack Edge Pro à 1 nœud. L’appareil à 1 nœud n’est pas hautement disponible et, en cas de défaillance du nœud unique, l’appareil s’arrête. Le cluster Kubernetes s’arrête également.

![Architecture Kubernetes pour appareil Azure Stack Edge Pro à 1 nœud](media/azure-stack-edge-gpu-kubernetes-overview/kubernetes-architecture-1-node.png)

Pour plus d’informations sur l’architecture de cluster Kubernetes, accédez à [Concepts de base de Kubernetes](https://kubernetes.io/docs/concepts/architecture/).

Les nœuds Master et Worker sont des machines virtuelles qui consomment le processeur et la mémoire. Lors du déploiement de charges de travail Kubernetes, il est important de comprendre les exigences de calcul des machines virtuelles Master et Worker.

|Type de machine virtuelle Kubernetes|Configuration requise en termes de mémoire et de processeur|
|---------|---------|
|Machine virtuelle Master|4 cœurs, 4 Go de RAM|
|Machine virtuelle Worker|12 cœurs, 32 Go de RAM|
<!--The Kubernetes cluster control plane components make global decisions about the cluster. The control plane has:

- *kubeapiserver* that is the front end of the Kubernetes API and exposes the API.
- *etcd* that is a highly available key value store that backs up all the Kubernetes cluster data.
- *kube-scheduler* that makes scheduling decisions.
- *kube-controller-manager* that runs controller processes such as those for node controllers, replications controllers, endpoint controllers, and service account and token controllers. -->

## <a name="storage-volume-provisioning"></a>Approvisionnement du volume de stockage

Pour prendre en charge les charges de travail d’application, vous pouvez monter des volumes de stockage pour les données persistantes sur vos partages d’appareils Azure Stack Edge Pro. Les volumes statiques et dynamiques peuvent être utilisés. 

Pour plus d’informations, consultez les options de provisionnement de stockage pour les applications dans [Stockage Kubernetes pour votre appareil Azure Stack Edge Pro](azure-stack-edge-gpu-kubernetes-storage.md).

## <a name="networking"></a>Mise en réseau

La mise en réseau Kubernetes vous permet de configurer la communication au sein de votre réseau Kubernetes, y compris la mise en réseau entre conteneurs, la mise en réseau entre pods, la mise en réseau entre pod et service, et la mise en réseau entre Internet et service. Pour plus d’informations, consultez le modèle de réseau dans [Réseau Kubernetes pour votre appareil Azure Stack Edge Pro](azure-stack-edge-gpu-kubernetes-networking.md).

## <a name="updates"></a>Mises à jour

À mesure que de nouvelles versions de Kubernetes deviennent disponibles, votre cluster peut être mis à niveau à l’aide des mises à jour standard disponibles pour votre appareil Azure Stack Edge Pro. Pour plus d’informations sur la procédure de mise à niveau, consultez [Appliquer des mises à jour pour votre Azure Stack Edge Pro](azure-stack-edge-gpu-install-update.md).

## <a name="access-monitoring"></a>Accès, surveillance

Le cluster Kubernetes sur votre appareil Azure Stack Edge Pro autorise le contrôle d’accès en fonction du rôle Kubernetes (Kubernetes RBAC). Pour plus d’informations, consultez [Contrôle d’accès en fonction du rôle Kubernetes sur votre appareil Azure Stack Edge Pro avec GPU](azure-stack-edge-gpu-kubernetes-rbac.md).

Vous pouvez également surveiller l’intégrité de votre cluster et de vos ressources via le tableau de bord Kubernetes. Des journaux de conteneur sont également disponibles. Pour plus d’informations, consultez [Utiliser le tableau de bord Kubernetes pour superviser l’intégrité du cluster Kubernetes sur votre appareil Azure Stack Edge Pro](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).

Azure Monitor est également disponible en tant que module complémentaire pour collecter des données d’intégrité des conteneurs, nœuds et contrôleurs. Pour plus d’informations, consultez [Vue d’ensemble d’Azure Monitor](../azure-monitor/overview.md).

<!--## Private container registry

Kubernetes on Azure Stack Edge Pro device allows for the private storage of your images by providing a local container registry.-->

## <a name="application-management"></a>Gestion des applications

Dès lors qu’un cluster Kubernetes a été créé sur votre appareil Azure Stack Edge Pro, vous pouvez gérer les applications déployées sur ce cluster via l’une des méthodes suivantes :

- Accès natif via `kubectl`
- IoT Edge 
- Azure Arc

Les méthodes sont expliquées dans les sections suivantes.


### <a name="kubernetes-and-kubectl"></a>Kubernetes et kubectl

Une fois le cluster Kubernetes déployé, vous pouvez gérer les applications déployées dessus localement à partir d’un ordinateur client. Vous utilisez un outil natif tel que *kubectl* via la ligne de commande pour interagir avec les applications. 

Pour plus d’informations sur le déploiement d’un cluster Kubernetes, consultez [Déployer un cluster Kubernetes sur votre appareil Azure Stack Edge Pro](azure-stack-edge-gpu-create-kubernetes-cluster.md). Pour plus d’informations sur la gestion, consultez [Utiliser kubectl pour gérer un cluster Kubernetes sur votre appareil Azure Stack Edge Pro](azure-stack-edge-gpu-create-kubernetes-cluster.md).


### <a name="kubernetes-and-iot-edge"></a>Kubernetes et IoT Edge

Kubernetes peut également être intégré avec des charges de travail IoT Edge sur un appareil Azure Stack Edge Pro, dans lequel Kubernetes fournit la mise à l’échelle et l’écosystème, et IoT l’écosystème orienté IoT. La couche Kubernetes est utilisée comme couche d’infrastructure pour déployer les charges de travail d’Azure IoT Edge. La durée de vie du module et l’équilibrage de la charge réseau sont gérés par Kubernetes, tandis que la plateforme d’application de périphérie est gérée par IoT Edge.

Pour plus d’informations sur le déploiement d’applications sur votre cluster Kubernetes via IoT Edge, accédez à : 

- [Exposer des applications sans état sur un appareil Azure Stack Edge Pro via IoT Edge](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md).


### <a name="kubernetes-and-azure-arc"></a>Kubernetes et Azure Arc

Azure Arc est un outil de gestion hybride qui vous permet de déployer des applications sur vos clusters Kubernetes. Azure Arc vous permet également d’utiliser Azure Monitor pour conteneurs afin d’afficher et de surveiller vos clusters. Pour plus d’informations, consultez [Qu’est-ce que Kubernetes avec Azure Arc ?](../azure-arc/kubernetes/overview.md). Pour plus d’informations sur la tarification d’Azure Arc, consultez [Tarification d’Azure Arc](https://azure.microsoft.com/services/azure-arc/#pricing).

À compter de mars 2021, Kubernetes avec Azure Arc sera mis à la disposition générale pour les utilisateurs, et des frais d’utilisation standard s’appliquent. En tant que client de préversion apprécié, vous pouvez disposer de Kubernetes avec Azure Arc gratuitement pour des appareils Azure Stack Edge. Pour bénéficier de l’offre en préversion, créez une [Demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) :

1. Sous **Type de problème**, sélectionnez **Facturation**.
2. Sous **Abonnement**, sélectionnez votre abonnement.
3. Sous **Service**, sélectionnez **Mes services**, puis **Azure Stack Edge**.
4. Sous **Ressource**, sélectionnez votre ressource.
5. Sous **Résumé**, entrez une description de votre problème.
6. Sous **Type de problème**, sélectionnez **Frais imprévus**.
7. Sous **Sous-type de problème**, sélectionnez **M’aider à comprendre les frais facturés sur mon essai gratuit**.


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le stockage Kubernetes pour un [appareil Azure Stack Edge Pro](azure-stack-edge-gpu-kubernetes-storage.md).
- Comprendre le modèle de réseau Kubernetes sur un [appareil Azure Stack Edge Pro](azure-stack-edge-gpu-kubernetes-networking.md).
- Déployer [Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md) sur le portail Azure.
