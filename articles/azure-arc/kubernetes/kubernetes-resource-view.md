---
title: Accéder aux ressources Kubernetes à partir du portail Azure
services: azure-arc
ms.service: azure-arc
ms.date: 10/31/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Découvrez comment interagir avec les ressources Kubernetes pour gérer un cluster Kubernetes Azure Arc à partir du portail Azure.
ms.openlocfilehash: 5af26c853df67b213b1951224ae9603d15a9dd25
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132000618"
---
# <a name="access-kubernetes-resources-from-azure-portal"></a>Accéder aux ressources Kubernetes à partir du portail Azure

Le portail Azure inclut une vue des ressources Kubernetes pour un accès facile aux ressources Kubernetes dans votre cluster Kubernetes avec Azure Arc. L’affichage des ressources Kubernetes à partir du Portail Azure réduit le basculement de contexte entre le Portail Azure et l’outil de ligne de commande `kubectl`, ce qui simplifie l’expérience d’affichage et de modification de vos ressources Kubernetes. La visionneuse de ressources comprend actuellement plusieurs types de ressources, tels que les déploiements, les pods et les jeux de réplicas.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Prérequis

- Un cluster Kubernetes existant [connecté](quickstart-connect-cluster.md) à Azure en tant que ressource Kubernetes avec Azure Arc.

- [Fonctionnalité de connexion de cluster activée](cluster-connect.md#enable-cluster-connect-feature) sur le cluster Kubernetes avec Azure Arc.

- [Jeton de compte de service](cluster-connect.md#service-account-token-authentication-option) pour l’authentification auprès du cluster.

## <a name="view-kubernetes-resources"></a>Afficher des ressources Kubernetes

Pour afficher les ressources Kubernetes, accédez à votre cluster AKS dans le Portail Azure. Le volet de navigation sur la gauche est utilisé pour accéder à vos ressources. Les ressources incluent :

- **Espaces de noms** affiche les espaces de noms de votre cluster. Le filtre en haut de la liste des espaces de noms fournit un moyen rapide de filtrer et d’afficher les ressources de votre espace de noms.
- **Charges de travail** affiche des informations sur les déploiements, les pods, les jeux de réplicas, les jeux avec état, les ensembles Daemon, les travaux et les travaux cron déployés sur votre cluster.
- **Services et entrées** affiche l’ensemble du service et des ressources d’entrée de votre cluster.
- **Stockage** affiche vos classes de stockage Azure et les informations sur les volumes persistants.
- **Configuration** affiche les mappages de configuration et les secrets de votre cluster.

[![Informations sur les charges de travail Kubernetes affichées dans le portail Azure ](media/kubernetes-resource-view/workloads.png)](media/kubernetes-resource-view/workloads.png#lightbox)

## <a name="edit-yaml"></a>Modifier YAML

L’affichage des ressources Kubernetes comprend également un éditeur YAML. Un éditeur YAML intégré vous permet de mettre à jour des objets Kubernetes à partir du portail et d’appliquer les modifications immédiatement.

Après avoir modifié le YAML, les modifications sont appliquées en sélectionnant **Examiner + enregistrer**, en confirmant les modifications, puis en enregistrant à nouveau.

[![Éditeur YAML pour les objets Kubernetes affichés dans le portail Azure ](media/kubernetes-resource-view/yaml-editor.png)](media/kubernetes-resource-view/yaml-editor.png#lightbox)

>[!WARNING]
> Il est déconseillé d’effectuer des modifications de production directes par le biais de l’interface utilisateur ou de l’interface CLI. Vous devez envisager d’utiliser des [configurations (GitOps)](tutorial-use-gitops-connected-cluster.md){2} pour les environnements de production. Les fonctionnalités de gestion de Kubernetes du portail Azure et de l’éditeur YAML sont conçues pour l’apprentissage et le déploiement en mode Fighting de nouveaux déploiements dans un paramètre de développement et de test.

## <a name="next-steps"></a>Étapes suivantes

Azure Monitor pour conteneurs fournit des informations plus approfondies sur les nœuds et les conteneurs du cluster par rapport à l’affichage logique des ressources Kubernetes disponibles avec l’affichage des ressources Kubernetes décrit dans cet article. Découvrez comment [déployer Azure Monitor pour les conteneurs](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json) sur votre cluster.