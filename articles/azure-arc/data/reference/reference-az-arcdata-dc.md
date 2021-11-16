---
title: Informations de référence sur les commandes az arcdata dc
titleSuffix: Azure Arc-enabled data services
description: Article de référence sur les commandes az arcdata dc.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 23cbe9c98e3c0ad86704061dadfad0e83f627ac3
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852957"
---
# <a name="az-arcdata-dc"></a>az arcdata dc
## <a name="commands"></a>Commandes
| Commande | Description|
| --- | --- |
[az arcdata dc create](#az-arcdata-dc-create) | Créez un contrôleur de données.
[az arcdata dc upgrade](#az-arcdata-dc-upgrade) | Mettez à niveau le contrôleur de données.
[az arcdata dc list-upgrades](#az-arcdata-dc-list-upgrades) | Affichez la liste des versions de mise à niveau disponibles.
[az arcdata dc delete](#az-arcdata-dc-delete) | Supprimez un contrôleur de données.
[az arcdata dc endpoint](reference-az-arcdata-dc-endpoint.md) | Commandes relatives aux points de terminaison.
[az arcdata dc status](reference-az-arcdata-dc-status.md) | Commandes relatives à l’état.
[az arcdata dc config](reference-az-arcdata-dc-config.md) | Commandes de configuration.
[az arcdata dc debug](reference-az-arcdata-dc-debug.md) | Déboguez un contrôleur de données.
[az arcdata dc export](#az-arcdata-dc-export) | Exportez les métriques, les journaux ou l’utilisation.
[az arcdata dc upload](#az-arcdata-dc-upload) | Téléchargez le fichier de données exporté.
## <a name="az-arcdata-dc-create"></a>az arcdata dc create
Créer un contrôleur de données : la configuration Kube est requise sur votre système, ainsi que les informations d’identification pour les tableaux de bord de surveillance fournis par les variables d’environnement suivantes : AZDATA_LOGSUI_USERNAME et AZDATA_LOGSUI_PASSWORD pour le tableau de bord des journaux, et AZDATA_METRICSUI_USERNAME et AZDATA_METRICSUI_PASSWORD pour le tableau de bord des métriques. Les variables d’environnement AZDATA_USERNAME et AZDATA_PASSWORD seront utilisées comme solution de secours si des ensembles de variables d’environnement sont manquants.
```bash
az arcdata dc create 
```
### <a name="examples"></a>Exemples
Déployez un contrôleur de données connecté indirectement.
```bash
az arcdata dc create --name name --k8s-namespace namespace --connectivity-mode indirect --resource-group group  --location location --subscription subscription --use-k8s
```
Déployez un contrôleur de données connecté directement.
```bash
az arcdata dc create --name name  --connectivity-mode direct --resource-group group  --location location --subscription subscription  --custom-location custom-location         
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
## <a name="az-arcdata-dc-upgrade"></a>az arcdata dc upgrade
Mettez à niveau le contrôleur de données vers la version souhaitée spécifiée.  Si vous ne spécifiez pas la version souhaitée, une tentative de mise à niveau vers la version la plus récente sera effectuée. Si vous n’êtes pas sûr de la version souhaitée, vous pouvez utiliser la commande list-upgrades pour afficher les versions disponibles, ou utiliser l’argument --dry-run pour indiquer la version à utiliser.
```bash
az arcdata dc upgrade 
```
### <a name="examples"></a>Exemples
Mise à niveau du contrôleur de données.
```bash
az arcdata dc upgrade --k8s-namespace namespace --use-k8s
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
## <a name="az-arcdata-dc-list-upgrades"></a>az arcdata dc list-upgrades
Tente de répertorier les versions disponibles dans le registre d’images Docker pour la mise à niveau. La configuration Kubernetes est requise sur votre système avec les variables d’environnement suivantes ['AZDATA_USERNAME', 'AZDATA_PASSWORD'].
```bash
az arcdata dc list-upgrades 
```
### <a name="examples"></a>Exemples
Mise à niveau du contrôleur de données.
```bash
az arcdata dc list-upgrades --k8s-namespace namespace --use-k8s            
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
## <a name="az-arcdata-dc-delete"></a>az arcdata dc delete
Supprimez le contrôleur de données. La configuration de Kube est requise sur votre système.
```bash
az arcdata dc delete 
```
### <a name="examples"></a>Exemples
Supprimez un contrôleur de données connecté indirectement.
```bash
az arcdata dc delete --name name --k8s-namespace namespace --use-k8s
```
Supprimez un contrôleur de données connecté directement.
```bash
az arcdata dc delete --name name --resource-group resource-group            
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
## <a name="az-arcdata-dc-export"></a>az arcdata dc export
Exportez les métriques, les journaux ou l’utilisation dans un fichier.
```bash
az arcdata dc export 
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
## <a name="az-arcdata-dc-upload"></a>az arcdata dc upload
Téléchargez le fichier de données exporté à partir d’un contrôleur de données vers Azure.
```bash
az arcdata dc upload 
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
