---
title: Référence des commandes az arcdata dc status
titleSuffix: Azure Arc-enabled data services
description: Article de référence sur les commandes az arcdata dc status.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 542f0f9568150abf0646e1a494e14eec2be6e524
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131841633"
---
# <a name="az-arcdata-dc-status"></a>az arcdata dc status
## <a name="commands"></a>Commandes
| Commande | Description|
| --- | --- |
[az arcdata dc status show](#az-arcdata-dc-status-show) | Affichez l’état du contrôleur de données.
## <a name="az-arcdata-dc-status-show"></a>az arcdata dc status show
Affichez l’état du contrôleur de données.
```bash
az arcdata dc status show 
```
### <a name="examples"></a>Exemples
Affichez l’état du contrôleur de données dans un espace de noms kubernetes particulier.
```bash
az arcdata dc status show --k8s-namespace namespace --use-k8s
```
Affichez l’état d’un contrôleur de données connecté directement dans un groupe de ressources particulier.
```bash
az arcdata dc status show --resource-group resource-group    
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
