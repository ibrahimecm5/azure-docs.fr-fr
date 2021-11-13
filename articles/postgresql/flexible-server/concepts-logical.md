---
title: Réplication logique et décodage logique - Serveur flexible Azure Database pour PostgreSQL
description: Découvrez comment utiliser la réplication logique et le décodage logique dans le serveur flexible Azure Database pour PostgreSQL
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 13326622dae32c0ebd8fa86035967d51ab734c2a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130241589"
---
# <a name="logical-replication-and-logical-decoding-in-azure-database-for-postgresql---flexible-server"></a>Réplication logique et décodage logique dans le serveur flexible Azure Database pour PostgreSQL



Azure Database pour PostgreSQL - Serveur flexible prend en charge les méthodologies d’extraction et de réplication logiques des données suivantes :
1. **Réplication logique**
   1. Utilisation de la [réplication logique native](https://www.postgresql.org/docs/12/logical-replication.html) de PostgreSQL pour répliquer des objets de données. La réplication logique permet un contrôle affiné de la réplication des données, notamment la réplication des données au niveau de la table.
   2. Utilisation de l’extension [pglogical](https://github.com/2ndQuadrant/pglogical) qui fournit une réplication logique en streaming et des capacités supplémentaires telles que la copie du schéma initial de la base de données, la prise en charge de TRUNCATE, la possibilité de répliquer des DDL, etc. 
2. **Décodage logique** implémenté par le [décodage](https://www.postgresql.org/docs/12/logicaldecoding-explanation.html) du contenu du journal WAL (write-ahead log). 

## <a name="comparing-logical-replication-and-logical-decoding"></a>Comparaison de la réplication logique et du décodage logique
La réplication logique et le décodage logique ont plusieurs similitudes. Les deux :
* vous permettent de répliquer des données à partir de Postgres ;
* utilisent le [journal WAL (write-ahead log)](https://www.postgresql.org/docs/current/wal.html) comme source des modifications ;
* utilisent des [emplacements de réplication logique](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS) pour envoyer des données. Un emplacement représente un flux de modifications ;
* utilisent la propriété [REPLICA IDENTITY](https://www.postgresql.org/docs/current/sql-altertable.html#SQL-CREATETABLE-REPLICA-IDENTITY) d’une table pour déterminer les modifications qui peuvent être envoyées ;
* ne répliquent pas les modifications de DDL.


Les différences entre les deux technologies sont les suivantes. La réplication logique : 
* vous permet de spécifier une table ou un ensemble de tables à répliquer ;
* réplique des données entre des instances PostgreSQL.

Le décodage logique :
* extrait des modifications dans toutes les tables d’une base de données ;
* ne peut pas envoyer directement des données entre des instances PostgreSQL.

>[!NOTE]
> À ce stade, le serveur flexible ne prend pas en charge les réplicas de lecture inter-régions. Selon le type de charge de travail, vous pouvez choisir d’utiliser la fonctionnalité de réplication logique pour l’utilisation de la récupération d’urgence entre les régions.

## <a name="pre-requisites-for-logical-replication-and-logical-decoding"></a>Conditions préalables pour la réplication logique et le décodage logique

1. Accédez à la page Paramètres du serveur sur le portail.
2. Définissez le paramètre du serveur `wal_level` sur `logical`.
3. Si vous souhaitez utiliser l’extension pglogical, recherchez le paramètre `shared_preload_libaries` et sélectionnez `pglogical` dans la liste déroulante.
4. Changez la valeur de paramètre `max_worker_processes` et remplacez la par au moins 16. Sinon, vous risquez de rencontrer des problèmes tels que `WARNING: out of background worker slots`.
5. Enregistrez les modifications et redémarrez le serveur pour appliquer la modification `wal_level`.
6. Vérifiez que votre instance PostgreSQL autorise le trafic réseau à partir de votre ressource de connexion.
7. Accordez les autorisations de réplication de l’utilisateur administrateur.
   ```SQL
   ALTER ROLE <adminname> WITH REPLICATION;
   ```
8. Assurez-vous que le rôle que vous utilisez dispose de [privilèges](https://www.postgresql.org/docs/current/sql-grant.html) sur le schéma que vous répliquez. Sinon, vous risquez de rencontrer des erreurs telles que `Permission denied for schema`. 

## <a name="using-logical-replication-and-logical-decoding"></a>Utilisation de la réplication logique et du décodage logique

### <a name="native-logical-replication"></a>Réplication logique native
La réplication logique utilise les termes « éditeur » et « abonné ». 
* L’éditeur est la base de données PostgreSQL **à partir de laquelle** vous envoyez des données. 
* L’abonné est la base de données PostgreSQL **vers laquelle** vous envoyez des données.

Voici quelques exemples de code que vous pouvez utiliser pour tester la réplication logique.

1. Connectez-vous à la base de données de publication. Créez une table et ajoutez des données.
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   INSERT INTO basic(name) VALUES ('apple');
   INSERT INTO basic(name) VALUES ('banana');
   ```

2. Créez une publication pour la table.
   ```SQL
   CREATE PUBLICATION pub FOR TABLE basic;
   ```

3. Connectez-vous à la base de données de l’abonné. Créez une table avec le même schéma que sur l’éditeur.
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   ```

4. Créez un abonnement qui se connectera à la publication que vous avez créée plut tôt.
   ```SQL
   CREATE SUBSCRIPTION sub CONNECTION 'host=<server>.postgres.database.azure.com user=<admin> dbname=<dbname> password=<password>' PUBLICATION pub;
   ```

5. Vous pouvez maintenant interroger la table sur l’abonné. Vous verrez qu’il a reçu des données de l’éditeur.
   ```SQL
   SELECT * FROM basic;
   ```
   Vous pouvez ajouter des lignes à la table de l’éditeur et afficher les modifications sur l’abonné.

   Si vous ne voyez pas les données, activez le privilège de connexion pour `azure_pg_admin` et vérifiez le contenu de la table. 
   ```SQL 
   ALTER ROLE azure_pg_admin login;
   ```


Consultez la documentation PostgreSQL pour en savoir plus sur la [réplication logique](https://www.postgresql.org/docs/current/logical-replication.html).

### <a name="pglogical-extension"></a>Extension pglogical

Voici un exemple de configuration de pglogical au niveau du serveur de base de données du fournisseur et de l’abonné. Pour plus d’informations, reportez-vous à la [documentation de l’extension pglogical](https://github.com/2ndQuadrant/pglogical#usage). Assurez-vous également que vous avez effectué les tâches préalables ci-dessus.


1. Installez l’extension pglogical sur les serveurs de bases de données du fournisseur et de l’abonné.
    ```SQL
   \C myDB
   CREATE EXTENSION pglogical;
   ```
2. Si l’utilisateur de réplication est différent de l’utilisateur d’administration du serveur (qui a créé le serveur), assurez-vous que vous attribuez des privilèges `azure_pg_admin` et `replication` à l’utilisateur. Vous pouvez également accorder le rôle Administrateur à l’utilisateur de réplication. Pour plus d’informations, consultez la [documentation de pglogical](https://github.com/2ndQuadrant/pglogical#limitations-and-restrictions).
   ```SQL
   GRANT azure_pg_admin, replication to myUser;
   ```
   ou
   ```SQL
   GRANT myAdminUser to myUser;
   ```
2. Sur le serveur de base de données du **fournisseur** (source/éditeur), créez le nœud du fournisseur.
   ```SQL
   select pglogical.create_node( node_name := 'provider1', 
   dsn := ' host=myProviderServer.postgres.database.azure.com port=5432 dbname=myDB user=myUser password=myPassword');
   ```
3. Créez un jeu de réplication.
   ```SQL
   select pglogical.create_replication_set('myreplicationset');
   ```
4. Ajoutez toutes les tables de la base de données au jeu de réplication.
   ```SQL
   SELECT pglogical.replication_set_add_all_tables('myreplicationset', '{public}'::text[]);
   ```

   Vous pouvez aussi ajouter les tables d’un schéma particulier (par exemple, testUser) à un jeu de réplication par défaut.
   ```SQL
   SELECT pglogical.replication_set_add_all_tables('default', ARRAY['testUser']);
   ```

5. Sur le serveur de base de données de l’**abonné**, créez un nœud d’abonné.
   ```SQL
   select pglogical.create_node( node_name := 'subscriber1', 
   dsn := ' host=mySubscriberServer.postgres.database.azure.com port=5432 dbname=myDB user=myUser password=myPasword' );
   ```
6. Créez un abonnement pour démarrer le processus de synchronisation et de réplication.
    ```SQL
   select pglogical.create_subscription (
   subscription_name := 'subscription1',
   replication_sets := array['myreplicationset'],
   provider_dsn := 'host=myProviderServer.postgres.database.azure.com port=5432 dbname=myDB user=myUser password=myPassword');
   ```
7. Vous pouvez ensuite vérifier l’état de l’abonnement.
   ```SQL
   SELECT subscription_name, status FROM pglogical.show_subscription_status();
   ```

### <a name="logical-decoding"></a>Décodage logique
Le décodage logique peut être utilisé via le protocole de diffusion en continu ou une interface SQL. 

#### <a name="streaming-protocol"></a>Protocole de diffusion en continu
Il est souvent préférable de consommer les modifications à l’aide du protocole de diffusion en continu. Vous pouvez créer votre propre connecteur ou contrôle serveur consommateur, ou utiliser un service tiers comme [Debezium](https://debezium.io/). 

Consultez la documentation wal2json pour obtenir [un exemple utilisant le protocole de diffusion en continu avec pg_recvlogical](https://github.com/eulerto/wal2json#pg_recvlogical).

#### <a name="sql-interface"></a>Interface SQL 
Dans l’exemple ci-dessous, nous utilisons l’interface SQL avec le plug-in wal2json.
 
1. Créez un emplacement.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. Émettez des commandes SQL. Par exemple :
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. Consommez les modifications.
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   La sortie se présente ainsi :
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. Supprimez l’emplacement une fois que vous avez fini de l’utiliser.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```

Consultez la documentation PostgreSQL pour en savoir plus sur le [décodage logique](https://www.postgresql.org/docs/current/logicaldecoding.html).


## <a name="monitoring"></a>Surveillance
Vous devez surveiller le décodage logique. Tout emplacement de réplication non utilisé doit être supprimé. Les emplacements sont bloqués dans les journaux WAL Postgres et les catalogues système appropriés jusqu’à ce que les modifications aient été lues. Si votre abonné ou contrôle serveur consommateur échoue ou n’a pas été correctement configuré, les journaux non consommés s’accumulent et remplissent votre stockage. En outre, les journaux non consommés augmentent le risque d’un bouclage des ID de transaction. Les deux situations peuvent entraîner l’indisponibilité du serveur. Par conséquent, il est essentiel que les emplacements de réplication logique soient consommés en permanence. Si un emplacement de réplication logique n’est plus utilisé, supprimez-le immédiatement.

La colonne « active » de l’affichage pg_replication_slots indique si un contrôle serveur consommateur est connecté à un emplacement.
```SQL
SELECT * FROM pg_replication_slots;
```
[Définissez les alertes](howto-alert-on-metrics.md) sur le **nombre maximal d’ID de transactions utilisés** et le **stockage utilisé** des métriques de serveur flexible pour vous avertir lorsque les valeurs augmentent au-delà des seuils normaux. 

## <a name="limitations"></a>Limites
* Les limites de la **réplication logique** s’appliquent comme indiqué [ici](https://www.postgresql.org/docs/12/logical-replication-restrictions.html).
* **Réplicas en lecture** : Les réplicas en lecture d’Azure Database pour PostgreSQL ne sont pas pris en charge par les serveurs flexibles.
* **Emplacements et basculement à haute disponibilité** -Les emplacements de réplication logique sur le serveur principal ne sont pas disponibles sur le serveur de secours de votre serveur secondaire AZ. Cette situation s’applique si votre serveur utilise l’option haute disponibilité redondante interzone. En cas de basculement vers le serveur de secours, les emplacements de réplication logique ne sont pas disponibles sur le serveur de secours.

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur les [options de mise en réseau](concepts-networking.md)
* En savoir plus sur les [extensions](concepts-extensions.md) disponibles dans le serveur flexible
* En savoir plus sur la [haute disponibilité](concepts-high-availability.md)
