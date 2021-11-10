---
title: Déployer des charges de travail Azure IoT Edge
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
description: Déployer des charges de travail Azure IoT Edge
keywords: Kubernetes, Arc, Azure, K8s, conteneurs
ms.openlocfilehash: 9870e6ce0904510c549033dfd64de9d377b886af
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056413"
---
# <a name="deploy-azure-iot-edge-workloads"></a>Déployer des charges de travail Azure IoT Edge

## <a name="overview"></a>Vue d’ensemble

Azure Arc et Azure IoT Edge se complètent facilement. 

Azure Arc fournit des mécanismes pour les opérateurs de cluster permettant de configurer les principaux composants d’un cluster et d’appliquer des stratégies de cluster. 

Azure IoT Edge permet aux opérateurs d’application de déployer et de gérer les charges de travail, à distance et à grande échelle, grâce à une ingestion cloud pratique et à des primitives de communication bidirectionnelle. 

Le diagramme ci-dessous illustre la relation entre Azure Arc et Azure IoT Edge :

![Configuration IoT Arc](./media/edge-arc.png)

## <a name="pre-requisites"></a>Conditions préalables

* [Inscrivez un appareil IoT Edge](../../iot-edge/quickstart-linux.md#register-an-iot-edge-device) et [déployez le module de capteur de température simulé](../../iot-edge/quickstart-linux.md#deploy-a-module). Notez la chaîne de connexion de l’appareil pour *values.yaml* mentionné ci-dessous.

* Utilisez la [prise en charge IoT Edge pour Kubernetes](https://aka.ms/edgek8sdoc) afin de le déployer par le biais de l’opérateur Flux d’Azure Arc.

* Téléchargez le fichier [*values.yaml*](https://github.com/Azure/iotedge/blob/preview/iiot/kubernetes/charts/edge-kubernetes/values.yaml) pour le chart Helm IoT Edge et remplacez l’espace réservé `deviceConnectionString` situé à la fin du fichier par la chaîne de connexion notée précédemment. Définissez toutes les autres options d’installation de chart prises en charge selon vos besoins. Créez un espace de noms pour la charge de travail IoT Edge, puis générez-y un secret :

  ```
  $ kubectl create ns iotedge

  $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
  ```

  Vous pouvez également procéder à la configuration à distance en utilisant l’[exemple de configuration de cluster](./tutorial-use-gitops-connected-cluster.md).

## <a name="connect-a-cluster"></a>Connecter un cluster

Utilisez l’extension `az` Azure CLI `connectedk8s` pour connecter un cluster Kubernetes à Azure Arc :

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>Créer une configuration pour IoT Edge

Cet [exemple de référentiel Git](https://github.com/veyalla/edgearc) pointe vers le chart Helm IoT Edge et référence le secret créé dans la section concernant les prérequis.

Utilisez l’extension `az` Azure CLI `k8s-configuration` pour créer une configuration en vue de lier le cluster connecté au référentiel Git :

  ```
  az k8s-configuration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
  ```

Dans quelques minutes, vous devriez voir les modules de charge de travail IoT Edge déployés dans l’espace de noms `iotedge` de votre cluster. 

Affichez les journaux du pod `SimulatedTemperatureSensor` dans cet espace de noms pour voir les valeurs qui sont générées. Vous pouvez également regarder les messages arriver sur votre hub IoT avec l’[extension Azure IoT Hub Toolkit pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="cleanup"></a>Nettoyage

Supprimer la configuration :

```
az k8s-configuration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [utiliser Azure Policy pour gérer la configuration du cluster](./use-azure-policy.md).
