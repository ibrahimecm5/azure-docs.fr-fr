---
title: Référence az arcdata resource-kind
titleSuffix: Azure Arc-enabled data services
description: Article de référence sur les commandes az arcdata resource-kind.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: ad138bfc322aa109078e622b26d1f5254ec789ca
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852979"
---
# <a name="az-arcdata-resource-kind"></a>az arcdata resource-kind
## <a name="commands"></a>Commandes
| Commande | Description|
| --- | --- |
[az arcdata resource-kind list](#az-arcdata-resource-kind-list) | Listez les types de ressources personnalisés disponibles pour Arc qui peuvent être définis et créés.
[az arcdata resource-kind get](#az-arcdata-resource-kind-get) | Obtenez le fichier de modèle de type de ressource Arc.
## <a name="az-arcdata-resource-kind-list"></a>az arcdata resource-kind list
Listez les types de ressources personnalisés disponibles pour Arc qui peuvent être définis et créés. Après la création de la liste, vous pouvez passer à l’obtention du fichier de modèle nécessaire pour définir ou créer cette ressource personnalisée.
```bash
az arcdata resource-kind list 
```
### <a name="examples"></a>Exemples
Exemple de commande permettant de répertorier les types de ressources personnalisés disponibles pour Arc.
```bash
az arcdata resource-kind list
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
## <a name="az-arcdata-resource-kind-get"></a>az arcdata resource-kind get
Obtenez le fichier de modèle de type de ressource Arc.
```bash
az arcdata resource-kind get 
```
### <a name="examples"></a>Exemples
Exemple de commande pour obtenir un fichier de modèle CRD de type de ressource Arc.
```bash
az arcdata resource-kind get --kind SqlManagedInstance
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
