---
title: Comment suspendre et reprendre des pools SQL dédiés avec des pipelines Synapse
description: Apprenez à automatiser la suspension et la reprise d’un pool SQL dédié avec des pipelines Synapse dans Azure Synapse Analytics.
author: julieMSFT
ms.author: jrasnick
ms.service: synapse-analytics
ms.reviewer: wiassaf
ms.subservice: sql
ms.topic: how-to
ms.date: 08/12/2021
ms.custom: template-how-to
ms.openlocfilehash: d31cb6d07958c5f14ac5e45f0094e772b168a535
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847923"
---
# <a name="pause-and-resume-dedicated-sql-pools-with-synapse-pipelines"></a>Suspendre et reprendre des pools SQL dédiés avec des pipelines Synapse

La suspension et la reprise de pools SQL dédiés peuvent être automatisées à l’aide de canaux Synapse dans Azure Synapse Analytics. L’interruption et la reprise peuvent être utilisées pour réduire les coûts d’un pool SQL dédié. Cette solution peut être facilement incluse dans un processus d’orchestration de données existant. 

Les étapes suivantes vous guident tout au long de la configuration de la suspension et de la reprise automatisées.

1. Créer un pipeline.
1. Configurez des paramètres dans votre pipeline.
1. Identifiez la liste des pools SQL dédiés dans votre espace de travail Azure Synapse.
1. Filtrez les pools SQL dédiés que vous ne souhaitez pas suspendre ou reprendre à partir de la liste. 
1. Parcourez chaque pool SQL dédié et :
    1. Vérifiez l’état du pool SQL dédié.
    1. Évaluez l’état du pool SQL dédié.
    1. Suspendez ou reprenez le pool SQL dédié.

Ces étapes sont présentées dans un pipeline simple dans Azure Synapse :

![Pipeline Synapse simple](./media/how-to-pause-resume-pipelines/simple-pipeline.png)


Selon la nature de votre environnement, l’ensemble du processus décrit ici peut ne pas s’appliquer, et vous pouvez simplement choisir les étapes appropriées. Le processus décrit ici peut être utilisé pour suspendre ou reprendre toutes les instances dans un environnement de développement, de test ou PoC. Dans le cas d’un environnement de production, il est plus probable que vous planifiiez une pause ou une reprise instance par instance, et vous n’aurez donc besoin que des étapes 5A à 5C.

Les étapes ci-dessus utilisent les API REST pour Azure Synapse et Azure SQL :

- [Opérations sur les pools SQL dédiés](/rest/api/synapse/sqlpools)
 
- [API REST Azure SQL Database](/rest/api/sql)

Les pipelines Synapse permettent l’automatisation de la suspension et de la reprise, mais vous pouvez exécuter ces commandes à la demande par le biais de l’outil ou de l’application de votre choix.

## <a name="prerequisites"></a>Prérequis

- Un [espace de travail Azure Synapse](../get-started-create-workspace.md) existant
- Au moins un [pool SQL dédié](../get-started-analyze-sql-pool.md)
- Le rôle Contributeur Azure doit être attribué à votre espace de travail. Consultez [Accorder aux administrateurs de Synapse le rôle Contributeur Azure sur l’espace de travail](../security/how-to-set-up-access-control.md#step-5-grant-synapse-administrators-the-azure-contributor-role-on-the-workspace).

## <a name="step-1-create-a-pipeline-in-synapse-studio"></a>Étape 1 : Créer un pipeline dans Synapse Studio.
1. Accédez à votre espace de travail et ouvrez Synapse Studio. 
1. Sélectionnez l’icône **Intégrer**, puis sélectionnez le signe **+** pour créer un nouveau pipeline. 
1. Nommez votre pipeline PauseResume.

    ![Créer un pipeline dans Synapse Studio](./media/how-to-pause-resume-pipelines/create-pipeline.png) 

## <a name="step-2-create-pipeline-parameters"></a>Étape 2 : Créer des paramètres de pipeline 

Le pipeline que vous allez créer reposera sur des paramètres. Les paramètres vous permettent de créer un pipeline générique que vous pouvez utiliser sur plusieurs abonnements, groupes de ressources ou pools SQL dédiés. Sélectionnez l’onglet **Paramètres** en bas de l’écran du pipeline. Sélectionnez **+ Nouveau** pour créer chacun des paramètres suivants :

    
|Nom  |Type  |Valeur par défaut  |Description|
|---------|---------|---------|-----------|
|ResourceGroup    |string        |Synapse          |Nom du groupe de ressources pour vos pools SQL dédiés|
|SubscriptionID   |string        |`<SubscriptionID>` |ID d’abonnement pour votre groupe de ressources|
|WorkspaceName    |string        |Synapse          |Nom de votre espace de travail|
|SQLPoolName      |string        |SQLPool1         |Nom de votre pool SQL dédié|
|PauseorResume    |string        |Suspendre            |L’état souhaité à la fin de l’exécution du pipeline|

![Paramètres de pipeline dans Synapse Studio.](./media/how-to-pause-resume-pipelines/pipeline-parameters.png)

## <a name="step-3-create-list-of-dedicated-sql-pools"></a>Étape 3 : Créer une liste de pools SQL dédiés
 
 Configurez une activité **Web**. Vous utiliserez cette activité pour créer la liste des pools SQL dédiés en appelant la requête d’API REST Pools SQL dédiés - Liste par serveur. La sortie est une chaîne JSON qui contient une liste des pools SQL dédiés dans votre espace de travail. La chaîne JSON est passée à l’activité suivante.
1. Sous **Activités** > **Général**, faites glisser une activité **Web** vers le canevas du pipeline en tant que première étape de votre pipeline.  
1. Sous l’onglet **Général**, nommez cette étape GET List. 
1. Sélectionnez l’onglet **Paramètres**, cliquez dans l’espace d’entrée **URL**, puis sélectionnez **Ajouter du contenu dynamique**. Copiez et collez la requête GET qui a été paramétrée à l’aide de la fonction de chaîne @concat ci-dessous dans la zone de contenu dynamique. Sélectionnez **Terminer**.
Le code suivant est un exemple simple de requête Get :

    ```HTTP
    GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Synapse/workspaces/{workspace-name}/sqlPools?api-version=2019-06-01-preview
    ```
    
    Requête GET paramétrée à l’aide de la fonction de chaîne @concat :
    
    ```HTTP
    @concat('https://management.azure.com/subscriptions/',pipeline().parameters.SubscriptionID,'/resourceGroups/',pipeline().parameters.ResourceGroup,'/providers/Microsoft.Synapse/workspaces/',pipeline().parameters.WorkspaceName,'/sqlPools?api-version=2019-06-01-preview')
    ``` 
1. Sélectionnez la liste déroulante pour **Méthode** et sélectionnez **Get**.  
1. Sélectionnez **Avancé** pour développer le contenu. Sélectionnez **MSI** comme type d’authentification. Pour Ressource, entrez `https://management.azure.com/` 
    > [!IMPORTANT]
    > Pour tous les appels d’activités web/d’API REST, vous devez vous assurer que le pipeline Synapse est authentifié par rapport au pool SQL dédié. Une [identité managée](../../data-factory/control-flow-web-activity.md#managed-identity) est nécessaire pour exécuter ces appels d’API REST. 
        
        
    ![Liste des activités web pour les pools SQL dédiés](./media/how-to-pause-resume-pipelines/web-activity-list-sql-pools.png)



## <a name="step-4-filter-dedicated-sql-pools"></a>Étape 4 : Filtrer les pools SQL dédiés
Supprimez les pools SQL dédiés que vous ne souhaitez pas suspendre ou reprendre. Utilisez une activité de filtre qui filtre les valeurs transmises à partir de l’activité GET List.  Dans cet exemple, nous extrayons les enregistrements du tableau qui ne contiennent pas « prod » dans le nom. Appliquez d’autres conditions si nécessaire. Par exemple, filtrez sur la référence SKU/le nom de l’espace de travail Synapse pour vous assurer que seuls les pools SQL dédiés valides sont identifiés.
1. Sélectionnez et faites glisser l’activité **Filtre** sous **Itérations et conditions** dans le canevas du pipeline.    
![Filtrer les pools SQL dédiés](./media/how-to-pause-resume-pipelines/filter-sql-pools.png)    
1. Connectez l’activité web GET List à l’activité de filtre. Sélectionnez l’onglet vert sur l’activité web et faites-le glisser vers la zone de filtre.
1. Entrez `@activity('Get list').output.value` pour les **Éléments** où GET List est le nom de l’activité web précédente
1. Entrez `@not(endswith(item().name,'prod'))` pour **Condition**. Les enregistrements restants dans le tableau sont ensuite transmis à l’activité suivante.

## <a name="step-5-create-a-foreach-loop"></a>Étape 5 : Créer une boucle ForEach
Créez une activité ForEach pour effectuer une boucle sur chaque pool SQL dédié. 
1. Sélectionnez et faites glisser l’activité **ForEach** sous **Itérations et conditions** dans le canevas du pipeline.
1. Dans l’onglet **Général**, nommez l’activité. Nous avons utilisé « ForEach_pool ». 
1. Dans l’onglet Paramètres, sélectionnez l’entrée **Éléments**, puis sélectionnez **Ajouter du contenu dynamique**. Faites défiler jusqu’aux **Sorties d’activité**, puis sélectionnez la sortie de votre activité de filtre. Ajoutez `.value` à l’activité. la valeur doit être similaire à `@activity('Filter_PROD').output.value`. Sélectionnez **Terminer**.    
    ![Parcourir les pools SQL dédiés](./media/how-to-pause-resume-pipelines/loop-through-sql-pools.png)
1. Sélectionnez l’onglet **Activités** et sélectionnez le crayon Modifier pour ouvrir la zone de dessin de boucle ForEach. 

## <a name="step-5a-check-the-state-of-the-dedicated-sql-pools"></a>Étape 5a : Vérifier l’état des pools SQL dédiés    
La vérification de l’état du pool SQL dédié requiert une activité web, similaire à l’étape 1. Cette activité appelle l’[API REST Vérifier l’état du pool SQL dédié pour Azure Synapse](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md#check-database-state). 
1. Sélectionnez et faites glisser une activité **Web** sous **Général** vers le canevas du pipeline.    
2. Sous l’onglet **Général**, nommez cette étape CheckState. 
3. Sélectionnez l’onglet **Settings** (Paramètres).     
4. Cliquez dans l’espace d’entrée **URL**, puis sélectionnez **Ajouter du contenu dynamique**. Copiez et collez la requête GET qui a été paramétrée à l’aide de la fonction de chaîne @concat d’en dessous dans la zone de contenu dynamique. Sélectionnez **Terminer**. La vérification de l’état utilise à nouveau une requête Get à l’aide de l’appel suivant :

    ```HTTP
    GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Synapse/workspaces/{workspace-name}/sqlPools/{database-name}?api-version=2019-06-01-preview HTTP/1.1
    ```
    
    La requête GET paramétrisée avec la fonction de chaîne @concat :
    
    ```HTTP
    @concat('https://management.azure.com/subscriptions/',pipeline().parameters.SubscriptionID,'/resourceGroups/',pipeline().parameters.ResourceGroup,'/providers/Microsoft.Synapse/workspaces/',pipeline().parameters.WorkspaceName,'/sqlPools/',item().name,'?api-version=2019-06-01-preview')
    ```
    
    Dans ce cas, nous utilisons item().Name, qui est le nom du pool SQL dédié de l’étape 1 qui a été passé à cette activité à partir de la boucle ForEach. Si vous utilisez un pipeline pour contrôler un seul pool SQL dédié, vous pouvez incorporer le nom de votre pool SQL dédié ici ou utiliser un paramètre du pipeline. Par exemple, vous pouvez utiliser pipeline().parameters.SQLPoolName.

    La sortie est une chaîne JSON qui contient les détails du pool SQL dédié, y compris son état (dans properties.status). La chaîne JSON est passée à l’activité suivante. 
1. Sélectionnez la liste déroulante pour **Méthode** et sélectionnez **Get**. Sélectionnez **Avancé** pour développer le contenu. Sélectionnez **MSI** comme type d’authentification. Pour Ressource, entrez `https://management.azure.com/` 

![Vérifier l’état du pool SQL dédié](./media/how-to-pause-resume-pipelines/check-sql-pool-state.png)
    

    
## <a name="step-5b-evaluate-the-state-of-the-dedicated-sql-pools"></a>Étape5b : Évaluer l’état des pools SQL dédiés
Évaluez l’état souhaité, Suspendre ou Reprendre, et l’état actuel, En ligne ou Suspendre, puis lancez Suspendre ou Reprendre si nécessaire.

1. Sélectionnez et faites glisser l’activité **Switch** sous **Itérations et conditions** dans le canevas du pipeline.    
1. Connectez l’activité **Switch** à l’activité **CheckState**. Sélectionnez l’onglet vert sur l’activité web et faites-le glisser vers la zone Switch.  
1. Sous l’onglet **Général**, nommez l’étape State-PauseOrResume. 

    En fonction de l’état souhaité et de l’état actuel, seules les deux combinaisons suivantes nécessitent une modification de l’état : Suspendre -> Rependre ou En ligne -> Suspendre. 

1. Dans l’onglet **Activités**, copiez le code ci-dessous dans l’**Expression**.

    ```HTTP
    @concat(activity('CheckState').output.properties.status,'-',pipeline().parameters.PauseOrResume)
    ```
    
    Où Check State est le nom de l’activité web précédente avec output.properties.status définissant l’état actuel et pipeline().parameters.PauseOrResume indique l’état souhaité.
    
    La condition check effectue une vérification de l’état souhaité et de l’état actuel. Si l’état souhaité est Reprendre et que l’état actuel est En pause, une activité de reprise est appelée dans le cas En pause-Reprendre. Si l’état souhaité est Suspendre et que l’état actuel est En ligne, une activité Suspendre est appelée avec le cas En ligne-Suspendre. Tout autre cas, tel qu’un état souhaité de Suspendre et un état actuel En pause, ou un état souhaité de Reprendre et un état actuel En ligne, ne nécessite aucune action et est géré par le cas par défaut, qui n’a aucune activité.
1. Dans l’onglet Activités, sélectionnez **+ Ajouter un cas**.  Ajoutez les cas `Paused-Resume` et `Online-Pause`. 
    ![Vérifier la condition d’état du pool SQL dédié](./media/how-to-pause-resume-pipelines/check-condition.png)

### <a name="step-5c-pause-or-resume-dedicated-sql-pools"></a>Étape 5c : Suspendre ou reprendre des pools SQL dédiés     

L’étape finale, et la seule pertinente pour certaines exigences, consiste à lancer la mise en pause ou la reprise de votre pool SQL dédié. Cette étape utilise de nouveau une activité web, en appelant l’[API REST Suspendre ou Reprendre pour Azure Synapse](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md#pause-compute). 
1. Sélectionnez le crayon de modification d’activité et ajoutez une activité **Web** au canevas State-PauseorResume. 
1. Sélectionnez l’onglet **Paramètres**, cliquez dans l’espace d’entrée **URL**, puis sélectionnez **Ajouter du contenu dynamique**. Copiez et collez la requête POST qui a été paramétrée à l’aide de la fonction de chaîne @concat ci-dessous dans la zone de contenu dynamique. Sélectionnez **Terminer**. 
1. Sélectionnez la liste déroulante pour **Méthode** et sélectionnez **POST**.
1. Dans la section corps, tapez « Suspendre et Reprendre ».
1. Sélectionnez **Avancé** pour développer le contenu. Sélectionnez **MSI** comme type d’authentification. Pour Ressource, entrez `https://management.azure.com/` 
1. Ajoutez une deuxième activité pour la fonctionnalité de reprise à l’aide du code paramétré ci-dessous.

    ![Reprendre un pool SQL dédié](./media/how-to-pause-resume-pipelines/true-condition-resume.png)
    
    
    L’exemple suivant consiste à reprendre un pool SQL dédié, en appelant une requête POST à l’aide de l’appel suivant :
    
    ```HTTP
    POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Synapse/workspaces/{workspace-name}/sqlPools/{database-name}/resume?api-version=2019-06-01-preview HTTP/1.1
    ```
    
    Vous pouvez paramétrer l’instruction POST ci-dessus à l’aide de la fonction de chaîne @concat :
    
    ```HTTP
    @concat('https://management.azure.com/subscriptions/',pipeline().parameters.SubscriptionID,'/resourceGroups/',pipeline().parameters.ResourceGroup,'/providers/Microsoft.Synapse/workspaces/',pipeline().parameters.WorkspaceName,'/sqlPools/',activity('CheckState').output.name,'/resume?api-version=2019-06-01-preview')
    ```
    
    Dans ce cas, nous utilisons l’activité 'Check State'.output.name avec les noms des pools SQL dédiés de l’étape 3A qui ont été transmis à cette activité via la condition Switch. Si vous utilisez une seule activité sur une seule base de données, vous pouvez incorporer le nom de votre pool SQL dédié ici ou utiliser un paramètre du pipeline. Par exemple, vous pouvez utiliser pipeline().parameters.DatabaseName.
    
    La requête POST pour suspendre un pool SQL dédié est la suivante :
    
    ```HTTP
    POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Synapse/workspaces/{workspace-name}/sqlPools/{database-name}/pause?api-version=2019-06-01-preview HTTP/1.1    
    ```    
    
    La requête POST peut être paramétrée à l’aide de la fonction de chaîne @concat, comme indiqué ici :
    
    ```HTTP
    @concat('https://management.azure.com/subscriptions/',pipeline().parameters.SubscriptionID,'/resourceGroups/',pipeline().parameters.ResourceGroup,'/providers/Microsoft.Synapse/workspaces/',pipeline().parameters.WorkspaceName,'/sqlPools/',activity('CheckState').output.name,'/pause?api-version=2019-06-01-preview')
    ```

## <a name="pipeline-run-output"></a>Sortie de l’exécution du pipeline

Lorsque le pipeline complet est exécuté, la sortie indiquée ci-dessous s’affiche. Vous pouvez exécuter votre pipeline en sélectionnant le mode **Débogage** ou **Ajouter un déclencheur**. Pour les résultats du pipeline ci-dessous, le paramètre de pipeline nommé « ResourceGroup » a été défini sur un groupe de ressources unique qui avait deux espaces de travail Synapse. L’un était nommé testprod et a été filtré, tandis que le second était nommé test1. Le pool SQL dédié test1 a été suspendu, le travail a donc initié une reprise.

![Sortie de l’exécution du pipeline](./media/how-to-pause-resume-pipelines/pipeline-run-output.png)

## <a name="save-your-pipeline"></a>Enregistrer votre pipeline

Pour enregistrer votre pipeline, sélectionnez **Publier tout** au-dessus de votre pipeline.

## <a name="schedule-your-pause-or-resume-pipeline-to-run"></a>Planifier l’exécution de votre pipeline de suspension ou de reprise

Pour planifier votre pipeline, sélectionnez **Ajouter un déclencheur** en haut de votre pipeline. Suivez les écrans pour planifier l’exécution de votre pipeline à une heure spécifiée.

![Sélectionner un déclencheur pour définir l’heure à laquelle le pipeline doit s’exécuter](./media/how-to-pause-resume-pipelines/trigger.png)

## <a name="next-steps"></a>Étapes suivantes

Vous trouverez plus d’informations sur l’identité managée pour Azure Synapse et sur l’ajout d’une identité managée à votre pool SQL dédié ici :

[Identité managée de l’espace de travail Azure Synapse](../../data-factory/data-factory-service-identity.md?context=/azure/synapse-analytics/context/context&tabs=synapse-analytics)

[Octroyer des autorisations à une identité managée de l’espace de travail](../security/how-to-grant-workspace-managed-identity-permissions.md)
