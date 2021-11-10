---
title: Utiliser Azure Policy pour appliquer des configurations de cluster à grande échelle
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
description: Utiliser Azure Policy pour appliquer des configurations de cluster à grande échelle
keywords: Kubernetes, Arc, Azure, K8s, conteneurs
ms.openlocfilehash: 9713f57207942c9fed5613b298ea83865ef685bd
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059428"
---
# <a name="use-azure-policy-to-apply-gitops-configurations-at-scale"></a>Utiliser Azure Policy pour appliquer des configurations GitOps à grande échelle

Vous pouvez utiliser Azure Policy pour appliquer des configurations (type de ressource `Microsoft.KubernetesConfiguration/sourceControlConfigurations`) à grande échelle sur des clusters Kubernetes avec Azure Arc (`Microsoft.Kubernetes/connectedclusters`).

Pour utiliser Azure Policy, sélectionnez une définition de stratégie GitOps intégrée et créez une attribution de stratégie. Lors de la création de l’attribution de stratégie :
1. Définissez l’étendue de l’attribution.
    * L’étendue sera tous les groupes de ressources d’un abonnement ou un groupe d’administration ou des groupes de ressources spécifiques.
2. Définissez les paramètres de la configuration GitOps à créer. 

Une fois l’affectation créée, le moteur Azure Policy identifie tous les clusters Kubernetes avec Azure Arc situés dans l’étendue et applique la configuration GitOps à chaque cluster.

Pour permettre la séparation des préoccupations, vous pouvez créer plusieurs attributions de stratégies, chacune avec une configuration GitOps différente qui pointe vers un autre référentiel git. Par exemple, un référentiel peut être utilisé par les administrateurs de cluster et d’autres dépôts peuvent être utilisés par les équipes d’application.

> [!TIP]
> Il existe des définitions de stratégie intégrées pour ces scénarios :
> * Référentiel public ou référentiel privé avec les clés SSH créées par Flux : `Configure Kubernetes clusters with specified GitOps configuration using no secrets`
> * Référentiel privé avec les clés SSH fournies par l’utilisateur : `Configure Kubernetes clusters with specified GitOps configuration using SSH secrets`
> * Référentiel privé avec les clés HTTPS fournies par l’utilisateur : `Configure Kubernetes clusters with specified GitOps configuration using HTTPS secrets`

## <a name="prerequisite"></a>Configuration requise

Vérifiez que vous disposez d’autorisations `Microsoft.Authorization/policyAssignments/write` sur l’étendue (abonnement ou groupe de ressources) où vous allez créer cette affectation de stratégie.

## <a name="create-a-policy-assignment"></a>Créer une affectation de stratégie

1. Dans le portail Azure, accédez à **Stratégie**.
1. Dans la section **Création** de la barre latérale, sélectionnez **Définitions**.
1. Dans la catégorie « Kubernetes », choisissez la définition de stratégie intégrée « Configurer des clusters Kubernetes avec la configuration GitOps spécifiée sans secret ». 
1. Cliquez sur **Attribuer**.
1. Définissez **Portée** sur le groupe d’administration, l’abonnement ou le groupe de ressources auquel s’appliquera l’attribution de stratégie.
    * Si vous souhaitez exclure des ressources de la portée d’attribution de la stratégie, définissez **Exclusions**.
1. Donnez à l’attribution de stratégie un **Nom** et une **Description** facilement identifiables.
1. Assurez-vous que l’option **Application de la stratégie** est définie sur **Activée**.
1. Sélectionnez **Suivant**.
1. Définissez les valeurs de paramètre à utiliser lors de la création du `sourceControlConfiguration`.
    * Pour plus d’informations sur les paramètres, consultez le [didacticiel sur le déploiement des configurations GitOps](./tutorial-use-gitops-connected-cluster.md).
1. Sélectionnez **Suivant**.
1. Activez l’option **Créer une tâche de correction**.
1. Vérifiez que l’option **Créer une identité managée** est activée et que l’identité aura des autorisations de **Contributeur**. 
    * Pour plus d’informations, consultez le [Démarrage rapide Créer une attribution de stratégie](../../governance/policy/assign-policy-portal.md) et l’[article Corriger les ressources non conformes avec Azure Policy](../../governance/policy/how-to/remediate-resources.md).
1. Sélectionnez **Revoir + créer**.

Une fois l’affectation de stratégie créée, la configuration est appliquée aux nouveaux clusters Kubernetes avec Azure Arc créés dans l’étendue de l’affectation de stratégie.

Pour les clusters existants, vous devez exécuter manuellement une tâche de correction. Cette tâche nécessite généralement 10 à 20 minutes pour que l’attribution de stratégie prenne effet.

## <a name="verify-a-policy-assignment"></a>Vérifier une attribution de stratégie

1. Dans le portail Azure, accédez à l’un de vos clusters Kubernetes avec Azure Arc.
1. Dans la section **Paramètres** de la barre latérale, sélectionnez **Stratégies**. 
    * Dans la liste, vous devriez voir l’attribution de stratégie que vous avez créée ci-dessus, et l’**État de conformité** devrait être *Conforme*.
1. Dans la section **Paramètres** de la barre latérale, sélectionnez **GitOps**.
    * Dans la liste des configurations, vous devez voir la configuration créée par l’affectation de stratégie.
1. Utilisez `kubectl` pour interroger le cluster. 
    * Vous devez voir l’espace de noms et les artefacts créés par la configuration GitOps.
    * Vous devez voir les objets décrits par les manifestes dans le référentiel Git déployé sur le cluster.

## <a name="next-steps"></a>Étapes suivantes

[Configurer Azure Monitor pour des conteneurs incluant des clusters Kubernetes avec Azure Arc](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md).
