---
title: Limite géographique du service Azure Maps Creator
description: En savoir plus sur les mappages géographiques du service de Azure Maps Creator dans Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.custom: mvc, references_regions
ms.openlocfilehash: 9941db10efb39a5b68181224f06f1096bce5be7f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310492"
---
# <a name="creator-service-geographic-scope"></a>Limite géographique du service Creator

Azure Maps Creator est un service d’étendue géographique. Creator offre une API de fournisseur de ressources qui, dans une région Azure donnée, crée une instance de données Creator déployée au niveau géographique. Le mappage à partir d’une région Azure vers la zone géographique se produit en arrière-plan, comme décrit dans le tableau ci-dessous. Pour plus d’informations sur les régions et zones géographiques Azure, consultez [Zones géographiques Azure](https://azure.microsoft.com/global-infrastructure/geographies).

## <a name="data-locations"></a>Emplacements des données

Pour la récupération d’urgence et la haute disponibilité, Microsoft peut répliquer les données client dans d’autres régions uniquement au sein de la même zone géographique. Par exemple, les données d’Europe de l’Ouest peuvent être répliquées vers l’Europe du Nord, mais pas vers les États-Unis.  Quelle que soit la zone géographique sélectionnée par le client, Microsoft ne contrôle pas et ne limite pas les emplacements à partir desquels les clients ou leurs utilisateurs finaux peuvent accéder aux données client via l’API Azure Maps.  

## <a name="geographic-and-regional-mapping"></a>Mappage géographique et régional

Le tableau suivant décrit le mappage entre les zones géographiques et les régions Azure prises en charge, ainsi que le point de terminaison d’API géographique respectif. Par exemple, si un compte Creator est approvisionné dans la région USA Ouest 2 qui se trouve dans la zone géographique États-Unis, tous les appels d’API au service de conversion doivent être effectués sur `us.atlas.microsoft.com/conversion/convert`.


| Zones géographiques Azure (zones géographiques) | Centres de données Azure (régions) | Point de terminaison géographique de l’API |
|------------------------|----------------------|-------------|
| Europe| Europe Ouest, Europe Nord | eu.atlas.microsoft.com |
|États-Unis | USA Ouest 2, USA Est 2 | us.atlas.microsoft.com |
