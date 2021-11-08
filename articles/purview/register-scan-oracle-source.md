---
title: Connexion à Oracle et gestion
description: Ce guide décrit comment se connecter à Oracle dans Azure Purview, et utiliser les fonctionnalités de Purview pour analyser et gérer votre source Oracle.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 6387149cc9f2392d2dccf382280ca7559593a6cc
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441987"
---
# <a name="connect-to-and-manage-oracle-in-azure-purview"></a>Connexion à Oracle et gestion dans Azure Purview

Cet article décrit comment inscrire Oracle et comment s’authentifier et interagir avec Oracle dans Azure Purview. Pour plus d’informations sur Azure Purview, consultez l’[article d’introduction](overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

|**Extraction des métadonnées**|  **Analyse complète**  |**Analyse incrémentielle**|**Analyse délimitée**|**Classification**|**Stratégie d'accès**|**Traçabilité**|
|---|---|---|---|---|---|---|
| [Oui](#register)| [Oui](#scan)| Non | Non | Non | Non| [Oui](how-to-lineage-oracle.md)|

> [!Important]
> Les versions de serveur Oracle prises en charge sont 6i à 19c

Le serveur proxy n’est pas pris en charge lors de l’analyse de la source Oracle.

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource Purview](create-catalog-portal.md) active.

* Vous devez être un administrateur de source de données et un lecteur de données pour inscrire une source et la gérer dans Purview Studio. Pour plus d’informations, consultez notre [page d’autorisations Azure Purview](catalog-permissions.md).

* Configurez le dernier [Runtime d’intégration auto-hébergé](https://www.microsoft.com/download/details.aspx?id=39717). Pour plus d’informations, consultez [le guide Créer et configurer un runtime d’intégration auto-hébergé](../data-factory/create-self-hosted-integration-runtime.md).

* Vérifiez que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) est installé sur la machine virtuelle où est installé le runtime d’intégration auto-hébergé.

* Vérifiez que le package Redistributable Visual C++ pour Visual Studio 2012 Update 4 est installé sur la machine dotée du runtime d’intégration auto-hébergé. Si cette mise à jour n’est pas installée, [vous pouvez la télécharger ici](https://www.microsoft.com/download/details.aspx?id=30679).

* Téléchargez manuellement le pilote Oracle JDBC [ici](https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html) sur la machine virtuelle où s’exécute le runtime d’intégration auto-hébergé.

    > [!Note]
    > Le pilote doit être accessible à tous les comptes de la machine virtuelle. Ne l’installez pas dans un compte d’utilisateur.

## <a name="register"></a>S’inscrire

Cette section décrit comment inscrire Oracle dans Azure Purview à l’aide de [Purview Studio](https://web.purview.azure.com/).

### <a name="prerequisites-for-registration"></a>Prérequis pour l’inscription

Un accès en lecture seule aux tables système est nécessaire.

L’utilisateur doit avoir l’autorisation de créer une session, ainsi que le rôle SELECT\_CATALOG\_ROLE attribué. L’utilisateur peut également disposer de l’autorisation SELECT pour chaque table système à partir de laquelle ce connecteur interroge des métadonnées :

```sql
grant create session to [user];
grant select on all_users to [user];
grant select on dba_objects to [user];
grant select on dba_tab_comments to [user];
grant select on dba_external_locations to [user];
grant select on dba_directories to [user];
grant select on dba_mviews to [user];
grant select on dba_clu_columns to [user];
grant select on dba_tab_columns to [user];
grant select on dba_col_comments to [user];
grant select on dba_constraints to [user];
grant select on dba_cons_columns to [user];
grant select on dba_indexes to [user];
grant select on dba_ind_columns to [user];
grant select on dba_procedures to [user];
grant select on dba_synonyms to [user];
grant select on dba_views to [user];
grant select on dba_source to [user];
grant select on dba_triggers to [user];
grant select on dba_arguments to [user];
grant select on dba_sequences to [user];
grant select on dba_dependencies to [user];
grant select on dba_type_attrs to [user];
grant select on V_$INSTANCE to [user];
grant select on v_$database to [user];
```

### <a name="authentication-for-registration"></a>Authentification pour l’inscription

La seule authentification prise en charge pour une source Oracle est l’**Authentification de base**.

### <a name="steps-to-register"></a>Procédure d’inscription

Pour inscrire une nouvelle source Oracle dans votre catalogue de données, procédez comme suit :

1. Accédez à votre compte Purview Studio dans [Purview Studio](https://web.purview.azure.com/resource/).
1. Sélectionnez **Mappage de données** dans le volet de navigation de gauche.
1. Sélectionnez **Inscrire**.
1. Dans Inscrire des sources, sélectionnez **Oracle**. Sélectionnez **Continuer**.

    :::image type="content" source="media/register-scan-oracle-source/register-sources.png" alt-text="Inscrire des sources" border="true":::

Dans l’écran **Inscrire des sources (Oracle)** , procédez comme suit :

1. Entrez le **Nom** sous lequel la source de données apparaîtra dans le catalogue.

1. Entrez le nom d’**Hôte** pour la connexion à une source Oracle. Cela peut être :
    * Nom d’hôte utilisé par JDBC pour se connecter au serveur de base de données. Par exemple : MyDatabaseServer.com
    * Adresse IP. Par exemple : 192.169.1.2
    * Sa chaîne de connexion JDBC complète. Par exemple :

         ```
        jdbc:oracle:thin:@(DESCRIPTION=(LOAD_BALANCE=on)(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver1)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver2)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver3)(PORT=1521))(CONNECT_DATA=(SERVICE_NAME=orcl)))
        ```

1. Entrez le **Numéro de port** utilisé par JDBC se connecter au serveur de base de données (1521 par défaut pour Oracle).

1. Entrez le **Nom du service Oracle** utilisé par JDBC pour se connecter au serveur de base de données.

1. Sélectionnez une collection ou créez-en une (facultatif)

1. Terminez pour inscrire la source de données.

    :::image type="content" source="media/register-scan-oracle-source/register-sources-2.png" alt-text="options pour inscrire des sources" border="true":::

## <a name="scan"></a>Analyser

Suivez les étapes ci-dessous pour analyser Oracle afin d’automatiquement identifier les ressources et classer vos données. Pour plus d’informations sur l’analyse en général, consultez notre [Présentation des analyses et de l’ingestion](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Créer et exécuter une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1. Dans le centre d’administration, sélectionnez Runtimes d’intégration. Assurez-vous qu’un runtime d’intégration auto-hébergé est configuré. Si ce n’est pas le cas, suivez les étapes mentionnées [ici](./manage-integration-runtimes.md) pour créer un runtime d’intégration auto-hébergé.

1. Accédez aux **Sources**.

1. Sélectionnez la source Oracle inscrite.

1. Sélectionnez **+ Nouvelle analyse**.

1. Fournissez les renseignements ci-dessous :

    1. **Nom** : nom de l’analyse

    1. **Se connecter via le runtime d’intégration** : sélectionnez le runtime d’intégration auto-hébergé configuré

    1. **Informations d’identification** : sélectionnez les informations d’identification pour vous connecter à votre source de données. Veillez à respecter les points suivants :
        * Sélectionnez Authentification de base quand vous créez des informations d’identification.
        * Indiquez le nom d’utilisateur utilisé par JDBC pour se connecter au serveur de base de données dans le champ d’entrée Nom d’utilisateur.
        * Stockez le nom d’utilisateur utilisé par JDBC pour se connecter au serveur de base de données dans la clé secrète.

    1. **Schéma** : répertorie le sous-ensemble de schémas à importer, exprimé sous la forme d’une liste séparée par des points-virgules. Par exemple : schema1; schema2. Tous les schémas utilisateur sont importés si cette liste est vide. Tous les schémas système (par exemple, SysAdmin) et les objets sont ignorés par défaut. Si la liste est vide, tous les schémas disponibles sont importés.
        Les modèles de nom de schéma acceptables utilisant la syntaxe d’expressions de type SQL LIKE incluent l’utilisation de %.
        Par exemple : A%; %B; %C%; D
           - commençant par A ou
           - se terminant par B ou
           - contenant C ou
           - égalant D

        L’utilisation de NOT et des caractères spéciaux n’est pas autorisée.

1. **Emplacement du pilote** : spécifiez le chemin d’accès à l’emplacement du pilote JDBC dans votre machine virtuelle où s’exécute le runtime d’intégration auto-hébergé. Il doit s’agir du chemin vers l’emplacement du dossier JAR valide.

    > [!Note]
    > Le pilote doit être accessible à tous les comptes de la machine virtuelle. Ne procédez pas à l’installation dans un compte d’utilisateur.

1. **Mémoire maximale disponible** : mémoire maximale (en Go) disponible sur la machine virtuelle du client pouvant être utilisée par les processus d’analyse. Elle dépend de la taille de la source SAP S/4HANA à analyser.

    > [!Note]
    > En règle générale, prévoyez 1 Go de mémoire pour 1 000 tables

    :::image type="content" source="media/register-scan-oracle-source/scan.png" alt-text="Analyser Oracle" border="true":::

1. Sélectionnez **Continuer**.

1. Choisissez votre **déclencheur d’analyse**. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

1. Passez en revue votre analyse et sélectionnez **Enregistrer et exécuter**.

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez inscrit votre source, suivez les guides ci-dessous pour en savoir plus sur Purview et sur vos données.

- [Insights de données dans Azure Purview](concept-insights.md)
- [Lignage dans Azure Purview](catalog-lineage-user-guide.md)
- [Rechercher dans un catalogue de données](how-to-search-catalog.md)
