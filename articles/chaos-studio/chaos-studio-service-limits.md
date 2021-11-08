---
title: Limites du service Azure Chaos Studio
description: Comprendre les limites d’utilisation et de requêtes pour Azure Chaos Studio
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.date: 11/01/2021
ms.topic: reference
ms.custom: ignite-fall-2021
ms.openlocfilehash: f9b5352597afb00b379363a2f151615a902dff8f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096905"
---
# <a name="azure-chaos-studio-service-limits"></a>Limites du service Azure Chaos Studio
Cet article fournit des limites de service pour Azure Chaos Studio. 
## <a name="experiment-and-target-limits"></a>Limites de l’expérience et de la cible

Chaos Studio applique des limites au nombre d’objets, à la durée des activités et à la rétention des données.

| Limite | Valeur |
| -- | -- |
| Actions par expérience | 9 |
| Branches par expérience | 9 |
| Étapes par expérience | 4 |
| Durée d’action (en heures) | 12 |
| Expériences simultanées exécutées par région et par abonnement | 5 |
| Durée totale de l’expérience (en heures) | 12 |
| Nombre d’expériences par région et par abonnement | 500 |
| Nombre de cibles par action | 50 |
| Nombre d’agents actifs par cible | 1 000 |
| Nombre de cibles par région et par abonnement | 10 000 |

## <a name="api-throttling-limits"></a>Limites de la limitation des API

Chaos Studio applique des limites à toutes les opérations ARM. Les requêtes qui dépassent la limite sont limitées. Toutes les limites de requête sont appliquées pour un intervalle de cinq minutes, sauf indication contraire.

| Opération | Demandes |
| -- | -- |
| Microsoft.Chaos/experiments/write | 100 |
| Microsoft.Chaos/experiments/read | 300 |
| Microsoft.Chaos/experiments/delete | 100 |
| Microsoft.Chaos/experiments/start/action | 20 |
| Microsoft.Chaos/experiments/cancel/action | 100 |
| Microsoft.Chaos/experiments/statuses/read | 100 |
| Microsoft.Chaos/experiments/executionDetails/read | 100 |
| Microsoft.Chaos/targets/write | 200 |
| Microsoft.Chaos/targets/read | 600 |
| Microsoft.Chaos/targets/delete | 200 |
| Microsoft.Chaos/targets/capabilities/write | 600 |
| Microsoft.Chaos/targets/capabilities/read | 1800 |
| Microsoft.Chaos/targets/capabilities/delete | 600 |
| Microsoft.Chaos/locations/targetTypes/read | 50 |
| Microsoft.Chaos/locations/targetTypes/capabilityTypes/read | 50 |
