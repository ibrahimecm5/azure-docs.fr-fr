---
title: Surveiller et protéger les machines virtuelles avec les services Azure natifs
description: Découvrez comment intégrer et déployer les outils natifs Microsoft Azure pour surveiller et gérer vos charges de travail Azure VMware Solution.
ms.topic: how-to
ms.date: 08/15/2021
ms.openlocfilehash: 8c95a74df7608aafbec09da9af94b0f82eb2ced3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315821"
---
# <a name="monitor-and-protect-vms-with-azure-native-services"></a>Surveiller et protéger les machines virtuelles avec les services Azure natifs

Les services natifs Microsoft Azure vous permettent de surveiller, gérer et protéger vos machines virtuelles dans un environnement hybride (Azure, Azure VMware Solution et en local). Dans cet article, vous allez intégrer les services natifs Azure dans votre cloud privé Azure VMware Solution. Vous apprendrez également à utiliser les outils permettant de gérer vos machines virtuelles tout au long de leur cycle de vie.

Les services natifs Azure que vous pouvez intégrer à Azure VMware Solution sont les suivants :

- **Azure Arc** étend la gestion d’Azure à toute infrastructure, notamment Azure VMware Solution, infrastructure locale ou autre plateforme cloud. Les [serveurs avec Azure Arc](../azure-arc/servers/overview.md) vous permettent de gérer vos machines virtuelles et vos serveurs physiques Windows et Linux hébergés *en dehors* d’Azure sur votre réseau d’entreprise ou sur un autre fournisseur de cloud. Vous pouvez attacher un cluster Kubernetes hébergé dans votre environnement Azure VMware Solution à l’aide de [Kubernetes avec Azure Arc](../azure-arc/kubernetes/overview.md). 

- **Azure Monitor** collecte et analyse la télémétrie provenant de vos environnements cloud et locaux, puis agit en conséquence. Il ne nécessite aucun déploiement.  Vous pouvez surveiller les performances du système d’exploitation invité, ainsi que découvrir et mapper des dépendances d’application pour des machines virtuelles Azure VMware Solution ou locales. Votre espace de travail Log Analytics dans Azure Monitor active la collecte des journaux et la collecte des compteurs de performances à l’aide de l’agent ou d’extensions Log Analytics. 

   Avec Azure Monitor, vous pouvez collecter des données provenant de différentes [sources à surveiller et analyser](../azure-monitor/agents/data-sources.md) et différents types de [données à analyser ou visualiser et utilisées pour gérer des alertes](../azure-monitor/data-platform.md). Vous pouvez également créer des règles d’alerte afin d’identifier des problèmes dans votre environnement, tels que l’utilisation intensive de ressources, des patchs manquants, un espace disque faible et des pulsations de vos machines virtuelles. Vous pouvez également définir une réponse automatisée aux événements détectés en envoyant une alerte aux outils ITSM (gestion des services informatiques). Une notification de détection d’alerte peut également être envoyée par e-mail.

- **Microsoft Defender pour le cloud** renforce la sécurité des centres de données et offre une protection avancée contre les menaces dans les charges de travail hybrides dans le cloud ou en local. Il évalue la vulnérabilité des machines virtuelles d’Azure VMware Solution, déclenche des alertes en fonction des besoins et les transmet à Azure Monitor à des fins de résolution. Il évalue par exemple les correctifs de système d’exploitation manquants, les configurations de sécurité incorrectes et la [protection des points de terminaison](../security-center/security-center-services.md). Vous pouvez également définir des stratégies de sécurité dans [Microsoft Defender pour le cloud](azure-security-integration.md).

- **Azure Update Management** gère les mises à jour du système d’exploitation de vos ordinateurs Windows et Linux dans un environnement hybride dans Azure Automation. Il surveille la conformité des mises à jour correctives, et transfère les alertes d’écart de mise à jour corrective à Azure Monitor à des fins de correction. La solution Azure Update Management doit se connecter à votre espace de travail Log Analytics pour utiliser les données stockées afin d’évaluer l’état des mises à jour sur vos machines virtuelles.

- L’**espace de travail Log Analytics** stocke les données du journal. Chaque espace de travail a son propre dépôt de données et sa propre configuration pour stocker des données. Vous pouvez surveiller les machines virtuelles Azure VMware Solution via l’agent Log Analytics. Les machines connectées à des espaces de travail Log Analytics utilisent l’[agent Log Analytics](../azure-monitor/agents/log-analytics-agent.md) pour collecter des données sur les modifications apportées aux logiciels installés, aux services Microsoft, au registre et aux fichiers Windows, ainsi qu’aux démons Linux sur les serveurs analysés. Lorsque des données sont disponibles, l’agent les envoie aux journaux Azure Monitor pour traitement. Les journaux Azure Monitor appliquent une logique aux données reçues, les enregistrent et les rendent disponibles pour analyse. Utilisez la [prise en charge des extensions de machine virtuelle](../azure-arc/servers/manage-vm-extensions.md) des serveurs avec Azure Arc pour déployer les agents Log Analytics les machines virtuelles.

## <a name="benefits"></a>Avantages

- Vous pouvez utiliser les services natifs d’Azure pour gérer vos machines virtuelles dans un environnement hybride (Azure, Azure VMware Solution et local).
- Surveillance et visibilité intégrées de vos machines virtuelles Azure, de Azure VMware Solution et locales.
- Avec Azure Update Management dans Azure Automation, vous pouvez gérer les mises à jour de système d’exploitation pour vos machines Windows et Linux.
- Microsoft Defender pour le cloud offre une protection avancée contre les menaces, notamment :
  - Monitoring d’intégrité de fichier
  - Alertes de sécurité sans fichier
  - Évaluation de correctif du système d’exploitation
  - Évaluation des erreurs de configuration de la sécurité
  - Évaluation de la protection des points de terminaison
- Déployez facilement l’agent Log Analytics à l’aide des serveurs Azure Arc prenant en charge l’extension de machine virtuelle pour les machines virtuelles nouvelles et existantes.
- Votre espace de travail Log Analytics dans Azure Monitor active la collecte des journaux et la collecte des compteurs de performances à l’aide de l’agent ou d’extensions Log Analytics. Collectez les données et les journaux sur un point unique, et présentez-les à différents services natifs Azure.
- Les avantages supplémentaires d’Azure Monitor sont les suivants :
  - Surveillance transparente
  - Meilleure visibilité de l’infrastructure
  - Notifications instantanées
  - Résolution automatique
  - Rentabilité

## <a name="topology"></a>Topologie

Le diagramme illustre l’architecture de surveillance intégrée pour les machines virtuelles du service Azure VMware Solution.

:::image type="content" source="media/concepts/integrated-azure-monitoring-architecture.png" alt-text="Diagramme montrant l’architecture de supervision Azure intégrée." border="false":::

L’agent Log Analytics active la collecte de données de journal à partir d’Azure, d’Azure VMware Solution et de machines virtuelles locales. Les données de journal sont envoyées aux journaux Azure Monitor et stockées dans un espace de travail Log Analytics. Vous pouvez déployer l’agent Log Analytics à l’aide de serveurs compatibles avec Arc [prenant en charge les extensions de machine virtuelle](../azure-arc/servers/manage-vm-extensions.md) pour les machines virtuelles nouvelles et existantes.

Une fois que l’espace de travail Log Analytics collecte les journaux, vous pouvez configurer l’espace de travail Log Analytics avec Defender pour le cloud pour évaluer l’état de vulnérabilité des machines virtuelles d’Azure VMware Solution et déclencher une alerte pour toute vulnérabilité critique.  Il évalue par exemple les correctifs de système d’exploitation manquants, les configurations de sécurité incorrectes et la [protection des points de terminaison](../security-center/security-center-services.md).

Vous pouvez configurer l’espace de travail Log Analytics avec Microsoft Sentinel pour la détection des alertes, la visibilité des menaces, la chasse et la réponse aux menaces. Dans le diagramme précédent, Defender pour le cloud est connecté à Microsoft Sentinel à l’aide du connecteur Defender pour le cloud. Defender pour le cloud transmet la vulnérabilité de l’environnement à Microsoft Sentinel pour créer un incident et la mappe à d’autres menaces. Vous pouvez également créer la requête de règles planifiées pour détecter les activités indésirables et les convertir en incidents.

## <a name="before-you-start"></a>Avant de commencer

Si vous débutez avec Azure ou si vous découvrez les services mentionnés précédemment, consultez les articles suivants :

- [Vue d’ensemble de l’authentification des comptes Automation](../automation/automation-security-overview.md)
- [Conception de votre déploiement de journaux Azure Monitor](../azure-monitor/logs/design-logs-deployment.md) et [Azure Monitor](../azure-monitor/overview.md)
- [Planification](../security-center/security-center-planning-and-operations-guide.md) et [Plateformes prises en charge](../security-center/security-center-os-coverage.md) pour Microsoft Defender pour le cloud
- [Vue d’ensemble de l’activation d’Azure Monitor pour machines virtuelles](../azure-monitor/vm/vminsights-enable-overview.md)
- [Que sont les serveurs Azure Arc ?](../azure-arc/servers/overview.md) et [Que sont les Kubernetes Azure Arc ?](../azure-arc/kubernetes/overview.md)
- [Vue d’ensemble de Update Management](../automation/update-management/overview.md)



## <a name="enable-azure-update-management"></a>Activer Azure Update Management

Le service [Azure Update Management](../automation/update-management/overview.md) dans Azure Automation gère les mises à jour du système d’exploitation de vos ordinateurs Windows et Linux dans un environnement hybride. Il surveille la conformité des mises à jour correctives, et transfère les alertes d’écart de mise à jour corrective à Azure Monitor à des fins de correction. La solution Azure Update Management doit se connecter à votre espace de travail Log Analytics pour utiliser les données stockées afin d’évaluer l’état des mises à jour sur vos machines virtuelles.

1. Avant de pouvoir ajouter l’espace de travail Log Analytics à la solution Azure Update Management, vous devez commencer par [Créer un compte Azure Automation](../automation/automation-create-standalone-account.md). 

   >[!TIP]
   >Vous pouvez [utiliser un modèle Azure Resource Manager (ARM) afin de créer un compte Automation](../automation/quickstart-create-automation-account-template.md). L’utilisation d’un modèle ARM comprend moins d’étapes que les autres méthodes de déploiement.

1. [Créer un espace de travail Log Analytics dans le portail Azure](../azure-monitor/logs/quick-create-workspace.md). Si vous préférez, vous pouvez également créer un espace de travail via [interface de ligne de commande](../azure-monitor/logs/resource-manager-workspace.md), [PowerShell](../azure-monitor/logs/powershell-workspace-configuration.md) ou [modèle Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md).

1. [Activez Update Management à partir d’un compte Automation](../automation/update-management/enable-from-automation-account.md). Dans le processus, vous allez associer votre espace de travail Log Analytics à votre compte Automation. 
 
1. Une fois Update Management activé, vous pouvez [déployer des mises à jour sur des machines virtuelles et examiner les résultats](../automation/update-management/deploy-updates.md). 

## <a name="enable-microsoft-defender-for-cloud"></a>Activer Microsoft Defender pour le cloud

Évaluez la vulnérabilité des machines virtuelles du service Azure VMware Solution, et déclenchez des alertes en fonction des besoins. Ces alertes de sécurité peuvent être transférées à Azure Monitor à des fins de résolution. Pour plus d’informations, consultez [Fonctionnalités prises en charge pour les machines virtuelles](../security-center/security-center-services.md).

Defender pour le cloud offre de nombreuses fonctionnalités, notamment :

- Monitoring d’intégrité de fichier
- Détection des attaques sans fichier
- Évaluation de correctif du système d’exploitation
- Évaluation des erreurs de configuration de la sécurité
- Évaluation de la protection des points de terminaison

>[!NOTE]
>Microsoft Defender pour le cloud est un outil préconfiguré qui ne nécessite aucun déploiement, mais vous devez l’activer dans le portail Azure.

1. [Ajoutez des machines virtuelles Azure VMware Solution à Defender pour le cloud](azure-security-integration.md#add-azure-vmware-solution-vms-to-defender-for-cloud).

2. [Activez Microsoft Defender pour le cloud](../security-center/enable-azure-defender.md). Defender pour le cloud évalue les machines virtuelles pour détecter les éventuels problèmes de sécurité. Il affiche également des [recommandations de sécurité](../security-center/security-center-recommendations.md) sous l’onglet Vue d’ensemble.

3. [Définissez des stratégies de sécurité](../security-center/tutorial-security-policy.md) dans Defender pour le cloud.

Pour plus d’informations, consultez [Intégrer Microsoft Defender pour le cloud à Azure VMware Solution](azure-security-integration.md).

## <a name="onboard-vms-to-azure-arc-enabled-servers"></a>Intégrer des machines virtuelles à des serveurs Azure Arc

Étend la gestion d’Azure à toute infrastructure, notamment Azure VMware Solution, les infrastructures locales ou autres plateformes cloud.  Pour plus d’informations sur l’activation de serveurs Azure Arc pour plusieurs machines virtuelles Windows ou Linux, consultez [Connecter des machines hybrides à Azure à grande échelle](../azure-arc/servers/onboard-service-principal.md).



## <a name="onboard-hybrid-kubernetes-clusters-with-azure-arc-enabled-kubernetes"></a>Intégrer des clusters Kubernetes hybrides à Kubernetes avec Azure Arc

Attachez un cluster Kubernetes hébergé dans votre environnement Azure VMware Solution à l’aide de Kubernetes avec Azure Arc. Pour plus d’informations, consultez [Créer un principal de service d’intégration compatible avec Azure Arc](../azure-arc/kubernetes/create-onboarding-service-principal.md).


## <a name="deploy-the-log-analytics-agent"></a>Déployer l’agent Log Analytics

Surveillez les machines virtuelles Azure VMware Solution via l’agent Log Analytics. Les machines connectées à des espaces de travail Log Analytics utilisent l’[agent Log Analytics](../azure-monitor/agents/log-analytics-agent.md) pour collecter des données sur les modifications apportées aux logiciels installés, aux services Microsoft, au registre et aux fichiers Windows, ainsi qu’aux démons Linux sur les serveurs analysés. Lorsque des données sont disponibles, l’agent les envoie aux journaux Azure Monitor pour traitement. Les journaux Azure Monitor appliquent une logique aux données reçues, les enregistrent et les rendent disponibles pour analyse.

Déployez l’agent Log Analytics en utilisant la [prise en charge de l’extension VM des serveurs avec Azure Arc](../azure-arc/servers/manage-vm-extensions.md).




## <a name="enable-azure-monitor"></a>Activer Azure Monitor

Vous pouvez collecter des données provenant de différentes [sources à surveiller et analyser](../azure-monitor/agents/data-sources.md) et différents types de [données à analyser ou visualiser et utilisées pour gérer des alertes](../azure-monitor/data-platform.md). Vous pouvez également créer des règles d’alerte afin d’identifier des problèmes dans votre environnement, tels que l’utilisation intensive de ressources, des patchs manquants, un espace disque faible et des pulsations de vos machines virtuelles. Vous pouvez également définir une réponse automatisée aux événements détectés en envoyant une alerte aux outils ITSM (gestion des services informatiques). Une notification de détection d’alerte peut également être envoyée par e-mail.

Surveillez les performances du système d’exploitation invité, et découvrez et mappez des dépendances d’application pour des machines virtuelles Azure VMware Solution ou locales. Votre espace de travail Log Analytics dans Azure Monitor active la collecte des journaux et la collecte des compteurs de performances à l’aide de l’agent ou d’extensions Log Analytics. 


1. [Concevoir votre déploiement de journaux d’activité Azure Monitor](../azure-monitor/logs/design-logs-deployment.md)

1. [Vue d’ensemble de l’activation d’Azure Monitor pour machines virtuelles](../azure-monitor/vm/vminsights-enable-overview.md)

1. [Configurer l’espace de travail Log Analytics pour Azure Monitor pour machines virtuelles](../azure-monitor/vm/vminsights-configure-workspace.md).

1. Créez des règles d’alerte afin d’identifier les problèmes dans votre environnement :

   - [Créer, afficher et gérer des alertes de mesure à l’aide d’Azure Monitor](../azure-monitor/alerts/alerts-metric.md).

   - [Créer, afficher et gérer des alertes de journal à l’aide d’Azure Monitor](../azure-monitor/alerts/alerts-log.md).

   - [Règles d’action](../azure-monitor/alerts/alerts-action-rules.md) pour définir des actions et notifications automatisées.

   - [Connecter Azure aux outils de gestion des services informatiques (ITSM) à l’aide du connecteur de gestion des services informatiques](../azure-monitor/alerts/itsmc-overview.md).


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez abordé les concepts de réseau et d’interconnexion d’Azure VMware Solution, vous souhaitez peut-être en savoir plus sur l’[intégration de Microsoft Defender pour le cloud à Azure VMware Solution](azure-security-integration.md).
