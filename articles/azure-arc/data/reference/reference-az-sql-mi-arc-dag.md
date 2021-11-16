---
title: Référence az sql mi-arc dag
titleSuffix: Azure Arc-enabled data services
description: Article de référence sur les commandes az sql mi-arc dag.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: f568f7086f2730bde53a1cd4dd31cf25092c5dd7
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848645"
---
# <a name="az-sql-mi-arc-dag"></a>az sql mi-arc dag
## <a name="commands"></a>Commandes
| Commande | Description|
| --- | --- |
[az sql mi-arc dag create](#az-sql-mi-arc-dag-create) | Créer une ressource personnalisée de groupe de disponibilité distribué
[az sql mi-arc dag delete](#az-sql-mi-arc-dag-delete) | Supprimer une ressource personnalisée de groupe de disponibilité distribuée sur une instance sqlmi.
[az sql mi-arc dag show](#az-sql-mi-arc-dag-show) | Afficher une ressource personnalisée de groupe de disponibilité distribué.
## <a name="az-sql-mi-arc-dag-create"></a>az sql mi-arc dag create
Créer une ressource personnalisée de groupe de disponibilité distribuée pour créer un groupe de disponibilité distribué
```bash
az sql mi-arc dag create 
```
### <a name="examples"></a>Exemples
Exemple 1 : créer une ressource personnalisée de groupe de disponibilité distribué dagCr1 pour créer un groupe de disponibilité distribué dagName1 entre l’instance sqlmi locale sqlmi1 et l’instance sqlmi distante sqlmi2. Elle requiert le miroir principal sqlmi distant remotePrimary : 5022 et le fichier de certificat du point de terminaison de miroir distant sqlmi./sqlmi2.cer.
```bash
az sql mi-arc dag create --name dagCr1 --dag-name dagName1 --local-instance-name sqlmi1 --local-primary local --remote-instance-name sqlmi2 --remote-mirroring-url remotePrimary:5022 --remote-mirroring-cert-file ./sqlmi2.cer --use-k8s
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
## <a name="az-sql-mi-arc-dag-delete"></a>az sql mi-arc dag delete
Supprimer une ressource personnalisée de groupe de disponibilité distribuée sur une instance sqlmi pour supprimer un groupe de disponibilité distribué. Requiert un nom de ressource personnalisé.
```bash
az sql mi-arc dag delete 
```
### <a name="examples"></a>Exemples
Ex 1 : supprimer les ressources du groupe de disponibilité distribué nommé dagCr1.
```bash
az sql mi-arc dag delete --name dagCr1 --use-k8s
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
## <a name="az-sql-mi-arc-dag-show"></a>az sql mi-arc dag show
Afficher une ressource personnalisée de groupe de disponibilité distribué. Il requiert un nom de ressource personnalisé
```bash
az sql mi-arc dag show 
```
### <a name="examples"></a>Exemples
Ex 1 : afficher les ressources du groupe de disponibilité distribué nommé dagCr1.
```bash
az sql mi-arc dag show --name dagCr1 --use-k8s
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
