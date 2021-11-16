---
title: Référence az sql mi-arc
titleSuffix: Azure Arc-enabled data services
description: Article de référence sur les commandes az sql mi-arc.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 0a98cd873de2da0daaf793a2e40df5c4d5c38285
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852291"
---
# <a name="az-sql-mi-arc"></a>az sql mi-arc
## <a name="commands"></a>Commandes
| Commande | Description|
| --- | --- |
[az sql mi-arc endpoint](reference-az-sql-mi-arc-endpoint.md) | Afficher et gérer les points de terminaison SQL.
[az sql mi-arc create](#az-sql-mi-arc-create) | Créez une instance SQL gérée.
[az sql mi-arc update](#az-sql-mi-arc-update) | Mettez à jour la configuration d’une instance SQL gérée.
[az sql mi-arc delete](#az-sql-mi-arc-delete) | Supprimez une instance SQL gérée.
[az sql mi-arc show](#az-sql-mi-arc-show) | Affichez les détails d’une instance SQL gérée.
[az sql mi-arc get-mirroring-cert](#az-sql-mi-arc-get-mirroring-cert) | Récupère le certificat du point de terminaison de mise en miroir de groupe de disponibilité à partir de sql mi et le stocke dans un fichier.
[az sql mi-arc upgrade](#az-sql-mi-arc-upgrade) | Mettez à niveau une instance SQL gérée.
[az sql mi-arc list](#az-sql-mi-arc-list) | Répertoriez les instances SQL gérées.
[az sql mi-arc config](reference-az-sql-mi-arc-config.md) | Commandes de configuration.
[az sql mi-arc dag](reference-az-sql-mi-arc-dag.md) | Créer ou supprimer un groupe de disponibilité distribué.
## <a name="az-sql-mi-arc-create"></a>az sql mi-arc create
Pour définir le mot de passe de l’instance SQL gérée, définissez la variable d’environnement AZDATA_PASSWORD
```bash
az sql mi-arc create 
```
### <a name="examples"></a>Exemples
Créer une instance SQL gérée connectée indirectement.
```bash
az sql mi-arc create -n sqlmi1 --k8s-namespace namespace --use-k8s
```
Créer une instance SQL gérée connectée indirectement avec 3 réplicas dans un scénario de haute disponibilité.
```bash
az sql mi-arc create -n sqlmi2 --replicas 3  --k8s-namespace namespace --use-k8s
```
Créer une instance SQL gérée connectée directement.
```bash
az sql mi-arc create --name name --resource-group group  --location location --subscription subscription   --custom-location custom-location
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
## <a name="az-sql-mi-arc-update"></a>az sql mi-arc update
Mettez à jour la configuration d’une instance SQL gérée.
```bash
az sql mi-arc update 
```
### <a name="examples"></a>Exemples
Mettez à jour la configuration d’une instance SQL gérée.
```bash
az sql mi-arc update --path ./spec.json -n sqlmi1 --use-k8s
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
## <a name="az-sql-mi-arc-delete"></a>az sql mi-arc delete
Supprimez une instance SQL gérée.
```bash
az sql mi-arc delete 
```
### <a name="examples"></a>Exemples
Supprimer une instance SQL gérée en utilisant un espace de noms fourni.
```bash
az sql mi-arc delete --name sqlmi1 --k8s-namespace namespace --use-k8s
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
## <a name="az-sql-mi-arc-show"></a>az sql mi-arc show
Affichez les détails d’une instance SQL gérée.
```bash
az sql mi-arc show 
```
### <a name="examples"></a>Exemples
Afficher les détails d’une instance SQL gérée connectée indirectement.
```bash
az sql mi-arc show --name sqlmi1 --k8s-namespace namespace --use-k8s
```
Afficher les détails d’une instance SQL gérée connectée indirectement.
```bash
az sql mi-arc show --name sqlmi1 --resource-group resource-group            
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
## <a name="az-sql-mi-arc-get-mirroring-cert"></a>az sql mi-arc get-mirroring-cert
Récupère le certificat du point de terminaison de mise en miroir de groupe de disponibilité à partir de sql mi et le stocke dans un fichier.
```bash
az sql mi-arc get-mirroring-cert 
```
### <a name="examples"></a>Exemples
Récupère le certificat du point de terminaison de mise en miroir de groupe de disponibilité à partir de sqlmi1 et le stocke dans le fichier fileName1
```bash
az sql mi-arc get-mirroring-cert -n sqlmi1 --cert-file fileName1
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
## <a name="az-sql-mi-arc-upgrade"></a>az sql mi-arc upgrade
Mettez à niveau l’instance SQL gérée vers la version souhaitée spécifiée.  Si la version souhaitée n’est pas spécifiée, la version du contrôleur de données est utilisée.
```bash
az sql mi-arc upgrade 
```
### <a name="examples"></a>Exemples
Mettez à niveau une instance SQL gérée.
```bash
az sql mi-arc upgrade -n sqlmi1 -k arc --desired-version v1.1.0 --use-k8s
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
## <a name="az-sql-mi-arc-list"></a>az sql mi-arc list
Répertoriez les instances SQL gérées.
```bash
az sql mi-arc list 
```
### <a name="examples"></a>Exemples
Répertoriez les instances SQL gérées.
```bash
az sql mi-arc list --use-k8s
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
