---
title: Mettre à niveau une instance gérée avec Azure Arc en mode indirect à l’aide de l’interface CLI
description: Mettre à niveau une instance gérée avec Azure Arc en mode indirect à l’aide de l’interface CLI
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: grrlgeek
ms.author: jeschult
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: daff60736185f448183d24207099670884402795
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566783"
---
# <a name="upgrade-an-indirect-mode-azure-arc-enabled-managed-instance-using-the-cli"></a>Mettre à niveau une instance gérée avec Azure Arc en mode indirect à l’aide de l’interface CLI

## <a name="prerequisites"></a>Prérequis

### <a name="install-tools"></a>Installer des outils

Avant de pouvoir effectuer les tâches de cet article, vous devez disposer des éléments suivants :

- L’interface [Azure CLI (az)](/cli/azure/install-azure-cli)
- L’[`arcdata`extension pour Azure CLI](install-arcdata-extension.md)

## <a name="limitations"></a>Limites

Le contrôleur de données Azure Arc doit être mis à niveau vers la nouvelle version avant de pouvoir mettre à niveau l’instance gérée.

Actuellement, une seule instance gérée peut être mise à niveau à la fois.

## <a name="upgrade-the-managed-instance"></a>Mettre à niveau l’instance gérée

Une test peut d’abord être exécuté. Cela permet de valider le schéma de version et de répertorier la ou les instances à mettre à niveau.

````cli
az sql mi-arc upgrade --name <instance name> --k8s-namespace <namespace> --dry-run --use-k8s
````

La sortie se présente comme suit :

```output
Preparing to upgrade sql sqlmi-1 in namespace arc to data controller version.
****Dry Run****1 instance(s) would be upgraded by this commandsqlmi-1 would be upgraded to 20211024.1.
```

### <a name="general-purpose"></a>Usage général

Pour mettre à niveau une instance gérée, utilisez la commande suivante :

````cli
az sql mi-arc upgrade --name <instance name> --desired-version <version> --k8s-namespace <namespace> --use-k8s
````

Exemple :

````cli
az sql mi-arc upgrade --name instance1 --target v1.0.0.20211028 --k8s-namespace arc1 --use-k8s
````

## <a name="monitor"></a>Superviser

Vous pouvez surveiller la progression de la mise à niveau à l’aide de kubectl ou de l’interface CLI.

### <a name="kubectl"></a>kubectl

```console
kubectl describe sqlmi --namespace <namespace>
```

### <a name="cli"></a>Interface de ligne de commande

```cli
az sql mi-arc show --name <instance name> --k8s-namespace <namespace> --use-k8s
```

### <a name="output"></a>Output

La sortie de la commande affiche les informations sur les ressources. Les informations de mise à niveau seront dans État.

Pendant la mise à niveau, ```State``` affiche ```Updating``` et ```Running Version``` correspond à la version actuelle :

```output
Status:
  Log Search Dashboard:  https://30.88.222.48:5601/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:sqlmi-1'))
  Metrics Dashboard:     https://30.88.221.32:3000/d/40q72HnGk/sql-managed-instance-metrics?var-hostname=sqlmi-1-0
  Observed Generation:   2
  Primary Endpoint:      30.76.129.38,1433
  Ready Replicas:        1/1
  Running Version:       v1.0.0_2021-07-30
  State:                 Updating
```

Une fois la mise à niveau terminée, ```State``` affiche ```Ready``` et ```Running Version``` correspond à la nouvelle version :

```output
Status:
  Log Search Dashboard:  https://30.88.222.48:5601/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:sqlmi-1'))
  Metrics Dashboard:     https://30.88.221.32:3000/d/40q72HnGk/sql-managed-instance-metrics?var-hostname=sqlmi-1-0
  Observed Generation:   2
  Primary Endpoint:      30.76.129.38,1433
  Ready Replicas:        1/1
  Running Version:       20211024.1
  State:                 Ready
```

## <a name="troubleshoot-upgrade-problems"></a>Résoudre les problèmes de mise à niveau

Si vous rencontrez des problèmes avec la mise à niveau, consultez le [Guide de résolution des problèmes](troubleshoot-guide.md).
