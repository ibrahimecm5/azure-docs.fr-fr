---
title: Informations de référence sur az postgres arc-server endpoint
titleSuffix: Azure Arc-enabled data services
description: Article de référence sur les commandes az postgres arc-server endpoint.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 78111c030c1641cab22664d889e26505de879cf7
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847695"
---
# <a name="az-postgres-arc-server-endpoint"></a>az postgres arc-server endpoint
## <a name="commands"></a>Commandes
| Commande | Description|
| --- | --- |
[az postgres arc-server endpoint list](#az-postgres-arc-server-endpoint-list) | Lister les points de terminaison de groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
## <a name="az-postgres-arc-server-endpoint-list"></a>az postgres arc-server endpoint list
Lister les points de terminaison de groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
```bash
az postgres arc-server endpoint list 
```
### <a name="examples"></a>Exemples
Lister les points de terminaison de groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
```bash
az postgres arc-server endpoint list --name postgres01  --k8s-namespace namespace --use-k8s
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
