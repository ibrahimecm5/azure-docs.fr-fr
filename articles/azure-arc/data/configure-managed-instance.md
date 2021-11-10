---
title: Configurer une instance managée de SQL avec Azure Arc
description: Configurez une instance managée de SQL avec Azure Arc.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: d4dc8843804d48fb98fef7cd336e6b56f54f49ae
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131554352"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>Configurer une instance managée de SQL avec Azure Arc

Cet article explique comment configurer une instance managée de SQL avec Azure Arc.


## <a name="configure-resources-such-as-cores-memory"></a>Configurer des ressources telles que des cœurs, de la mémoire


### <a name="configure-using-cli"></a>Configurer via l’interface CLI

Vous pouvez modifier la configuration d’instances managées de SQL avec Azure Arc via l’interface CLI. Exécutez la commande suivante pour afficher les options de configuration. 

```azurecli
az sql mi-arc edit --help
```

Vous pouvez mettre à jour la mémoire et les cœurs disponibles pour une instance managée SQL avec Azure Arc à l’aide de la commande suivante :

```azurecli
az sql mi-arc edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s
```

L’exemple suivant définit les demandes et les limites de mémoire et de cœur de processeur.

```azurecli
az sql mi-arc edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n sqlinstance1 --k8s-namespace arc --use-k8s
```

Pour afficher les modifications apportées à l’instance managée SQL avec Azure Arc, vous pouvez utiliser les commandes suivantes pour afficher le fichier yaml de configuration :

```azurecli
az sql mi-arc show -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s
```

## <a name="configure-server-options"></a>Configurer les options de serveur

Vous pouvez définir les paramètres de configuration du serveur pour une instance managée de SQL avec Azure Arc après sa création. Cet article explique comment configurer des paramètres tels que l’activation ou la désactivation de l’agent mssql, et activer des indicateurs de trace spécifiques pour les scénarios de résolution des problèmes.


### <a name="enable-sql-server-agent"></a>Activer l’agent du Serveur SQL

Par défaut, l’agent du Serveur SQL est désactivé. Il peut être activé en exécutant la commande suivante :

```azurecli
az sql mi-arc edit -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s --agent-enabled true
```
Par exemple :
```azurecli
az sql mi-arc edit -n sqlinstance1 --k8s-namespace arc --use-k8s --agent-enabled true
```

### <a name="enable-trace-flags"></a>Activer les indicateurs de trace

Les indicateurs de trace peuvent être activés comme suit :
```azurecli
az sql mi-arc edit -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s --trace-flags "3614,1234" 
```

