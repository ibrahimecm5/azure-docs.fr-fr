---
title: Fichier Include
description: Fichier include
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: f691f760c9abfed773db8602f878dfc7ba0c08ba
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520684"
---
### <a name="general-query-limits"></a>Limites générales concernant les requêtes

| Limite | Description |
|:---|:---|
| Langage de requête | Azure Monitor utilise le même [langage de requête Kusto](/azure/kusto/query/) qu’Azure Data Explorer. Consultez [Différences propres au langage de requête de journal d’Azure Monitor](/azure/data-explorer/kusto/query/) pour les éléments du langage KQL non pris en charge dans Azure Monitor. |
| Régions Azure | Les requêtes sur les journaux peuvent connaître une surcharge excessive quand des données concernent des espaces de travail Log Analytics dans plusieurs régions Azure. Pour plus d’informations, consultez [Limites des requêtes](../articles/azure-monitor/logs/scope.md#query-scope-limits). |
| Requêtes inter-ressources | Nombre maximal de ressources Application Insights et d’espaces de travail Log Analytics dans une requête unique limitée à 100.<br>Les requêtes inter-ressources ne sont pas prises en charge dans le Concepteur de vue.<br>Les requêtes inter-ressources des alertes de journal sont prises en charge par la nouvelle API scheduledQueryRules.<br>Pour plus de détails, voir [Limites de requête inter-ressources](../articles/azure-monitor/logs/cross-workspace-query.md#cross-resource-query-limits). |

### <a name="user-query-throttling"></a>Limitation de requêtes utilisateur
Azure Monitor applique plusieurs limitations pour gérer les situations où les utilisateurs envoient un nombre de requêtes excessif. Ce comportement peut en effet surcharger les ressources back-end du système et compromettre la réactivité du service. Les limites suivantes sont conçues pour protéger les clients des interruptions et maintenir le niveau de service. Les limites et limitations appliquées aux utilisateurs sont conçues pour impacter uniquement les cas d’usage extrêmes. Elles ne concernent pas les cas d’usage classiques.


| Measure | Limite par utilisateur | Description |
|:---|:---|:---|
| Requêtes simultanées | 5 | Un utilisateur peut exécuter jusqu’à 5 requêtes simultanées, toute requête supplémentaire est ajoutée à une file d’attente. Lorsque l’une des requêtes en cours d’exécution se termine, la première requête de la file d’attente est extraite de la file d’attente et commence à s’exécuter. Remarque : les requêtes d’alertes ne font pas partie de cette limite.
| Temps de conservation dans la file d’attente de concurrence | 3 minutes | Si une requête reste dans la file d’attente pendant plus de 3 minutes sans être démarrée, elle se termine avec une réponse d’erreur HTTP avec le code 429. |
| Nombre total de requêtes dans la file d’attente de concurrence | 200 | Une fois que le nombre de requêtes dans la file d’attente atteint 200, la requête suivante est rejetée avec un code d’erreur HTTP 429. Ce nombre s’ajoute aux 5 requêtes qui peuvent être exécutées simultanément. |
| Taux de requêtes | 200 requêtes par tranche de 30 secondes | Taux global d’émission de requêtes par un même utilisateur vers tous les espaces de travail. Cette limite s’applique aux requêtes de programmation ou aux requêtes lancées par des composants de visualisation tels que les tableaux de bord Azure ou la page récapitulative de l’espace de travail Log Analytics. |

- Optimisez vos requêtes comme décrit dans [Optimiser les requêtes de journal dans Azure Monitor](../articles/azure-monitor/logs/query-optimization.md).
- Les tableaux de bord et les classeurs peuvent contenir plusieurs requêtes dans une même vue, générant une rafale de requêtes chaque fois qu’ils sont chargés ou actualisés. Envisagez de les scinder en plusieurs vues, chargées à la demande. 
- Dans Power BI, pensez à extraire uniquement des résultats agrégés plutôt que des journaux bruts.
