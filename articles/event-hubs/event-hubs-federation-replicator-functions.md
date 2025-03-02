---
title: Tâches et applications de réplication d’événements - Azure Event Hubs | Microsoft Docs
description: Cet article fournit une vue d’ensemble de la création d’applications et de tâches de réplication d’événements avec Azure Functions.
ms.topic: article
ms.date: 09/28/2021
ms.openlocfilehash: 92c6357e5c4302232bddabdd1e1818e2f325246b
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217331"
---
# <a name="event-replication-tasks-and-applications-with-azure-functions"></a>Tâches et applications de réplication d’événements avec Azure Functions

> [!TIP]
> Pour toutes les tâches de réplication avec état où vous devez prendre en compte les charges utiles de vos événements et les transformer, les agréger, les enrichir ou les réduire, utilisez [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) au lieu d’Azure Functions.

Comme expliqué dans l’article sur [la réplication d’événements et la fédération entre les régions](event-hubs-federation-overview.md), la réplication sans état des flux d’événements entre les paires d’Event Hubs et entre les Event Hubs et les autres sources et cibles de flux d’événements s’appuie sur Azure Functions.

[Azure Functions](../azure-functions/functions-overview.md) est un environnement d’exécution fiable et évolutif qui permet de configurer et d’exécuter des applications serverless, y compris les tâches de réplication d’événements et de fédération.

Dans cette vue d’ensemble, vous découvrirez les fonctionnalités intégrées d’Azure Functions pour ces applications, ainsi que les blocs de code que vous pouvez adapter et modifier pour les tâches de transformation, et vous apprendrez à configurer une application Azure Functions de sorte qu’elle s’intègre facilement à Event Hubs et à d’autres services de messagerie Azure. Cet article va très souvent faire référence à la documentation Azure Functions.

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]









