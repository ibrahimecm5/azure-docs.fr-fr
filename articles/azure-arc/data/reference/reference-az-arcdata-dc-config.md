---
title: az arcdata dc config reference
titleSuffix: Azure Arc-enabled data services
description: Article de référence sur les commandes az arcdata dc config.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 72df8db00f1ab0366cc4daf8af8e5613645fc813
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852519"
---
# <a name="az-arcdata-dc-config"></a>az arcdata dc config
## <a name="commands"></a>Commandes
| Commande | Description|
| --- | --- |
[az arcdata dc config init](#az-arcdata-dc-config-init) | Initialisez un profil de configuration du contrôleur de données qui peut être utilisé avec `az arcdata dc create`.
[az arcdata dc config list](#az-arcdata-dc-config-list) | Répertoriez les choix de profils de configuration disponibles.
[az arcdata dc config add](#az-arcdata-dc-config-add) | Ajoutez une valeur pour un chemin d’accès json dans un fichier de configuration.
[az arcdata dc config remove](#az-arcdata-dc-config-remove) | Supprimez une valeur pour un chemin d’accès json dans un fichier de configuration.
[az arcdata dc config replace](#az-arcdata-dc-config-replace) | Remplacez une valeur pour un chemin d’accès json dans un fichier de configuration.
[az arcdata dc config patch](#az-arcdata-dc-config-patch) | Corrigez un fichier de configuration basé sur un fichier patch json.
## <a name="az-arcdata-dc-config-init"></a>az arcdata dc config init
Initialisez un profil de configuration du contrôleur de données qui peut être utilisé avec `az arcdata dc create`. La source spécifique du profil de configuration peut être spécifiée dans les arguments.
```bash
az arcdata dc config init 
```
### <a name="examples"></a>Exemples
Expérience d’initialisation de configuration du contrôleur de données guidée : vous serez invité à entrer les valeurs nécessaires.
```bash
az arcdata dc config init
```
La commande arcdata dc config init avec des arguments crée un profil de configuration aks-dev-test dans ./custom.
```bash
az arcdata dc config init --source azure-arc-kubeadm --path custom
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
## <a name="az-arcdata-dc-config-list"></a>az arcdata dc config list
Répertoriez les choix de profils de configuration disponibles à utiliser dans `arcdata dc config init`
```bash
az arcdata dc config list 
```
### <a name="examples"></a>Exemples
Affiche les noms de profils de configuration disponibles.
```bash
az arcdata dc config list
```
Affiche json d’un profil de configuration spécifique.
```bash
az arcdata dc config list --config-profile aks-dev-test
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
## <a name="az-arcdata-dc-config-add"></a>az arcdata dc config add
Ajoutez la valeur au niveau du chemin d’accès json dans le fichier config. Tous les exemples ci-dessous sont fournis dans Bash.  Si vous utilisez une autre ligne de commande, vous devrez peut-être placer les guillemets dans une séquence d’échappement de manière appropriée.  Vous pouvez également utiliser la fonctionnalité de fichier de correctif.
```bash
az arcdata dc config add 
```
### <a name="examples"></a>Exemples
Ajoutez un stockage du contrôleur de données.
```bash
az arcdata dc config add --path custom/control.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
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
## <a name="az-arcdata-dc-config-remove"></a>az arcdata dc config remove
Supprimez la valeur au niveau du chemin d’accès json dans le fichier config.  Tous les exemples ci-dessous sont fournis dans Bash.  Si vous utilisez une autre ligne de commande, vous devrez peut-être placer les guillemets dans une séquence d’échappement de manière appropriée.  Vous pouvez également utiliser la fonctionnalité de fichier de correctif.
```bash
az arcdata dc config remove 
```
### <a name="examples"></a>Exemples
Exemple 1 : supprimez un stockage du contrôleur de données.
```bash
az arcdata dc config remove --path custom/control.json --json-path ".spec.storage"
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
## <a name="az-arcdata-dc-config-replace"></a>az arcdata dc config replace
Remplacez la valeur au niveau du chemin d’accès json dans le fichier config.  Tous les exemples ci-dessous sont fournis dans Bash.  Si vous utilisez une autre ligne de commande, vous devrez peut-être placer les guillemets dans une séquence d’échappement de manière appropriée.  Vous pouvez également utiliser la fonctionnalité de fichier de correctif.
```bash
az arcdata dc config replace 
```
### <a name="examples"></a>Exemples
Exemple 1 : remplacez le port d’un point de terminaison unique (point de terminaison du contrôleur de données).
```bash
az arcdata dc config replace --path custom/control.json --json-values "$.spec.endpoints[?(@.name=="Controller")].port=30080"
```
Exemple 2 : remplacez un stockage du contrôleur de données.
```bash
az arcdata dc config replace --path custom/control.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
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
## <a name="az-arcdata-dc-config-patch"></a>az arcdata dc config patch
Corrigez le fichier config en fonction du fichier patch donné. Consultez http://jsonpatch.com/ pour mieux comprendre comment les chemins d’accès doivent être composés. L’opération de remplacement peut utiliser des conditions dans son chemin d’accès en raison de la bibliothèque jsonpath https://jsonpath.com/. Tous les fichiers json des correctifs doivent commencer par une clé « correctif » qui contient un tableau de correctifs avec les opérations (ajouter, remplacer, supprimer), le chemin d’accès et la valeur correspondants. L’opération « supprimer » ne requiert pas de valeur, juste un chemin d’accès. Considérons les exemples ci-dessous.
```bash
az arcdata dc config patch 
```
### <a name="examples"></a>Exemples
Exemple 1 : remplacez le port d’un point de terminaison unique (point de terminaison du contrôleur de données) par un fichier correctif.
```bash
az arcdata dc config patch --path custom/control.json --patch ./patch.json

    Patch File Example (patch.json):
        {"patch":[{"op":"replace","path":"$.spec.endpoints[?(@.name=="Controller")].port","value":30080}]}
```
Exemple 2 : remplacez un stockage du contrôleur de données par un fichier correctif.
```bash
az arcdata dc config patch --path custom/control.json --patch ./patch.json

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
