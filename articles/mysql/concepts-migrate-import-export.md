---
title: Importation et exportation - Azure Database pour MySQL
description: Cet article explique les méthodes courantes d’importation et d’exportation de bases de données dans une base de données Azure pour MySQL à l’aide d’outils tels que MySQL Workbench.
author: savjani
ms.author: pariks
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: b26cfa516cc9080faac7d47c512b054e5909c1c6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132345240"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migration d’une base de données MySQL à l’aide de l’importation et de l’exportation

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Cet article explique deux approches communes de l’importation et de l’exportation de données vers un serveur de base de données Azure pour MySQL à l’aide de MySQL Workbench.

Pour obtenir des conseils de migration détaillés et complets, consultez les [Ressources du guide de migration](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide). 

Pour d’autres scénarios de migration, consultez le [Guide de migration des bases de données](https://datamigration.microsoft.com/).

## <a name="prerequisites"></a>Prérequis

Avant de commencer la migration de votre base de données MySQL, vous devez :

- Créer un [serveur Azure Database pour MySQL à l’aide du portail Azure](quickstart-create-mysql-server-database-using-azure-portal.md).
- Télécharger et installer [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) ou un autre outil MySQL tiers à des fins d’importation et d’exportation.

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Créer une base de données sur le serveur de base de données Azure pour MySQL

Créer une base de données vide sur le serveur Azure Database pour MySQL à l’aide de MySQL Workbench, Toad ou Navicat. La base de données peut avoir le même nom que celle qui contient les données sauvegardées, mais vous pouvez également créer une base de données avec un autre nom.

Pour vous connecter, procédez comme suit :

1. Dans le portail Azure, recherchez les informations de connexion dans le volet **Vue d’ensemble** de votre instance Azure Database pour MySQL.

   :::image type="content" source="./media/concepts-migrate-import-export/1_server-overview-name-login.png" alt-text="Capture d’écran des informations de connexion au serveur Azure Database pour MySQL dans le portail Azure.":::

1. Ajoutez les informations de connexion à MySQL Workbench.

   :::image type="content" source="./media/concepts-migrate-import-export/2_setup-new-connection.png" alt-text="Capture d’écran du volet de détails de la chaîne de connexion MySQL Workbench.":::

## <a name="determine-when-to-use-import-and-export-techniques"></a>Déterminer quand utiliser des techniques d’importation et d’exportation

> [!TIP]
> Pour les scénarios dans lesquels vous voulez vider et restaurer la base de données dans sa totalité, utilisez plutôt la [méthode de vidage et restauration](concepts-migrate-dump-restore.md).

Dans les scénarios suivants, utilisez les outils MySQL pour importer et exporter des bases de données dans votre base de données MySQL. Pour les autres outils, accédez à la section « Méthodes de migration » (page 22) du [Guide de migration de MySQL vers Azure Database](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide). 

- Lorsque vous devez choisir spécifiquement plusieurs tables à importer à partir d’une base de données MySQL existante vers votre base de données Azure MySQL, il est préférable d’utiliser la technique d’importation et d’exportation. En procédant ainsi, vous pouvez omettre toutes les tables non nécessaires de la migration pour économiser du temps et des ressources. Par exemple, utilisez le commutateur `--include-tables` ou `--exclude-tables` avec [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) et le commutateur `--tables` avec [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Lorsque vous déplacez des objets de base de données autres que des tables, vous devez les créer explicitement. Incluez les contraintes (clé primaire, clé étrangère et index), vues, fonctions, procédures, déclencheurs et tout autre objet de base de données que vous souhaitez migrer.
- Lorsque vous effectuez la migration de données à partir de sources de données externes autres qu’une base de données MySQL, créez des fichiers plats et importez-les à l’aide de [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

> [!Important]
> Le serveur unique et le serveur flexible prennent tous les deux en charge le moteur de stockage InnoDB uniquement. Assurez-vous que toutes les tables de la base de données utilisent le moteur de stockage InnoDB lors du chargement de données dans votre base de données Azure pour MySQL.
>
> Si votre base de données source utilise un autre moteur de stockage, effectuez une conversion vers le moteur InnoDB avant de migrer la base de données. Par exemple, si votre site WordPress ou votre application web utilise le moteur MyISAM, commencez par convertir les tables en effectuant une migration des données vers des tables InnoDB. Utilisez la clause `ENGINE=INNODB` pour définir le moteur utilisé pour la création d’une table, puis transférez les données dans la table compatible avant la migration.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Recommandations en matière de performances pour l’importation et l’exportation

Pour des performances optimales pour l’importation et l’exportation des données, nous vous recommandons d’effectuer les opérations suivantes :

- Créez des index cluster et des clés primaires avant de charger des données. Chargez les données dans l’ordre des clés primaires.
- Reportez la création des index secondaires après le chargement des données.
- Désactivez les contraintes de clé étrangère avant de charger les données. La désactivation des vérifications de clé étrangère offre d’importants gains de performances. Activez les contraintes et vérifiez les données après le chargement pour garantir l’intégrité référentielle.
- Chargez les données en parallèle. Évitez tout parallélisme excessif, qui vous ferait atteindre une limite de ressources, et surveillez les ressources à l’aide des mesures disponibles dans le Portail Azure.
- Utilisez des tables partitionnées le cas échéant.

## <a name="import-and-export-data-by-using-mysql-workbench"></a>Importer et exporter des données à l’aide de MySQL Workbench

Il existe deux façons d’exporter et d’importer des données dans MySQL Workbench : dans le menu contextuel de l’Explorateur d’objets ou dans le volet Navigateur. Chaque méthode répond à un objectif différent.

> [!NOTE]
> Si vous ajoutez une connexion à un serveur unique ou flexible MySQL sur MySQL Workbench, procédez comme suit :
>
> - Pour un serveur MySQL unique, assurez-vous que le nom d’utilisateur est au format *\<username@servername>* .
> - Pour un serveur flexible MySQL, utilisez uniquement *\<username>* . Si vous utilisez *\<username@servername>* pour vous connecter, la connexion échouera.

### <a name="run-the-table-data-export-and-import-wizards-from-the-object-browser-context-menu"></a>Exécutez les assistants d’importation et d’exportation de données de table accessibles à partir du menu contextuel de l’Explorateur d’objets

:::image type="content" source="./media/concepts-migrate-import-export/p1.png" alt-text="Capture d’écran des commandes de l’assistant Exportation et importation de MySQL Workbench dans le menu contextuel de l’Explorateur d’objets.":::

Les assistants de données de table prennent en charge les opérations d’importation et d’exportation à l’aide de fichiers CSV et JSON. Les assistants offrent plusieurs options de configuration, notamment les séparateurs, la sélection de colonne et la sélection du codage. Vous pouvez exécuter chaque Assistant sur des serveurs MySQL locaux ou connectés à distance. L’action d’importation inclut le mappage de table, de colonne et de type.

Pour accéder à ces assistants à partir du menu contextuel de l’Explorateur d’objets, cliquez avec le bouton droit sur une table, puis sélectionnez **Assistant Exportation de données de table** ou **Assistant Importation de données de table**.

#### <a name="the-table-data-export-wizard"></a>L’Assistant Exportation de données de table

Pour exporter une table dans un fichier CSV :

1. Cliquez avec le bouton droit sur la table de la base de données à exporter.
1. Sélectionnez **Assistant Exportation de données de table**. Sélectionnez les colonnes à exporter, le décalage de ligne (le cas échéant) et le nombre (le cas échéant).
1. Sur la page **Sélectionner les données à exporter**, sélectionnez **Suivant**. Sélectionnez le chemin d’accès et le type de fichier CSV ou JSON. Sélectionnez également le séparateur de ligne, les caractères entourant les chaînes et le séparateur de champs.
1. Sur la page **Sélectionner l’emplacement du fichier de sortie**, sélectionnez **Suivant**.
1. Dans le volet **Exporter des données**, sélectionnez **Suivant**.

#### <a name="the-table-data-import-wizard"></a>L’Assistant Importation de données de table

Pour importer une table à partir d’un fichier CSV :

1. Cliquez avec le bouton droit sur la table de la base de données à importer.
1. Recherchez et sélectionnez le fichier CSV à importer, puis sélectionnez **Suivant**.
1. Sélectionnez la table de destination (nouvelle ou existante), puis activez ou désactivez la case à cocher **Truncate table before import** (Tronquer la table avant l’importation) et sélectionnez **Suivant**.
1. Sélectionnez le codage et les colonnes à importer, puis sélectionnez **Suivant**.
1. Dans le volet **Importer des données**, sélectionnez **Suivant**. L’Assistant importe les données en.

### <a name="run-the-sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Exécuter les assistants d’exportation et d’importation de données SQL accessibles à partir du volet Navigateur

Utilisez un Assistant pour exporter ou importer des données SQL générées à partir de MySQL Workbench ou avec la commande mysqldump. Vous pouvez accéder aux assistants à partir du volet **Navigateur**, ou vous pouvez sélectionner **Serveur** dans le menu principal.

#### <a name="export-data"></a>Exporter des données

:::image type="content" source="./media/concepts-migrate-import-export/p2.png" alt-text="Capture d’écran de l’utilisation du volet Navigateur pour afficher le volet Exportation de données dans MySQL Workbench.":::

Vous pouvez utiliser le volet **Exportation des données** pour exporter vos données MySQL.

1. Dans MySQL Workbench, dans le volet **Navigateur**, sélectionnez **Exportation de données**.

1. Dans le volet **Exportation de données**, sélectionnez chaque schéma que vous souhaitez exporter.
 
   Pour chaque schéma, vous pouvez sélectionner des objets de schéma ou des tables spécifiques à exporter. Parmi les options de configuration figurent la possibilité d’exporter dans un dossier de projet ou un fichier SQL autonome, de vider les événements et les routines stockés et d’ignorer les données de table.

   Vous pouvez également utiliser l’option **Export a Result Set** (Exporter un jeu de résultats) pour exporter un jeu de résultats spécifique de l’éditeur SQL vers un autre format, par exemple CSV, JSON, HTML ou XML.

1. Sélectionnez les objets de base de données à exporter et configurez les options associées.
1. Sélectionnez **Actualiser** pour charger les objets actifs.
1. Si vous le souhaitez, sélectionnez **Options avancées** en haut à droite pour affiner l’opération d’exportation. Par exemple, ajoutez des verrous de table, utilisez des instructions `replace` à la place des instructions `insert` et placez les identificateurs entre accents graves.
1. Sélectionnez **Start Export** (Démarrer l’exportation) pour commencer le processus d’exportation.


#### <a name="import-data"></a>Importer des données

:::image type="content" source="./media/concepts-migrate-import-export/p3.png" alt-text="Capture d’écran de l’utilisation du volet Navigateur pour afficher le volet Importation de données dans MySQL Workbench.":::

Vous pouvez utiliser le volet **Data Import** (Importation des données) pour importer ou restaurer les données exportées avec l’opération d’exportation de données ou la commande mysqldump.

1. Dans MySQL Workbench, dans le volet **Navigateur**, sélectionnez **Exportation/Restauration de données**.
1. Choisissez le dossier du projet ou le fichier SQL autonome puis le schéma dans lequel importer, ou sélectionnez le bouton **Nouveau** pour définir un nouveau schéma.
1. Sélectionnez **Start Import** (Démarrer l’importation) pour commencer le processus d’importation.

## <a name="next-steps"></a>Étapes suivantes

- Pour découvrir une autre approche de migration, consultez [Migrer une base de données MySQL à l’aide des images mémoire et de la restauration dans la base de données Azure pour MySQL](concepts-migrate-dump-restore.md).
- Pour plus d’informations sur la migration de bases de données vers Azure Database pour MySQL, consultez le [Guide de migration des bases de données](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
