---
title: Migrer une base de données - Azure Database pour PostgreSQL - Serveur unique
description: Décrit comment extraire une base de données PostgreSQL dans un fichier de script et importer les données dans la base de données cible à partir de ce fichier.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 5b8f07ed32931533383475f936a3d1f08b7e3960
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122527899"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Migration de votre base de données PostgreSQL par exportation et importation
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server.md)]

Vous pouvez utiliser [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) pour extraire une base de données PostgreSQL dans un fichier de script et [psql](https://www.postgresql.org/docs/current/static/app-psql.html) pour importer les données dans la base de données cible à partir de ce fichier.

## <a name="prerequisites"></a>Prérequis
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- Un [serveur Azure Database pour PostgreSQL](quickstart-create-server-database-portal.md) avec des règles de pare-feu autorisant l’accès et la base de données sous-jacente.
- Utilitaire de ligne de commande [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) installé
- Utilitaire de ligne de commande [psql](https://www.postgresql.org/docs/current/static/app-psql.html) installé

Suivez ces étapes pour exporter et importer votre base de données PostgreSQL.

## <a name="create-a-script-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Création d’un fichier de script à l’aide de pg_dump qui contient les données à charger
Pour exporter votre base de données PostgreSQL existante en local ou dans une machine virtuelle vers un fichier de script sql, exécutez la commande suivante dans votre environnement existant :

```bash
pg_dump --host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Par exemple, si vous avez un serveur local contenant une base de données appelée **testdb** :
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>Importation des données dans la base de données cible Azure Database pour PostgreSQL
Vous pouvez utiliser la ligne de commande psql et le paramètre --dbname (-d) pour importer les données dans le serveur Azure Database pour PostrgeSQL et charger les données à partir du fichier sql.

```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user> --dbname=<target database name>
```
Cet exemple utilise l’utilitaire psql et un fichier de script nommé **testdb.sql** de l’étape précédente pour importer les données dans la base de données **mypgsqldb** sur le serveur cible **mydemoserver.postgres.database.azure.com**.

Pour un **serveur unique**, utilisez cette commande 
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

Pour un **serveur flexible**, utilisez cette commande  
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin --dbname=mypgsqldb
```



## <a name="next-steps"></a>Étapes suivantes
- Pour migrer une base de données PostgreSQL par vidage et restauration, consultez [Migration de votre base de données PostgreSQL par vidage et restauration](howto-migrate-using-dump-and-restore.md).
- Pour plus d’informations sur la migration de bases de données vers Azure Database pour PostgreSQL, consultez le [Guide de migration des bases de données](/data-migration/).
