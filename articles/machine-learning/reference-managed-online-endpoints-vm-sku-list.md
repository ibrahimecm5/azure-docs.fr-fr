---
title: Liste des références SKU de VM pour les points de terminaison en ligne managés (préversion)
titleSuffix: Azure Machine Learning
description: Liste les références SKU de VM qui peuvent être utilisées pour les points de terminaison en ligne managés (préversion) dans Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: laobri
ms.author: seramasu
author: rsethur
ms.custom: devplatv2
ms.date: 05/10/2021
ms.openlocfilehash: 0081f43ba5574469646dd41267ce2a12a33afe4a
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520120"
---
# <a name="managed-online-endpoints-sku-list-preview"></a>Liste des références SKU pour les points de terminaison en ligne managés (préversion)

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

Ce tableau présente les références SKU de VM prises en charge pour les points de terminaison en ligne managés Azure Machine Learning (préversion).

* L’attribut `instance_type` utilisé pour le déploiement doit être spécifié sous la forme « Standard_F4s_v2 ». Le tableau ci-dessous liste les noms d’instance, par exemple F2s v2. Ces noms doivent présenter la forme spécifiée (`Standard_{name}`) pour Azure CLI ou les demandes de modèles ARM (Azure Resource Manager) visant à créer ou mettre à jour des déploiements. 

* Pour plus d’informations sur les détails de configuration comme le processeur ou la RAM, consultez [Tarification Azure Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

| Size | Usage général | Optimisé pour le calcul | Mémoire optimisée | GPU |
| --- | --- | --- | --- | --- | --- | 
| V.Small | DS2 v2 | F2s v2 | E2s v3 | NC4as_T4_v3 |
| Petite | DS3 v2 | F4s v2 |  E4s v3 | NC6s v2 <br/> NC6s v3 <br/> NC8as_T4_v3 |
| Moyenne | DS4 v2 | F8s v2 | E8s v3 | NC12s v2 <br/> NC12s v3 <br/> NC16as_T4_v3 |
| grand | DS5 v2 | F16s v2 | E16s v3 | NC24s v2 <br/> NC24s v3 <br/> NC64as_T4_v3 |
| X-Large| - | F32s v2 <br/> F48s v2 <br/> F64s v2 <br/> F72s v2 | E32s v3 <br/> E48s v3 <br/> E64s v3 | - |


