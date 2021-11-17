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
ms.openlocfilehash: 4900a05ead0f3abcc16177a2b7ef874f310c597f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455318"
---
# <a name="azure-maps-qps-rate-limits"></a>Limites de taux RPS dans Azure Maps

Azure Maps n’a pas de limite quotidienne maximale quant au nombre de requêtes qui peuvent effectuées. Toutefois, il existe des limites au nombre maximal de requêtes par seconde (RPS).

Vous trouverez ci-dessous les limites d’utilisation de RPS pour chaque service Azure Maps.

| Service Azure Maps                                                                                         | Limite RPS   |
| ---------------------------------------------------------------------------------------------------------- | ----------- |
| Créateur : Alias, GetTilesetDetails                                                                         | 10 |
| Créateur : Conversion, Jeu de données, État de la fonctionnalité, WFS                                                          | 50 |
| Service de données et Service de données v2                                                                           | 50 |
| Service Elevation                                                                                          | 50 |
| Service de géolocalisation                                                                                        | 50 |
| Service Render : Copyright                                                                                 | 10 |
| Service Render : Vignettes de contour, Vignettes DEM, Vignettes d’élévation, Vignettes client, Vignettes de trafic et Cartes statiques  | 50 |
| Service Render : Vignettes de route                                                                                | 500 |
| Service Render : Vignettes d’imagerie                                                                             | 250 |
| Service Render : Vignettes météo                                                                             | 100 |
| Service Route : Batch                                                                                      | 10 |
| Service Route : Non Batch                                                                                  | 50 |
| Service Search : Batch                                                                                     | 10 |
| Service Search : Non Batch                                                                                 | 500 |
| Service Search : Non Batch inverse                                                                         | 250 |
| Service spatial                                                                                            | 50 |
| Service Time Zone                                                                                          | 50 |
| Service de trafic                                                                                            | 50 |
| Service météo                                                                                            | 50 |

Lorsque les limites de RPS sont atteintes, une erreur HTTP 429 est renvoyée. Créez une demande de support *technique* Azure Maps dans le [portail Azure](https://ms.portal.azure.com/) si vous avez besoin d’augmenter une limite de RPS spécifique.
