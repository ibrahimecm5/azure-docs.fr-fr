---
title: include file description: include file services: event-hubs author: spelluru ms.service: event-hubs ms.topic: include ms.date: 11/04/2021 ms.author: spelluru ms.custom: "include file","fasttrack-edit","iot","event-hubs"

---

Le tableau suivant indique les limites qui peuvent être différentes pour les niveaux de base, standard et dédié. Dans le tableau, UC signifie [unité de capacité](../event-hubs-dedicated-overview.md), UT [unité de traitement](../event-hubs-scalability.md#processing-units) et UD [unité de débit](../event-hubs-scalability.md#throughput-units). 

| Limite | De base | standard | Premium |  Dédié |
| ----- | ----- | -------- | -------- | --------- | 
| Taille maximale d’une publication Event Hubs | 256 KB | 1 Mo | 1 Mo |  1 Mo |
| Nombre de groupes de consommateurs par Event Hub | 1 | 20 | 100 | 1 000<br/>Aucune limite par UC  |
| Nombre de connexions réparties par espace de noms | 100 | 5 000 | 10 000 par unité de traitement par UT | 100 000 par UC |
| Période de rétention maximale des données d’événement | 1 jour | 7 jours | 90 jours<br/>1 To par UT | 90 jours<br/>10 To par UC |
| Nombre maximal d’UD, d’UT et d’UC | 40 UD | 40 UD | 16 UT | 20 CUS |
| Nombre de partitions par Event Hub | 32 | 32 | 100 par Event Hub, 200 par PU | 1 024 par hub d’événements<br/> 2 000 par unité de capacité |
| Nombre d’espaces de noms par abonnement | 1 000 | 1 000 | 1 000 | 1 000 (50 per UC) |
| Nombre d’Event Hubs par espace de noms | 10 | 10 | 100 par UT | 1 000 |
| Capture | N/A | Paiement par heure | Inclus | Inclus |
| Taille du registre de schémas (espace de noms) en mégaoctets | N/A | 25 | 100 | 1 024 |
| Nombre de groupes de schémas dans un registre de schémas ou espace de noms | N/A | 1 - sans compter le groupe par défaut | 100 <br/>1 Mo par schéma | 1 000<br/>1 Mo par schéma |
| Nombre de versions de schéma pour tous les groupes de schémas | N/A | 25 | 1 000 | 10000 |
| Débit par unité | Entrée - 1 Mo/s ou 1 000 événements par seconde<br/>Sortie - 2 Mo/s ou 4 096 événements par seconde | Entrée - 1 Mo/s ou 1 000 événements par seconde<br/>Sortie - 2 Mo/s ou 4 096 événements par seconde | Aucune limite par UT * | Aucune limite par UC * |

\* Dépend de divers facteurs, tels que l’allocation des ressources, le nombre de partitions, le stockage, etc. 
 

> [!NOTE]
> Vous pouvez publier les événements individuellement ou par lots. La limite de publication (selon le SKU) s’applique, qu’il s’agisse d’un événement unique ou d’un lot. La publication d’événements plus volumineux que le seuil maximum sera rejetée.

