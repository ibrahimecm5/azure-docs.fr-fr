---
title: Déployer l’agent Log Analytics sur des serveurs avec Arc
description: Cet article passe en revue les différentes méthodes de déploiement de l’agent Log Analytics sur des machines Windows et Linux inscrites sur des serveurs avec Azure Arc dans votre centre de données local ou un autre environnement cloud.
ms.date: 10/22/2021
ms.topic: conceptual
ms.openlocfilehash: 5ba1ccc8407903ca40677938b19846a7e6c205a0
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132369452"
---
# <a name="understand-deployment-options-for-the-log-analytics-agent-on-azure-arc-enabled-servers"></a>Comprendre les options de déploiement de l’agent Log Analytics sur les serveurs avec Azure Arc

Azure Monitor prend en charge plusieurs méthodes pour installer l’agent Log Analytics et connecter au service votre machine ou serveur inscrit sur des serveurs avec Azure Arc. Les serveurs avec Azure Arc prennent en charge le framework d’extension VM Azure qui fournit des tâches d’automatisation et de configuration après le déploiement, ce qui vous permet de simplifier la gestion de vos machines hybrides comme s’il s’agissait de machines virtuelles Azure.

L’agent Log Analytics est nécessaire si vous voulez :

* Monitorer le système d’exploitation, et toutes les charges de travail s’exécutant sur la machine ou le serveur en utilisant les [Insights VM](../../azure-monitor/vm/vminsights-overview.md). Analyser plus en profondeur et créer des alertes en utilisant d’autres fonctionnalités d’[Azure Monitor](../../azure-monitor/overview.md).
* Assurez une surveillance de la sécurité dans Azure à l’aide de [Microsoft Defender pour le cloud](../../defender-for-cloud/defender-for-cloud-introduction.md) ou de [Microsoft Sentinel](../../sentinel/overview.md).
* Gérer les mises à jour du système d’exploitation avec [Update Management d’Azure Automation](../../automation/update-management/overview.md).
* Collecter l’inventaire et suivre les changements avec [Suivi des modifications et inventaire d’Azure Automation](../../automation/change-tracking/overview.md).
* Exécuter des runbooks Automation directement sur la machine et sur les ressources de l’environnement en utilisant un [Runbook Worker hybride Azure Automation](../../automation/automation-hybrid-runbook-worker.md).

Cet article passe en revue les méthodes de déploiement de l’extension VM de l’agent Log Analytics sur plusieurs serveurs physiques ou machines virtuelles de production dans votre environnement, pour vous aider à déterminer celle qui convient le mieux à votre organisation. Si vous êtes intéressé par le nouvel agent Azure Monitor et que vous voulez voir une comparaison détaillée, consultez l’article [Vue d’ensemble des agents Azure Monitor](../../azure-monitor//agents/agents-overview.md).  

## <a name="installation-options"></a>Options d'installation

Vous pouvez utiliser différentes méthodes individuelle pour installer l’extension VM ou utiliser une combinaison d’entre elles. Cette section décrit chacune des méthodes à prendre en compte.

### <a name="using-arc-enabled-servers"></a>Utilisation de serveurs avec Azure Arc

Cette méthode prend en charge l’installation, la gestion et la suppression des extensions VM à partir du [portail Azure](manage-vm-extensions-portal.md), de [PowerShell](manage-vm-extensions-powershell.md), d’[Azure CLI](manage-vm-extensions-cli.md) ou d’un [modèle Azure Resource Manager (ARM)](manage-vm-extensions-template.md).

#### <a name="advantages"></a>Avantages

* Peut être utile dans le cadre de tests.
* Utile si vous avez peu de machines à gérer.

#### <a name="disadvantages"></a>Inconvénients

* Automatisation limitée en cas d’utilisation d’un modèle Azure Resource Manager, sinon prend beaucoup de temps.
* Peut se concentrer sur un seul serveur avec Arc et non plusieurs instances.
* Prend en charge la spécification d’un seul espace de travail auquel rapporter. Nécessite PowerShell ou Azure CLI pour configurer l’extension VM de l’agent Windows Log Analytics afin de rapporter à quatre espaces de travail maximum.
* Ne prend pas en charge le déploiement de Dependency Agent à partir du portail. Vous pouvez utiliser uniquement PowerShell, Azure CLI ou un modèle ARM.

### <a name="using-azure-policy"></a>Utilisation d’Azure Policy

Vous pouvez utiliser Azure Policy pour déployer l’extension VM de l’agent Log Analytics à grande échelle sur les machines de votre environnement et maintenir la conformité de la configuration. Pour ce faire, vous pouvez utiliser la définition de stratégie **Configurer l’extension Log Analytics sur des serveurs Linux avec Azure Arc** / **Configurer l’extension Log Analytics sur des serveurs Windows avec Azure Arc**, ou l’initiative de stratégie **Activer Azure Monitor pour machines virtuelles**.

Azure Policy comprend plusieurs définitions prédéfinies en rapport avec Azure Monitor. Pour obtenir la liste complète des stratégies intégrées de la catégorie **Monitoring**, consultez [Définitions intégrées d’Azure Policy pour Azure Monitor](../../azure-monitor/policy-reference.md).

#### <a name="advantages"></a>Avantages

* Si l’extension VM est supprimée, elle est réinstallée après l’évaluation de la stratégie.
* Identifie et installe l’extension VM quand un nouveau serveur avec Azure Arc est inscrit sur Azure.
* Prend en charge la spécification d’un seul espace de travail auquel rapporter. Nécessite PowerShell ou Azure CLI pour configurer l’extension VM de l’agent Windows Log Analytics afin de rapporter à quatre espaces de travail maximum.

#### <a name="disadvantages"></a>Inconvénients

* La stratégie **Configurer l’extension Log Analytics sur des serveurs** *système d’exploitation* **avec Azure Arc** se contente d’installer l’extension VM Log Analytics et de configurer l’agent pour qu’il rapporte à l’espace de travail Log Analytics spécifié. Si vous êtes intéressé par les insights VM pour monitorer les performances du système d’exploitation, et voulez mapper les processus et les dépendances en cours d’exécution sur d’autres ressources, vous devez appliquer l’initiative de stratégie **Activer Azure Monitor pour machines virtuelles**. Elle installe et configure à la fois l’extension VM Log Analytics et l’extension VM Dependency Agent qui sont nécessaires.
* Le cycle d’évaluation de conformité standard se produit une fois par 24 heures. Une analyse d’évaluation d’un abonnement ou d’un groupe de ressources peut être démarrée en utilisant Azure CLI, Azure PowerShell, un appel à l’API REST ou l’action GitHub pour l’analyse de conformité Azure Policy. Pour plus d’informations, consultez [Déclencheurs d’évaluation](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

### <a name="using-azure-automation"></a>Utilisation d’Azure Automation

L’environnement d’exploitation de l’automatisation des processus dans Azure Automation et sa prise en charge pour les runbooks PowerShell et Python peuvent vous permettre d’automatiser le déploiement de l’extension VM de l’agent Log Analytics à grande échelle sur les machines de votre environnement.

#### <a name="advantages"></a>Avantages

* Peut utiliser une méthode scriptée pour automatiser son déploiement et sa configuration en utilisant les langages de script qui vous sont familiers.
* S’exécute selon une planification que vous définissez et contrôlez.
* Sécurise l’authentification du compte Automation sur les serveurs avec Arc en utilisant une identité managée.

#### <a name="disadvantages"></a>Inconvénients

* Nécessite un compte Azure Automation.
* Nécessite une expérience en matière de création et de gestion des runbooks dans Azure Automation.
* Nécessite de créer un runbook basé sur PowerShell ou Python selon le système d’exploitation cible.

## <a name="next-steps"></a>Étapes suivantes

* Pour gérer les mises à jour du système d’exploitation avec Update Management d’Azure Automation, consultez [Activer à partir d’un compte Automation](../../automation/update-management/enable-from-automation-account.md), puis suivez les étapes pour permettre aux machines de rapporter à l’espace de travail.

* Pour suivre les changements avec la fonctionnalité Suivi des modifications et inventaire d’Azure Automation, consultez [Activer à partir d’un compte Automation](../../automation/change-tracking/enable-from-automation-account.md), puis suivez les étapes pour permettre aux machines de rapporter à l’espace de travail.

* Vous pouvez utiliser la fonctionnalité Runbook Worker hybride utilisateur d’Azure Automation pour exécuter des runbooks directement sur des serveurs avec Azure Arc ou des machines inscrites sur des serveurs avec Azure Arc. Consultez l’article [Déployer l’extension VM du Runbook Worker hybride](../../automation/extension-based-hybrid-runbook-worker-install.md).

* Pour commencer à collecter des événements liés à la sécurité avec Microsoft Sentinel, consultez [Intégrer à Microsoft Sentinel](scenario-onboard-azure-sentinel.md). Ou, pour les collecter avec Microsoft Defender pour le cloud, consultez [Intégrer à Microsoft Defender pour le cloud](../../security-center/quickstart-onboard-machines.md).

* Consultez les articles des insights VM [Monitorer les performances](../../azure-monitor/vm/vminsights-performance.md) et [Mapper des dépendances](../../azure-monitor/vm/vminsights-maps.md) pour voir les performances de votre machine et les composants d’application découverts.
