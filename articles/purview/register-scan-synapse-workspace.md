---
title: Se connecter à des espaces de travail Azure Synapse Analytics et les gérer
description: Ce guide explique comment se connecter aux espaces de travail Azure Synapse Analytics dans Azure Purview, et utiliser les fonctionnalités de Purview pour analyser et gérer votre source d’espace de travail Azure Synapse Analytics.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: a5921e2af4445ad645ce48f6102c311c9478b9e9
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848018"
---
# <a name="connect-to-and-manage-azure-synapse-analytics-workspaces-in-azure-purview"></a>Se connecter à des espaces de travail Azure Synapse Analytics et les gérer dans Azure Purview

Cet article explique comment inscrire des espaces de travail Azure Synapse Analytics, ainsi que s’authentifier et interagir avec des espaces de travail Azure Synapse Analytics dans Azure Purview. Pour plus d’informations sur Azure Purview, consultez l’[article d’introduction](overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

|**Extraction des métadonnées**|  **Analyse complète**  |**Analyse incrémentielle**|**Analyse délimitée**|**Classification**|**Stratégie d'accès**|**Traçabilité**|
|---|---|---|---|---|---|---|
| [Oui](#register) | [Oui](#scan)| [Oui](#scan) | [Oui](#scan)| [Oui](#scan)| Non| [Oui- Pipelines Synapse](how-to-lineage-azure-synapse-analytics.md)|


<!-- 4. Prerequisites
Required. Add any relevant/source-specific prerequisites for connecting with this source. Authentication/Registration should be covered by the sections below and does not need to be covered here.
-->

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource Purview](create-catalog-portal.md) active.

* Vous devez être un administrateur de source de données et un lecteur de données pour inscrire une source et la gérer dans Purview Studio. Pour plus d’informations, consultez notre [page d’autorisations Azure Purview](catalog-permissions.md).

## <a name="register"></a>Inscrire

Cette section explique comment inscrire des espaces de travail Azure Synapse Analytics dans Azure Purview à l’aide de [Purview Studio](https://web.purview.azure.com/).

### <a name="authentication-for-registration"></a>Authentification pour l’inscription

Seuls les utilisateurs disposant d’au moins un rôle *Lecteur* sur l’espace de travail Azure Synapse et qui sont également *administrateurs de source de données* dans Azure Purview peuvent inscrire un espace de travail Azure Synapse.

### <a name="steps-to-register"></a>Procédure d’inscription

1. Accédez à votre compte Azure Purview.
1. Dans le volet gauche, sélectionnez **Sources**.
1. Sélectionnez **Inscription**.
1. Sous **Inscrire des sources**, sélectionnez **Azure Synapse Analytics (multiple)** .
1. Sélectionnez **Continuer**.

   :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source.png" alt-text="Capture d’écran d’une sélection de sources dans Azure Purview, incluant Azure Synapse Analytics.":::

1. Sur la page **Inscrire les sources (Azure Synapse Analytics)** , procédez comme suit :

    a. Entrez un **Nom**. Il sera utilisé pour répertorier la source de données dans le catalogue.  
    b. Si vous le souhaitez, choisissez un **abonnement** pour filtrer les valeurs.  
    c. Dans la liste déroulante **Nom de l’espace de travail**, sélectionnez l’espace de travail que vous utilisez.  
    d. Dans les listes déroulantes des points de terminaison, les points de terminaison SQL sont renseignés automatiquement en fonction de la sélection de votre espace de travail.  
    e. Dans la liste déroulante **Sélectionner une collection**, choisissez la collection que vous utilisez ou, si vous le souhaitez, créez-en une.  
    f. Ensuite, sélectionnez **Inscrire** pour inscrire la source de données.

    :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source-details.png" alt-text="Capture d’écran de la page « Inscrire les sources (Azure Synapse Analytics) » qui permet de saisir des informations sur la source Azure Synapse.":::

## <a name="scan"></a>Analyser

Suivez les étapes ci-dessous pour analyser des espaces de travail Azure Synapse Analytics afin d’identifier automatiquement les ressources et de classer vos données. Pour plus d’informations sur l’analyse en général, consultez notre [Présentation des analyses et de l’ingestion](concept-scans-and-ingestion.md).

Vous devez d’abord configurer l’authentification pour l’énumération de vos ressources [dédiées](#authentication-for-enumerating-dedicated-sql-database-resources) ou [serverless](#authentication-for-enumerating-serverless-sql-database-resources). Cela permettra à Purview d’énumérer les ressources de votre espace de travail et d’effectuer des analyses délimitée.

Ensuite, vous devez [appliquer des autorisations pour analyser les contenus de l’espace de travail](#apply-permissions-to-scan-the-contents-of-the-workspace).

### <a name="authentication-for-enumerating-dedicated-sql-database-resources"></a>Authentification pour l’énumération des ressources de base de données SQL dédiées

1. Dans le portail Azure, accédez à la ressource de l’espace de travail Azure Synapse.  
1. Dans le volet gauche, sélectionnez  **Contrôle d’accès (IAM)** .

   > [!NOTE]
   > Vous devez être le *propriétaire* ou l’*administrateur de l’accès utilisateur* pour ajouter un rôle à la ressource.

1. Sélectionnez le bouton **Ajouter**.
1. Définissez le rôle de **Lecteur** et entrez le nom de votre compte Azure Purview, qui représente son identité de service managé (ou MSI, pour « Managed Service Identity »).
1. Sélectionnez **Enregistrer** pour finaliser l’attribution du rôle.

> [!NOTE]
> Si vous prévoyez d’inscrire et d’analyser plusieurs espaces de travail Azure Synapse dans votre compte Azure Purview, vous pouvez aussi attribuer le rôle à partir d’un niveau supérieur, tel qu’un groupe de ressources ou un abonnement.

### <a name="authentication-for-enumerating-serverless-sql-database-resources"></a>Authentification pour l’énumération des ressources de base de données SQL serverless

Pour que Purview puisse énumérer vos ressources de base de données SQL serverless, vous devrez définir l’authentification pour trois emplacements : l’espace de travail Synapse, le stockage associé et les bases de données serverless. Pour ce faire, procédez comme suit :

1. Dans le portail Azure, accédez à la ressource de l’espace de travail Azure Synapse.  
1. Dans le volet gauche, sélectionnez  **Contrôle d’accès (IAM)** . 

   > [!NOTE]
   > Vous devez être le *propriétaire* ou l’*administrateur de l’accès utilisateur* pour ajouter un rôle à la ressource.
   
1. Sélectionnez le bouton **Ajouter**.   
1. Définissez le rôle de **Lecteur** et entrez le nom de votre compte Azure Purview, qui représente son identité de service managé (ou MSI, pour « Managed Service Identity »).
1. Sélectionnez **Enregistrer** pour finaliser l’attribution du rôle.
1. Dans le portail Azure, accédez au **groupe de ressources** ou à l’**abonnement** dans lequel se trouve l’espace de travail Azure Synapse.
1. Dans le volet gauche, sélectionnez  **Contrôle d’accès (IAM)** . 

   > [!NOTE]
   > Vous devez être le *propriétaire* ou l’*administrateur de l’accès utilisateur* pour ajouter un rôle dans le champ **Groupe de ressources** ou **Abonnement**. 

1. Sélectionnez le bouton **Ajouter**. 
1. Définissez le rôle **Lecteur des données blob du stockage**, puis entrez le nom de votre compte Azure Purview (qui représente sa MSI) dans la zone **Sélectionner**. 
1. Sélectionnez **Enregistrer** pour finaliser l’attribution du rôle.
1. Accédez à votre espace de travail Azure Synapse et ouvrez Synapse Studio.
1. Sélectionnez l’onglet **Données** sur la gauche.
1. Sélectionnez l’icône de points de suspension ( **...** ) placée à côté d’une de vos bases de données, puis démarrez un nouveau script SQL.
1. Ajoutez la MSI du compte Azure Purview (représentée par le nom du compte) aux bases de données SQL serverless. Pour ce faire, exécutez la commande suivante dans votre script SQL :
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ```

### <a name="apply-permissions-to-scan-the-contents-of-the-workspace"></a>Appliquer des autorisations pour analyser les contenus de l’espace de travail

Vous pouvez configurer l’authentification pour une source Azure Synapse de deux manières :

- Utiliser une identité managée
- Utiliser un principal de service

> [!IMPORTANT]
> Ces étapes pour les bases de données serverless ne s’appliquent **pas** aux bases de données répliquées. Actuellement, dans Synapse, les bases de données serverless qui sont répliquées à partir des bases de données Spark sont en lecture seule. Pour en savoir plus, consultez [cette page](../synapse-analytics/sql/resources-self-help-sql-on-demand.md#operation-is-not-allowed-for-a-replicated-database).

> [!NOTE]
> Vous devez configurer l’authentification sur chaque base de données SQL dédiée dans votre espace de travail Azure Synapse que vous souhaitez inscrire et analyser. Les autorisations mentionnées dans les sections suivantes pour une base de données SQL serverless s’appliquent à toutes les bases de données dans votre espace de travail. Autrement dit, vous ne devez configurer l’authentification qu’une seule fois.

#### <a name="use-a-managed-identity-for-dedicated-sql-databases"></a>Utiliser une identité managée pour les bases de données SQL dédiées

1. Accédez à votre espace de travail Azure Synapse.
1. Accédez à la section **Données**, puis à l’une de vos bases de données SQL dédiées.
1. Sélectionnez l’icône de points de suspension ( **...** ) placée à côté, puis démarrez un nouveau script SQL.

   > [!NOTE]
   > Pour exécuter les commandes de la procédure suivante, vous devez être *administrateur Azure Synapse* sur l’espace de travail. Pour plus d’informations sur les autorisations Azure Synapse Analytics, consultez la page [Guide pratique pour configurer le contrôle d’accès pour votre espace de travail Synapse](../synapse-analytics/security/how-to-set-up-access-control.md).

1. Ajoutez la MSI du compte Azure Purview (représentée par le nom du compte) comme **db_datareader** sur la base de données SQL dédiée. Pour ce faire, exécutez la commande suivante dans votre script SQL :

    ```sql
    CREATE USER [PurviewAccountName] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_datareader', [PurviewAccountName]
    GO
    ```

#### <a name="use-a-managed-identity-for-serverless-sql-databases"></a>Utiliser une identité managée pour les bases de données SQL serverless

1. Accédez à votre espace de travail Azure Synapse.
1. Accédez à la section **Données** et procédez comme suit pour **chaque base de données que vous souhaitez analyser**.
1. Sélectionnez l’icône de points de suspension ( **...** ) placée à côté de votre base de données, puis démarrez un nouveau script SQL.
1. Ajoutez la MSI du compte Azure Purview (représentée par le nom du compte) comme **db_datareader** sur les bases de données SQL serverless. Pour ce faire, exécutez la commande suivante dans votre script SQL :
    ```sql
    CREATE USER [PurviewAccountName] FOR LOGIN [PurviewAccountName];
    ALTER ROLE db_datareader ADD MEMBER [PurviewAccountName]; 
    ```

#### <a name="grant-permission-to-use-credentials-for-external-tables"></a>Accorder l’autorisation d’utiliser les informations d’identification pour les tables externes

Si l’espace de travail Azure Synapse contient des tables externes, l’identité managée Azure Purview doit disposer de l’autorisation Références sur les informations d’identification délimitées à la table externe. Avec l’autorisation Références, Azure Purview peut lire les données à partir de tables externes.

```sql
GRANT REFERENCES ON DATABASE SCOPED CREDENTIAL::[scoped_credential] TO [PurviewAccountName];
```

#### <a name="use-a-service-principal-for-dedicated-sql-databases"></a>Utiliser un principal de service pour les bases de données SQL dédiées

> [!NOTE]
> Vous devez d’abord configurer de nouvelles *informations d’identification* de type *Principal de service* en suivant les instructions de la page [Informations d'identification pour l'authentification des sources dans Azure Purview](manage-credentials.md).

1. Accédez à votre **espace de travail Azure Synapse**.
1. Accédez à la section **Données**, puis à l’une de vos bases de données SQL dédiées.
1. Sélectionnez l’icône de points de suspension ( **...** ) placée à côté, puis démarrez un nouveau script SQL.
1. Ajoutez l’**ID du principal de service** comme **db_datareader** sur la base de données SQL dédiée. Pour ce faire, exécutez la commande suivante dans votre script SQL :

    ```sql
    CREATE USER [ServicePrincipalID] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_datareader', [ServicePrincipalID]
    GO
    ```

> [!NOTE]
> Répétez l’étape précédente pour toutes les bases de données SQL dédiées dans votre espace de travail Synapse. 

#### <a name="use-a-service-principal-for-serverless-sql-databases"></a>Utiliser un principal de service pour les bases de données SQL serverless

1. Accédez à votre espace de travail Azure Synapse.
1. Accédez à la section **Données**, puis à l’une de vos bases de données SQL serverless.
1. Sélectionnez l’icône de points de suspension ( **...** ) placée à côté, puis démarrez un nouveau script SQL.
1. Ajoutez l’**ID de principal de service** sur les bases de données SQL serverless. Pour ce faire, exécutez la commande suivante dans votre script SQL :
    ```sql
    CREATE LOGIN [ServicePrincipalID] FROM EXTERNAL PROVIDER;
    ```
    
1. Ajoutez l’**ID de principal de service** en tant que **db_datareader** sur chaque base de données SQL serverless que vous voulez analyser. Pour ce faire, exécutez la commande suivante dans votre script SQL :
   ```sql
    CREATE USER [ServicePrincipalID] FOR LOGIN [ServicePrincipalID];
    ALTER ROLE db_datareader ADD MEMBER [ServicePrincipalID]; 
    ```

### <a name="set-up-azure-synapse-workspace-firewall-access"></a>Configurer l’accès à l’espace de travail Azure Synapse dans le pare-feu

1. Dans le portail Azure, accédez à l’espace de travail Azure Synapse. 

1. Dans le volet gauche, sélectionnez **Pare-feux**.

1. Cliquez sur **ON** (Activer) pour **Autoriser les services et ressources Azure à accéder à cet espace de travail**.

1. Sélectionnez **Enregistrer**.

### <a name="create-and-run-scan"></a>Créer et exécuter une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1. Sélectionnez l’onglet **Data Map** dans le volet gauche de [Purview Studio](https://web.purview.azure.com/resource/).

1. Sélectionnez la source de données que vous avez inscrite.

1. Sélectionnez **Afficher les détails**, puis l'onglet **Nouvelle analyse**. Vous pouvez également sélectionner l’icône d’action rapide **Analyse** sur la mosaïque source.

1. Dans le volet d’informations **Analyse**, dans la zone **Nom**, entrez un nom pour l’analyse.
1. Dans la liste déroulante **Type**, sélectionnez les types de ressources que vous souhaitez analyser au sein de cette source. **Base de données SQL** est le seul type que nous prenons en charge actuellement dans un espace de travail Azure Synapse.
   
    :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-setup.png" alt-text="Capture d’écran du volet d’informations de l’analyse de source Azure Synapse.":::

1. Dans la liste déroulante **Informations d’identification**, sélectionnez vos informations d’identification pour vous connecter aux ressources dans votre source de données. 
  
1. Dans chaque type, vous pouvez choisir d’analyser toutes les ressources ou un sous-ensemble par nom.

1.  Sélectionnez **Continuer** pour poursuivre. 

1.  Sélectionnez **Ensemble de règles d’analyse**, puis le type **Azure Synapse SQL**. Vous pouvez également créer des ensembles de règles d’analyse inline.

1. Choisissez votre déclencheur d’analyse. Vous pouvez planifier une exécution **hebdomadaire, mensuelle** ou **unique**.

1. Vérifiez votre analyse, puis sélectionnez **Enregistrer** pour terminer la configuration.  

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez inscrit votre source, suivez les guides ci-dessous pour en savoir plus sur Purview et sur vos données.

- [Insights de données dans Azure Purview](concept-insights.md)
- [Lignage dans Azure Purview](catalog-lineage-user-guide.md)
- [Rechercher dans un catalogue de données](how-to-search-catalog.md)
