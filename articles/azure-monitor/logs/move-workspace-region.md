---
title: Déplacer un espace de travail Log Analytics vers une autre région Azure à l’aide du portail Azure
description: Utilisez un modèle Azure Resource Manager pour déplacer espace de travail Log Analytics d’une région Azure à une autre à l’aide du portail Azure.
author: yossiy
ms.topic: how-to
ms.date: 08/17/2021
ms.author: yossiy
ms.openlocfilehash: 01d7852e514931a1546a9ab66c3b582bea115f2b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319621"
---
# <a name="move-a-log-analytics-workspace-to-another-region-by-using-the-azure-portal"></a>Déplacer un espace de travail Log Analytics vers une autre région à l’aide du portail Azure

Il existe différents scénarios dans lesquels vous pouvez être amené à déplacer votre espace de travail Log Analytics existant d’une région à une autre. Par exemple, Log Analytics est récemment devenu disponible dans une région qui héberge la plupart de vos ressources et vous voulez que l’espace de travail soit plus proche et fasse des économies sur les frais de sortie. Vous pouvez également souhaiter déplacer votre espace de travail vers une région nouvellement ajoutée pour des raisons de souveraineté des données.

Un espace de travail Log Analytics ne peut pas être déplacé d’une région à une autre. Toutefois, vous pouvez utiliser un modèle Azure Resource Manager pour exporter la ressource d’espace de travail et les ressources associées. Vous pouvez ensuite indexer les ressources dans une autre région en exportant l’espace de travail vers un modèle, en modifiant les paramètres pour qu’ils correspondent à la région de destination, puis en déployant le modèle dans la nouvelle région. Pour plus d’informations sur Resource Manager et les modèles, consultez [Démarrage rapide : Créer et déployer des modèles Azure Resource Manager à l’aide du portail Azure](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md). 

Un environnement d’espace de travail peut être complexe et inclure des sources connectées, des solutions gérées, des services liés, des alertes et des packs de requêtes. Toutes les ressources ne peuvent pas être exportées dans un modèle Resource Manager, et certaines nécessitent une configuration distincte lorsque vous déplacez un espace de travail.

## <a name="prerequisites"></a>Prérequis

- Pour exporter la configuration de l’espace de travail vers un modèle qui peut être déployé dans une autre région, vous devez avoir le rôle [Contributeur Log Analytics](../../role-based-access-control/built-in-roles.md#log-analytics-contributor) ou [Contributeur d’analyse](../../role-based-access-control/built-in-roles.md#monitoring-contributor), ou avoir un rôle ayant des privilèges plus élevés.

- Identifiez toutes les ressources actuellement associées à votre espace de travail, notamment :
  - *Agents connectés* : Accédez à **Journaux** dans votre espace de travail et interrogez la table [heartbeat](../insights/solution-agenthealth.md#azure-monitor-log-records) pour répertorier les agents connectés.
    ```kusto
    Heartbeat
    | summarize by Computer, Category, OSType, _ResourceId
    ```
  - *Paramètres de diagnostic* : Les ressources peuvent envoyer des journaux à Diagnostics Azure ou à des tables dédiées dans votre espace de travail. Accédez à **Journaux** dans votre espace de travail, puis exécutez cette requête pour les ressources qui envoient des données à la table `AzureDiagnostics` :

    ```kusto
    AzureDiagnostics
    | where TimeGenerated > ago(12h)
    | summarize by  ResourceProvider , ResourceType, Resource
    | sort by ResourceProvider, ResourceType
    ```

    Exécutez cette requête pour les ressources qui envoient des données à des tables dédiées :

    ```kusto
    search *
    | where TimeGenerated > ago(12h)
    | where isnotnull(_ResourceId)
    | extend ResourceProvider = split(_ResourceId, '/')[6]
    | where ResourceProvider !in ('microsoft.compute', 'microsoft.security')
    | extend ResourceType = split(_ResourceId, '/')[7]
    | extend Resource = split(_ResourceId, '/')[8]
    | summarize by tostring(ResourceProvider) , tostring(ResourceType), tostring(Resource)
    | sort by ResourceProvider, ResourceType
    ```

  - *Solutions installées* : Sélectionnez **Solutions** dans le volet de navigation de l’espace de travail pour obtenir la liste des solutions installées.
  - *API de collecte de données* : Les données arrivant par le biais d’une [API de collecte de données](../logs/data-collector-api.md) sont stockées dans des tables de journaux personnalisés. Pour obtenir la liste des tables de journaux personnalisés, sélectionnez **Journaux** dans le volet de navigation de l’espace de travail, puis sélectionnez **Journal personnalisé** dans le volet du schéma.
  - *Services liés* : Les espaces de travail peuvent avoir des services liés à des ressources dépendantes telles qu’un compte Azure Automation, un compte de stockage ou un cluster dédié. Supprimez les services liés de votre espace de travail. Reconfigurez-les manuellement dans l’espace de travail cible.
  - *Alertes* : Pour répertorier les alertes, sélectionnez **Alertes** dans le volet de navigation de votre espace de travail, puis sélectionnez **Gérer les règles d’alerte** dans la barre d’outils. Les alertes des espaces de travail créés après le 1er juin 2019 ou des espaces de travail qui ont été [mis à niveau de l’API d’alerte Log Analytics vers l’API scheduledQueryRules](../alerts/alerts-log-api-switch.md) peuvent être incluses dans le modèle. 
  
     Vous pouvez [vérifier si l’API scheduledQueryRules est utilisée pour les alertes dans votre espace de travail](../alerts/alerts-log-api-switch.md#check-switching-status-of-workspace). Vous pouvez également configurer manuellement les alertes dans l’espace de travail cible.
  - *Packs de requêtes* : Un espace de travail peut être associé à plusieurs packs de requêtes. Pour identifier les packs de requêtes dans votre espace de travail, sélectionnez **Journaux** dans le volet de navigation de l’espace de travail, sélectionnez **requêtes** dans le volet gauche, puis cliquez sur le bouton de sélection à droite de la zone de recherche. Une boîte de dialogue avec les packs de requêtes sélectionnés s’ouvre à droite. Si vos packs de requêtes se trouvent dans le même groupe de ressources que l’espace de travail que vous déplacez, vous pouvez les inclure dans cette migration.
- Vérifiez que votre abonnement Azure vous permet de créer des espaces de travail Log Analytics dans la région cible.

## <a name="prepare-and-move"></a>Préparer et déplacer
Les procédures suivantes montrent comment préparer l’espace de travail et les ressources pour le déplacement en utilisant un modèle Resource Manager, puis comment les déplacer vers la région cible en utilisant le portail. Suivez les procédures dans l’ordre.

> [!NOTE]
> Toutes les ressources ne peuvent pas être exportées par le biais d’un modèle. Vous devez les configurer séparément après la création de l’espace de travail dans la région cible.

### <a name="select-resource-groups-and-edit-parameters"></a>Sélectionner les groupes de ressources et modifier les paramètres

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis sélectionnez **Groupes de ressources**.
1. Recherchez le groupe de ressources contenant votre espace de travail et sélectionnez-le.
1. Pour afficher une ressource d’alerte, cochez la case **Afficher les types cachés**.
1. Sélectionnez le filtre **Type**. Sélectionnez **Espace de travail Log Analytics**, **Solution**, **SavedSearches**, **microsoft.insights/scheduledqueryrules**, **defaultQueryPack** et d’autres ressources liées à l’espace de travail dont vous disposez (par exemple, un compte Automation). Ensuite, sélectionnez **Appliquer**.
1. Sélectionnez l’espace de travail, les solutions, les recherches enregistrées, les alertes, les packs de requêtes et les autres ressources liées à l’espace de travail dont vous disposez (par exemple, un compte Automation). Sélectionnez ensuite **Exporter le modèle** dans la barre d’outils.
    
    > [!NOTE]
    > Microsoft Sentinel ne peut pas être exporté avec un modèle. Vous devez [intégrer Sentinel](../../sentinel/quickstart-onboard.md) à un espace de travail cible.
   
1. Sélectionnez **Déployer** dans la barre d’outils pour modifier et préparer le modèle pour le déploiement.
1. Sélectionnez **Modifier les paramètres** dans la barre d’outils pour ouvrir le fichier *parameters.json* dans l’éditeur en ligne.
1. Pour modifier les paramètres, modifiez la propriété `value` sous `parameters`. Voici un exemple :

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaces_name": {
          "value": "my-workspace-name"
        },
        "workspaceResourceId": {
          "value": "/subscriptions/resource-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/workspaces/workspace-name"
        },
        "alertName": {
          "value": "my-alert-name"
        },
        "querypacks_name": {
          "value": "my-default-query-pack-name"
        }
      }
    }
    ```

1. Sélectionnez **Enregistrer** dans l’éditeur.

### <a name="edit-the-template"></a>Modifier le modèle

1. Sélectionnez **Modifier le modèle** dans la barre d’outils pour ouvrir le fichier *template.json* dans l’éditeur en ligne.
1. Pour modifier la région cible dans laquelle l’espace de travail Log Analytics sera déployé, modifiez la propriété `location` sous `resources` dans l’éditeur en ligne. 

   Pour obtenir les codes d’emplacement des régions, consultez [Résidence des données dans Azure](https://azure.microsoft.com/global-infrastructure/locations/). Le code d’une région est le nom de la région sans espace. Par exemple, **Central US** (nom de la région USA Centre en anglais) doit être `centralus`.
1. Supprimez les ressources des services liés (`microsoft.operationalinsights/workspaces/linkedservices`) si elles sont présentes dans le modèle. Vous devez reconfigurer ces ressources manuellement dans l’espace de travail cible.

   L’exemple de modèle suivant inclut l’espace de travail, la recherche enregistrée, les solutions, l’alerte et le pack de requêtes :

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaces_name": {
          "type": "String"
        },
        "workspaceResourceId": {
          "type": "String"
        },
        "alertName": {
          "type": "String"
        },
        "querypacks_name": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [
        {
          "type": "microsoft.operationalinsights/workspaces",
          "apiVersion": "2020-08-01",
          "name": "[parameters('workspaces_name')]",
          "location": "france central",
          "properties": {
            "sku": {
              "name": "pergb2018"
            },
            "retentionInDays": 30,
            "features": {
              "enableLogAccessUsingOnlyResourcePermissions": true
            },
            "workspaceCapping": {
              "dailyQuotaGb": -1
            },
            "publicNetworkAccessForIngestion": "Enabled",
            "publicNetworkAccessForQuery": "Enabled"
          }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
          "apiVersion": "2020-08-01",
          "name": "[concat(parameters('workspaces_name'), '/2b5112ec-5ad0-5eda-80e9-ad98b51d4aba')]",
          "dependsOn": [
            "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaces_name'))]"
          ],
          "properties": {
            "category": "VM Monitoring",
            "displayName": "List all versions of curl in use",
            "query": "VMProcess\n| where ExecutableName == \"curl\"\n| distinct ProductVersion",
            "tags": [],
            "version": 2
          }
        },
        {
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "2015-11-01-preview",
          "name": "[concat('Updates(', parameters('workspaces_name'))]",
          "location": "france central",
          "dependsOn": [
            "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name'))]"
          ],
          "plan": {
            "name": "[concat('Updates(', parameters('workspaces_name'))]",
            "promotionCode": "",
            "product": "OMSGallery/Updates",
            "publisher": "Microsoft"
          },
          "properties": {
            "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name'))]",
            "containedResources": [
              "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name')), '/views/Updates(', parameters('workspaces_name'), ')')]"
            ]
          }
        }
        {
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "2015-11-01-preview",
          "name": "[concat('VMInsights(', parameters('workspaces_name'))]",
          "location": "france central",
          "plan": {
            "name": "[concat('VMInsights(', parameters('workspaces_name'))]",
            "promotionCode": "",
            "product": "OMSGallery/VMInsights",
            "publisher": "Microsoft"
          },
          "properties": {
            "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name'))]",
            "containedResources": [
              "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name')), '/views/VMInsights(', parameters('workspaces_name'), ')')]"
            ]
          }
        },
        {
          "type": "microsoft.insights/scheduledqueryrules",
          "apiVersion": "2021-08-01",
          "name": "[parameters('alertName')]",
          "location": "france central",
          "properties": {
            "displayName": "[parameters('alertName')]",
            "severity": 3,
            "enabled": true,
            "evaluationFrequency": "PT5M",
            "scopes": [
              "[parameters('workspaceResourceId')]"
            ],
            "windowSize": "PT15M",
            "criteria": {
              "allOf": [
                {
                  "query": "Heartbeat | where computer == 'my computer name'",
                  "timeAggregation": "Count",
                  "operator": "LessThan",
                  "threshold": 14,
                  "failingPeriods": {
                    "numberOfEvaluationPeriods": 1,
                    "minFailingPeriodsToAlert": 1
                  }
                }
              ]
            },
            "autoMitigate": true,
            "actions": {}
          }
        },
        {
          "type": "Microsoft.OperationalInsights/querypacks",
          "apiVersion": "2019-09-01-preview",
          "name": "[parameters('querypacks_name')]",
          "location": "francecentral",
          "properties": {}
        },
        {
          "type": "Microsoft.OperationalInsights/querypacks/queries",
          "apiVersion": "2019-09-01-preview",
          "name": "[concat(parameters('querypacks_name'), '/00000000-0000-0000-0000-000000000000')]",
          "dependsOn": [
            "[resourceId('Microsoft.OperationalInsights/querypacks', parameters('querypacks_name'))]"
          ],
          "properties": {
            "displayName": "my-query-name",
            "body": "my-query-text",
            "related": {
              "categories": [],
              "resourceTypes": [
                  "microsoft.operationalinsights/workspaces"
              ]
            },
            "tags": {
              "labels": []
            }
          }
        }
      ]
    }
    ```

1. Sélectionnez **Enregistrer** dans l’éditeur en ligne.

### <a name="deploy-the-workspace"></a>Déployer l’espace de travail

1. Sélectionnez **Abonnement** pour choisir l’abonnement dans lequel l’espace de travail cible sera déployé.
1. Sélectionnez **Groupe de ressources** pour choisir le groupe de ressources dans lequel l’espace de travail cible sera déployé. Vous pouvez sélectionner **Créer nouveau** pour créer un groupe de ressources pour l’espace de travail cible.
1. Vérifiez que le paramètre **Région** est défini sur l’emplacement cible où vous voulez déployer le groupe de sécurité réseau.
1. Sélectionnez le bouton **Vérifier + créer** pour vérifier votre modèle.
1. Sélectionnez **Créer** pour déployer l’espace de travail et la ressource sélectionnée dans la région cible.
1. Votre espace de travail, y compris les ressources sélectionnées, est maintenant déployé dans la région cible. Vous pouvez terminer la configuration restante dans l’espace de travail pour jumeler les fonctionnalités avec celles de l’espace de travail d’origine.
   - *Connecter des agents* : Utilisez l’une des options disponibles, notamment Règles de collecte de données, pour configurer les agents requis sur les machines virtuelles et les groupes de machines virtuelles identiques et pour spécifier le nouvel espace de travail cible comme destination.
   - *Paramètres de diagnostic* : Mettez à jour les paramètres de diagnostic dans les ressources identifiées, avec l’espace de travail cible comme destination.
   - *Installer des solutions* : Certaines solutions, comme [Microsoft Sentinel](../../sentinel/quickstart-onboard.md), nécessitent des procédures d’intégration particulières et n’ont pas été incluses dans le modèle. Vous devez les intégrer séparément au nouvel espace de travail.
   - *Configurer l’API de collecte de données* : Configurez les instances de l’API de collecte de données pour envoyer des données à l’espace de travail cible.
   - *Configurer des règles d’alerte* : Lorsque les alertes ne sont pas exportées dans le modèle, vous devez les configurer manuellement dans l’espace de travail cible.
1. Vérifiez que les nouvelles données ne sont pas ingérées dans l’espace de travail d’origine. Exécutez la requête suivante dans votre espace de travail d’origine, et observez qu’il n’y a pas d’ingestion après la migration :

    ```kusto
    search *
    | where TimeGenerated > ago(12h)
    | summarize max(TimeGenerated) by Type
    ```

Une fois les sources de données connectées à l’espace de travail cible, les données ingérées sont stockées dans l’espace de travail cible. Les anciennes données restent dans l’espace de travail d’origine et sont soumises à la stratégie de rétention. Vous pouvez effectuer une [requête sur plusieurs espaces de travail](./cross-workspace-query.md#performing-a-query-across-multiple-resources). Si les deux espaces de travail ont reçu le même nom, utilisez un nom qualifié (*subscriptionName/resourceGroup/componentName*) dans la référence de l’espace de travail.

Voici un exemple de requête sur deux espaces de travail portant le même nom :

```kusto
union 
  workspace('subscription-name1/<resource-group-name1/<original-workspace-name>')Update, 
  workspace('subscription-name2/<resource-group-name2/<target-workspace-name>').Update, 
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="discard"></a>Abandonner

Si vous souhaitez abandonner l’espace de travail source, supprimez les ressources exportées ou le groupe de ressources qui les contient :

1. Sélectionnez le groupe de ressources cible dans le portail Azure.
1. Sur la page **Vue d’ensemble** :
   
   - Si vous avez créé un nouveau groupe de ressources pour ce déploiement, sélectionnez **Supprimer le groupe de ressources** dans la barre d’outils pour supprimer le groupe de ressources.
   - Si le modèle a été déployé dans un groupe de ressources existant, sélectionnez les ressources qui ont été déployées avec le modèle, puis sélectionnez **Supprimer** dans la barre d’outils pour supprimer les ressources sélectionnées.

## <a name="clean-up"></a>Nettoyage

Alors que les nouvelles données sont ingérées dans votre nouvel espace de travail, les anciennes données de l’espace de travail d’origine restent disponibles pour les requêtes et sont soumises à la stratégie de rétention définie dans l’espace de travail. Nous vous recommandons de conserver l’espace de travail d’origine aussi longtemps que vous aurez besoin des anciennes données pour [interroger plusieurs](./cross-workspace-query.md#performing-a-query-across-multiple-resources) espaces de travail. 

Si vous n’avez plus besoin d’accéder aux anciennes données de l’espace de travail d’origine :

1. Sélectionnez le groupe de ressources d’origine dans le portail Azure. 
1. Sélectionnez toutes les ressources que vous souhaitez supprimer, puis sélectionnez **Supprimer** dans la barre d’outils.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez déplacé un espace de travail Log Analytics et les ressources associées d’une région à une autre et nettoyé les ressources sources. Pour plus d’informations sur le déplacement de ressources entre régions et la reprise d’activité dans Azure, consultez :

- [Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Déplacer des machines virtuelles Azure vers une autre région](../../site-recovery/azure-to-azure-tutorial-migrate.md)
