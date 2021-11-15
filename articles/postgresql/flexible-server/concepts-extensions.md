---
title: Extensions - Azure Database pour PostgreSQL - Serveur flexible
description: Découvrez les extensions Postgres disponibles dans Azure Database pour PostgreSQL - Serveur flexible
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: bc176567721b3c023afcb82e920b33fca7ca0a53
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061227"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---flexible-server"></a>Extensions PostgreSQL dans Azure Database pour PostgreSQL - Serveur flexible



PostgreSQL offre la possibilité d’étendre les fonctionnalités d’une base de données à l’aide des extensions. Les extensions regroupent plusieurs objets SQL associés au sein d’un package qui peut être chargé ou supprimé de votre base de données à l’aide d’une commande. Une fois chargées dans la base de données, les extensions fonctionnent comme des fonctionnalités intégrées.

## <a name="how-to-use-postgresql-extensions"></a>Guide pratique pour utiliser les extensions PostgreSQL
Les extensions PostgreSQL doivent être installées dans votre base de données pour être utilisables. Pour installer une extension en particulier, exécutez la commande [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html). Cette commande charge les objets empaquetés dans votre base de données.

Azure Database pour PostgreSQL prend en charge un sous-ensemble d’extensions clés, comme indiqué ci-dessous. Ces informations sont également disponibles en exécutant `SHOW azure.extensions;`. Les extensions non répertoriées dans ce document ne sont pas prises en charge sur Azure Database pour PostgreSQL - Serveur flexible. Vous ne pouvez pas créer ni charger votre propre extension dans Azure Database pour PostgreSQL.

## <a name="postgres-13-extensions"></a>Extensions Postgres 13

Les extensions suivantes sont disponibles dans les serveurs Azure Database pour PostgreSQL - Serveur flexible qui disposent de la version 13 de Postgres. 

> [!div class="mx-tableFixed"]
> | **Extension**| **Version d’extension** | **Description** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 3.1.1           | Utilisé pour analyser une adresse et la décomposer en éléments constitutifs, |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 3.1.1           | Exemple de jeu de données de normalisation des adresses aux États-Unis|
> |[amcheck](https://www.postgresql.org/docs/13/amcheck.html)                    | 1.2             | Fonctions permettant de vérifier l’intégrité de la relation|
> |[bloom](https://www.postgresql.org/docs/13/bloom.html)                    | 1.0             | Méthode d’accès Bloom, index basé sur un fichier de signature|
> |[btree_gin](https://www.postgresql.org/docs/13/btree-gin.html)                    | 1.3             | Prise en charge de l’indexation des types de données communs dans GIN|
> |[btree_gist](https://www.postgresql.org/docs/13/btree-gist.html)                   | 1.5             | Prise en charge de l’indexation des types de données communs dans GiST|
> |[citext](https://www.postgresql.org/docs/13/citext.html)                       | 1.6             | Type de données des chaînes de caractères sans respect de la casse|
> |[cube](https://www.postgresql.org/docs/13/cube.html)                         | 1.4             | Type de données pour les cubes multidimensionnels|
> |[dblink](https://www.postgresql.org/docs/13/dblink.html)                       | 1.2             | Se connecte à d’autres bases de données PostgreSQL à partir d’une base de données|
> |[dict_int](https://www.postgresql.org/docs/13/dict-int.html)                     | 1.0             | Modèle de dictionnaire de recherche de texte pour les entiers|
> |[dict_xsyn](https://www.postgresql.org/docs/13/dict-xsyn.html)                     | 1.0             | modèle de dictionnaire de recherche de texte pour le traitement de synonyme étendu|
> |[earthdistance](https://www.postgresql.org/docs/13/earthdistance.html)                | 1.1             | Calcule les distances orthodromiques à la surface de la Terre|
> |[fuzzystrmatch](https://www.postgresql.org/docs/13/fuzzystrmatch.html)                | 1.1             | Détermine les ressemblances et la distance entre les chaînes|
> |[hstore](https://www.postgresql.org/docs/13/hstore.html)                       | 1.7             | Type de données permettant de stocker des paires clé/valeur|
> |[intagg](https://www.postgresql.org/docs/13/intagg.html)                     | 1.1             | agrégateur et énumérateur d’entier. (Obsolète)|
> |[intarray](https://www.postgresql.org/docs/13/intarray.html)                     | 1.3             | Fonctions, opérateurs et prise en charge d’index pour les tableaux d’entiers 1D|
> |[isn](https://www.postgresql.org/docs/13/isn.html)                          | 1.2             | Types de données pour les standards internationaux de numérotation de produits|
> |[lo](https://www.postgresql.org/docs/13/lo.html)                            | 1.1             | Maintenance des objets volumineux |
> |[ltree](https://www.postgresql.org/docs/13/ltree.html)                        | 1.2             | Type de données pour les structures hiérarchiques de type arborescence|
> |[pageinspect](https://www.postgresql.org/docs/13/pageinspect.html)                        | 1.8             | inspectez le contenu de pages de base de données de bas niveau|
> |[pg_buffercache](https://www.postgresql.org/docs/13/pgbuffercache.html)               | 1.3             | Examine le cache des tampons partagé|
> |[pg_cron](https://github.com/citusdata/pg_cron)                        | 1.3             | Planificateur de travaux pour PostgreSQL|
> |[pg_freespacemap](https://www.postgresql.org/docs/13/pgfreespacemap.html)               | 1.2             | examinez le mappage d’espace libre (FSM)|
> |[pg_partman](https://github.com/pgpartman/pg_partman)         | 4.5.0           | Extension pour gérer les tables partitionnées par date et heure ou par ID |
> |[pg_prewarm](https://www.postgresql.org/docs/13/pgprewarm.html)                   | 1.2             | Préchauffe les données de relation|
> |[pg_stat_statements](https://www.postgresql.org/docs/13/pgstatstatements.html)           | 1.8             | Suit les statistiques d’exécution de toutes les instructions SQL exécutées|
> |[pg_trgm](https://www.postgresql.org/docs/13/pgtrgm.html)                      | 1.5             | Mesure de la similarité du texte et recherche d’index sur la base de trigrammes|
> |[pg_visibility](https://www.postgresql.org/docs/13/pgvisibility.html)                      | 1.2             | examinez le mappage de visibilité (machine virtuelle) et les informations de visibilité au niveau des pages|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.5             | fournit des fonctionnalités d’audit|
> |[pgcrypto](https://www.postgresql.org/docs/13/pgcrypto.html)                     | 1.3             | Fonctions de chiffrement| 
> |[pglogical](https://github.com/2ndQuadrant/pglogical)       | 2.3.2                | Réplication logique en continu |
> |[pgrowlocks](https://www.postgresql.org/docs/13/pgrowlocks.html)                   | 1.2             | Affiche les informations de verrouillage au niveau des lignes|
> |[pgstattuple](https://www.postgresql.org/docs/13/pgstattuple.html)                  | 1.5             | Affiche les statistiques au niveau du tuple|
> |[plpgsql](https://www.postgresql.org/docs/13/plpgsql.html)                      | 1.0             | Langage procédural PL/pgSQL|
> |[postgis](https://www.postgis.net/)                      | 3.1.1           | Géographie, géométrie PostGIS |
> |[postgis_raster](https://www.postgis.net/)               | 3.1.1           | Types et fonctions raster PostGIS| 
> |[postgis_sfcgal](https://www.postgis.net/)               | 3.1.1           | Fonctions SFCGAL PostGIS|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 3.1.1           | PostGIS tiger geocoder et geocoder inverse|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 3.1.1           | Types et fonctions spatiaux de topologie PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/13/postgres-fdw.html)                 | 1.0             | Wrapper de données externes pour les serveurs PostgreSQL distants|
> |[sslinfo](https://www.postgresql.org/docs/13/sslinfo.html)                    | 1.2             | informations sur les certificats SSL|
> |[tsm_system_rows](https://www.postgresql.org/docs/13/tsm-system-rows.html)                    | 1.0             |  méthode TABLESAMPLE, qui accepte le nombre de lignes en tant que limite|
> |[tsm_system_time](https://www.postgresql.org/docs/13/tsm-system-time.html)                    | 1.0             |  méthode TABLESAMPLE, qui accepte le temps en millisecondes en tant que limite|
> |[unaccent](https://www.postgresql.org/docs/13/unaccent.html)                     | 1.1             | Dictionnaire de recherche de texte qui supprime les accents|
> |[uuid-ossp](https://www.postgresql.org/docs/13/uuid-ossp.html)                    | 1.1             | Génère des identificateurs uniques universels (UUID)|

## <a name="postgres-12-extensions"></a>Extensions Postgres 12

Les extensions suivantes sont disponibles dans les serveurs Azure Database pour PostgreSQL - Serveur flexible qui disposent de la version 12 de Postgres. 

> [!div class="mx-tableFixed"]
> | **Extension**| **Version d’extension** | **Description** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 3.0.0           | Utilisé pour analyser une adresse et la décomposer en éléments constitutifs, |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 3.0.0           | Exemple de jeu de données de normalisation des adresses aux États-Unis|
> |[amcheck](https://www.postgresql.org/docs/12/amcheck.html)                    | 1.2             | Fonctions permettant de vérifier l’intégrité de la relation|
> |[bloom](https://www.postgresql.org/docs/12/bloom.html)                    | 1.0             | Méthode d’accès Bloom, index basé sur un fichier de signature|
> |[btree_gin](https://www.postgresql.org/docs/12/btree-gin.html)                    | 1.3             | Prise en charge de l’indexation des types de données communs dans GIN|
> |[btree_gist](https://www.postgresql.org/docs/12/btree-gist.html)                   | 1.5             | Prise en charge de l’indexation des types de données communs dans GiST|
> |[citext](https://www.postgresql.org/docs/12/citext.html)                       | 1.6             | Type de données des chaînes de caractères sans respect de la casse|
> |[cube](https://www.postgresql.org/docs/12/cube.html)                         | 1.4             | Type de données pour les cubes multidimensionnels|
> |[dblink](https://www.postgresql.org/docs/12/dblink.html)                       | 1.2             | Se connecte à d’autres bases de données PostgreSQL à partir d’une base de données|
> |[dict_int](https://www.postgresql.org/docs/12/dict-int.html)                     | 1.0             | Modèle de dictionnaire de recherche de texte pour les entiers|
> |[dict_xsyn](https://www.postgresql.org/docs/12/dict-xsyn.html)                     | 1.0             | modèle de dictionnaire de recherche de texte pour le traitement de synonyme étendu|
> |[earthdistance](https://www.postgresql.org/docs/12/earthdistance.html)                | 1.1             | Calcule les distances orthodromiques à la surface de la Terre|
> |[fuzzystrmatch](https://www.postgresql.org/docs/12/fuzzystrmatch.html)                | 1.1             | Détermine les ressemblances et la distance entre les chaînes|
> |[hstore](https://www.postgresql.org/docs/12/hstore.html)                       | 1.6             | Type de données permettant de stocker des paires clé/valeur|
> |[hypopg](https://github.com/HypoPG/hypopg)                                   |  1,2             | prise en charge de l’ajout d’extension pour des index hypothétiques |
> |[intagg](https://www.postgresql.org/docs/12/intagg.html)                     | 1.1             | agrégateur et énumérateur d’entier. (Obsolète)|
> |[intarray](https://www.postgresql.org/docs/12/intarray.html)                     | 1.2             | Fonctions, opérateurs et prise en charge d’index pour les tableaux d’entiers 1D|
> |[isn](https://www.postgresql.org/docs/12/isn.html)                          | 1.2             | Types de données pour les standards internationaux de numérotation de produits|
> |[lo](https://www.postgresql.org/docs/12/lo.html)                            | 1.1             | Maintenance des objets volumineux |
> |[ltree](https://www.postgresql.org/docs/12/ltree.html)                        | 1.1             | Type de données pour les structures hiérarchiques de type arborescence|
> |[pageinspect](https://www.postgresql.org/docs/12/pageinspect.html)                        | 1.7             | inspectez le contenu de pages de base de données de bas niveau|
> |[pg_buffercache](https://www.postgresql.org/docs/12/pgbuffercache.html)               | 1.3             | Examine le cache des tampons partagé|
> |[pg_cron](https://github.com/citusdata/pg_cron)                        | 1.3             | Planificateur de travaux pour PostgreSQL|
> |[pg_freespacemap](https://www.postgresql.org/docs/12/pgfreespacemap.html)               | 1.2             | examinez le mappage d’espace libre (FSM)|
> |[pg_partman](https://github.com/pgpartman/pg_partman)         | 4.5.0           | Extension pour gérer les tables partitionnées par date et heure ou par ID |
> |[pg_prewarm](https://www.postgresql.org/docs/12/pgprewarm.html)                   | 1.2             | Préchauffe les données de relation|
> |[pg_stat_statements](https://www.postgresql.org/docs/12/pgstatstatements.html)           | 1.7             | Suit les statistiques d’exécution de toutes les instructions SQL exécutées|
> |[pg_trgm](https://www.postgresql.org/docs/12/pgtrgm.html)                      | 1.4             | Mesure de la similarité du texte et recherche d’index sur la base de trigrammes|
> |[pg_visibility](https://www.postgresql.org/docs/12/pgvisibility.html)                      | 1.2             | examinez le mappage de visibilité (machine virtuelle) et les informations de visibilité au niveau des pages|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.4             | fournit des fonctionnalités d’audit|
> |[pgcrypto](https://www.postgresql.org/docs/12/pgcrypto.html)                     | 1.3             | Fonctions de chiffrement|
>|[pglogical](https://github.com/2ndQuadrant/pglogical)       | 2.3.2                | Réplication logique en continu |
> |[pgrowlocks](https://www.postgresql.org/docs/12/pgrowlocks.html)                   | 1.2             | Affiche les informations de verrouillage au niveau des lignes|
> |[pgstattuple](https://www.postgresql.org/docs/12/pgstattuple.html)                  | 1.5             | Affiche les statistiques au niveau du tuple|
> |[plpgsql](https://www.postgresql.org/docs/12/plpgsql.html)                      | 1.0             | Langage procédural PL/pgSQL|
> |[postgis](https://www.postgis.net/)                      | 3.0.0           | Géographie, géométrie PostGIS |
> |[postgis_raster](https://www.postgis.net/)               | 3.0.0           | Types et fonctions raster PostGIS| 
> |[postgis_sfcgal](https://www.postgis.net/)               | 3.0.0           | Fonctions SFCGAL PostGIS|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 3.0.0           | PostGIS tiger geocoder et geocoder inverse|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 3.0.0           | Types et fonctions spatiaux de topologie PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/12/postgres-fdw.html)                 | 1.0             | Wrapper de données externes pour les serveurs PostgreSQL distants|
> |[sslinfo](https://www.postgresql.org/docs/12/sslinfo.html)                    | 1.2             | informations sur les certificats SSL|
> |[tsm_system_rows](https://www.postgresql.org/docs/12/tsm-system-rows.html)                    | 1.0             |  méthode TABLESAMPLE, qui accepte le nombre de lignes en tant que limite|
> |[tsm_system_time](https://www.postgresql.org/docs/12/tsm-system-time.html)                    | 1.0             |  méthode TABLESAMPLE, qui accepte le temps en millisecondes en tant que limite|
> |[unaccent](https://www.postgresql.org/docs/12/unaccent.html)                     | 1.1             | Dictionnaire de recherche de texte qui supprime les accents|
> |[uuid-ossp](https://www.postgresql.org/docs/12/uuid-ossp.html)                    | 1.1             | Génère des identificateurs uniques universels (UUID)|

## <a name="postgres-11-extensions"></a>Extensions Postgres 11

Les extensions suivantes sont disponibles dans les serveurs Azure Database pour PostgreSQL - Serveur flexible qui disposent de la version 11 de Postgres. 

> [!div class="mx-tableFixed"]
> | **Extension**| **Version d’extension** | **Description** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Utilisé pour analyser une adresse et la décomposer en éléments constitutifs, |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Exemple de jeu de données de normalisation des adresses aux États-Unis|
> |[amcheck](https://www.postgresql.org/docs/11/amcheck.html)                    | 1.1             | Fonctions permettant de vérifier l’intégrité de la relation|
> |[bloom](https://www.postgresql.org/docs/11/bloom.html)                    | 1.0             | Méthode d’accès Bloom, index basé sur un fichier de signature|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | Prise en charge de l’indexation des types de données communs dans GIN|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | Prise en charge de l’indexation des types de données communs dans GiST|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | Type de données des chaînes de caractères sans respect de la casse|
> |[cube](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | Type de données pour les cubes multidimensionnels|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | Se connecte à d’autres bases de données PostgreSQL à partir d’une base de données|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | Modèle de dictionnaire de recherche de texte pour les entiers|
> |[dict_xsyn](https://www.postgresql.org/docs/11/dict-xsyn.html)                     | 1.0             | modèle de dictionnaire de recherche de texte pour le traitement de synonyme étendu|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | Calcule les distances orthodromiques à la surface de la Terre|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | Détermine les ressemblances et la distance entre les chaînes|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | Type de données permettant de stocker des paires clé/valeur|
> |[hypopg](https://github.com/HypoPG/hypopg)                                   |  1.1.2            | prise en charge de l’ajout d’extension pour des index hypothétiques |
> |[intagg](https://www.postgresql.org/docs/11/intagg.html)                     | 1.1             | agrégateur et énumérateur d’entier. (Obsolète)|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | Fonctions, opérateurs et prise en charge d’index pour les tableaux d’entiers 1D|
> |[isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | Types de données pour les standards internationaux de numérotation de produits|
> |[lo](https://www.postgresql.org/docs/11/lo.html)                            | 1.1             | Maintenance des objets volumineux |
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | Type de données pour les structures hiérarchiques de type arborescence|
> |[pageinspect](https://www.postgresql.org/docs/11/pageinspect.html)                        | 1.7             | inspectez le contenu de pages de base de données de bas niveau|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | Examine le cache des tampons partagé|
> |[pg_cron](https://github.com/citusdata/pg_cron)                        | 1.3             | Planificateur de travaux pour PostgreSQL|
> |[pg_freespacemap](https://www.postgresql.org/docs/11/pgfreespacemap.html)               | 1.2             | examinez le mappage d’espace libre (FSM)|
> |[pg_partman](https://github.com/pgpartman/pg_partman)         | 4.5.0           | Extension pour gérer les tables partitionnées par date et heure ou par ID |
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | Préchauffe les données de relation|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | Suit les statistiques d’exécution de toutes les instructions SQL exécutées|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | Mesure de la similarité du texte et recherche d’index sur la base de trigrammes|
> |[pg_visibility](https://www.postgresql.org/docs/11/pgvisibility.html)                      | 1.2             | examinez le mappage de visibilité (machine virtuelle) et les informations de visibilité au niveau des pages|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | fournit des fonctionnalités d’audit|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | Fonctions de chiffrement|
>|[pglogical](https://github.com/2ndQuadrant/pglogical)       | 2.3.2                | Réplication logique en continu |
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | Affiche les informations de verrouillage au niveau des lignes|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | Affiche les statistiques au niveau du tuple|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | Langage procédural PL/pgSQL|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | Géométrie, géographie et types et fonctions spatiaux raster PostGIS|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | Fonctions SFCGAL PostGIS|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS tiger geocoder et geocoder inverse|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | Types et fonctions spatiaux de topologie PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | Wrapper de données externes pour les serveurs PostgreSQL distants|
> |[sslinfo](https://www.postgresql.org/docs/11/sslinfo.html)                    | 1.2             | informations sur les certificats SSL|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | Fonctions qui manipulent des tables entières, y compris les tables d’analyse croisée|
> |[tsm_system_rows](https://www.postgresql.org/docs/11/tsm-system-rows.html)                    | 1.0             |  méthode TABLESAMPLE, qui accepte le nombre de lignes en tant que limite|
> |[tsm_system_time](https://www.postgresql.org/docs/11/tsm-system-time.html)                    | 1.0             |  méthode TABLESAMPLE, qui accepte le temps en millisecondes en tant que limite|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | Dictionnaire de recherche de texte qui supprime les accents|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | Génère des identificateurs uniques universels (UUID)|


## <a name="dblink-and-postgres_fdw"></a>dblink et postgres_fdw
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) et [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) vous permettent de vous connecter d’un serveur PostgreSQL à un autre ou à une autre base de données dans le même serveur. Le serveur flexible prend en charge les connexions entrantes et sortantes vers n’importe quel serveur PostgreSQL. Le serveur d’envoi doit autoriser les connexions sortantes vers le serveur de réception. De même, le serveur de réception doit autoriser les connexions à partir du serveur d’envoi. 

Nous vous recommandons de déployer vos serveurs avec [intégration au réseau virtuel](concepts-networking.md) si vous envisagez d’utiliser ces deux extensions. Par défaut, l’intégration au réseau virtuel autorise les connexions entre les serveurs dans le réseau virtuel. Vous pouvez également choisir d’utiliser des [groupes de sécurité réseau de réseau virtuel](../../virtual-network/manage-network-security-group.md) pour personnaliser l’accès.

## <a name="pg_prewarm"></a>pg_prewarm

L’extension pg_prewarm charge des données relationnelles dans le cache. Le préchauffage de vos caches signifie que vos requêtes ont de meilleurs temps de réponse lors de leur première exécution après un redémarrage. La fonctionnalité de préchauffage automatique n’est actuellement pas disponible dans Azure Database pour PostgreSQL - Serveur flexible.

## <a name="pg_cron"></a>pg_cron

[pg_cron](https://github.com/citusdata/pg_cron/) est un planificateur de travaux simple basé sur cron pour PostgreSQL qui s’exécute dans la base de données en tant qu’extension. Vous pouvez utiliser l’extension pg_cron pour exécuter des tâches de maintenance planifiées dans une base de données PostgreSQL. Par exemple, vous pouvez exécuter le vide périodique d’une table ou supprimer d’anciens travaux de données.

`pg_cron` peut exécuter plusieurs travaux en parallèle, mais il exécute au plus une instance d’un travail à la fois. Si une deuxième exécution est censée démarrer avant la fin de la première, la deuxième exécution est mise en file d’attente et démarrée dès que la première exécution est terminée. Cela garantit que les travaux s’exécutent exactement autant de fois que prévu, et qu’ils ne s’exécutent pas simultanément avec eux-mêmes.

Exemples :

Pour supprimer les anciennes données le samedi à 3h30 (GMT)
```
SELECT cron.schedule('30 3 * * 6', $$DELETE FROM events WHERE event_time < now() - interval '1 week'$$);
```
Pour exécuter un vide tous les jours à 10h00 (GMT)
```
SELECT cron.schedule('0 10 * * *', 'VACUUM');
```

Pour déplanifier toutes les tâches de pg_cron
```
SELECT cron.unschedule(jobid) FROM cron.job;
```
> [!NOTE]
> L’extension pg_cron est préchargée dans chaque Azure Database pour PostgreSQL – Serveur flexible à l’intérieur d’une base de données Postgres pour vous permettre de planifier l’exécution de travaux dans d’autres bases de données au sein de votre instance de base de données PostgreSQL sans compromettre la sécurité.

## <a name="pg_stat_statements"></a>pg_stat_statements

L’[extension pg_stat_statements](https://www.postgresql.org/docs/current/pgstatstatements.html) est préchargée sur chaque serveur flexible Azure Database pour PostgreSQL afin de vous fournir un moyen de suivre les statistiques d’exécution des instructions SQL.
Le paramètre `pg_stat_statements.track`, qui contrôle quelles instructions sont comptées par l’extension, a la valeur par défaut `top`, ce qui signifie que toutes les instructions exécutées directement par les clients sont suivies. Les deux autres niveaux de suivi sont `none` et `all`. Ce paramètre peut être configuré en tant que paramètre de serveur.

Un compromis existe entre les informations d’exécution des requêtes fournies par pg_stat_statements et l’impact sur les performances du serveur en raison de la journalisation de chaque instruction SQL. Si vous n’utilisez pas l’extension pg_stat_statements de façon active, nous vous recommandons de définir `pg_stat_statements.track` sur `none`. Notez que certains services de surveillance tiers peuvent s’appuyer sur pg_stat_statements pour fournir des insights sur les performances des requêtes : vérifiez donc si c’est ou non le cas pour vous.


## <a name="next-steps"></a>Étapes suivantes

Si vous ne voyez pas une extension que vous souhaitez utiliser, faites-le-nous savoir. Votez pour les demandes existantes ou créez de nouvelles demandes dans notre [Forum de commentaires](https://feedback.azure.com/d365community/forum/c5e32b97-ee24-ec11-b6e6-000d3a4f0da0).
