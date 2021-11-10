---
title: Référence des commandes az postgres arc-server
titleSuffix: Azure Arc-enabled data services
description: Article de référence pour les commandes az postgres arc-server.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 9a5746f4a18cf0df2045c22c58cf26a9aa44fc74
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845700"
---
# <a name="az-postgres-arc-server"></a>az postgres arc-server
## <a name="commands"></a>Commandes
| Commande | Description|
| --- | --- |
[az postgres arc-server create](#az-postgres-arc-server-create) | Créer un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
[az postgres arc-server edit](#az-postgres-arc-server-edit) | Modifier la configuration d’un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
[az postgres arc-server delete](#az-postgres-arc-server-delete) | Supprimer un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
[az postgres arc-server show](#az-postgres-arc-server-show) | Afficher les détails d’un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
[az postgres arc-server list](#az-postgres-arc-server-list) | Lister les groupes de serveurs PostgreSQL Hyperscale avec Azure Arc.
[az postgres arc-server endpoint](reference-az-postgres-arc-server-endpoint.md) | Gérer les points de terminaison de groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
## <a name="az-postgres-arc-server-create"></a>az postgres arc-server create
Pour définir le mot de passe du groupe de serveurs, définissez la variable d’environnement AZDATA_PASSWORD
```bash
az postgres arc-server create 
```
### <a name="examples"></a>Exemples
Créer un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
```bash
az postgres arc-server create -n pg1 --k8s-namespace namespace --use-k8s
```
Créer un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc en utilisant les paramètres du moteur. Les deux exemples ci-dessous sont valides.
```bash
az postgres arc-server create -n pg1 --engine-settings "key1=val1" --k8s-namespace namespace 
az postgres arc-server create -n pg1 --engine-settings "key2=val2" --k8s-namespace namespace --use-k8s
```
Créer un groupe de serveurs PostgreSQL avec des montages de revendications de volume.
```bash
az postgres arc-server create -n pg1 --volume-claim-mounts backup-pvc:backup 
```
Créer un groupe de serveurs PostgreSQL avec une limite de mémoire spécifique pour différents rôles de nœud.
```bash
az postgres arc-server create -n pg1 --memory-limit "coordinator=2Gi,w=1Gi" --workers 1 --k8s-namespace namespace --use-k8s
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
## <a name="az-postgres-arc-server-edit"></a>az postgres arc-server edit
Modifier la configuration d’un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
```bash
az postgres arc-server edit 
```
### <a name="examples"></a>Exemples
Modifier la configuration d’un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
```bash
az postgres arc-server edit --path ./spec.json -n pg1 --k8s-namespace namespace --use-k8s
```
Modifier un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc en utilisant les paramètres du moteur pour le nœud coordinateur.
```bash
az postgres arc-server edit -n pg1 --coordinator-settings "key2=val2" --k8s-namespace namespace
```
Modifie un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc et remplace les paramètres du moteur existants par le nouveau paramètre « key1=val1 ».
```bash
az postgres arc-server edit -n pg1 --engine-settings "key1=val1" --replace-settings --k8s-namespace namespace
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
## <a name="az-postgres-arc-server-delete"></a>az postgres arc-server delete
Supprimer un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
```bash
az postgres arc-server delete 
```
### <a name="examples"></a>Exemples
Supprimer un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
```bash
az postgres arc-server delete -n pg1 --k8s-namespace namespace --use-k8s
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
## <a name="az-postgres-arc-server-show"></a>az postgres arc-server show
Afficher les détails d’un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
```bash
az postgres arc-server show 
```
### <a name="examples"></a>Exemples
Afficher les détails d’un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
```bash
az postgres arc-server show -n pg1 --k8s-namespace namespace --use-k8s
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
## <a name="az-postgres-arc-server-list"></a>az postgres arc-server list
Lister les groupes de serveurs PostgreSQL Hyperscale avec Azure Arc.
```bash
az postgres arc-server list 
```
### <a name="examples"></a>Exemples
Lister les groupes de serveurs PostgreSQL Hyperscale avec Azure Arc.
```bash
az postgres arc-server list --k8s-namespace namespace --use-k8s
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
