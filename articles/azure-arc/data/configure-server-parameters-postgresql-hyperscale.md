---
title: Configurer des paramètres de serveur du moteur Postgres pour votre groupe de serveurs PostgreSQL Hyperscale sur Azure Arc
titleSuffix: Azure Arc-enabled data services
description: Configurer des paramètres de serveur du moteur Postgres pour votre groupe de serveurs PostgreSQL Hyperscale sur Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 609ed203c794447f4fd0e0cd1a5267186a8eb67e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301738"
---
# <a name="set-the-database-engine-settings-for-azure-arc-enabled-postgresql-hyperscale"></a>Définir les paramètres du moteur de base de données pour PostgreSQL Hyperscale activé par Azure Arc

Ce document décrit les étapes permettant de définir les paramètres du moteur de base de données de votre groupe de serveurs PostgreSQL Hyperscale sur des valeurs personnalisées (autres que par défaut). Pour plus d’informations sur les paramètres du moteur de base de données qui peuvent être définis et leur valeur par défaut, consultez la documentation de PostgreSQL [ici](https://www.postgresql.org/docs/current/runtime-config.html).

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!NOTE]
> La préversion ne prend pas en charge la définition des paramètres suivants : 
>
> - `archive_command`
> - `archive_timeout`
> - `log_directory`
> - `log_file_mode`
> - `log_filename`
> - `restore_command`
> - `shared_preload_libraries`
> - `synchronous_commit`
> - `ssl`
> - `wal_level`

## <a name="syntax"></a>Syntaxe

Le format général de la commande permettant de configurer les paramètres du moteur de base de données est le suivant :

```azurecli
az postgres arc-server edit -n <server group name>, [{--engine-settings, -e}] [{--replace-settings , --re}] {'<parameter name>=<parameter value>, ...'} --k8s-namespace <namespace> --use-k8s
```

## <a name="show-current-custom-values-if-they-have-been-set"></a>Affichage des valeurs personnalisées actuelles si elles ont été définies

**Avec une commande CLI az :**

```azurecli
az postgres arc-server show -n <server group name> --k8s-namespace <namespace> --use-k8s
```

Par exemple :

```azurecli
az postgres arc-server show -n postgres01 --k8s-namespace arc --use-k8s 
```


**Avec une commande kubectl :**
```console
   kubectl describe postgresql <server group name> -n <namespace name>
   ```

   Par exemple :

   ```console
   kubectl describe postgresql postgres01 -n arc
```

Ces deux commandes retournent les spécifications du groupe de serveurs, parmi lesquelles apparaissent les paramètres que vous avez définis. S’il n’existe pas de section engine\settings, cela signifie que tous les paramètres s’exécutent sur la base de leur valeur par défaut :

:::row:::
    :::column:::
        Voici un exemple de sortie dans le cas où aucune valeur personnalisée n’a été définie pour aucun des paramètres du moteur Postgres. Les spécifications ne présentent pas de section engine\settings.
    :::column-end:::
    :::column:::
        ```console
          ...
          "spec": {
            "dev": false,
            "engine": {
              "extensions": [
                {
                  "name": "citus"
                }
              ],
              "version": 12
            },
            "scale": {
              "replicas": 1,
              "syncReplicas": "0",
          "workers": 4
            },
            ...
        ```
        :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        Example of an output when custom values have been set for some of Postgres engine setting. The specs do show a section engine\settings.
    :::column-end:::
    :::column:::
        ```console
             ...
                Spec:
                  Dev:  false
                  Engine:
                    Extensions:
                      Name:  citus
                    Settings:
                      Default:
                        max_connections:  51
                      Roles:
                        Coordinator:
                          max_connections:  53
                        Worker:
                          max_connections:  52
                    Version:                12
                  Scale:
                    Replicas:       1
                    Sync Replicas:  0
                    Workers:        4
            ...
            ```
    :::column-end:::
:::row-end:::


The default value is, refer to the PostgreSQL documentation [here](https://www.postgresql.org/docs/current/runtime-config.html).



## Set custom values for engine settings

### Set a single parameter

**On both coordinator and worker roles:**

General syntax of the command:
```azurecli
az postgres arc-server edit -n <servergroup name> --engine-settings  '<ParameterName>=<CustomParameterValue>' --k8s-namespace <namespace> --use-k8s
```

Par exemple :
```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  'max_connections=51' --k8s-namespace arc --use-k8s
```

**Sur le rôle de travail uniquement :**

Syntaxe générale de la commande :
```azurecli
az postgres arc-server edit -n <servergroup name> --worker-settings '<ParameterName>=<CustomParameterValue>' --k8s-namespace <namespace> --use-k8s
```

Par exemple :
```azurecli
az postgres arc-server edit -n postgres01 --worker-settings 'max_connections=52' --k8s-namespace arc --use-k8s
```

**Sur le rôle de coordination uniquement :**

Syntaxe générale de la commande :
```azurecli
az postgres arc-server edit -n <servergroup name> --coordinator-settings '<ParameterName>=<CustomParameterValue>' --k8s-namespace <namespace> --use-k8s
```

Exemple :
```azurecli
az postgres arc-server edit -n postgres01 --coordinator-settings 'max_connections=53' --k8s-namespace arc --use-k8s
```



### <a name="set-multiple-parameters-with-a-single-command"></a>Définir plusieurs paramètres avec une seule commande

**Sur le rôle de coordination et le rôle de travail :**  
 
Syntaxe générale de la commande :
```azurecli
az postgres arc-server edit -n <servergroup name> --engine-settings  '<ParameterName1>=<CustomParameterValue1>, ..., <ParameterNameN>=<CustomParameterValueN>' --k8s-namespace <namespace> --use-k8s
```

Par exemple :
```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  'shared_buffers=8MB, max_connections=60' --k8s-namespace arc --use-k8s
```

**Sur le rôle de travail uniquement :**

Syntaxe générale de la commande :
```azurecli
az postgres arc-server edit -n <servergroup name> --worker-settings '<ParameterName1>=<CustomParameterValue1>, ..., <ParameterNameN>=<CustomParameterValueN>' --k8s-namespace <namespace> --use-k8s
```

Par exemple :
```azurecli
az postgres arc-server edit -n postgres01 --worker-settings 'shared_buffers=8MB, max_connections=60' --k8s-namespace arc --use-k8s
```

**Sur le rôle de coordination uniquement :**

Syntaxe générale de la commande :
```azurecli
az postgres arc-server edit -n <servergroup name> --coordinator-settings '<ParameterName1>=<CustomParameterValue1>, ..., <ParameterNameN>=<CustomParameterValueN>' --k8s-namespace <namespace> --use-k8s
```

Par exemple :
```azurecli
az postgres arc-server edit -n postgres01 --coordinator-settings 'shared_buffers=8MB, max_connections=60' --k8s-namespace arc --use-k8s
```

### <a name="reset-one-parameter-to-its-default-value"></a>Restauration de la valeur par défaut d’un paramètre

**Sur le rôle de coordination et le rôle de travail :**

Syntaxe générale de la commande :
```azurecli
az postgres arc-server edit -n <servergroup name> --engine-settings '<ParameterName>='  --coordinator-settings '<ParameterName>=' --worker-settings '<ParameterName>=' --k8s-namespace <namespace> --use-k8s
```
Par exemple :
```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  'shared_buffers='  --coordinator-settings 'shared_buffers=' --worker-settings 'shared_buffers=' --k8s-namespace arc --use-k8s
```

**Sur le rôle de coordination uniquement :**

Syntaxe générale de la commande :
```azurecli
az postgres arc-server edit -n <servergroup name> --coordinator-settings '<ParameterName>=' --k8s-namespace <namespace> --use-k8s
```
Par exemple :
```azurecli
az postgres arc-server edit -n postgres01 --coordinator-settings 'shared_buffers=' --k8s-namespace arc --use-k8s
```

**Sur le rôle de travail uniquement :**

Syntaxe générale de la commande :
```azurecli
az postgres arc-server edit -n <servergroup name> --worker-settings '<ParameterName>=' --k8s-namespace <namespace> --use-k8s
```
Exemple :
```azurecli
az postgres arc-server edit -n postgres01 --worker-settings 'shared_buffers=' --k8s-namespace arc --use-k8s
````

### <a name="reset-all-parameters-to-their-default-values"></a>Rétablir les valeurs par défaut de tous les paramètres

**Sur le rôle de coordination et le rôle de travail :**

Syntaxe générale de la commande :
```azurecli
az postgres arc-server edit -n <servergroup name> --engine-settings  `'`' --worker-settings `'`' --coordinator-settings `'`' --replace-settings --k8s-namespace <namespace> --use-k8s
```

Par exemple :
```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  `'`' --worker-settings `'`' --coordinator-settings `'`'  --replace-settings --k8s-namespace arc --use-k8s
```

**Sur le rôle de coordination uniquement :**

Syntaxe générale de la commande :
```azurecli
az postgres arc-server edit -n <servergroup name> --coordinator-settings `'`'  --replace-settings --k8s-namespace <namespace> --use-k8s

```
Par exemple :
```azurecli
az postgres arc-server edit -n postgres01 --coordinator-settings `'`'  --replace-settings --k8s-namespace arc --use-k8s
```

**Sur le rôle de travail uniquement :**

Syntaxe générale de la commande :
```azurecli
az postgres arc-server edit -n <servergroup name> --worker-settings `'`'  --replace-settings --k8s-namespace <namespace> --use-k8s
```
Exemple :
```azurecli
az postgres arc-server edit -n postgres01 --worker-settings `'`'  --replace-settings --k8s-namespace arc --use-k8s
```



## <a name="special-considerations"></a>Considérations spéciales

### <a name="set-a-parameter-which-value-contains-a-comma-space-or-special-character"></a>Définir un paramètre dont la valeur contient une virgule, une espace ou un caractère spécial

```azurecli
az postgres arc-server edit -n <server group name> --engine-settings  '<parameter name>="<parameter value>"' --k8s-namespace <namespace> --use-k8s
```

Exemple :

```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  'custom_variable_classes = "plpgsql,plperl"' --k8s-namespace <namespace> --use-k8s
```

### <a name="pass-an-environment-variable-in-a-parameter-value"></a>Passer une variable d’environnement dans une valeur de paramètre

Pour que la variable d’environnement ne soit pas résolue avant sa définition, elle doit être encapsulée dans "’’".

Par exemple : 

```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  'search_path = "$user"' --k8s-namespace <namespace> --use-k8s
```

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [effectuer un scale-out (ajout de nœuds Worker)](scale-out-in-postgresql-hyperscale-server-group.md) de votre groupe de serveurs
- Découvrez comment [effectuer un scale-up ou un scale-down (augmentation/diminution de la mémoire/des vCores)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) de votre groupe de serveurs
