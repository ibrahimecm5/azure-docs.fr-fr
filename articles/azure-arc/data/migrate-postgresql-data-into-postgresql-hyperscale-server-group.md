---
title: Migrer les données d’une base de données PostgreSQL vers un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc
titleSuffix: Azure Arc-enabled database services
description: Migrez les données d’une base de données PostgreSQL vers un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: ec41b338acbf055e8fa499ff6b4e867844b30e4d
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131553054"
---
# <a name="migrate-postgresql-database-to-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Migrer une base de données PostgreSQL vers un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc

Ce document décrit les étapes à suivre pour placer votre base de données PostgreSQL existante (qui n’est pas hébergée dans les services de données avec Azure Arc) dans votre groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="considerations"></a>Considérations

Un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc est la version de la communauté de PostgreSQL et s’exécute avec l’extension CitusData activée. Par conséquent, tout outil qui fonctionne sur PostgreSQL en dehors d’Azure Arc doit fonctionner avec un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.


Par conséquent, avec l’ensemble d’outils que vous utilisez aujourd’hui pour Postgres, vous devez être en mesure d’effectuer les opérations suivantes :
1. sauvegarder votre base de données Postgres à partir de votre instance hébergée en dehors d’Azure Arc ;
2. la restaurer dans votre groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.

Voici ce qu’il vous reste à faire :
- réinitialiser les paramètres du serveur ;
- réinitialiser les contextes de sécurité (recréer des utilisateurs, des rôles et réinitialiser des autorisations...).

Pour effectuer cette opération de sauvegarde/restauration, vous pouvez utiliser n’importe quel outil capable d’effectuer des sauvegardes/restaurations pour Postgres. Exemple :
- Azure Data Studio et son extension Postgres
- `pgcli`
- `pgAdmin`
- `pg_dump`
- `pg_restore`
- `psql`
- ...

## <a name="example"></a>Exemple

Illustrons ces étapes à l’aide de l’outil `pgAdmin`.
Prenons la configuration suivante :
- **Source :**  
    un serveur Postgres s’exécutant localement sur un serveur nu et nommé JEANYDSRV. Il est de la version 12 et héberge une base de données nommée MyOnPremPostgresDB contenant une table T1 comportant 1 ligne :::image type="content" source="media/postgres-hyperscale/migrate-pg-source.jpg" alt-text="Migrate-source":::

- **Destination :**  
    un serveur Postgres s’exécutant dans un environnement Azure Arc et nommé postgres01. Il est de la version 12. Il n’a pas de base de données à l’exception de la base de données Postgres standard.  
    :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination.jpg" alt-text="Migrate-destination":::


### <a name="take-a-backup-of-the-source-database-on-premises"></a>Effectuez une sauvegarde de la base de données localement

:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup.jpg" alt-text="Migrate-source-backup":::

Configurez-la :
1. Donnez-lui un nom de fichier : **MySourceBackup**
2. Définissez le format sur **Personnalisé**
:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup2.jpg" alt-text="Migrate-source-backup-configure":::

La sauvegarde se termine correctement :  
:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup3.jpg" alt-text="Migrate-source-backup-completed":::

### <a name="create-an-empty-database-on-the-destination-system-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Créer une base de données vide sur le système de destination dans votre groupe de serveurs PostgreSQL Hyperscale avec Azure Arc

> [!NOTE]
> Pour inscrire une instance Postgres dans l’outil `pgAdmin`, vous devez utiliser une adresse IP publique de votre instance dans votre cluster Kubernetes et définir le port et le contexte de sécurité de manière appropriée. Vous trouverez ces détails sur la ligne du point de terminaison `psql` après avoir exécuté la commande suivante :

```azurecli
az postgres arc-server endpoint list -n postgres01 --k8s-namespace <namespace> --use-k8s
```
Une sortie semblable à la suivante est renvoyée :
```console
{
  "instances": [
    {
      "endpoints": [
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
],
"engine": "PostgreSql",
"name": "postgres01"
}
  ],
  "namespace": "arc"
}
```

Nommons la base de données de destination **RESTORED_MyOnPremPostgresDB**.

:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg" alt-text="Migrate-destination-db-create" lightbox="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg":::

### <a name="restore-the-database-in-your-arc-setup"></a>Restaurez la base de données dans votre configuration Arc

:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore.jpg" alt-text="Migratre-db-restore":::

Configurez la restauration :
1. Pointez sur le fichier qui contient la sauvegarde à restaurer : **MySourceBackup**
2. Conservez le format défini sur **Personnalisé ou tar**
   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore2.jpg" alt-text="Migrate-db-restore-configure":::

3. Cliquez sur **Restaurer**.  

   La restauration a réussi.  
   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore3.jpg" alt-text="Migrate-db-restore-completed":::

### <a name="verify-that-the-database-was-successfully-restored-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Vérifier que la base de données a été correctement restaurée dans votre groupe de serveurs PostgreSQL Hyperscale avec Azure Arc

Utilisez l'une des méthodes suivantes :

**À partir de `pgAdmin`:**  

Développez l’instance Postgres hébergée dans votre installation Azure Arc. Vous voyez la table dans la base de données que vous avez restaurée et, lorsque vous sélectionnez les données, elle affiche la même ligne que dans l’instance locale :

   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestoreverif.jpg" alt-text="Migrate-db-restore-verification":::

**À partir de `psql` à l’intérieur de votre configuration Azure Arc :**  

Au sein de votre configuration Arc, vous pouvez utiliser `psql` pour vous connecter à votre instance Postgres, définir le contexte de la base de données sur `RESTORED_MyOnPremPostgresDB`, puis interroger les données :

1. Répertoriez les points de terminaison pour former votre chaîne de connexion `psql` :

   ```Az CLI
   az postgres arc-server endpoint list -n postgres01 --k8s-namespace <namespace> --use-k8s
   ```

   ```Az CLI
   {
     "instances": [
       {
         "endpoints": [
       "Description": "PostgreSQL Instance",
       "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
     },
     {
       "Description": "Log Search Dashboard",
       "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
     },
     {
       "Description": "Metrics Dashboard",
       "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
     }
   ],
   "engine": "PostgreSql",
   "name": "postgres01"
   }
     ],
     "namespace": "arc"
   }
   ```

1. À partir de votre chaîne de connexion `psql`, utilisez le paramètre `-d` pour indiquer le nom de la base de données. Avec la commande ci-dessous, vous êtes invité à entrer le mot de passe :

   ```console
   psql -d RESTORED_MyOnPremPostgresDB -U postgres -h 10.0.0.4 -p 32639
   ```

   Connexions `psql`.

   ```output
   Password for user postgres:
   psql (10.12 (Ubuntu 10.12-0ubuntu0.18.04.1), server 12.3 (Debian 12.3-1.pgdg100+1))
   WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
   SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
   Type "help" for help.

   RESTORED_MyOnPremPostgresDB=#   
   ```

1. Sélectionnez la table pour voir les données que vous avez restaurées à partir de l’instance Postgres locale :

   ```console
   RESTORED_MyOnPremPostgresDB=# select * from t1;
   ```

   ```output
    col1 |    col2
   ------+-------------
       1 | BobbyIsADog
   (1 row)
   ```

> [!NOTE]
> - Vous ne verrez pas beaucoup d’avantages en matière de performances résultant de l’exécution sur PostgreSQL Hyperscale avec Azure Arc tant que vous n’aurez pas effectué un scale-out et partitionné/distribué les données entre les nœuds Worker de votre groupe de serveurs PostgreSQL Hyperscale. Consultez les [Étapes suivantes](#next-steps).
>
> - Il n’est pas possible aujourd’hui d’« intégrer dans Azure Arc » une instance Postgres existante qui s’exécuterait localement ou dans un autre cloud. En d’autres termes, il n’est pas possible d’installer une sorte d’« agent Azure Arc » sur votre instance Postgres existante pour en faire une configuration de Postgres activé par Azure Arc. Au lieu de cela, vous devez créer une nouvelle instance Postgres et y transférer des données. Vous pouvez utiliser la technique décrite ci-dessus pour effectuer cette opération, ou vous utiliser n’importe quel outil ETL de votre choix.

## <a name="next-steps"></a>Étapes suivantes

- Lisez les concepts et les guides pratiques d'Azure Database pour PostgreSQL Hyperscale afin de distribuer vos données sur différents nœuds PostgreSQL Hyperscale et de tirer parti de toute la puissance d'Azure Database pour PostgreSQL Hyperscale :
    * [Nœuds et tables](../../postgresql/concepts-hyperscale-nodes.md)
    * [Déterminer le type d’application](../../postgresql/concepts-hyperscale-app-type.md)
    * [Choisir une colonne de distribution](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Colocalisation de table](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuer et modifier des tables](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Concevoir une base de données multilocataire](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Concevoir un tableau de bord d’analytique en temps réel](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> *Dans ces documents, ignorez les sections **Connectez-vous au portail Azure** et **Créer un serveur Azure Database pour Postgres – Hyperscale (Citus)** . Implémentez les étapes restantes dans votre déploiement Azure Arc. Ces sections sont spécifiques à Azure Database pour PostgreSQL Hyperscale (Citus) proposé en tant que service PaaS dans le cloud Azure, mais les autres parties des documents s’appliquent directement à votre instance PostgreSQL Hyperscale avec Azure Arc.

- [Effectuer un scale-out de votre groupe de serveurs Azure Database pour PostgreSQL Hyperscale](scale-out-in-postgresql-hyperscale-server-group.md)
