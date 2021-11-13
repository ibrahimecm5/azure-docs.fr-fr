---
title: Extensions de cluster – Kubernetes avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 10/19/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Cet article fournit une vue d’ensemble conceptuelle de la fonctionnalité d’extensions de cluster de Kubernetes avec Azure Arc
ms.openlocfilehash: 1f6bc02e111041e9a89e8066a57586c331c1a297
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130265535"
---
# <a name="cluster-extensions"></a>Extensions de cluster

Les services de gestion comme Azure Monitor et Azure Defender pour Kubernetes, ou les services comme Azure App, Azure Data peuvent être instanciés sur des clusters Kubernetes avec la fonctionnalité Extensions de cluster. Les [graphiques Helm](https://helm.sh/) vous aident à gérer les applications Kubernetes en fournissant les blocs de construction nécessaires pour définir, installer et mettre à niveau les applications Kubernetes les plus complexes. La fonctionnalité d’extension de cluster s’appuie sur les composants d’empaquetage de Helm en fournissant une expérience pilotée par Azure Resource Manager pour l’installation et la gestion du cycle de vie des instances des services que vous voulez activer sur votre cluster Kubernetes. Un opérateur ou un administrateur de cluster peut utiliser la fonctionnalité d’extensions de cluster pour 

- Installer différentes extensions pour la fonctionnalité souhaitée, et obtenir un inventaire de tous les clusters et des extensions installées sur ces clusters en utilisant des interfaces Azure comme le portail Azure, l’interface CLI, le SDK, etc. 
- Comme pour toutes les autres ressources Azure, vous pouvez contrôler l’accès à la ressource d’extension de cluster avec le contrôle d’accès en fonction du rôle (RBAC) Azure
- Utiliser Azure Policy pour automatiser le déploiement à grande échelle des extensions de cluster sur tous les clusters de votre environnement. 
- S’abonner aux trains de mise en production (par exemple : préversion, stable) pour chaque extension.
- Gérer les mises à jour en configurant la mise à niveau automatique pour les extensions ou en les épinglant à une version spécifique.
- Gérer le cycle de vie des extensions, y compris les mises à jour des propriétés d’extension, ou la suppression d’une ou plusieurs instances d’extension.

Une extension peut être limitée à un cluster ou à un espace de noms. Chaque type d’extension (par exemple : Azure Monitor, Azure Defender, les services Azure App) définit l’étendue sur laquelle elle fonctionne sur le cluster. 

## <a name="architecture-for-azure-arc-enabled-kubernetes-clusters"></a>Architecture des clusters Kubernetes avec Azure Arc

[ ![Architecture des extensions de cluster](./media/conceptual-extensions.png) ](./media/conceptual-extensions.png#lightbox)

L’instance d’extension de cluster est créée comme une ressource Azure Resource Manager d’extension (`Microsoft.KubernetesConfiguration/extensions`) en plus de la ressource Kubernetes avec Azure Arc (représentée par `Microsoft.Kubernetes/connectedClusters`) dans Azure Resource Manager. Une représentation dans Azure Resource Manager vous permet de créer une stratégie qui vérifie toutes les ressources Kubernetes avec Azure Arc, avec ou sans extension de cluster spécifique. Une fois que vous avez déterminé les clusters qui n’ont pas les extensions de cluster avec les valeurs de propriété souhaitées, vous pouvez corriger ces ressources non conformes avec Azure Policy.

Le `config-agent` qui s’exécute dans votre cluster monitore et effectue le suivi des ressources d’extension nouvellement créées ou des mises à jour des ressources d’extension existantes sur la ressource Kubernetes avec Azure Arc. Le composant `extensions-manager` qui s’exécute dans votre cluster tire ensuite le graphique Helm associé à une extension de cluster à partir d’Azure Container Registry ou de Microsoft Container Registry, et l’installe sur le cluster. 

Les composants `config-agent` et `extensions-manager` qui s’exécutent dans le cluster gèrent les mises à jour vers les nouvelles versions, et d’autres opérations comme les mises à jour et la suppression des propriétés de l’instance d’extension. Ces agents utilisent une identité managée affectée par le système pour communiquer de manière sécurisée avec le service back-end dans Azure. 

> [!NOTE]
> * `config-agent` monitore la création de ressources d’extension ou leur mise à jour sur la ressource Kubernetes avec Azure Arc. Les agents ont donc besoin d’une connectivité pour tirer l’état souhaité de l’extension vers le cluster. Si les agents ne parviennent pas à se connecter à Azure, il se produit un retard dans la propagation de l’état souhaité vers le cluster.
> * Les paramètres ProtectedConfiguration sont l’une des propriétés que vous pouvez définir sur les extensions de cluster. Les paramètres de configuration protégés pour une extension sont stockés pendant 48 heures maximum dans les services Kubernetes avec Azure Arc. Par conséquent, si le cluster reste déconnecté pendant les 48 heures après la création de la ressource d’extension sur Azure, l’extension passe d’un état `Pending` à un état `Failed`. Nous vous recommandons de mettre les clusters en ligne aussi régulièrement que possible.

## <a name="next-steps"></a>Étapes suivantes

* Utilisez notre guide de démarrage rapide pour [connecter un cluster Kubernetes à Azure Arc](./quickstart-connect-cluster.md).
* [Déployez des extensions de cluster](./extensions.md) sur votre cluster Kubernetes avec Azure Arc.
