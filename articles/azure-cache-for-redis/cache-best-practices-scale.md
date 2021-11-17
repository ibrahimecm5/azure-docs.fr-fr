---
title: Meilleures pratiques relatives à la mise à l’échelle
titleSuffix: Azure Cache for Redis
description: Apprenez comment mettre à l’échelle Azure Cache pour Redis.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: b0d43e062db591600d1cbcc0cdb18b7ce6b8ebac
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132323502"
---
# <a name="scaling"></a>Mise à l'échelle

## <a name="scaling-under-load"></a>Mise à l’échelle sous charge

Lors de la mise à l’échelle d’un cache sous charge, configurez le paramètre maxmemory-reserved afin d’améliorer la réactivité du système. Pour plus d’informations, consultez [Configurer votre paramètre maxmemory-reserved](cache-best-practices-memory-management.md#configure-your-maxmemory-reserved-setting).

## <a name="scaling-clusters"></a>Mise à l’échelle de clusters

Essayez de réduire autant que possible les données dans le cache avant de mettre à l’échelle votre cache en cluster. La réduction des données permet de déplacer des volumes de données plus petits, ce qui réduit le temps nécessaire à l’opération de mise à l’échelle. Pour savoir quel est le meilleur moment pour la mise à l’échelle, consultez [Quand mettre à l’échelle ?](cache-how-to-scale.md#when-to-scale).

## <a name="scale-before-load-is-too-high"></a>Mise à l’échelle avant que la charge ne soit trop élevée

Commencez la mise à l’échelle avant que la charge du serveur ou l’utilisation de la mémoire ne soit trop élevée. Si elle est trop élevée, cela signifie que le serveur Redis est occupé. Le serveur Redis occupé ne dispose pas de suffisamment de ressources pour mettre à l’échelle et redistribuer les données.

## <a name="cache-sizes"></a>Tailles de cache

Si vous utilisez TLS et que vous disposez d’un grand nombre de connexions, envisagez une opération de scale-out afin de pouvoir distribuer la charge sur plus de cœurs. Certaines tailles de cache sont hébergées sur des machines virtuelles à quatre cœurs, voire plus.

Distribuez le chiffrement/déchiffrement TLS et les charges de travail de connexion/déconnexion TLS sur plusieurs cœurs, afin de réduire l’utilisation globale du processeur sur les machines virtuelles du cache. Pour plus d’informations, consultez les [informations sur les cœurs et les tailles de machines virtuelles](./cache-planning-faq.yml#azure-cache-for-redis-performance).

## <a name="next-steps"></a>Étapes suivantes

- [Configurer le paramètre maxmemory-reserved](cache-best-practices-memory-management.md#configure-your-maxmemory-reserved-setting)
- [Mettre à l'échelle une instance Cache Redis Azure](cache-how-to-scale.md)
