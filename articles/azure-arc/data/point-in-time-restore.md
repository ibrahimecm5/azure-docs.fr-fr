---
title: Restaurer une base de données dans SQL Managed Instance avec Azure Arc à une limite antérieure dans le temps
description: Explique comment restaurer une base de données à une limite spécifique dans le temps dans SQL Managed Instance avec Azure Arc.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 51235a67d6f00410ee2b3db753e892cf05992baa
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563261"
---
#  <a name="perform-a-point-in-time-restore"></a>Effectuer une limite de restauration dans le temps

Utilisez la limite de restauration dans le temps (PITR) pour créer une base de données en tant que copie d’une autre base de données datant d’un moment donné dans le passé dans la limite de la période de rétention. Cet article explique comment réaliser une limite de restauration dans le temps d’une base de données dans SQL Managed Instance avec Azure Arc.

La limite de restauration dans le temps peut restaurer une base de données :

- à partir d’une base de données existante ;
- Vers une nouvelle base de données sur la même instance gérée SQL avec Azure Arc

Vous pouvez restaurer une base de données à une limite dans le temps dans le cadre d’un paramètre de rétention préconfiguré.
vous pouvez vérifier le paramètre de rétention d’une instance gérée par Azure Arc SQL, comme suit :

Pour le mode de connexion **directe** :
```
az sql mi-arc show --name <SQL instance name> --resource-group <resource-group>
#Example
az sql mi-arc show --name sqlmi --resource-group myresourcegroup
```
Pour le mode de connexion **indirecte** :
```
az sql mi-arc show --name <SQL instance name> --k8s-namespace <SQL MI namespace> --use-k8s
#Example
az sql mi-arc show --name sqlmi --k8s-namespace arc --use-k8s
```

Actuellement, la limite de restauration dans le temps peut restaurer une base de données :

- à partir d’une base de données existante sur une instance ;
- vers une nouvelle base de données sur la même instance.

## <a name="automatic-backups"></a>Sauvegardes automatiques

L’instance gérée SQL avec Azure Arc a activé la fonctionnalité de sauvegardes automatiques intégrées. Chaque fois que vous créez ou restaurez une nouvelle base de données, l’instance gérée SQL avec Azure Arc lance immédiatement une sauvegarde complète et planifie automatiquement les sauvegardes différentielles et du journal des transactions. L’instance gérée SQL stocke ces sauvegardes dans la classe de stockage spécifiée pendant le déploiement. 

La restauration à un instant dans le passé permet de restaurer une base de données à un point spécifique dans le temps, au cours de la période de rétention. Pour restaurer une base de données à une limite spécifique dans le temps, les services de données avec Azure Arc appliquent les fichiers de sauvegarde dans un ordre spécifique. Par exemple :

1. Sauvegarde complète
2. Sauvegarde différentielle 
3. Une ou plusieurs sauvegardes de fichier journal

:::image type="content" source="media/point-in-time-restore/point-in-time-restore.png" alt-text="Restauration dans le temps":::

Actuellement, les sauvegardes complètes sont effectuées une fois par semaine, les sauvegardes différentielles sont effectuées toutes les 12 heures et les sauvegardes du journal des transactions toutes les 5 minutes.

## <a name="retention-period"></a>Période de rétention

La période de rétention par défaut pour une nouvelle instance gérée SQL avec Azure Arc est de sept jours et peut être ajustée avec des valeurs de 0 ou de 1-35 jours. La période de rétention peut être définie lors du déploiement de l’instance gérée SQL en spécifiant la propriété `--retention-days`. Les fichiers de sauvegarde antérieurs à la période de rétention configurée sont automatiquement supprimés.


## <a name="create-a-database-from-a-point-in-time-using-az"></a>Créer une base de données à partir d’une limite dans le temps avec az

```azurecli
az sql midb-arc restore --managed-instance <SQL managed instance> --name <source DB name> --dest-name <Name for new db> --k8s-namespace <namespace of managed instance> --time "YYYY-MM-DDTHH:MM:SSZ" --use-k8s
#Example
az sql midb-arc restore --managed-instance sqlmi1 --name Testdb1 --dest-name mynewdb --k8s-namespace arc --time "2021-10-29T01:42:14.00Z" --use-k8s
```

Vous pouvez également utiliser l’option `--dry-run` pour valider votre opération de restauration sans réellement restaurer la base de données. 

```azurecli
az sql midb-arc restore --managed-instance <SQL managed instance> --name <source DB name> --dest-name <Name for new db> --k8s-namespace <namespace of managed instance> --time "YYYY-MM-DDTHH:MM:SSZ" --use-k8s --dry-run
#Example
az sql midb-arc restore --managed-instance sqlmi1 --name Testdb1 --dest-name mynewdb --k8s-namespace arc --time "2021-10-29T01:42:14.00Z" --use-k8s --dry-run
```


## <a name="create-a-database-from-a-point-in-time-using-azure-data-studio"></a>Créer une base de données à partir d’une limite dans le temps avec Azure Data Studio

Vous pouvez également restaurer une base de données à un point dans le temps à partir d’Azure Data Studio comme suit :
1. Lancer Azure Data Studio
2. Vérifiez que vous disposez des extensions Arc nécessaires, comme décrit dans [Outils](install-client-tools.md).
3. Se connecter au contrôleur de données Azure Arc
4. Développez le nœud de contrôleur de données, cliquez avec le bouton droit sur l’instance gérée SQL avec Azure Arc, puis sélectionnez « Gérer ». Azure Data Studio lance le tableau de bord de l’instance gérée SQL.
5. Cliquez sur l’onglet **Sauvegardes** dans le tableau de bord
6. Vous devriez voir une liste de bases de données sur l’instance gérée SQL, ainsi que leurs fenêtres de temps de restauration les plus anciennes et récentes, ainsi qu’une icône pour lancer la **restauration**
7. Cliquez sur l’icône de la base de données à partir de laquelle vous souhaitez effectuer la restauration. Azure Data Studio lance un panneau sur le côté droit
8. Fournissez l’entrée requise dans le panneau, puis cliquez sur **Restaurer**

### <a name="monitor-progress"></a>Surveiller la progression

Lorsqu’une restauration est lancée, une tâche est créée dans le cluster Kubernetes qui exécute les opérations de restauration de sauvegardes complètes, différentielles et de journal. La progression de cette activité peut être analysée à partir de votre cluster Kubernetes, comme suit :

```console
kubectl get sqlmirestoretask -n <namespace>
#Example
kubectl get sqlmirestoretask -n arc
```

Vous pouvez obtenir plus de détails sur le travail en exécutant `kubectl describe` sur la tâche. Par exemple :

```console
kubectl describe sqlmirestoretask <nameoftask> -n <namespace>
```

## <a name="configure-retention-period"></a>Configurer la période de rétention

La période de rétention d’une instance gérée SQL avec Azure Arc peut être reconfigurée à partir de sa valeur d’origine, comme suit :

> [!WARNING] 
> Si vous réduisez la période de rétention actuelle, vous perdez la possibilité de restaurer à des points dans le temps antérieurs à la nouvelle période de rétention. Les sauvegardes qui ne sont plus nécessaires pour fournir la fonctionnalité PITR dans la nouvelle période de rétention sont supprimées. Si vous augmentez la période de rétention actuelle, vous n’avez pas immédiatement la possibilité de restaurer à des points dans le temps antérieurs dans la nouvelle période de rétention. Vous obtenez cette possibilité dans le temps, car le système commence à conserver les sauvegardes plus longtemps.

### <a name="change-retention-period-for-direct-connected-sql-managed-instance"></a>Modifier la période de rétention pour l’instance gérée SQL à **connexion directe**

```azurecli
az sql mi-arc edit  --name <SQLMI name> --custom-location dn-octbb-cl --resource-group dn-testdc --location eastus --retention-days 10
#Example
az sql mi-arc edit  --name sqlmi --custom-location dn-octbb-cl --resource-group dn-testdc --location eastus --retention-days 10
```

### <a name="change-retention-period-for-indirect-connected-sql-managed-instance"></a>Modifier la période de rétention pour l’instance gérée SQL à **connexion indirecte**

```azurecli
az sql mi-arc edit  --name <SQLMI name> --k8s-namespace <namespace>  --use-k8s --retention-days <retentiondays>
#Example
az sql mi-arc edit  --name sqlmi --k8s-namespace arc  --use-k8s --retention-days 10
```

## <a name="disable-automatic-backups"></a>Désactiver les sauvegardes automatiques

Vous pouvez désactiver les sauvegardes automatiques pour une instance spécifique de SQL Managed Instance avec Azure Arc en définissant la propriété `--retention-days` sur 0, comme suit.

> [!WARNING]
> Si vous désactivez les sauvegardes automatiques pour une instance gérée SQL avec Azure Arc, toutes les sauvegardes automatiques configurées seront supprimées et vous perdrez la possibilité d’effectuer une restauration à un point dans le temps. Vous pouvez modifier la propriété `retention-days` pour relancer les sauvegardes automatiques si nécessaire.

### <a name="disable-automatic-backups-for-direct-connected-sql-managed-instance"></a>Désactiver les sauvegardes automatiques pour une instance gérée SQL à connexion **directe**

```azurecli
az sql mi-arc edit  --name <SQLMI name> --custom-location dn-octbb-cl --resource-group dn-testdc --location eastus --retention-days 0
#Example
az sql mi-arc edit  --name sqlmi --custom-location dn-octbb-cl --resource-group dn-testdc --location eastus --retention-days 0
```

### <a name="disable-automatic-backups-for-indirect-connected-sql-managed-instance"></a>Désactiver les sauvegardes automatiques pour une instance gérée SQL à connexion **indirecte**

```azurecli
az sql mi-arc edit  --name <SQLMI name> --k8s-namespace <namespace>  --use-k8s --retention-days 0
#Example
az sql mi-arc edit  --name sqlmi --k8s-namespace arc  --use-k8s --retention-days 0
```

## <a name="monitor-backups"></a>Surveiller les sauvegardes

Les sauvegardes sont stockées sous le dossier `/var/opt/mssql/backups/archived/<dbname>/<datetime>`, où `<dbname>` est le nom de la base de données et `<datetime>` serait un timestamp au format UTC, pour le début de chaque sauvegarde complète. Chaque fois qu’une sauvegarde complète est lancée, un nouveau dossier est créé contenant la sauvegarde complète et toutes les sauvegardes différentielles et de fichier journal ultérieures. La sauvegarde complète la plus récente et ses sauvegardes différentielles et de fichier journal ultérieures sont stockées dans le dossier `/var/opt/mssql/backups/current/<dbname><datetime>`.

## <a name="limitations"></a>Limites

La limite de restauration dans le temps vers SQL Managed Instance avec Azure Arc présente les limites suivantes :

- La limite de restauration dans le temps de l’ensemble d’une instance gérée SQL avec Azure Arc n’est pas possible. 
- Une instance gérée SQL avec Azure Arc qui est déployée avec une haute disponibilité (préversion) ne prend pas actuellement en charge la limite de restauration dans le temps.
- Vous pouvez uniquement effectuer la restauration vers la même instance gérée SQL avec Azure Arc.
- La suppression et la création de différentes bases de données portant le même nom ne sont pas gérées correctement pour le moment.
- Si vous fournissez une date future lors de l’exécution de l’opération de restauration à l’aide de ```--dry-run```, une erreur se produit




## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur les Fonctionnalités et les Capacités de l’instance managée SQL compatible avec Azure Arc ](managed-instance-features.md)

[Commencer en créant un contrôleur de données](create-data-controller.md)

[Avez-vous déjà créé un Contrôleur de données ? Créez une Instance managée SQL compatible avec Azure Arc](create-sql-managed-instance.md)
