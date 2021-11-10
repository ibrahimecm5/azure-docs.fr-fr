---
title: Vue d’ensemble de l’agent Azure Monitor
description: Vue d’ensemble de l’agent Azure Monitor, qui collecte des données de supervision à partir du système d’exploitation invité des machines virtuelles.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/21/2021
ms.custom: references_regions
ms.openlocfilehash: 22569277eefafc518f407f06e34a69c061509b96
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458966"
---
# <a name="azure-monitor-agent-overview"></a>Vue d’ensemble de l’agent Azure Monitor
L’agent Azure Monitor (AMA) collecte des données de supervision auprès du système d’exploitation invité des machines virtuelles Azure et les délivre à Azure Monitor. Cet article fournit une vue d’ensemble de l’agent Azure Monitor et inclut des informations sur la façon de l’installer et de configurer la collecte des données.

## <a name="relationship-to-other-agents"></a>Relation avec les autres agents
L’agent Azure Monitor remplace les agents hérités suivants qui sont actuellement utilisés par Azure Monitor pour collecter des données d’invités auprès des machines virtuelles ([voir les lacunes connues](../faq.yml)) :

- [Agent Log Analytics](./log-analytics-agent.md) : Envoie des données à un espace de travail Log Analytics et prend en charge les insights de machine virtuelle et les solutions de supervision.
- [Extension Diagnostics](./diagnostics-extension-overview.md) : Envoie des données aux métriques Azure Monitor (Windows uniquement), à Azure Event Hubs et au Stockage Azure.
- [Agent Telegraf](../essentials/collect-custom-metrics-linux-telegraf.md) : Envoie des données aux métriques Azure Monitor (Linux uniquement).

En plus de consolider cette fonctionnalité en un seul agent, l’agent Azure Monitor offre les avantages suivants par rapport aux agents existants :

- **Étendue de supervision :** Configurez de manière centralisée une collection de différents jeux de données à partir de différents ensembles de machines virtuelles.
- **Multihébergement Linux :** Envoyez des données à partir de machines virtuelles Linux vers plusieurs espaces de travail.
- **Filtrage d’événements Windows :** Utilisez des requêtes XPATH pour filtrer les événements Windows qui sont collectés.
- **Gestion améliorée des extensions :** L’agent Azure Monitor utilise une nouvelle méthode de gestion de l’extensibilité qui est plus transparente et contrôlable que les packs d’administration et les plug-ins Linux dans les agents Log Analytics actuels.

### <a name="current-limitations"></a>Limites actuelles
Comparé aux agents existants, ce nouvel agent n’a pas encore une parité complète.
- **Comparaison avec les agents Log Analytics (MMA/OMS) :**
    - Toutes les solutions Log Analytics ne sont pas prises en charge actuellement. Voyez [ce qui est pris en charge](#supported-services-and-features).
    - Pas de prise en charge des liaisons privées Azure.
    - Pas de prise en charge de la collecte des journaux basés sur des fichiers ou des journaux IIS.
- **Comparaison avec les extensions Azure Diagnostics (WAD/LAD) :**
    - Pas de prise en charge des hubs d’événements et des comptes de stockage en tant que destinations.
    - Pas de prise en charge de la collecte des journaux basés sur des fichiers, des journaux IIS, des événements ETW, des événements .NET et des vidages sur incident.

### <a name="changes-in-data-collection"></a>Modifications de la collecte de données
Les méthodes permettant de définir la collecte de données pour les agents existants diffèrent les unes des autres. Chaque méthode présente des défis qui sont résolus avec l’agent Azure Monitor.

- L’agent Log Analytics obtient sa configuration à partir d’un espace de travail Log Analytics. Il est facile de configurer de façon centralisée, mais il est difficile d’établir des définitions indépendantes pour différentes machines virtuelles. Il peut uniquement envoyer des données à un espace de travail Log Analytics.
- L’extension de diagnostic a une configuration pour chaque machine virtuelle. Définir des définitions indépendantes pour différentes machines virtuelles est facile, mais il est difficile de les gérer de façon centralisée. Il peut uniquement envoyer des données aux métriques Azure Monitor, à Azure Event Hubs ou au Stockage Azure. Pour les agents Linux, l’agent Telegraf open source est nécessaire pour envoyer des données aux métriques Azure Monitor.

L’agent Azure Monitor utilise des [règles de collecte de données (DCR)](data-collection-rule-overview.md) pour configurer les données à collecter à partir de chaque agent. Les règles de collecte de données permettent de gérer les paramètres de regroupement à grande échelle tout en autorisant des configurations uniques et étendues pour les sous-ensembles d’ordinateurs. Elles ne dépendent pas de l’espace de travail ni de la machine virtuelle, ce qui leur permet d’être définies puis réutilisées entre les ordinateurs et les environnements. Consultez [Configurer la collecte de données pour l’agent Azure Monitor](data-collection-rule-azure-monitor-agent.md).

## <a name="should-i-switch-to-the-azure-monitor-agent"></a>Dois-je basculer sur l’agent Azure Monitor ?
L’agent Azure Monitor remplace les [agents hérités pour Azure Monitor](agents-overview.md). Pour commencer la transition de vos machines virtuelles des agents actuels vers le nouvel agent, prenez en compte les facteurs suivants :

- **Configuration requise pour l’environnement :** L’agent Azure Monitor prend actuellement en charge [ces systèmes d’exploitation](./agents-overview.md#supported-operating-systems). La prise en charge de versions futures des systèmes d’exploitation, la prise en charge de l’environnement et les exigences réseau seront probablement fournies dans ce nouvel agent. 
 
  Évaluez si votre environnement est pris en charge par l’agent Azure Monitor. Si ce n’est pas le cas, il peut être nécessaire de conserver l’agent actuel. Si l’agent Azure Monitor prend en charge votre environnement actuel, envisagez une transition vers celui-ci.
- **Exigences actuelles et nouvelles en matière de fonctionnalités :** L’agent Azure Monitor introduit plusieurs nouvelles fonctionnalités, telles que le filtrage, l’étendue et l’hébergement multiple. Toutefois, il n’est pas encore sur un pied d’égalité avec les agents actuels pour d’autres fonctionnalités, telles que la collecte de journaux personnalisés et l’intégration à toutes les solutions. ([Consultez les solutions en préversion](../faq.yml).) 
 
  La plupart des nouvelles capacités d’Azure Monitor ne seront disponibles qu’avec l’agent Azure Monitor. Au fil du temps, des fonctionnalités supplémentaires seront disponibles uniquement dans le nouvel agent. Déterminez si l’agent Azure Monitor dispose des fonctionnalités dont vous avez besoin et si vous pouvez vous passer temporairement de certaines fonctionnalités pour obtenir d’autres fonctionnalités importantes dans le nouvel agent. 
  
  Si l’agent Azure Monitor dispose de toutes les fonctionnalités de base dont vous avez besoin, envisagez de passer à celui-ci. Si des fonctionnalités critiques vous sont nécessaires, continuez avec l’agent actuel jusqu’à ce que l’agent Azure Monitor les implémente.
- **Tolérance au remaniement :** Si vous configurez un nouvel environnement avec des ressources comme des scripts de déploiement et des modèles d’intégration, évaluez le travail que cela implique. Si cette configuration implique une quantité importante de travail, envisagez de configurer votre nouvel environnement avec le nouvel agent, car il est maintenant en disponibilité générale. 
 
  L’agent Log Analytics d’Azure Monitor sera mis hors service le 31 août 2024. Les agents actuels seront pris en charge pendant plusieurs années après le début de la dépréciation.

## <a name="supported-resource-types"></a>Types de ressources pris en charge
Les machines virtuelles Azure, les groupes de machines virtuelles identiques et les serveurs avec Azure Arc sont actuellement pris en charge. Azure Kubernetes Service et d’autres types de ressources de calcul ne sont pas pris en charge actuellement.

## <a name="supported-regions"></a>Régions prises en charge
L’agent Azure Monitor est disponible dans toutes les régions publiques qui prennent en charge Log Analytics, ainsi que dans les clouds Azure Government et Chine. Les clouds en air gap ne sont pas encore pris en charge.

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge
Pour obtenir la liste des versions de système d’exploitation Windows et Linux que l’agent Azure Monitor prend actuellement en charge, consultez [Systèmes d’exploitation pris en charge](agents-overview.md#supported-operating-systems).

## <a name="supported-services-and-features"></a>Services et fonctionnalités pris en charge
Le tableau suivant indique la prise en charge actuelle de l’agent Azure Monitor avec d’autres services Azure.

| Service Azure | Prise en charge actuelle | Informations complémentaires |
|:---|:---|:---|
| [Centre de sécurité Azure](../../security-center/security-center-introduction.md) | Préversion privée | [Lien d’inscription](https://aka.ms/AMAgent) |
| [Azure Sentinel](../../sentinel/overview.md) | <ul><li>WEF (Windows Event Forwarding) : préversion privée</li><li>Événements de sécurité Windows : [préversion publique](../../sentinel/connect-windows-security-events.md?tabs=AMA)</li></ul>  | <ul><li>[Lien d’inscription](https://aka.ms/AMAgent) </li><li>Aucune inscription nécessaire</li></ul> |

Le tableau suivant indique la prise en charge actuelle de l’agent Azure Monitor avec les fonctionnalités Azure Monitor.

| Fonctionnalité Azure Monitor | Prise en charge actuelle | Informations complémentaires |
|:---|:---|:---|
| [VM Insights](../vm/vminsights-overview.md) | Préversion privée  | [Lien d’inscription](https://aka.ms/amadcr-privatepreviews) |
| [Se connecter à l’aide de liaisons privées ou de l’étendue de liaison privée Azure Monitor (AMPLS)](../logs/private-link-security.md) | Préversion privée pour l’agent Azure Monitor (AMA) | [Lien d’inscription](https://aka.ms/amadcr-privatepreviews) |
| [Intégrité de l’invité – Insights de machine virtuelle](../vm/vminsights-health-overview.md) | Préversion publique | Disponible seulement sur le nouvel agent |
| [Insights SQL](../insights/sql-insights-overview.md) | Préversion publique | Disponible seulement sur le nouvel agent |

Le tableau suivant indique la prise en charge actuelle de l’agent Azure Monitor avec les solutions Azure.

| Solution | Prise en charge actuelle | Informations complémentaires |
|:---|:---|:---|
| [Suivi des modifications](../../automation/change-tracking/overview.md) | Prise en charge en tant que supervision de l’intégrité des fichiers dans la préversion privée d’Azure Security Center.  | [Lien d’inscription](https://aka.ms/AMAgent) |
| [Gestion des mises à jour](../../automation/update-management/overview.md) | Utilisez Update Management v2 (préversion privée) qui ne nécessite pas d’agent. | [Lien d’inscription](https://www.yammer.com/azureadvisors/threads/1064001355087872) |

## <a name="coexistence-with-other-agents"></a>Coexistence avec d’autres agents
L’agent Azure Monitor peut coexister avec les agents existants afin que vous puissiez continuer à utiliser leurs fonctionnalités existantes lors de l’évaluation ou de la migration. Cette capacité est importante en raison des limitations dans la prise en charge des solutions existantes. Soyez prudent lors de la collecte de doublons de données, car cela peut fausser les résultats de la requête et générer des frais supplémentaires pour l’ingestion et la rétention de données. 

Par exemple, Insights de machine virtuelle utilise l’agent Log Analytics pour envoyer des données de performances à un espace de travail Log Analytics. Vous avez peut-être également configuré l’espace de travail pour collecter les événements Windows et les événements Syslog à partir d’agents. Si vous installez l’agent Azure Monitor et que vous créez une règle de collecte de données pour ces mêmes événements et données de performances, des données dupliquées sont générées.

Par conséquent, vérifiez que vous ne collectez pas les mêmes données à partir des deux agents. Si c’est le cas, veillez à ce qu’elles aient des destinations différentes.

## <a name="costs"></a>Coûts
Il n’y a aucun coût lié à l’agent Azure Monitor, mais les données ingérées peuvent vous être facturées. Pour plus d’informations sur la collecte et la rétention de données Log Analytics et les métriques client, consultez [Tarification Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="data-sources-and-destinations"></a>Sources et destinations de données
Le tableau suivant liste les types de données que vous pouvez actuellement collecter avec l’agent Azure Monitor à l’aide de règles de collecte de données, et l’emplacement où vous pouvez envoyer ces données. Pour obtenir la liste des insights, solutions et autres solutions qui utilisent l’agent Azure Monitor pour collecter d’autres types de données, consultez [Quels sont les éléments supervisés par Azure Monitor ?](../monitor-reference.md).

L’agent Azure Monitor envoie des données aux métriques Azure Monitor (préversion) ou à un espace de travail Log Analytics qui prend en charge les journaux Azure Monitor.

| Source de données | Destinations | Description |
|:---|:---|:---|
| Performances        | Métriques Azure Monitor (préversion)<sup>1</sup><br>Espace de travail Log Analytics | Valeurs numériques mesurant les performances de différents aspects du système d’exploitation et des charges de travail |
| Journaux d’événements Windows | Espace de travail Log Analytics | Informations envoyées au système de journalisation des événements Windows |
| syslog             | Espace de travail Log Analytics | Informations envoyées au système de journalisation des événements Linux |

<sup>1</sup> [Cliquez ici](../essentials/metrics-custom-overview.md#quotas-and-limits) pour passer en revue les autres limitations liées à l’utilisation de métriques Azure Monitor. Sur Linux, l’utilisation de métriques Azure Monitor comme seule destination est prise en charge dans la version 1.10.9.0 ou ultérieure. 

## <a name="security"></a>Sécurité
L’agent Azure Monitor n’a besoin d’aucune clé, mais nécessite une [identité managée affectée par le système](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity). Vous devez disposer d’une identité managée affectée par le système activée sur chaque machine virtuelle avant de déployer l’agent.

## <a name="networking"></a>Mise en réseau
L’agent Azure Monitor prend en charge les étiquettes de service Azure. Les étiquettes AzureMonitor et AzureResourceManager sont toutes les deux requises. L’agent Azure Monitor ne fonctionne pas encore avec les étendues de liaisons privées Azure Monitor. Si la machine se connecte via un serveur proxy pour communiquer sur Internet, passez en revue les exigences ci-dessous pour comprendre la configuration réseau requise.

### <a name="proxy-configuration"></a>Configuration du proxy

Les extensions de l’agent Azure Monitor pour Windows et Linux peuvent communiquer via un serveur proxy ou une passerelle Log Analytics avec Azure Monitor en utilisant le protocole HTTPS. Utilisez-le pour les machines virtuelles Azure, les groupes de machines virtuelles identiques Azure et Azure Arc pour serveurs. Utilisez les paramètres d’extension pour la configuration, comme décrit dans les étapes suivantes. L’authentification anonyme et l’authentification de base à l’aide d’un nom d’utilisateur et d’un mot de passe sont toutes les deux prises en charge.

> [!IMPORTANT]
> La configuration du proxy n’est pas prise en charge pour les [métriques Azure Monitor (préversion)](../essentials/metrics-custom-overview.md) comme destination. Par conséquent, si vous envoyez des métriques à cette destination, elle utilise l’Internet public sans proxy.

1. Utilisez cet organigramme pour déterminer en premier lieu les valeurs des paramètres *setting* et *protectedSetting*.

   ![Organigramme pour déterminer les valeurs des paramètres setting et protectedSetting lorsque vous activez l’extension.](media/azure-monitor-agent-overview/proxy-flowchart.png)


2. Une fois que les valeurs des paramètres *setting* et *protectedSetting* sont déterminées, spécifiez ces paramètres supplémentaires lorsque vous déployez l’agent Azure Monitor en utilisant des commandes PowerShell. Les exemples suivants se rapportent aux machines virtuelles Azure.

    | Paramètre | Valeur |
    |:---|:---|
    | Paramètre | Objet JSON de l’organigramme précédent converti en chaîne. À ignorer si non applicable. Exemple : {"proxy":{"mode":"application","address":"http://[adresse]:[port]","auth": false}}. |
    | ProtectedSetting | Objet JSON de l’organigramme précédent converti en chaîne. À ignorer si non applicable. Exemple : {"proxy":{"username": "[nom_utilisateur]","password": "[mot_de_passe]"}}. |


# <a name="windows-vm"></a>[Machine virtuelle Windows](#tab/PowerShellWindows)
```powershell
Set-AzVMExtension -ExtensionName AzureMonitorWindowsAgent -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.0 -Setting <settingString> -ProtectedSetting <protectedSettingString>
```

# <a name="linux-vm"></a>[Machine virtuelle Linux](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -ExtensionName AzureMonitorLinuxAgent -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.5 -Setting <settingString> -ProtectedSetting <protectedSettingString>
```

# <a name="windows-arc-enabled-server"></a>[Serveur avec Windows Arc](#tab/PowerShellWindowsArc)
```powershell
New-AzConnectedMachineExtension -Name AzureMonitorWindowsAgent -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <arc-server-name> -Location <arc-server-location> -Setting <settingString> -ProtectedSetting <protectedSettingString>
```

# <a name="linux-arc-enabled-server"></a>[Serveur avec Linux Arc](#tab/PowerShellLinuxArc)
```powershell
New-AzConnectedMachineExtension -Name AzureMonitorLinuxAgent -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <arc-server-name> -Location <arc-server-location> -Setting <settingString> -ProtectedSetting <protectedSettingString>
```

---

## <a name="next-steps"></a>Étapes suivantes

- [Installez l’agent Azure Monitor](azure-monitor-agent-install.md) sur des machines virtuelles Windows et Linux.
- [Créez une règle de collecte de données](data-collection-rule-azure-monitor-agent.md) pour collecter des données à partir de l’agent et les envoyer à Azure Monitor.
