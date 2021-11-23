---
title: Solution Wire Data dans Azure Monitor | Microsoft Docs
description: Les données de communication sont des données de performances et réseau consolidées provenant des ordinateurs avec des agents Log Analytics. Les données réseau sont associées à vos données de journaux pour vous aider à mettre les données en corrélation.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/26/2021
ms.openlocfilehash: 74d3544fd7e858a685b669ad826bc17e0eb6e64f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312917"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor-retired"></a>Solution Wire Data 2.0 (préversion) dans Azure Monitor (mis hors service)

![Symbole de Wire Data](media/wire-data/wire-data2-symbol.png)

> [!NOTE]
> La solution Wire Data a été remplacée par la solution [VM Insights](../vm/vminsights-overview.md) et [Service Map](../vm/service-map.md).  Toutes deux utilisent l’agent Log Analytics et l’agent de dépendances pour collecter les données de connexion réseau dans Azure Monitor.
>
> Le support pour la solution Wire Data prendra fin le **31 mars 2022**.  Jusqu’à la date de mise hors service, les clients actuels qui utilisent la solution Wire Data 2.0 (préversion) peuvent continuer à l’utiliser.
>
> Les anciens et nouveaux clients doivent installer la solution [VM Insights](../vm/vminsights-enable-overview.md) ou [Service Map](../vm/service-map.md).  Le jeu de données Map qu’elles collectent est comparable au jeu de données de Wire Data 2.0 (préversion).  VM Insights comprend le jeu de données Service Map ainsi que des fonctionnalités et des données de performances supplémentaires pour l’analyse. Les deux offres sont [connectées à Microsoft Sentinel](../../sentinel/connect-data-sources.md#map-data-types-with-microsoft-sentinel-connection-options).

Les données de communication sont des données de performances et réseau consolidées, collectées à partir d’ordinateurs connectés à Windows et à Linux avec l’agent Log Analytics, notamment les ordinateurs analysés par Operations Manager dans votre environnement. Les données réseau sont associées aux autres données de journaux pour faciliter la mise en corrélation des données.

Outre l’agent Log Analytics, la solution Wire Data utilise des agents Microsoft Dependency Agent installés sur les ordinateurs de votre infrastructure informatique. Les agents de dépendances surveillent les données réseau envoyées par et à ces ordinateurs pour les niveaux de réseau 2 et 3 dans le [modèle OSI](https://en.wikipedia.org/wiki/OSI_model), y compris les différents protocoles et ports utilisés. Les données sont ensuite envoyées à Azure Monitor à l’aide d’agents.

## <a name="migrate-to-azure-monitor-vm-insights-or-service-map"></a>Migrer vers Azure Monitor VM Insights ou Service Map

Dans de nombreux cas, nous constatons que les clients ont à la fois Wire Data 2.0 (préversion) et la solution [VM Insights](../vm/vminsights-overview.md) ou [Service Map](../vm/service-map.md) déjà activés sur les mêmes machines virtuelles.  Cela signifie que l’offre de remplacement est activée sur votre machine virtuelle.  Vous pouvez simplement [supprimer la solution Wire Data 2.0 (préversion) de votre espace de travail Log Analytics](./solutions.md?tabs=portal#remove-a-monitoring-solution).

Si vous avez des machines virtuelles sur lesquelles seule la solution Wire Data 2.0 (préversion) est activée, vous pouvez intégrer les machines virtuelles à la solution [VM Insights](../vm/vminsights-enable-overview.md) ou [Service Map](../vm/service-map.md), puis [supprimer la solution Wire Data 2.0 (préversion) de votre espace de travail Log Analytics](./solutions.md?tabs=portal#remove-a-monitoring-solution).

## <a name="migrate-your-queries-to-the-vmconnection-table-from-azure-monitor-vm-insights"></a>Migrer vos requêtes vers la table VMConnection à partir d’Azure Monitor VM Insights

### <a name="agents-providing-data"></a>Agents fournissant des données

#### <a name="wire-data-20-query"></a>Requête Wire Data 2.0

```
WireData
| summarize AggregatedValue = sum(TotalBytes) by Computer
| limit 500000
```

#### <a name="vm-insights-and-service-map-query"></a>Requête VM Insights et Service Map

```
VMConnection
| summarize AggregatedValue = sum(BytesReceived + BytesSent) by Computer
| limit 500000
```

### <a name="ip-addresses-of-the-agents-providing-data"></a>Adresses IP des agents fournissant des données

#### <a name="wire-data-20-query"></a>Requête Wire Data 2.0

```
WireData
| summarize AggregatedValue = count() by LocalIP
```

#### <a name="vm-insights-and-service-map-query"></a>Requête VM Insights et Service Map

```
VMComputer
| distinct Computer, tostring(Ipv4Addresses)
```

### <a name="all-outbound-communications-by-remote-ip-address"></a>Toutes les communications sortantes par adresse IP distante

#### <a name="wire-data-20-query"></a>Requête Wire Data 2.0

```
WireData
| where Direction == "Outbound"
| summarize AggregatedValue = count() by RemoteIP
```

#### <a name="vm-insights-and-service-map-query"></a>Requête VM Insights et Service Map

```
VMConnection
| where Direction == "outbound"
| summarize AggregatedValue = count() by RemoteIp
```

### <a name="bytes-received-by-protocol-name"></a>Octets reçus par nom de protocole

#### <a name="wire-data-20-query"></a>Requête Wire Data 2.0

```
WireData 
| where Direction == "Inbound"
| summarize AggregatedValue = sum(ReceivedBytes) by ProtocolName
```

#### <a name="vm-insights-and-service-map-query"></a>Requête VM Insights et Service Map

```
VMConnection
| where Direction == "inbound"
| summarize AggregatedValue = sum(BytesReceived) by Protocol
```

### <a name="amount-of-network-traffic-in-bytes-by-process"></a>Quantité de trafic (en octets) par processus

#### <a name="wire-data-20-query"></a>Requête Wire Data 2.0

```
WireData
| summarize AggregatedValue = sum(TotalBytes) by ProcessName
```

#### <a name="vm-insights-and-service-map-query"></a>Requête VM Insights et Service Map

```
VMConnection
| summarize sum(BytesReceived), sum(BytesSent) by ProcessName
```

### <a name="more-examples-queries"></a>Autres exemples de requêtes

Pour obtenir d’autres exemples de requêtes, consultez la [documentation relative à la recherche dans les journaux VM Insights](../vm/vminsights-log-search.md) et la [documentation relative aux alertes VM Insights](../vm/monitor-virtual-machine-alerts.md).

## <a name="uninstall-wire-data-20-solution"></a>Désinstaller la solution Wire Data 2.0

Pour désinstaller Wire Data 2.0, il vous suffit de supprimer la solution de vos espaces de travail Log Analytics.  Cette opération aura les conséquences suivantes :

- Le pack d’administration de Wire Data est supprimé des machines virtuelles connectées à l’espace de travail.
- Le type de données Wire Data n’apparaît plus dans votre espace de travail

Suivez [ces instructions](./solutions.md?tabs=portal#remove-a-monitoring-solution) pour supprimer la solution Wire Data.

>[!NOTE]
>Si votre espace de travail est doté de la solution Service Map ou VM Insights, le pack d’administration n’est pas supprimé, car ces solutions utilisent également ce pack d’administration.

### <a name="wire-data-20-management-packs"></a>Packs d’administration Wire Data 2.0

Quand Wire Data est activé dans un espace de travail Log Analytics, un pack d’administration de 300 Ko est envoyé à tous les serveurs Windows de cet espace de travail. Si vous utilisez des agents System Center Operations Manager dans un [groupe d’administration connecté](../agents/om-agents.md), le pack d’administration Dependency Monitor est déployé à partir de System Center Operations Manager. Si les agents sont connectés directement, Azure Monitor remet le pack d’administration.

Le pack d’administration se nomme Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Il est enregistré dans : %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. La source de données utilisée par le pack d’administration est : %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="uninstall-the-dependency-agent"></a>Désinstaller l’agent de dépendances

>[!NOTE]
>Si vous envisagez de remplacer Wire Data par Service Map ou VM Insights, vous ne devez pas supprimer l’agent de dépendances.

Utilisez les sections suivantes comme guide pour supprimer l’agent de dépendances.  

### <a name="uninstall-the-dependency-agent-on-windows"></a>Désinstaller l’agent de dépendances sur Windows

Un administrateur peut désinstaller l’agent de dépendances pour Windows au moyen du Panneau de configuration.

Un administrateur peut également exécuter %Programfiles%\Microsoft Dependency Agent\Uninstall.exe pour désinstaller l’agent de dépendances.

### <a name="uninstall-the-dependency-agent-on-linux"></a>Désinstaller l’agent de dépendances sur Linux

Pour désinstaller complètement l’agent de dépendances de Linux, vous devez supprimer l’agent lui-même et le connecteur qui est installé automatiquement avec lui. Vous pouvez désinstaller les deux à l’aide de la commande ci-dessous :

```bash
rpm -e dependency-agent dependency-agent-connector
```

## <a name="using-the-wire-data-20-solution"></a>Utiliser la solution Wire Data 2.0

Sur la page **Vue d’ensemble** de votre espace de travail Log Analytics dans le portail Azure, cliquez sur la vignette **Wire Data 2.0** pour ouvrir le tableau de bord Wire Data. Le tableau de bord comprend les panneaux figurant dans le tableau suivant. Chaque panneau répertorie jusqu'à 10 éléments répondant à ses critères en ce qui concerne l’étendue et l’intervalle de temps spécifiés. Vous pouvez exécuter une recherche dans les journaux qui renvoie tous les enregistrements. Pour cela, cliquez sur **Afficher tout** en bas du panneau ou cliquez sur l’en-tête de panneau.

| **Panneau** | **Description** |
| --- | --- |
| Agents qui capturent le trafic réseau | Affiche le nombre d’agents qui capturent le trafic réseau et liste les 10 ordinateurs qui en capturent le plus. Cliquez sur le nombre pour exécuter une recherche de <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code> dans les journaux. Cliquez sur un ordinateur dans la liste pour exécuter une recherche dans les journaux qui renvoie le nombre total d’octets capturés. |
| Sous-réseaux locaux | Affiche le nombre de sous-réseaux locaux découverts par les agents.  Cliquez sur le nombre pour exécuter une recherche de <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code> dans les journaux, qui liste tous les sous-réseaux avec le nombre d’octets envoyés sur chacun d’eux. Cliquez sur un sous-réseau dans la liste pour exécuter une recherche dans les journaux qui renvoie le nombre total d’octets envoyés dessus. |
| Protocole de niveau application | Affiche le nombre de protocoles de niveau application en cours d’utilisation, découverts par les agents. Cliquez sur le nombre pour exécuter une recherche de <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code> dans les journaux. Cliquez sur un protocole dans la liste pour exécuter une recherche dans les journaux qui renvoie le nombre total d’octets envoyés par son biais. |

![Tableau de bord de Wire Data](./media/wire-data/wire-data-dash.png)

Vous pouvez utiliser le panneau **Agents qui capturent le trafic réseau** pour déterminer quelle quantité de bande passante réseau est consommée par les ordinateurs. Il vous permet de trouver facilement l’ordinateur _qui communique le plus_ dans votre environnement. Ces ordinateurs peuvent être surchargés, agir de façon anormale ou utiliser plus de ressources réseau que la normale.

![Capture d’écran du panneau Agents qui capturent le trafic réseau dans le tableau de bord de Wire Data 2.0, montrant la bande passante réseau consommée par chaque ordinateur.](./media/wire-data/log-search-example01.png)

De même, vous pouvez utiliser le panneau **Sous-réseaux locaux** pour déterminer quelle quantité de trafic réseau circule sur vos sous-réseaux. Les utilisateurs définissent souvent des sous-réseaux autour des zones critiques de leurs applications. Ce panneau permet de surveiller ces zones.

![Capture d’écran du panneau Sous-réseaux locaux dans le tableau de bord de Wire Data 2.0, montrant la bande passante réseau consommée par chaque LocalSubnet.](./media/wire-data/log-search-example02.png)

Le Panneau **Protocoles de niveau application** est utile, car il permet de savoir quels protocoles sont en cours d’utilisation. Par exemple, vous pouvez vouloir que SSH ne soit pas utilisé dans votre environnement réseau. Les informations disponibles sur le panneau peuvent le confirmer ou le démentir rapidement.

![Capture d’écran du panneau Protocoles au niveau de l’application dans le tableau de bord de Wire Data 2.0, montrant la bande passante réseau consommée par chaque protocole.](./media/wire-data/log-search-example03.png)

Il est également utile de savoir si le trafic des protocoles augmente ou diminue au fil du temps. Par exemple, si la quantité de données transmises par une application augmente, il peut être nécessaire ou intéressant de le savoir.

## <a name="input-data"></a>Données d’entrée

Les données de communication collectent des métadonnées sur le trafic réseau à l’aide des agents que vous avez activés. Chaque agent envoie des données toutes les 15 secondes environ.

## <a name="output-data"></a>Données de sortie

Un enregistrement de type _WireData_ est créé pour chaque type de données d’entrée. Les propriétés de ces enregistrements sont décrites dans le tableau suivant :

| Propriété | Description |
|---|---|
| Computer | Nom de l’ordinateur sur lequel les données ont été recueillies |
| TimeGenerated | Heure de l’enregistrement |
| LocalIP | Adresse IP de l’ordinateur local |
| SessionState | Connecté ou déconnecté |
| ReceivedBytes | Nombre d’octets reçus |
| ProtocolName | Nom du protocole réseau utilisé |
| IPVersion | Version de l’adresse IP |
| Sens | Entrant ou sortant |
| MaliciousIP | Adresse IP d’une source malveillante connue |
| severity | Niveau de gravité suspecté |
| RemoteIPCountry | Pays/région de l’adresse IP distante |
| ManagementGroupName | Nom du groupe d’administration Operations Manager |
| SourceSystem | Source où les données ont été recueillies |
| SessionStartTime | Heure de début de la session |
| SessionEndTime | Heure de fin de la session |
| LocalSubnet | Sous-réseau sur lequel les données ont été recueillies |
| LocalPortNumber | Numéro de port local |
| RemoteIP | Adresse IP distante utilisée par l’ordinateur distant |
| RemotePortNumber | Numéro de port utilisé par l’adresse IP distante |
| SessionID | Valeur unique qui identifie la session de communication entre deux adresses IP |
| SentBytes | Nombre d’octets envoyés |
| TotalBytes | Nombre total d’octets envoyés au cours de la session |
| ApplicationProtocol | Type de protocole réseau utilisé   |
| ProcessID | ID du processus Windows |
| ProcessName | Chemin d’accès et nom de fichier du processus |
| RemoteIPLongitude | Valeur de longitude de l’adresse IP |
| RemoteIPLatitude | Valeur de latitude de l’adresse IP |

## <a name="next-steps"></a>Étapes suivantes

- Pour connaître les exigences et les méthodes pour activer la surveillance de vos machines virtuelles, consultez [Déployer VM Insights](../vm/vminsights-enable-overview.md).
- [Lancez une recherche dans les journaux d’activité](../logs/log-query-overview.md) pour afficher des enregistrements détaillés sur les recherches de données de communication.
