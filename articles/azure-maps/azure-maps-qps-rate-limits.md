---
title: Limites de taux RPS dans Azure Maps
description: Limitation d’Azure Maps au nombre de requêtes par seconde.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/15/2021
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: eriklind
ms.openlocfilehash: 0015eeccd2ea4ed12eb0435cac72db8e86f9119a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846442"
---
# <a name="azure-maps-qps-rate-limits"></a>Limites de taux RPS dans Azure Maps

Azure Maps n’a pas de limite quotidienne maximale quant au nombre de requêtes qui peuvent effectuées. Toutefois, il existe des limites au nombre maximal de requêtes par seconde (RPS).

Vous trouverez ci-dessous les limites d’utilisation de RPS pour chaque service Azure Maps par niveau tarifaire.

| Service Azure Maps | Limite RPS : niveau tarifaire de génération 2 | Limite RPS : niveau tarifaire S1 de génération 1 | Limite RPS : niveau tarifaire S0 de génération 1 |
|  ----------------- |  :--------------------------: | :------------------------------: | :------------------------: |
| Service de copyright | 10 | 10 | 10 |
| Créateur : alias, détails de tileset | 10 | Non disponible | Non disponible |
| Créateur : conversion, jeu de données, état de la fonctionnalité, WFS | 50 | Non disponible | Non disponible |
| Data Service | 50 | 50 | 50 |
| Service Elevation | 50 | 50 | 50 |
| Service de géolocalisation | 50 | 50 | 50 |
| Service Render : vignettes de contour, vignettes DEM, vignettes d’élévation, vignettes client, vignettes de trafic et cartes statiques | 50 | 50 | 50 |
| Service Render : vignettes de route | 500 | 500 | 50 |
| Service Render : vignettes de satellite | 250 | 250 | 50 |
| Service Render : vignettes météo | 100 | 100 | 50 |
| Service Route : batch | 10 | 10 | 10 |
| Service Route : non-batch | 50 | 50 | 50 |
| Service de recherche : batch | 10 | 10 | 10 |
| Service de recherche : non-batch | 500 | 500 | 50 |
| Service de recherche : non-batch inverse | 250 | 250 | 50 |
| Service spatial | 50 | 50 | 50 |
| Service de fuseau horaire | 50 | 50 | 50 |
| Service de trafic | 50 | 50 | 50 |
| Service météo | 50 | 50 | 50 |

Lorsque les limites de RPS sont atteintes, une erreur HTTP 429 est renvoyée. Si vous utilisez les niveaux tarifaires S1 de génération 1 ou de génération 2, vous pouvez créer une demande de support *technique* Azure Maps dans le [portail Azure](https://ms.portal.azure.com/) pour augmenter une limite RPS spécifique, si nécessaire. Les limites RPS pour le niveau tarifaire S0 de génération 1 ne peuvent pas être augmentées.
