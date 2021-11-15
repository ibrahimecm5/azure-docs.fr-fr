---
title: az sql midb-arc
titleSuffix: Azure Arc-enabled data services
description: Article de référence sur les commandes az sql midb-arc.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 8053ea28fe1ce1f7f05c1bb710265fb86839dcd2
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858651"
---
# <a name="az-sql-midb-arc"></a>az sql midb-arc
## <a name="commands"></a>Commandes
| Commande | Description|
| --- | --- |
[az sql midb-arc restore](#az-sql-midb-arc-restore) | Restaurez une base de données sur une SQL Managed Instance avec Azure Arc.
## <a name="az-sql-midb-arc-restore"></a>az sql midb-arc restore
Restaurez une base de données sur une SQL Managed Instance avec Azure Arc.
```bash
az sql midb-arc restore 
```
### <a name="examples"></a>Exemples
Ex 1 – Restaurez une base de données à l’aide d’une limite de restauration dans le temps.
```bash
az sql midb-arc restore --managed-instance sqlmi1 --name mysourcedb
 --dest-name mynewdb --time "2021-10-20T05:34:22Z" --k8s-namespace
 arc --use-k8s --dry-run
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
