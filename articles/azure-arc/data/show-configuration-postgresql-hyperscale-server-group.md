---
title: Afficher la configuration d’un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc
titleSuffix: Azure Arc-enabled data services
description: Afficher la configuration d’un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: bf0972342cf70b542bef01a070f777dec615c13b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564363"
---
# <a name="show-the-configuration-of-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Afficher la configuration d’un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc

Cet article explique comment afficher la configuration de vos groupes de serveurs. Pour cela, il anticipe certaines questions que vous pourriez vous poser et y répond. Il peut parfois y avoir plusieurs réponses valides. Cet article présente les réponses les plus courantes ou les plus utiles. Il regroupe ces questions par thème :

- Du point de vue de Kubernetes
- Du point de vue des services de données activés pour Azure Arc

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-a-kubernetes-point-of-view"></a>Du point de vue de Kubernetes

### <a name="what-are-the-postgres-server-groups-deployed-and-how-many-pods-are-they-using"></a>Quels sont les groupes de serveurs Postgres déployés et combien de pods utilisent-ils ?

Listez les ressources Kubernetes de type Postgres. Exécutez la commande suivante :

```console
kubectl get postgresqls -n <namespace>
```

La sortie de cette commande montre la liste des groupes de serveurs créés. Pour chacun, il indique le nombre de pods. Exemple :

```output
NAME         STATE   READY-PODS   PRIMARY-ENDPOINT     AGE
postgres01   Ready   5/5          20.101.12.221:5432   12d
```

Cet exemple montre qu’un groupe de serveurs est créé. Il s’exécute sur cinq pods : un coordinateur et quatre Workers.

### <a name="what-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Quels pods sont utilisés par PostgreSQL Hyperscale activé pour Azure Arc ?

Exécutez :

```console
kubectl get pods -n <namespace>
```

La commande retourne la liste des pods. Vous voyez les pods utilisés par vos groupes de serveurs d’après les noms que vous avez donnés à ces groupes de serveurs. Exemple :

```console 
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-4jrtl   1/1     Running   0          12d
control-kz8gh        2/2     Running   0          12d
controldb-0          2/2     Running   0          12d
logsdb-0             3/3     Running   0          12d
logsui-qjkgz         3/3     Running   0          12d
metricsdb-0          2/2     Running   0          12d
metricsdc-4jslw      2/2     Running   0          12d
metricsdc-4tl2g      2/2     Running   0          12d
metricsdc-fkxv2      2/2     Running   0          12d
metricsdc-hs4h5      2/2     Running   0          12d
metricsdc-tvz22      2/2     Running   0          12d
metricsui-7pcch      2/2     Running   0          12d
postgres01c0-0       3/3     Running   0          2d19h
postgres01w0-0       3/3     Running   0          2d19h
postgres01w0-1       3/3     Running   0          2d19h
postgres01w0-2       3/3     Running   0          2d19h
postgres01w0-3       3/3     Running   0          2d19h
```

### <a name="what-pod-is-used-for-what-role-in-the-server-group"></a>Quel pod est utilisé pour quel rôle dans le groupe de serveurs ?

Les noms des pods avec le suffixe `c` représentent un nœud coordinateur. Tout nom de nœud suffixé par `w` est un nœud Worker.
Par exemple, les cinq pods qui hébergent le groupe de serveurs :
- `postgres01c0-0` sur le nœud coordinateur
- `postgres01w0-0` : un nœud Worker
- `postgres01w0-1` : un nœud Worker
- `postgres01w0-2` : un nœud Worker
- `postgres01w0-3` : un nœud Worker

Vous pouvez ignorer pour l’instant le caractère `0` affiché après `c` et `w` (ServerGroupName`c0`-x ou ServerGroupName`w0`-x). Il s’agira d’un scoring utilisé lorsque le produit offrira des expériences haute disponibilité.


### <a name="what-is-the-status-of-the-pods"></a>Quel est l’état des pods ?

Exécutez `kubectl get pods -n <namespace>` et examinez la colonne `STATUS`.

### <a name="what-persistent-volume-claims-pvcs-are-being-used"></a>Quelles revendications de volume persistant sont utilisées ? 

Pour comprendre quelles revendications de volume persistant sont utilisées, et lesquelles sont utilisées spécifiquement pour les données, les journaux et les sauvegardes, exécutez : 

```console
kubectl get pvc -n <namespace>
```

Par défaut, le préfixe du nom d’une revendication de volume persistant indique son utilisation :

- `backups-`... : est une revendication de volume persistant utilisée pour les sauvegardes
- `data-`... : est une revendication de volume persistant utilisée pour les fichiers de données
- `logs-`... : est une revendication de volume persistant utilisée pour les journaux de transactions/les fichiers WAL

Exemple :

```output
NAME                                            STATUS   VOLUME              CAPACITY   ACCESS MODES   STORAGECLASS    AGE
backups-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    local-pv-485e37db   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    local-pv-9d3d4a15   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    local-pv-7b8dd819   1938Gi     RWO            local-storage   6d6h
...
data-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-3c1a8cc5   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-8303ab19   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-55572fe6   1938Gi     RWO            local-storage   6d6h
...
logs-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-5e852b76   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-55d309a7   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-5ccd02e6   1938Gi     RWO            local-storage   6d6h
...
```

### <a name="how-much-memory-and-vcores-are-being-used-and-what-extensions-were-created-for-a-server-group"></a>Quelle quantité de mémoire et de vCores est utilisée, et quelles extensions ont été créées pour un groupe de serveurs ?

Utilisez kubectl pour décrire une ressource Postgres. Pour ce faire, vous avez besoin de son genre (nom de la ressource Kubernetes (CRD) pour Postgres dans Azure Arc) et du nom du groupe de serveurs.

Le format général de cette commande est le suivant :

```console
kubectl describe <CRD name>/<server group name> -n <namespace>
```

Exemple :

```console
kubectl describe postgresql/postgres01 -n arc
```

Cette commande montre la configuration du groupe de serveurs :

```output
Name:         postgres01
Namespace:    arc
Labels:       <none>
Annotations:  <none>
API Version:  arcdata.microsoft.com/v1beta2
Kind:         PostgreSql
Metadata:
  Creation Timestamp:  2021-10-13T01:09:25Z
  Generation:          29
  Managed Fields:
    API Version:  arcdata.microsoft.com/v1beta2
    Fields Type:  FieldsV1
    fieldsV1:
      f:spec:
        .:
        f:dev:
        f:engine:
          .:
          f:extensions:
          f:version:
        f:scale:
          .:
          f:replicas:
          f:workers:
        f:scheduling:
          .:
          f:default:
            .:
            f:resources:
              .:
              f:requests:
                .:
                f:memory:
          f:roles:
            .:
            f:coordinator:
              .:
              f:resources:
                .:
                f:limits:
                  .:
                  f:cpu:
                  f:memory:
                f:requests:
                  .:
                  f:cpu:
                  f:memory:
            f:worker:
              .:
              f:resources:
                .:
                f:limits:
                  .:
                  f:cpu:
                  f:memory:
                f:requests:
                  .:
                  f:cpu:
                  f:memory:
        f:services:
          .:
          f:primary:
            .:
            f:port:
            f:type:
        f:storage:
          .:
          f:backups:
            .:
            f:volumes:
          f:data:
            .:
            f:volumes:
          f:logs:
            .:
            f:volumes:
    Manager:      OpenAPI-Generator
    Operation:    Update
    Time:         2021-10-22T22:37:51Z
    API Version:  arcdata.microsoft.com/v1beta2
    Fields Type:  FieldsV1
    fieldsV1:
      f:IsValid:
      f:spec:
        f:scale:
          f:syncReplicas:
      f:status:
        .:
        f:logSearchDashboard:
        f:metricsDashboard:
        f:observedGeneration:
        f:podsStatus:
        f:primaryEndpoint:
        f:readyPods:
        f:state:
    Manager:         unknown
    Operation:       Update
    Time:            2021-10-22T22:37:53Z
  Resource Version:  1541521
  UID:               23565e53-2e7a-4cd6-8f80-3a79397e1d7a
Spec:
  Dev:  false
  Engine:
    Extensions:
      Name:   citus
    Version:  12
  Scale:
    Replicas:       1
    Sync Replicas:  0
    Workers:        4
  Scheduling:
    Default:
      Resources:
        Requests:
          Memory:  256Mi
    Roles:
      Coordinator:
        Resources:
          Limits:
            Cpu:     2
            Memory:  1Gi
          Requests:
            Cpu:     1
            Memory:  256Mi
      Worker:
        Resources:
          Limits:
            Cpu:     2
            Memory:  1Gi
          Requests:
            Cpu:     1
            Memory:  256Mi
  Services:
    Primary:
      Port:  5432
      Type:  LoadBalancer
  Storage:
    Backups:
      Volumes:
        Size:  5Gi
    Data:
      Volumes:
        Class Name:  managed-premium
        Size:        5Gi
    Logs:
      Volumes:
        Class Name:  managed-premium
        Size:        5Gi
Status:
  Log Search Dashboard:  https://12.235.78.99:5601/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))
  Metrics Dashboard:     https://12.346.578.99:3000/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01
  Observed Generation:   29
  Pods Status:
    Conditions:
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  Initialized
      Last Transition Time:  2021-10-22T22:40:55.000000Z
      Status:                True
      Type:                  Ready
      Last Transition Time:  2021-10-22T22:40:55.000000Z
      Status:                True
      Type:                  ContainersReady
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  PodScheduled
    Name:                    postgres01w0-1
    Role:                    worker
    Conditions:
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  Initialized
      Last Transition Time:  2021-10-22T22:42:41.000000Z
      Status:                True
      Type:                  Ready
      Last Transition Time:  2021-10-22T22:42:41.000000Z
      Status:                True
      Type:                  ContainersReady
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  PodScheduled
    Name:                    postgres01c0-0
    Role:                    coordinator
    Conditions:
      Last Transition Time:  2021-10-22T22:37:54.000000Z
      Status:                True
      Type:                  Initialized
      Last Transition Time:  2021-10-22T22:40:52.000000Z
      Status:                True
      Type:                  Ready
      Last Transition Time:  2021-10-22T22:40:52.000000Z
      Status:                True
      Type:                  ContainersReady
      Last Transition Time:  2021-10-22T22:37:54.000000Z
      Status:                True
      Type:                  PodScheduled
    Name:                    postgres01w0-3
    Role:                    worker
    Conditions:
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  Initialized
      Last Transition Time:  2021-10-22T22:38:35.000000Z
      Status:                True
      Type:                  Ready
      Last Transition Time:  2021-10-22T22:38:35.000000Z
      Status:                True
      Type:                  ContainersReady
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  PodScheduled
    Name:                    postgres01w0-0
    Role:                    worker
    Conditions:
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  Initialized
      Last Transition Time:  2021-10-22T22:42:40.000000Z
      Status:                True
      Type:                  Ready
      Last Transition Time:  2021-10-22T22:42:40.000000Z
      Status:                True
      Type:                  ContainersReady
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  PodScheduled
    Name:                    postgres01w0-2
    Role:                    worker
  Primary Endpoint:          20.101.12.221:5432
  Ready Pods:                5/5
  Running Version:           v1.1.0_2021-10-12_patching_0bcb7bcaf
  State:                     Ready
Events:                      <none>
```

####  <a name="interpret-the-configuration-information"></a>Interpréter les informations de configuration

Détaillons quelques points d’intérêt spécifiques dans la description du `servergroup` présentée ci-dessus. Que nous dit-elle de ce groupe de serveurs ?

- Il utilise la version 12 de Postgres et exécute l’extension Citus : 

   ```output
   Spec:
     Dev:  false
     Engine:
       Extensions:
         Name:   citus
       Version:  12
   ```

- Il a été créé le 13 octobre 2021 :

   ```output
     Metadata:
     Creation Timestamp:  2021-10-13T01:09:25Z
   ```

- Quatre nœuds Worker sont utilisés :

   ```output
        Scale:
          Replicas:       1
          Sync Replicas:  0
          Workers:        4
   ```

- Configuration des ressources : dans cet exemple, son coordinateur et ses Workers ont 256Mi de mémoire garantie. Le coordinateur et les nœuds Worker ne peuvent pas utiliser plus que 1Gi de mémoire. Le coordinateur et les Workers sont assurés d’un vCore et ne peuvent pas consommer plus de deux vCores. 

   ```console
        Scheduling:
       Default:
         Resources:
           Requests:
             Memory:  256Mi
       Roles:
         Coordinator:
           Resources:
             Limits:
               Cpu:     2
               Memory:  1Gi
             Requests:
               Cpu:     1
               Memory:  256Mi
         Worker:
           Resources:
             Limits:
               Cpu:     2
               Memory:  1Gi
             Requests:
               Cpu:     1
               Memory:  256Mi
   ```

 - Quel est l’état du groupe de serveurs ? Est-il disponible pour mes applications ? 
 
   Oui, tous les pods (le nœud coordinateur et les quatre nœuds Worker sont prêts)

   ```console
   Ready Pods:                5/5
   ```

## <a name="from-an-azure-arc-enabled-data-services-point-of-view"></a>Du point de vue des services de données activés pour Azure Arc

Utilisez les commandes Azure CLI.

### <a name="what-are-the-postgres-server-groups-deployed-and-how-many-workers-are-they-using"></a>Quels sont les groupes de serveurs Postgres déployés et combien de Workers utilisent-ils ?
Exécutez la commande suivante : 

   ```azurecli
   az postgres arc-server list --k8s-namespace <namespace> --use-k8s
   ```

Elle répertorie les groupes de serveurs qui sont déployés.

   ```output
   [
     {
       "name": "postgres01",
       "replicas": 1,
       "state": "Ready",
       "workers": 4
     }
   ]
   ```

Elle indique également le nombre de nœuds Worker que le groupe de serveurs utilise. Chaque nœud Worker est déployé sur un pod auquel vous devez ajouter un pod utilisé pour héberger le nœud coordinateur.

### <a name="how-much-memory-and-vcores-are-being-used-and-what-extensions-were-created-for-a-group"></a>Quelle quantité de mémoire et de vCores est utilisée, et quelles extensions ont été créées pour un groupe ?

Exécutez une des commandes suivantes.

```azurecli
az postgres arc-server show -n <server group name>  --k8s-namespace <namespace> --use-k8s
```

Par exemple :

```azurecli
az postgres arc-server show -n postgres01 --k8s-namespace arc --use-k8s
```

Retourne les informations dans un format et avec un contenu similaire à celui retourné par kubectl. Utilisez l’outil de votre choix pour interagir avec le système.

## <a name="next-steps"></a>Étapes suivantes
- [Découvrir les concepts de PostgreSQL Hyperscale activé pour Azure Arc](concepts-distributed-postgres-hyperscale.md)
- [Découvrir le scale-out (ajout des nœuds worker) d’un groupe de serveurs](scale-out-in-postgresql-hyperscale-server-group.md)
- [Découvrir le scale-up/scale-down (augmentation ou réduction de la mémoire et/ou des vCores) d’un groupe de serveurs](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- [Explorer la configuration du stockage](storage-configuration.md)
- [Lisez comment surveiller une instance de base de données](monitor-grafana-kibana.md)
- [Utiliser les extensions PostgreSQL dans votre groupe de serveurs PostgreSQL Hyperscale activé pour Azure Arc](using-extensions-in-postgresql-hyperscale-server-group.md)
- [Configurer la sécurité pour votre groupe de serveurs PostgreSQL Hyperscale activé pour Azure Arc](configure-security-postgres-hyperscale.md)
