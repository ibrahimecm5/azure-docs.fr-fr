---
title: az arcdata dc endpoint reference
titleSuffix: Azure Arc-enabled data services
description: Article de référence sur les commandes az arcdata dc endpoint.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 9d07d5e5263fd887af49552bb45eb98ae7f35441
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845738"
---
# <a name="az-arcdata-dc-endpoint"></a>az arcdata dc endpoint
## <a name="commands"></a>Commandes
| Commande | Description|
| --- | --- |
[az arcdata dc endpoint list](#az-arcdata-dc-endpoint-list) | Répertoriez le point de terminaison du contrôleur de données.
## <a name="az-arcdata-dc-endpoint-list"></a>az arcdata dc endpoint list
Répertoriez le point de terminaison du contrôleur de données.
```bash
az arcdata dc endpoint list 
```
### <a name="examples"></a>Exemples
Liste tous les points de terminaison du contrôleur de données disponibles.
```bash
az arcdata dc endpoint list --k8s-namespace namespace
```
### <a name="global-arguments"></a>Arguments globaux
#### `--debug`
Augmentez le niveau de détail de la journalisation pour afficher tous les journaux de débogage.
#### `--help -h`
Affichez ce message d’aide et quittez.
#### `--output -o`
Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.
#### `--query -q`
Chaîne de requêtes JMESPath. Pour obtenir plus d’informations et des exemples, consultez [http://jmespath.org/](http://jmespath.org).
#### `--verbose`
Augmentez le niveau de détail de la journalisation. Utilisez `--debug` pour les journaux de débogage complets.
