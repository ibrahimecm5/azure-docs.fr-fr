---
title: az sql mi-arc endpoint reference
titleSuffix: Azure Arc-enabled data services
description: Article de référence sur les commandes az sql mi-arc endpoint.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: db7c1fb6366cda40a5978a7be55100ae7a12ac4a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852399"
---
# <a name="az-sql-mi-arc-endpoint"></a>az sql mi-arc endpoint
## <a name="commands"></a>Commandes
| Commande | Description|
| --- | --- |
[az sql mi-arc endpoint list](#az-sql-mi-arc-endpoint-list) | Liste les points de terminaison SQL.
## <a name="az-sql-mi-arc-endpoint-list"></a>az sql mi-arc endpoint list
Liste les points de terminaison SQL.
```bash
az sql mi-arc endpoint list 
```
### <a name="examples"></a>Exemples
Listez les points de terminaison d’une instance gérée SQL.
```bash
az sql mi-arc endpoint list -n sqlmi1
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
