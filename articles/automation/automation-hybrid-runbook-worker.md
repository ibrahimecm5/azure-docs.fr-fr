---
title: Vue d’ensemble du Runbook Worker hybride d’Azure Automation
description: Cet article fournit une vue d’ensemble du Runbook Worker hybride qui vous permet d’exécuter des runbooks sur des machines de votre centre de données local ou de votre fournisseur de cloud.
services: automation
ms.subservice: process-automation
ms.date: 11/11/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d7d9d5bb3c347a726efae4a2aa30938e6918e0ac
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287314"
---
# <a name="automation-hybrid-runbook-worker-overview"></a>Vue d’ensemble du Runbook Worker hybride d’Automation

Dans Azure Automation, les Runbooks peuvent ne pas avoir accès aux ressources d’autres clouds ou de votre environnement local, car ils s’exécutent dans la plateforme cloud Azure. Vous pouvez utiliser la fonctionnalité Runbook Worker hybride d’Azure Automation pour exécuter des runbooks directement sur la machine qui héberge le rôle et sur les ressources de l’environnement afin de gérer ces ressources locales. Les runbooks sont stockés et gérés dans Azure Automation et remis ensuite à une ou plusieurs machines assignées.

Azure Automation assure l’intégration native du rôle Runbook Worker hybride par le biais de l’infrastructure d’extension de machine virtuelle Azure. L’agent de machine virtuelle Azure est responsable de la gestion de l’extension sur les machines virtuelles Azure sous Windows et Linux et sur les machines non Azure par le biais de l’agent Connected Machine des serveurs Arc. À présent, deux plateformes d’installation de Runbooks Workers hybrides sont prises en charge par Azure Automation.

| Plateforme | Description |
|---|---|
|Basé sur un agent (v1)  |Installée après l’achèvement de l’[agent Log Analytics](../azure-monitor/agents/log-analytics-agent.md) qui rend compte à un [espace de travail Log Analytics](../azure-monitor/logs/design-logs-deployment.md) Azure Monitor.|
|Basé sur une extension (v2)  |Installée à l’aide de l’[extension de machine virtuelle de Runbook Worker hybride](./extension-based-hybrid-runbook-worker-install.md), sans aucune dépendance vis-à-vis de l’agent Log Analytics qui rend compte à un espace de travail Log Analytics Azure Monitor. Il s’agit de la plateforme recommandée.|

:::image type="content" source="./media/automation-hybrid-runbook-worker/hybrid-worker-group-platform.png" alt-text="Groupe de Workers hybrides montrant le champ de la plateforme":::

Voici une liste des avantages disponibles avec le rôle Runbook Worker hybride basé sur une extension :

| Avantage | Description |
|---|---|
|Intégration transparente| Supprime la dépendance sur une solution Log Analytics pour l’intégration des Runbooks Worker hybrides, qui est un processus en plusieurs étapes, long et sujet aux erreurs. |
|Expérience d’intégration unifiée| L’installation est gérée à l’aide des mêmes méthodes prises en charge pour les machines Azure et non Azure. |
|Facilité de gestion| Intégration native avec une identité ARM pour le Runbook Worker hybride, et offre la flexibilité nécessaire pour une gouvernance à grande échelle en s’appuyant sur des stratégies et des modèles. |
|Authentification basée sur Azure AD| Utilise les identités affectées par le système des machines virtuelles fournies par Azure AD. Cela permet de centraliser le contrôle et la gestion des identités et des informations d’identification des ressources.|

Pour les opérations sur les Runbooks Workers hybrides après l’installation, le processus d’exécution des runbooks sur des Runbooks Workers hybrides est le même. L’objectif de l’approche basée sur une extension est de simplifier l’installation et la gestion du rôle Runbook Worker hybride et de supprimer la complexité liée à l’utilisation de la version basée sur un agent. La nouvelle installation basée sur une extension n’a pas d’impact sur l’installation ou la gestion d’un rôle Runbook Worker hybride basé sur un agent. Les deux types de runbook Worker hybrides peuvent coexister sur la même machine.

Le Runbook Worker hybride basé sur une extension prend uniquement en charge le type Runbook Worker hybride utilisateur et n’inclut pas le Runbook Worker hybride système requis pour la fonctionnalité Update Management.

>[!NOTE]
> Pour le moment, PowerShell ne prend pas en charge l’installation du Runbook Worker hybride basé sur une extension.

## <a name="runbook-worker-types"></a>Types de Runbook Worker

Il existe deux types de Runbook Worker : système et utilisateur. Le tableau suivant décrit les différences entre les deux.

|Type | Description |
|-----|-------------|
|**Système** |Prend en charge un ensemble de runbooks masqués utilisés par la fonctionnalité Update Management et conçus pour installer des mises à jour spécifiées par l’utilisateur sur des ordinateurs Windows et Linux.<br> Ce type de Runbook Worker hybride n’est pas membre d’un groupe de Runbooks Workers hybrides et, par conséquent, n’exécute pas de runbooks qui ciblent un groupe de Runbooks Workers. |
|**Utilisateur** |Prend en charge des runbooks définis par l’utilisateur, destinés à s’exécuter directement sur l’ordinateur Windows et Linux, et qui sont membres d’un ou plusieurs groupes Runbook Worker. |

Les Runbooks Worker hybrides basés sur un agent (v1) dépendent de l’[agent Log Analytics](../azure-monitor/agents/log-analytics-agent.md) qui rapporte à un [espace de travail Log Analytics](../azure-monitor/logs/design-logs-deployment.md) Azure Monitor. L’espace de travail ne collecte pas seulement des données de monitoring sur la machine, il télécharge aussi les composants nécessaires pour installer le Runbook Worker hybride basé sur agent.

Quand la solution [Update Management](./update-management/overview.md) d’Azure Automation est activée, toute machine connectée à votre espace de travail Log Analytics est automatiquement configurée en tant que Runbook Worker hybride système. Pour la configurer en tant que Runbook Worker hybride Windows utilisateur, consultez [Déployer un Runbook Worker hybride Windows basé sur un agent dans Automation](automation-windows-hrw-install.md) ou [Déployer un Runbook Worker hybride Linux basé sur un agent dans Automation](./automation-linux-hrw-install.md).

## <a name="runbook-worker-limits"></a>Limites de Runbook Worker

Le tableau suivant indique le nombre maximal de Runbook Worker hybrides système et utilisateur dans un compte Automation. Si vous avez plus de 4 000 machines à gérer, nous vous recommandons de créer un autre compte Automation.

|Type de Worker| Nombre maximum pris en charge par un compte Automation.|
|---|---|
|Système|4000|
|Utilisateur |4000|

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?

Chaque Runbook Worker hybride utilisateur est membre d'un groupe Runbook Worker hybride que vous spécifiez lorsque vous installez le worker. Un groupe peut inclure un seul worker, mais vous pouvez inclure plusieurs workers dans un groupe pour une haute disponibilité. Chaque machine peut héberger un Runbook Worker hybride rendant compte à un compte Automation. Vous ne pouvez pas inscrire le Worker hybride dans plusieurs comptes Automation. Un worker hybride ne peut écouter des travaux qu’à partir d’un seul compte Automation. 

:::image type="content" source="./media/automation-hybrid-runbook-worker/user-hybrid-runbook-worker.png" alt-text="Diagramme technique Runbook Worker hybride d’utilisateur":::

Pour les ordinateurs qui hébergent le Runbook Worker hybride système géré par Update Management, ils peuvent être ajoutés à un groupe de Runbook Workers hybrides. Toutefois, vous devez utiliser le même compte Automation pour Update Management et l’appartenance au groupe de Runbook Workers hybrides.

:::image type="content" source="./media/automation-hybrid-runbook-worker/system-hybrid-runbook-worker.png" alt-text="Diagramme technique Runbook Worker hybride système":::

Lorsque vous démarrez un runbook sur un Runbook Worker hybride utilisateur, vous spécifiez le groupe sur lequel il s’exécute. Chaque worker du groupe interroge Azure Automation pour voir si des travaux sont disponibles. Si un travail est disponible, le premier worker qui le reçoit s’en occupe. Le temps de traitement de la file d’attente des travaux varie selon la charge et le profil matériel du Worker hybride. Vous ne pouvez pas spécifier un worker en particulier. Le worker hybride travaille selon un mécanisme d’interrogation (toutes les 30 secondes) et suit l’ordre du premier arrivé, premier servi. Selon le moment auquel un travail a été envoyé (Push), le worker hybride qui effectue un test ping du service Automation récupère le travail. Un seul worker hybride peut généralement récupérer quatre travaux par test Ping (c’est-à-dire, toutes les 30 secondes). Si votre taux d’envoi de travaux est supérieur à 4 toutes les 30 secondes, il est possible qu’un autre Worker hybride du groupe de Runbooks Workers hybrides ait récupéré le travail.

Un Runbook Worker hybride est exempt de bon nombre des [limites](automation-runbook-execution.md#runbook-execution-environment) de ressources de [bac à sable (sandbox)](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) Azure en matière d’espace disque, de mémoire ou de sockets réseau. Les limites d’un worker hybride sont uniquement liées aux ressources de ce dernier, et elles ne sont pas limitées par la limite de temps en matière de [répartition de charge équilibrée](automation-runbook-execution.md#fair-share) que présentent les bacs à sable Azure.

Pour contrôler la distribution de runbooks sur les Runbook Workers hybrides, ainsi que le moment ou la façon dont les travaux sont déclenchés, vous pouvez inscrire le worker hybride dans différents groupes Runbook Worker hybrides dans votre compte Automation. Ciblez les travaux sur le ou les groupes spécifiques afin de prendre en charge votre organisation d’exécution.

## <a name="hybrid-runbook-worker-installation"></a>Installation de Runbook Worker hybride

Le processus d’installation d’un Runbook Worker hybride utilisateur diffère selon le système d’exploitation. Le tableau ci-dessous définit les types de déploiement.

|Système d’exploitation  |Types de déploiement  |
|---------|---------|
|Windows | [Automatisé](automation-windows-hrw-install.md#automated-deployment)<br>[Manuel](automation-windows-hrw-install.md#manual-deployment). |
|Linux   | [Manuel](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker) |
|Vous pouvez soit utiliser  | Pour les Runbooks Worker hybrides utilisateur, consultez [Déployer un Runbook Worker hybride utilisateur Windows ou Linux basé sur une extension dans Automation](./extension-based-hybrid-runbook-worker-install.md). Il s’agit de la méthode recommandée. |

## <a name="network-planning"></a><a name="network-planning"></a>Planification réseau

Pour plus d’informations sur les ports, les URL et la mise en réseau qui sont requis pour le Runbook Worker hybride, consultez [Configuration réseau d’Azure Automation](automation-network-configuration.md#network-planning-for-hybrid-runbook-worker).

### <a name="proxy-server-use"></a>Utilisation du serveur proxy

Si vous utilisez un serveur proxy pour la communication entre Azure Automation et les machines exécutant l’agent Log Analytics, veillez à ce que les ressources appropriées soient accessibles. Le délai d’expiration pour les demandes du Runbook Worker hybride et des services Automation est de 30 secondes. Après trois tentatives, une demande échoue.

### <a name="firewall-use"></a>Utilisation du pare-feu

Si vous utilisez un pare-feu pour restreindre l’accès à Internet, vous devez configurer le pare-feu pour autoriser l’accès. Si vous utilisez la passerelle Log Analytics en tant que proxy, vérifiez qu’elle est configurée pour les Runbooks Workers hybrides. Consultez [Configurer de la passerelle Log Analytics pour les Runbooks Workers hybrides Azure Automation](../azure-monitor/agents/gateway.md).

### <a name="service-tags"></a>Étiquettes de service

Azure Automation prend en charge les étiquettes de service de réseau virtuel Azure, à commencer par l’étiquette de service [GuestAndHybridManagement](../virtual-network/service-tags-overview.md). Vous pouvez utiliser des étiquettes de service pour définir des contrôles d’accès réseau sur des [groupes de sécurité réseau](../virtual-network/network-security-groups-overview.md#security-rules) ou sur le [pare-feu Azure](../firewall/service-tags.md). Les étiquettes de service peuvent être utilisées à la place d’adresses IP spécifiques pendant la création de règles de sécurité. En spécifiant le nom d’étiquette de service **GuestAndHybridManagement** dans le champ source ou de destination approprié d’une règle, vous pouvez autoriser ou refuser le trafic pour le service Automation. Cette étiquette de service ne permet pas d’autoriser un contrôle plus précis en limitant les plages d’adresses IP à une région spécifique.

L’étiquette de service du service Azure Automation fournit uniquement les adresses IP utilisées pour les scénarios suivants :

* Déclencher des webhooks à partir de votre réseau virtuel
* Autoriser les Runbooks Worker hybrides ou les agents State Configuration de votre réseau virtuel à communiquer avec le service Automation

>[!NOTE]
>L’étiquette de service **GuestAndHybridManagement** ne prend actuellement pas en charge l’exécution de tâches de runbook dans un bac à sable (sandbox) Azure, uniquement directement sur un Runbook Worker hybride.

## <a name="support-for-impact-level-5-il5"></a>Prise en charge d’Impact Level 5 (IL5)

Le Runbook Worker hybride d’Azure Automation peut être utilisé dans Azure Government pour prendre en charge les charges de travail Impact Level 5 dans l’une des deux configurations suivantes :

* [Machine virtuelle isolée](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines). Une fois déployées, elles consomment la totalité de l’hôte physique pour cette machine, fournissant ainsi le niveau d’isolation nécessaire pour prendre en charge les charges de travail IL5.

* Les [hôtes dédiés Azure](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-host), qui fournissent des serveurs physiques capables d’héberger une ou plusieurs machines virtuelles et dédiés à un abonnement Azure.

>[!NOTE]
>L’isolation de calcul via le rôle Runbook Worker hybride est disponible pour les clouds commerciaux Azure et le cloud US Government.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Adresses Update Management pour Runbook Worker hybride

En plus des adresses et des ports standard requis pour le Runbook Worker hybride, Update Management a d’autres exigences en matière de configuration du réseau décrites dans la section relative à la [planification du réseau](./update-management/plan-deployment.md#ports).

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Azure Automation State Configuration sur un Runbook Worker hybride

Vous pouvez exécuter [Azure Automation State Configuration](automation-dsc-overview.md) sur un Runbook Worker hybride. Pour gérer la configuration des serveurs qui prennent en charge le Runbook Worker hybride, vous devez les ajouter en tant que nœuds DSC. Consultez [Activer la gestion des machines par Azure Automation State Configuration](automation-dsc-onboarding.md).

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbooks sur un Runbook Worker hybride

Vous pouvez faire en sorte que les runbooks gèrent les ressources sur la machine locale ou qu’ils s’exécutent sur des ressources de l’environnement local dans lequel un Runbook Worker hybride utilisateur est déployé. Dans ce cas, vous pouvez choisir d’exécuter vos runbooks sur le Worker hybride plutôt que dans un compte Automation. Les runbooks exécutés sur un runbook Worker hybride sont identiques en structure à ceux que vous exécutez dans le compte Automation. Consultez [Exécuter des runbooks sur un runbook Worker hybride](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Travaux Runbook Worker hybride

Les travaux du Runbook Worker hybride s’exécutent sous le compte **Système** local sur Windows, ou le compte [nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) sur Linux. Azure Automation gère les travaux exécutés sur des Runbooks Workers hybrides différemment des travaux exécutés dans des bacs à sable Azure. Consultez [Environnement d’exécution de runbook](automation-runbook-execution.md#runbook-execution-environment).

Si la machine hôte du runbook Worker hybride redémarre, tous les travaux du runbook en cours d’exécution redémarrent au début ou au dernier point de contrôle pour les runbooks PowerShell Workflow. Quand un travail de runbook a redémarré plus de trois fois, il est suspendu.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Autorisations des runbooks pour un runbook Worker hybride

Étant donné qu’ils accèdent à des ressources non-Azure, les runbooks s’exécutant sur un Runbook Worker hybride utilisateur ne peuvent pas utiliser le mécanisme d’authentification généralement utilisé par les runbooks pour s’authentifier auprès des ressources Azure. Un runbook peut fournir sa propre authentification auprès des ressources locales ou en configurer une à l’aide d’[identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Vous pouvez également spécifier un compte d’identification pour fournir un contexte utilisateur pour l’ensemble des runbooks.

## <a name="view-system-hybrid-runbook-workers"></a>Afficher les Runbooks Workers hybrides système

Une fois la fonctionnalité Update Management activée sur les machines Windows ou Linux, vous pouvez inventorier la liste du groupe Runbook Workers hybrides système dans le portail Azure. Vous pouvez afficher un maximum de 2 000 Workers sur le portail en sélectionnant l'onglet **Groupe de Workers hybrides du système** de l'option **Groupe de Workers hybrides** dans le volet de gauche du compte Automation sélectionné.

:::image type="content" source="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png" alt-text="Page des groupes de Workers hybrides du système sur le compte Automation" border="false" lightbox="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png":::

Si vous disposez de plus de 2 000 Workers hybrides, pour obtenir la liste complète de ceux-ci, vous pouvez exécuter le script PowerShell suivant :

```powershell
"Get-AzSubscription -SubscriptionName "<subscriptionName>" | Set-AzContext
$workersList = (Get-AzAutomationHybridWorkerGroup -ResourceGroupName "<resourceGroupName>" -AutomationAccountName "<automationAccountName>").Runbookworker
$workersList | export-csv -Path "<Path>\output.csv" -NoClobber -NoTypeInformation"
```

## <a name="next-steps"></a>Étapes suivantes

* Pour apprendre à configurer vos Runbooks afin d’automatiser les processus dans votre centre de données local ou un autre environnement cloud, consultez la rubrique [Exécuter des Runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md).

* Pour savoir comment résoudre les problèmes de vos Runbook Workers hybrides, consultez [Résoudre les problèmes liés à la fonctionnalité Runbook Worker hybride](troubleshoot/hybrid-runbook-worker.md#general).
