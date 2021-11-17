---
title: 'Tutoriel : Migration d’une base de données Azure Database pour MySQL - Serveur unique vers Azure Database pour MySQL - Serveur flexible avec un temps d’arrêt minimal'
description: Cet article décrit comment effectuer une migration d’une base de données Azure Database pour MySQL avec un temps d’arrêt minimal - Serveur unique vers Azure Database pour MySQL - Serveur flexible.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: how-to
ms.date: 06/18/2021
ms.openlocfilehash: 3bc4ee8d7d7491e577fba2e29d905262e37764cb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339539"
---
# <a name="tutorial-migrate-azure-database-for-mysql--single-server-to-azure-database-for-mysql--flexible-server-with-minimal-downtime"></a>Tutoriel : Migration d’une base de données Azure Database pour MySQL - Serveur unique vers Azure Database pour MySQL - Serveur flexible avec un temps d’arrêt minimal

Vous pouvez migrer une instance d’Azure Database pour MySQL, un serveur unique vers Azure Database pour MySQL, un serveur flexible avec un temps d’arrêt minimal pour vos applications à l’aide d’une combinaison d’outils open source tels que mydumper/myLoader et la réplication de données.

> [!NOTE]
> Cet article contient des références au terme *esclave*, un terme que Microsoft n’utilise plus. Lorsque le terme sera supprimé du logiciel, nous le supprimerons de cet article.

La réplication de données est une technique qui réplique les modifications de données du serveur source vers le serveur de destination en fonction de la méthode de position de fichier journal binaire. Dans ce scénario, l’instance MySQL fonctionnant comme source (sur laquelle les modifications sont apportées à la base de données) écrit des mises à jour et des modifications en tant que « événements » dans le journal binaire. Les informations contenues dans le journal binaire sont stockées dans différents formats de journalisation en fonction des modifications de la base de données en cours d’enregistrement. Les réplicas sont configurés pour lire le journal binaire à partir de la source et pour exécuter les événements dans le journal binaire sur la base de données locale du réplica.

Si vous configurez la réplication des données pour synchroniser les données d’une instance de base de données Azure pour MySQL vers une autre, vous pouvez effectuer un basculement sélectif de vos applications à partir de la base de données principale (ou de la base de données source) vers le réplica (ou la base de données cible).

Dans ce didacticiel, vous allez utiliser mydumper/myLoader et la réplication de données pour migrer un exemple de base de données ([classicmodels](https://www.mysqltutorial.org/mysql-sample-database.aspx)) à partir d’une instance d’Azure Database pour MySQL-serveur unique vers une instance d’Azure Database pour MySQL-flexible Server, puis synchroniser les données.

Dans ce tutoriel, vous allez apprendre à :

* Configurez les Paramètres réseau pour la réplication des données dans différents scénarios.
* Configurez la réplication des données entre le réplica principal et le réplica.
* Tester le déploiement de réplication.
* Basculement pour terminer la migration.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* Une instance d’Azure Database pour MySQL mono-serveur exécutant la version 5,7 ou 8,0.
    > [!Note]
    > Si vous exécutez Azure Database pour MySQL avec un serveur unique version 5.6, mettez à niveau votre instance vers 5.7, puis configurez les données dans la réplication. Pour en savoir plus, consultez[Mise à niveau de version majeure dans Azure Database pour MySQL Serveur unique](how-to-major-version-upgrade.md).
* Instance de Serveur flexible Azure Database pour MySQL. Pour plus d’informations, consultez l’article [Créer une instance dans Azure Database pour MySQL serveur flexible](./flexible-server/quickstart-create-server-portal.md).
    > [!Note]
    > La configuration de la réplication des données pour les serveurs à haute disponibilité redondante interzone n’est pas prise en charge. Si vous souhaitez avoir une haute disponibilité redondante dans une zone pour votre serveur cible, procédez comme suit :
    >
    > 1. Créer le serveur avec une haute disponibilité redondante interzone
    > 2. Désactiver la haute disponibilité
    > 3. Suivez l’article pour configurer les données dans la réplication
    > 4. Après le basculement, supprimez les données dans la configuration de la réplication
    > 5. Activer la haute disponibilité
    >
    > *Assurez-vous que **[GTID_Mode](./concepts-read-replicas.md#global-transaction-identifier-gtid)** a le même paramètre sur les serveurs source et cible.*

* Pour vous connecter et créer une base de données à l’aide de MySQL Workbench. Pour plus d’informations, consultez l’article [Utiliser MySQL Workbench pour vous connecter et interroger des données](./flexible-server/connect-workbench.md).
* Pour vous assurer que vous disposez d’une machine virtuelle Azure exécutant Linux dans la même région (ou sur le même réseau virtuel, avec accès privé) qui héberge vos bases de données source et cible.
* Pour installer MySQL client ou MySQL Workbench (les outils clients) sur votre machine virtuelle Azure. Assurez-vous que vous pouvez vous connecter à la fois au serveur principal et au serveur de réplication. Dans le cadre de cet article, le client MySQL est installé.
* Pour installer mydumper/myloader sur votre machine virtuelle Azure. Pour plus d’informations, consultez l’article [mydumper/myloader](concepts-migrate-mydumper-myloader.md).
* Pour télécharger et exécuter le script de base de données exemple pour la base de données [classicmodels](https://www.mysqltutorial.org/wp-content/uploads/2018/03/mysqlsampledatabase.zip) sur le serveur source.

## <a name="configure-networking-requirements"></a>Configuration réseau requise

Pour configurer la réplication des données, vous devez vous assurer que la cible peut se connecter à la source sur le port 3306. En fonction du type de point de terminaison configuré sur la source, procédez comme suit.

* Si un point de terminaison public est activé sur la source, assurez-vous que la cible peut se connecter à la source en activant l’option « autoriser l’accès aux services Azure » dans la règle de pare-feu. Pour en savoir plus, [Gérer les règles de pare-feu - Azure CLI - Azure Database pour MySQL](./concepts-firewall-rules.md#connecting-from-azure). 
* Si un point de terminaison privé et [Refuser l’accès public](concepts-data-access-security-private-link.md#deny-public-access-for-azure-database-for-mysql) est activé sur la source, installez le lien privé dans le même réseau virtuel qui héberge la cible. Pour en savoir plus, consultez [la section Private Link - Azure Database for MySQL](concepts-data-access-security-private-link.md).

## <a name="configure-data-in-replication"></a>Configurer la réplication de données entrantes

Pour configurer Data in replication, effectuez les étapes suivantes :

1. Connectez-vous à la machine virtuelle Azure sur laquelle vous avez installé l’outil client MySQL.

2. Connecter à la source et à la cible à l’aide de l’outil client mysql.

3. Utilisez l’outil client MySQL pour déterminer si log_bin est activé sur la source en exécutant la commande suivante :

    ```sql
    SHOW VARIABLES LIKE 'log_bin';
    ```

    > [!Note]
    > Avec le serveur de base de données Azure pour MySQL avec un stockage de grande taille, qui prend en charge jusqu’à 16 To, cette option est activée par défaut.

    > [!Tip]
    > Avec Azure Database pour MySQL Single Server, qui prend en charge jusqu’à 4 to, cette option n’est pas activée par défaut. Toutefois, si vous promouvez un [réplica en lecture](howto-read-replicas-portal.md) pour le serveur source et que vous supprimez ensuite le réplica en lecture, le paramètre prend la valeur ON.

4. Sur la base de l'application SSL pour le serveur source, créez un utilisateur dans le serveur source avec la permission de réplication en exécutant la commande appropriée.

    Si vous utilisez SSL, exécutez la commande suivante :

    ```sql
    CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
    GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
    ```

    Si vous n'utilisez pas SSL, exécutez la commande suivante :

    ```sql
    CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
    GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
    ```

5. Pour sauvegarder la base de données à l'aide de mydumper, exécutez la commande suivante sur la VM Azure où nous avons installé le mydumper\myloader :

    ```bash
    $ mydumper --host=<primary_server>.mysql.database.azure.com --user=<username>@<primary_server> --password=<Password> --outputdir=./backup --rows=100 -G -E -R -z --trx-consistency-only --compress --build-empty-files --threads=16 --compress-protocol --ssl  --regex '^(classicmodels\.)' -L mydumper-logs.txt
    ```

    > [!Tip]
    > L'option **--trx-consistency-only** est nécessaire pour la cohérence transactionnelle pendant la sauvegarde.
    >
    > * Mydumper équivalent de mysqldump --Single-transaction.
    > * Utile si toutes vos tables sont InnoDB.
    > * Le thread "principal" n'a besoin que de maintenir le verrou global jusqu'à ce que les threads "copie de sauvegarde" puissent lancer une transaction.
    > * Offre la durée la plus brève du verrouillage global

    Le thread "principal" n'a besoin que de maintenir le verrou global jusqu'à ce que les threads "copie de sauvegarde" puissent lancer une transaction.

    Les variables de cette commande sont expliquées ci-dessous :

    * **--hôte :** Nom du serveur primaire
    * **--utilisateur :**  Nom d'un utilisateur (au format username@servername depuis que le serveur primaire exécute Azure Database for MySQL - Single Server). Vous pouvez utiliser l'administrateur du serveur ou un utilisateur ayant les droits SÉLECTIONNER et RECHARGER.
    * **--Mot de passe :**  Mot de passe de l'utilisateur ci-dessus

    Pour plus d’informations sur l’utilisation de mydumper, consultez[mydumper/myLoader](concepts-migrate-mydumper-myloader.md)

6. Lisez le fichier de métadonnées pour déterminer le nom et le décalage du fichier journal binaire en exécutant la commande suivante :

    ```bash
    $ cat ./backup/metadata 
    ```

    Dans cette commande, **./backup** fait référence au répertoire de sortie utilisé dans la commande de l'étape précédente.

    Les résultats doivent s’afficher comme indiqué dans l’image suivante :

    :::image type="content" source="./media/howto-migrate-single-flexible-minimum-downtime/metadata.png" alt-text="Synchronisation continue avec le service Azure Database Migration Service":::

    Veillez à noter le nom du fichier binaire, car vous en aurez besoin par la suite.

7. Restaurez la base de données à l’aide de myLoader en exécutant la commande suivante :

    ```bash
    $ myloader --host=<servername>.mysql.database.azure.com --user=<username> --password=<Password> --directory=./backup --queries-per-transaction=100 --threads=16 --compress-protocol --ssl --verbose=3 -e 2>myloader-logs.txt
    ```

    Les variables de cette commande sont expliquées ci-dessous :

    * **--hôte :**  Nom du serveur de réplique
    * **--utilisateur :**  Nom d'un utilisateur. Vous pouvez utiliser l'administrateur du serveur ou un utilisateur ayant le droit de lecture et d'écriture capable de restaurer les schémas et les données de la base de données
    * **--Mot de passe :**  Mot de passe pour l'utilisateur ci-dessus

8. Selon la contrainte de mise en conformité SSL sur le serveur principal, connectez-vous au serveur de réplication à l’aide de l’outil client MySQL et suivez les étapes ci-dessous.

    * Si la contrainte de mise en conformité SSL est activée, procédez comme suit :

        i. Le certificat nécessaire pour communiquer via le protocole SSL avec votre serveur Azure Database pour MySQL se trouve [ici](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem).

        ii. Ouvrez le fichier dans le bloc-notes et collez le contenu dans la section "PLACEZ LE CONTEXTE DE VOTRE CERTIFICAT DE CLÉ PUBLIQUE ICI".

        ```sql
        SET @cert = '-----BEGIN CERTIFICATE-----
        PLACE YOUR PUBLIC KEY CERTIFICATE'S CONTEXT HERE 
        -----END CERTIFICATE-----'
        ```

        iii. Pour configurer des données dans la réplication, exécutez la commande suivante :

        ```sql
        CALL mysql.az_replication_change_master('<Primary_server>.mysql.database.azure.com', '<username>@<primary_server>', '<Password>', 3306, '<File_Name>', <Position>, @cert);
        ```

        > [!Note]
        > Déterminez la position et le nom de fichier à partir des informations obtenues à l’étape 6.

    * Si la contrainte de mise en conformité SSL n’est pas activée, exécutez la commande suivante :

        ```sql
        CALL mysql.az_replication_change_master('<Primary_server>.mysql.database.azure.com', '<username>@<primary_server>', '<Password>', 3306, '<File_Name>', <Position>, ‘’);
        ```

9. Pour démarrer la réplication à partir du serveur de réplication, appelez la procédure stockée ci-dessous.

    ```sql
    call  mysql.az_replication_start;
    ```

10. Pour vérifier l’état de réplication, sur le serveur de réplication, exécutez la commande suivante :

     ```sql
     show slave status \G; 
     ```

    > [!Note]
    > Si vous utilisez MySQL Workbench, le modificateur \G n’est pas obligatoire.

    Si l'état de *Slave_IO_Running* et *Slave_SQL_Running* est Oui et la valeur de *Seconds_Behind_Master* est 0, alors la réplication fonctionne bien. Seconds_Behind_Master indique la fin du réplica. Si la valeur est différente de 0, le réplica traite les mises à jour.

## <a name="testing-the-replication-optional"></a>Test de la réplication (facultatif)

Pour confirmer que la réplication des données fonctionne correctement, vous pouvez vérifier que les modifications apportées aux tables dans la base de données primaire ont été répliquées vers le réplica.

1. Identifiez une table à utiliser pour le test, par exemple la table Customers, puis confirmez que le nombre d’entrées qu’il contient est le même sur le serveur principal et le serveur de réplication en exécutant la commande suivante sur chaque :

    ```
    select count(*) from customers;
    ```

2. Notez le nombre d’entrées pour une comparaison ultérieure.

    Pour tester la réplication, essayez d’ajouter des données aux tables Customer sur le serveur principal, puis vérifiez que les nouvelles données sont répliquées. Dans ce cas, vous allez ajouter deux lignes à une table sur le serveur principal, puis confirmer qu’elles sont répliquées sur le serveur de réplication.

3. Dans la table Clients du serveur primaire, insérez des lignes en exécutant la commande suivante :

    ```sql
    insert  into `customers`(`customerNumber`,`customerName`,`contactLastName`,`contactFirstName`,`phone`,`addressLine1`,`addressLine2`,`city`,`state`,`postalCode`,`country`,`salesRepEmployeeNumber`,`creditLimit`) values 
    (<ID>,'name1','name2','name3 ','11.22.5555','54, Add',NULL,'Add1',NULL,'44000','country',1370,'21000.00');
    ```

4. Pour vérifier l'état de la réplication, appelez la *commande show slave status \G* pour confirmer que la réplication fonctionne comme prévu.

5. Pour confirmer que le nombre est le même, sur le serveur de réplication, exécutez la commande suivante :

    ```sql
    select count(*) from customers;
    ```

## <a name="ensure-a-successful-cutover"></a>S’assurer que le basculement est réussi

Pour garantir la réussite du basculement, effectuez les tâches suivantes :

1. Configurez les règles appropriées de pare-feu et de réseau virtuel au niveau du serveur pour vous connecter au Serveur cible. Vous pouvez comparer les règles de pare-feu de la [source](howto-manage-firewall-using-portal.md) et de la [cible](./flexible-server/how-to-manage-firewall-portal.md#create-a-firewall-rule-after-server-is-created) à partir du portail.
2. Configurez les connexions appropriées et les autorisations de niveau base de données sur le serveur cible. Vous pouvez exécuter *SELECT * FROM mysql.user ;*  sur les serveurs source et cible pour les comparer.
3. Assurez-vous que toutes les connexions entrantes au serveur unique Azure Database pour MySQL sont arrêtées.
    > [!Tip]
    > Vous pouvez définir le serveur de base de données Azure pour MySQL en lecture seule.
4. Assurez-vous que la réplique a rattrapé le primaire en exécutant *show slave status \G* et en confirmant que la valeur du paramètre *Seconds_Behind_Master* est 0.
5. Rediriger les clients et les applications clientes vers l’instance cible du serveur flexible Azure Database pour MySQL.
6. Effectuez le dernier basculement en exécutant la procédure stockée mysql.az_replication_stop, qui arrêtera la réplication à partir du serveur de réplication.
7. *Appelez MySQL.az_replication_remove_master* pour supprimer la configuration de la réplication des données.

À ce stade, vos applications sont connectées au nouveau serveur flexible Azure Database pour MySQL, et les modifications apportées à la source ne seront plus répliquées sur la cible.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la réplication des données dans [Répliquer les données dans Azure Database pour MySQL - Serveur flexible](flexible-server/concepts-data-in-replication.md) et [Configurer la réplication de données dans Azure Database pour MySQL - Serveur flexible](./flexible-server/how-to-data-in-replication.md)
* Pour en savoir plus sur la [résolution des erreurs courantes, consultez cet article](howto-troubleshoot-common-errors.md).
* En savoir plus sur la [migration de MySQL vers Azure Database pour MySQL hors ligne à l'aide d'Azure Database Migration Service](../dms/tutorial-mysql-azure-mysql-offline-portal.md).
