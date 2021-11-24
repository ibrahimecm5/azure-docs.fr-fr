---
title: Obtenez des points de terminaison de connexion et créez des chaînes de connexion pour votre groupe de serveurs PostgreSQL Hyperscale avec Azure Arc
titleSuffix: Azure Arc-enabled data services
description: Récupérez des points de terminaison de connexion et créez des chaînes de connexion pour votre groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 35a7a184ae3b699d5ed4d25c1cb6b78269dcea11
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132279294"
---
# <a name="get-connection-endpoints--create-the-connection-strings-for-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Récupération des points de terminaison de connexion et création des chaînes de connexion d’un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc

Cet article explique comment récupérer les points de terminaison de connexion d’un groupe de serveurs et comment former les chaînes de connexion utilisables avec des applications et des outils.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-connection-end-points"></a>Obtenez les points de terminaison de connexion :

Exécutez la commande suivante :
```azurecli
az postgres arc-server endpoint list -n <server group name> --k8s-namespace <namespace> --use-k8s
```
Par exemple :
```azurecli
az postgres arc-server endpoint list -n postgres01 --k8s-namespace arc --use-k8s
```

Elle retourne la liste des points de terminaison : le point de terminaison PostgreSQL, le tableau de bord Recherche dans les journaux (Kibana) et le tableau de bord Métriques (Grafana). Par exemple : 

```output
{
  "instances": [
    {
      "endpoints": [
        {
          "description": "PostgreSQL Instance",
          "endpoint": "postgresql://postgres:<replace with password>@12.345.567.89:5432"
        },
        {
          "description": "Log Search Dashboard",
          "endpoint": "https://23.456.78.99:5601/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))"
        },
        {
          "description": "Metrics Dashboard",
          "endpoint": "https://34.567.890.12:3000/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01"
        }
      ],
      "engine": "PostgreSql",
      "name": "postgres01"
    }
  ],
  "namespace": "arc"
}
```

Utilisez ces points de terminaison pour :

- Former vos chaînes de connexion et vous connecter avec vos outils clients ou vos applications clientes
- Accéder aux tableaux de bord Grafana et Kibana depuis votre navigateur

Par exemple, vous pouvez utiliser le point de terminaison nommé _PostgreSQL Instance_ pour vous connecter avec psql à votre groupe de serveurs :
```console
psql postgresql://postgres:MyPassworkd@12.345.567.89:5432
psql (10.14 (Ubuntu 10.14-0ubuntu0.18.04.1), server 12.4 (Ubuntu 12.4-1.pgdg16.04+1))
WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=#
```
> [!NOTE]
>
> - Le mot de passe de l’utilisateur _postgres_ indiqué dans le point de terminaison nommé « _Instance PostgreSQL_ » est le mot de passe que vous avez choisi lors du déploiement du groupe de serveurs.


## <a name="from-cli-with-kubectl"></a>À partir de l’interface CLI avec kubectl
```console
kubectl get postgresqls/<server group name> -n <namespace name>
```

Par exemple :
```azurecli
kubectl get postgresqls/postgres01 -n arc
```

Ces commandes produisent une sortie similaire à celle-ci. Vous pouvez utiliser ces informations pour former vos chaînes de connexion :

```console
NAME         STATE   READY-PODS   PRIMARY-ENDPOINT     AGE
postgres01   Ready   3/3          12.345.567.89:5432   9d
```

## <a name="form-connection-strings"></a>Formation de chaînes de connexion

Utilisez les exemples de chaînes de connexion ci-dessous pour votre groupe de serveurs. Copiez-collez-les, puis personnalisez-les en fonction des besoins :

### <a name="adonet"></a>ADO.NET

```ado.net
Server=192.168.1.121;Database=postgres;Port=24276;User Id=postgres;Password={your_password_here};Ssl Mode=Require;`
```

### <a name="c-libpq"></a>C++ (libpq)

```cpp
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="jdbc"></a>JDBC

```jdbc
jdbc:postgresql://192.168.1.121:24276/postgres?user=postgres&password={your_password_here}&sslmode=require
```

### <a name="nodejs"></a>Node.js

```node.js
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="php"></a>PHP

```php
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="psql"></a>psql

```psql
psql "host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require"
```

### <a name="python"></a>Python

```python
dbname='postgres' user='postgres' host='192.168.1.121' password='{your_password_here}' port='24276' sslmode='true'
```

### <a name="ruby"></a>Ruby

```ruby
host=192.168.1.121; dbname=postgres user=postgres password={your_password_here} port=24276 sslmode=require
```

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [effectuer un scale-out (ajout de nœuds Worker)](scale-out-in-postgresql-hyperscale-server-group.md) de votre groupe de serveurs
- Découvrez comment [effectuer un scale-up ou un scale-down (augmentation/diminution de la mémoire/des vCores)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) de votre groupe de serveurs
