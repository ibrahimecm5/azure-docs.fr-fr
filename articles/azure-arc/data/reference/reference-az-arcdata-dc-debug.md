---
title: Informations de référence sur az arcdata dc debug
titleSuffix: Azure Arc-enabled data services
description: Article d’informations de référence sur les commandes az arcdata dc degug.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: e230d788a06865a8868f90fb1195c9fb701c2750
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847790"
---
# <a name="az-arcdata-dc-debug"></a>az arcdata dc debug
## <a name="commands"></a>Commandes
| Commande | Description|
| --- | --- |
[az arcdata dc debug copy-logs](#az-arcdata-dc-debug-copy-logs) | Copie les journaux.
[az arcdata dc debug dump](#az-arcdata-dc-debug-dump) | Déclenchez le vidage de la mémoire.
## <a name="az-arcdata-dc-debug-copy-logs"></a>az arcdata dc debug copy-logs
Copiez les journaux de débogage à partir du contrôleur de données. La configuration Kubernetes est requise sur votre système.
```bash
az arcdata dc debug copy-logs 
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
## <a name="az-arcdata-dc-debug-dump"></a>az arcdata dc debug dump
Déclenchez le vidage de la mémoire et copiez le contenu hors du conteneur. La configuration Kubernetes est requise sur votre système.
```bash
az arcdata dc debug dump 
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
