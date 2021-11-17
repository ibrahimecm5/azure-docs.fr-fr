---
title: Connexion à des bases de données Hive Metastore et gestion
description: Ce guide décrit comment se connecter à des bases de données Hive Metastore dans Azure Purview et comment utiliser les fonctionnalités de Purview pour analyser et gérer votre source de base de données Hive Metastore.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 2e40125d65dd2b4b701d5b1049444a52e6cb7dc4
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850657"
---
# <a name="connect-to-and-manage-hive-metastore-databases-in-azure-purview"></a>Connexion à des bases de données Hive Metastore et gestion dans Azure Purview

Cet article explique comment inscrire des bases de données Hive Metastore et comment s’authentifier et interagir avec les bases de données Hive Metastore dans Azure Purview. Pour plus d’informations sur Azure Purview, consultez l’[article d’introduction](overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

|**Extraction des métadonnées**|  **Analyse complète**  |**Analyse incrémentielle**|**Analyse délimitée**|**Classification**|**Stratégie d'accès**|**Traçabilité**|
|---|---|---|---|---|---|---|
| [Oui](#register)| [Oui](#scan)| Non | Non | Non | Non| Oui** |

\** La traçabilité est prise en charge si le jeu de données est utilisé en tant que source/récepteur dans une [activité de copie Data Factory](how-to-link-azure-data-factory.md). 

> [!Important]
> Les plateformes prises en charge sont Apache Hadoop, Cloudera, Hortonworks et Databricks.
> Les versions de Hive prises en charge sont 2.x à 3.x. Les versions de Databricks prises en charge sont 8.0 et versions ultérieures.

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource Purview](create-catalog-portal.md) active.

* Vous devez être un administrateur de source de données et un lecteur de données pour inscrire une source et la gérer dans Purview Studio. Pour plus d’informations, consultez notre [page d’autorisations Azure Purview](catalog-permissions.md).

* Configurez le dernier [Runtime d’intégration auto-hébergé](https://www.microsoft.com/download/details.aspx?id=39717). Pour plus d’informations, consultez [le guide Créer et configurer un runtime d’intégration auto-hébergé](../data-factory/create-self-hosted-integration-runtime.md).

* Vérifiez que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) est installé sur la machine virtuelle où est installé le runtime d’intégration auto-hébergé.

* Vérifiez que le package Redistributable Visual C++ pour Visual Studio 2012 Update 4 est installé sur la machine dotée du runtime d’intégration auto-hébergé. Si cette mise à jour n’est pas installée, [vous pouvez la télécharger ici](https://www.microsoft.com/download/details.aspx?id=30679).

* Téléchargez et installez le pilote JDBC de Hive Metastore sur la machine sur laquelle le runtime d’intégration auto-hébergé est en cours d’exécution. Par exemple, si la base de données utilisée est mssql, veillez à télécharger le [pilote JDBC pour SQL Server de Microsoft](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

    > [!Note]
    > Le pilote doit être accessible à tous les comptes de la machine virtuelle. Ne l’installez pas dans un compte d’utilisateur.

## <a name="register"></a>S’inscrire

Cette section explique comment inscrire des bases de données Hive Metastore dans Azure Purview à l’aide de [Purview Studio](https://web.purview.azure.com/).

La seule authentification prise en charge pour une base de données de metastore Hive est l’**authentification de base**.

### <a name="steps-to-register"></a>Procédure d’inscription

1. Accédez à votre compte Purview.

1. Sélectionnez **Data Map** dans le volet de navigation de gauche.

1. Sélectionnez **Inscrire**.

1. Dans Inscrire des sources, sélectionnez **Metastore** Hive. Sélectionnez **Continuer.**

    :::image type="content" source="media/register-scan-hive-metastore-source/register-sources.png" alt-text="Inscrire une source Hive" border="true":::

Dans l’écran Inscrire des sources (Metastore Hive), effectuez les actions suivantes :

1. Entrez le **Nom** sous lequel la source de données apparaîtra dans le catalogue.

1. Entrez l’**URL du cluster Hive**. L’URL du cluster peut être obtenue à partir de l’URL d’Ambari ou de l’URL de l’espace de travail Databricks. Par exemple, hive.azurehdinsight.net ou adb-19255636414785.5.azuredatabricks.net

1. Entrez l’**URL du serveur Metastore Hive.** Par exemple, sqlserver://hive.database.windows.net ou jdbc:spark://adb-19255636414785.5.azuredatabricks.net:443

1. Sélectionnez une collection ou créez-en une (facultatif).

1. Terminez pour inscrire la source de données.

    :::image type="content" source="media/register-scan-hive-metastore-source/configure-sources.png" alt-text="Configurer une source Hive" border="true":::

## <a name="scan"></a>Analyser

Suivez les étapes ci-dessous pour analyser les bases de données Hive Metastore pour identifier automatiquement les ressources et classer vos données. Pour plus d’informations sur l’analyse en général, consultez notre [Présentation des analyses et de l’ingestion](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Créer et exécuter une analyse

1. Dans le centre d’administration, sélectionnez Runtimes d’intégration. Assurez-vous qu’un runtime d’intégration auto-hébergé est configuré. S’il n’est pas configuré, suivez les étapes mentionnées [ici] (./manage-integration-runtimes.md) pour configurer un runtime d’intégration auto-hébergé.

1. Accédez aux **Sources**.

1. Sélectionnez la base de données **Metastore Hive** inscrite.

1. Sélectionnez **+ Nouvelle analyse**.

1. Fournissez les renseignements ci-dessous :

    1. **Nom** : nom de l’analyse

    1. **Se connecter via le runtime d’intégration** : sélectionnez le runtime d’intégration auto-hébergé configuré.

    1. **Informations d’identification** : sélectionnez les informations d’identification pour vous connecter à votre source de données. Veillez à respecter les points suivants :

       * Sélectionnez Authentification de base quand vous créez des informations d’identification.
       * Spécifiez le nom d’utilisateur du metastore dans le champ d’entrée Nom d’utilisateur
       * Stockez le mot de passe du metastore dans la clé secrète.

       Pour plus d’informations sur les informations d’identification, reportez-vous à [ce lien](manage-credentials.md).

       **Utilisation de Databricks** : accédez à votre cluster Databricks -> Applications -> Lancer le terminal web. Exécutez l’applet de commande **cat /databricks/hive/conf/hive-site.xml**

       Le nom d’utilisateur et le mot de passe sont accessibles à partir des deux propriétés, comme indiqué ci-dessous :

       :::image type="content" source="media/register-scan-hive-metastore-source/databricks-credentials.png" alt-text="databricks-username-password-details" border="true":::

    1. **Emplacement du pilote JDBC Metastore** : spécifiez le chemin de l’emplacement du pilote JDBC sur votre machine virtuelle où s’exécute le runtime d’intégration auto-hébergé. Ce doit être le chemin vers l’emplacement du dossier des fichiers JAR valides.

       Si vous analysez Databricks, reportez-vous à la section sur Databricks ci-dessous.

       > [!Note]
       > Le pilote doit être accessible à tous les comptes de la machine virtuelle. Ne procédez pas à l’installation dans un compte d’utilisateur.

    1. **Classe du pilote JDBC Metastore** : spécifiez le nom de la classe du pilote de connexion. Par exemple : \com.microsoft.sqlserver.jdbc.SQLServerDriver.

       **Utilisation de Databricks** : accédez à votre cluster Databricks -> Applications -> Lancer le terminal web. Exécutez l’applet de commande **cat /databricks/hive/conf/hive-site.xml**

       La classe du pilote est accessible à partir de la propriété, comme indiqué ci-dessous.

        :::image type="content" source="media/register-scan-hive-metastore-source/databricks-driver-class-name.png" alt-text="databricks-driver-class-details" border="true":::

    1. **URL JDBC du metastore** : spécifiez la valeur de l’URL de connexion et définissez la connexion à l’URL du serveur de base de données du metastore. Par exemple : `jdbc:sqlserver://hive.database.windows.net;database=hive;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300`.

       **Utilisation de Databricks** : accédez à votre cluster Databricks -> Applications -> Lancer le terminal web. Exécutez l’applet de commande **cat /databricks/hive/conf/hive-site.xml**

       L’URL JDBC est accessible à partir de la propriété URL de connexion, comme indiqué ci-dessous.

       :::image type="content" source="media/register-scan-hive-metastore-source/databricks-jdbc-connection.png" alt-text="databricks-jdbc-url-details" border="true":::

       > [!NOTE]
       > Lorsque vous copiez l’URL de *hive-site.xml*, assurez-vous de supprimer `amp;` de la chaîne ou l’analyse échouera.

       À cette URL, ajoutez le chemin de l’emplacement où le certificat SSL est placé sur votre machine virtuelle. Le certificat SSL peut être téléchargé à partir d’[ici](../mysql/howto-configure-ssl.md).

       L’URL JDBC du metastore est donc :

       `jdbc:mariadb://consolidated-westus2-prod-metastore-addl-1.mysql.database.azure.com:3306/organization1829255636414785?trustServerCertificate=true&amp;useSSL=true&sslCA=D:\Drivers\SSLCert\BaltimoreCyberTrustRoot.crt.pem`

    1. **Nom de la base de données du metastore** : spécifiez le nom de la base de données du metastore Hive.

       Si vous analysez Databricks, reportez-vous à la section sur Databricks ci-dessous.

       **Utilisation de Databricks** : accédez à votre cluster Databricks -> Applications -> Lancer le terminal web. Exécutez l’applet de commande **cat /databricks/hive/conf/hive-site.xml**

       Le nom de la base de données est accessible à partir de la propriété URL JDBC, comme indiqué ci-dessous. Par exemple : organization1829255636414785

       :::image type="content" source="media/register-scan-hive-metastore-source/databricks-data-base-name.png" alt-text="databricks-database-name-details" border="true":::

    1. **Schéma** : spécifiez une liste de schémas Hive à importer. Par exemple : schema1; schema2.

        Tous les schémas utilisateur sont importés si cette liste est vide. Tous les schémas système (par exemple, SysAdmin) et les objets sont ignorés par défaut.

        Si la liste est vide, tous les schémas disponibles sont importés. Les modèles de nom de schéma acceptables utilisant la syntaxe d’expressions de type SQL LIKE incluent l’utilisation de %. Par exemple : A%; %B; %C%; D

        * commençant par A ou
        * se terminant par B ou
        * contenant C ou
        * égalant D

        L’utilisation de NOT et des caractères spéciaux n’est pas autorisée.

    1. **Mémoire maximale disponible** : mémoire maximale (en Go) disponible sur la machine virtuelle du client pouvant être utilisée par les processus d’analyse. Elle dépend de la taille de la base de données du metastore Hive à analyser.

        :::image type="content" source="media/register-scan-hive-metastore-source/scan.png" alt-text="analyser une source Hive" border="true":::

1. Sélectionnez **Continuer**.

1. Choisissez votre **déclencheur d’analyse**. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

1. Passez en revue votre analyse et sélectionnez **Enregistrer et exécuter**.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez inscrit votre source, suivez les guides ci-dessous pour en savoir plus sur Purview et sur vos données.

- [Insights de données dans Azure Purview](concept-insights.md)
- [Lignage dans Azure Purview](catalog-lineage-user-guide.md)
- [Rechercher dans un catalogue de données](how-to-search-catalog.md)
