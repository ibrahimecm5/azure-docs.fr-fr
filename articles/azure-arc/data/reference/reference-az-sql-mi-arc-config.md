---
title: az sql mi-arc config reference
titleSuffix: Azure Arc-enabled data services
description: Article de référence pour les commandes az sql mi-arc config.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 2dbe786f8a33528d028b4dc5f5476fa6f38a62b4
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845678"
---
# <a name="az-sql-mi-arc-config"></a>az sql mi-arc config
## <a name="commands"></a>Commandes
| Commande | Description|
| --- | --- |
[az sql mi-arc config init](#az-sql-mi-arc-config-init) | Initialisez les fichiers CRD et de spécification pour une instance gérée SQL.
[az sql mi-arc config add](#az-sql-mi-arc-config-add) | Ajoutez une valeur pour un chemin d’accès json dans un fichier de configuration.
[az sql mi-arc config remove](#az-sql-mi-arc-config-remove) | Supprimez une valeur pour un chemin d’accès json dans un fichier de configuration.
[az sql mi-arc config replace](#az-sql-mi-arc-config-replace) | Remplacez une valeur pour un chemin d’accès json dans un fichier de configuration.
[az sql mi-arc config patch](#az-sql-mi-arc-config-patch) | Corrige un fichier de configuration basé sur un fichier patch json.
## <a name="az-sql-mi-arc-config-init"></a>az sql mi-arc config init
Initialisez les fichiers CRD et de spécification pour une instance gérée SQL.
```bash
az sql mi-arc config init 
```
### <a name="examples"></a>Exemples
Initialisez les fichiers CRD et de spécification pour une instance gérée SQL.
```bash
az sql mi-arc config init --path ./template
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
## <a name="az-sql-mi-arc-config-add"></a>az sql mi-arc config add
Ajoutez la valeur au chemin d’accès json dans le fichier config.  Tous les exemples ci-dessous sont fournis dans Bash.  Si vous utilisez une autre ligne de commande, vous devrez peut-être placer les guillemets dans une séquence d’échappement de manière appropriée.  Vous pouvez également utiliser la fonctionnalité de fichier de correctif.
```bash
az sql mi-arc config add 
```
### <a name="examples"></a>Exemples
Ex 1 : ajoutez un stockage.
```bash
az sql mi-arc config add --path custom/spec.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
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
## <a name="az-sql-mi-arc-config-remove"></a>az sql mi-arc config remove
Supprimez la valeur au chemin d’accès json dans le fichier config. Tous les exemples ci-dessous sont fournis dans Bash. Si vous utilisez une autre ligne de commande, vous devrez peut-être placer les guillemets dans une séquence d’échappement de manière appropriée. Vous pouvez également utiliser la fonctionnalité de fichier de correctif.
```bash
az sql mi-arc config remove 
```
### <a name="examples"></a>Exemples
Ex 1 : supprimez un stockage.
```bash
az sql mi-arc config remove --path custom/spec.json --json-path ".spec.storage"
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
## <a name="az-sql-mi-arc-config-replace"></a>az sql mi-arc config replace
Remplacez la valeur au niveau du chemin d’accès json dans le fichier config.  Tous les exemples ci-dessous sont fournis dans Bash.  Si vous utilisez une autre ligne de commande, vous devrez peut-être placer les guillemets dans une séquence d’échappement de manière appropriée.  Vous pouvez également utiliser la fonctionnalité de fichier de correctif.
```bash
az sql mi-arc config replace 
```
### <a name="examples"></a>Exemples
Ex 1 : remplacez le port d’un point de terminaison unique.
```bash
az sql mi-arc config replace --path custom/spec.json --json-values "$.spec.endpoints[?(@.name=="Controller")].port=30080"
```
Ex 2 : remplacez un stockage.
```bash
az sql mi-arc config replace --path custom/spec.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
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
## <a name="az-sql-mi-arc-config-patch"></a>az sql mi-arc config patch
Corrigez le fichier config en fonction du fichier patch donné. Consultez http://jsonpatch.com/ pour mieux comprendre comment les chemins d’accès doivent être composés. L’opération de remplacement peut utiliser des conditions dans son chemin d’accès en raison de la bibliothèque jsonpath https://jsonpath.com/. Tous les fichiers json des correctifs doivent commencer par une clé `patch` qui contient un tableau de correctifs avec les opérations (ajouter, remplacer, supprimer), le chemin d’accès et la valeur correspondants. L’opération `remove` ne requiert pas de valeur, juste un chemin d’accès. Considérons les exemples ci-dessous.
```bash
az sql mi-arc config patch 
```
### <a name="examples"></a>Exemples
Ex 1 : remplacez le port d’un point de terminaison unique par un fichier du correctif.
```bash
az sql mi-arc config patch --path custom/spec.json --patch ./patch.json

    Patch File Example (patch.json):
        {"patch":[{"op":"replace","path":"$.spec.endpoints[?(@.name=="Controller")].port","value":30080}]}
```
Ex 2 : remplacez un stockage par un fichier du correctif.
```bash
az sql mi-arc config patch --path custom/spec.json --patch ./patch.json

    Patch File Example (patch.json):
        {"patch":[{"op":"replace","path":".spec.storage","value":{"accessMode":"ReadWriteMany","className":"managed-premium","size":"10Gi"}}]}
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
