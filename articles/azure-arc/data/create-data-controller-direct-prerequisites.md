---
title: Prérequis | Mode de connexion directe
description: Prérequis en vue de déployer le contrôleur de données en mode de connexion directe.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/03/2021
ms.topic: overview
ms.openlocfilehash: 0016e3129a755b86acff2dfb1907f0ffcb62b569
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564192"
---
# <a name="prerequisites-to-deploy-the-data-controller-in-direct-connectivity-mode"></a>Prérequis en vue de déployer le contrôleur de données en mode de connectivité directe

Cet article explique comment préparer le déploiement d’un contrôleur de données pour les services de données avec Azure Arc en mode de connexion directe. Avant de déployer un contrôleur de données Azure Arc, comprenez les concepts décrits dans [Planifier le déploiement des services de données Azure Arc](plan-azure-arc-data-services.md).

À un niveau élevé, les prérequis à la création d’un contrôleur de données Azure Arc en mode de connectivité **directe** sont les suivants :

1. Connecter le cluster Kubernetes à Azure à l’aide de Kubernetes avec Azure Arc
2. Créer un contrôleur de données pour les services de données avec Azure Arc. Cette étape implique la création des éléments suivants :
    - Extension des services de données Azure Arc
    - Emplacement personnalisé
    - Contrôleur de données Azure Arc
3. Si vous souhaitez que le chargement des journaux dans Azure Log Analytics soit automatique, l’ID de l’espace de travail Log Analytics et la clé d’accès partagé sont nécessaires dans le cadre du déploiement.

## <a name="1-connect-kubernetes-cluster-to-azure-using-azure-arc-enabled-kubernetes"></a>1. Connecter le cluster Kubernetes à Azure à l’aide de Kubernetes avec Azure Arc

Pour connecter votre cluster kubernetes à Azure, utilisez Azure CLI `az` avec les extensions suivantes ou Helm.

### <a name="install-tools"></a>Installer des outils

- Installez Azure CLI ou mettez-le à niveau vers sa version la plus récente ([installation](/sql/azdata/install/deploy-install-azdata)) 

### <a name="add-extensions-for-azure-cli"></a>Ajouter des extensions pour Azure CLI

Installez les versions les plus récentes des extensions az suivantes :
- `k8s-extension`
- `connectedk8s`
- `k8s-configuration`
- `customlocation`

Exécutez les commandes suivantes pour installer les extensions CLI az :

```azurecli
az extension add --name k8s-extension
az extension add --name connectedk8s
az extension add --name k8s-configuration
az extension add --name customlocation
```

Si les extensions `k8s-extension`, `connectedk8s`, `k8s-configuration` et `customlocation` sont déjà installées, mettez-les à jour vers la version la plus récente à l’aide de la commande suivante :

```azurecli
az extension update --name k8s-extension
az extension update --name connectedk8s
az extension update --name k8s-configuration
az extension update --name customlocation
```

### <a name="connect-your-cluster-to-azure"></a>Connecter votre cluster à Azure

Pour effectuer cette tâche, suivez les étapes décrites dans [Connecter un cluster Kubernetes existant à Azure Arc](../kubernetes/quickstart-connect-cluster.md).

## <a name="2-optionally-keep-the-log-analytics-workspace-id-and-shared-access-key-ready"></a>2. Garder à disposition l’ID de l’espace de travail Log Analytics et la clé d’accès partagé (facultatif)

Lorsque vous déployez un contrôleur de données Azure Arc, vous pouvez activer le chargement automatique des métriques et des journaux pendant la configuration. Le chargement des métriques utilise l’identité managée affectée par le système. En revanche, le chargement des journaux nécessite un ID d’espace de travail et la clé d’accès de l’espace de travail. 

Vous pouvez également activer ou désactiver le chargement automatique des métriques et des journaux après avoir déployé le contrôleur de données. 

## <a name="3-create-azure-arc-data-services"></a>3. Créer des services de données Azure Arc

Après avoir rempli ces prérequis, vous pouvez [déployer le contrôleur de données Azure Arc | Mode de connexion directe](create-data-controller-direct-azure-portal.md).
