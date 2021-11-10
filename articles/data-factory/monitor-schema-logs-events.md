---
title: Schéma des journaux et des événements
description: En savoir plus sur le schéma utilisé par les journaux et événements Azure Data Factory pour la surveillance.
author: joshuha-msft
ms.author: joowen
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 0245d988ab9b116a515ec76ecb9d7031936918be
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853925"
---
# <a name="schema-of-logs-and-events"></a>Schéma des journaux et des événements

Cet article décrit le schéma utilisé par les journaux et événements Azure Data Factory pour la surveillance.

## <a name="monitor-schema"></a>Schéma Azure Monitor
Les listes d’attributs suivantes sont utilisées pour la surveillance.

### <a name="activity-run-log-attributes"></a>Attributs du journal d’exécution d’activité

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Propriété | Type | Description | Exemple |
| --- | --- | --- | --- |
| **Niveau** |String | Niveau des journaux de diagnostic. Pour les journaux d’exécution d’activité, affectez la valeur 4 à la propriété. | `4` |
| **correlationId** |String | ID unique pour le suivi d’une demande particulière. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | Heure de l’événement au format UTC d’intervalle de temps `YYYY-MM-DDTHH:MM:SS.00000Z`. | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| String| ID de l’exécution d’activité. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| String| ID de l’exécution de pipeline. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| String | ID associé à la ressource de fabrique de données. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | Catégorie des journaux de diagnostic. Affectez la valeur `ActivityRuns` à la propriété. | `ActivityRuns` |
|**level**| String | Niveau des journaux de diagnostic. Affectez la valeur `Informational` à la propriété. | `Informational` |
|**operationName**| String | Nom de l’activité avec cet état. Si l’activité est la pulsation de début, la valeur de la propriété est `MyActivity -`. Si l’activité est la pulsation de fin, la valeur de la propriété est `MyActivity - Succeeded`. | `MyActivity - Succeeded` |
|**pipelineName**| String | Nom du pipeline. | `MyPipeline` |
|**activityName**| String | Nom de l’activité. | `MyActivity` |
|**start**| String | Heure de début des exécutions d’activité au format UTC d’intervalle de temps. | `2017-06-26T20:55:29.5007959Z`|
|**end**| String | Heure de fin des exécutions d’activité au format UTC d’intervalle de temps. Si le journal de diagnostic indique qu’une activité a démarré mais qu’elle n’est pas encore terminée, la valeur de la propriété est `1601-01-01T00:00:00Z`. | `2017-06-26T20:55:29.5007959Z` |

### <a name="pipeline-run-log-attributes"></a>Attributs du journal d’exécution de pipeline

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Propriété | Type | Description | Exemple |
| --- | --- | --- | --- |
| **Niveau** |String | Niveau des journaux de diagnostic. Pour les journaux d’exécution d’activité, affectez la valeur 4 à la propriété. | `4` |
| **correlationId** |String | ID unique pour le suivi d’une demande particulière. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | Heure de l’événement au format UTC d’intervalle de temps `YYYY-MM-DDTHH:MM:SS.00000Z`. | `2017-06-28T21:00:27.3534352Z` |
|**runId**| String| ID de l’exécution de pipeline. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| String | ID associé à la ressource de fabrique de données. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | Catégorie des journaux de diagnostic. Affectez la valeur `PipelineRuns` à la propriété. | `PipelineRuns` |
|**level**| String | Niveau des journaux de diagnostic. Affectez la valeur `Informational` à la propriété. | `Informational` |
|**operationName**| String | Nom du pipeline, ainsi que son état. Une fois l’exécution du pipeline terminée, la valeur de la propriété est `Pipeline - Succeeded`. | `MyPipeline - Succeeded`. |
|**pipelineName**| String | Nom du pipeline. | `MyPipeline` |
|**start**| String | Heure de début des exécutions d’activité au format UTC d’intervalle de temps. | `2017-06-26T20:55:29.5007959Z`. |
|**end**| String | Heure de fin des exécutions d’activité au format UTC d’intervalle de temps. Si le journal de diagnostic indique qu’une activité a démarré mais qu’elle n’est pas encore terminée, la valeur de la propriété est `1601-01-01T00:00:00Z`.  | `2017-06-26T20:55:29.5007959Z` |
|**statut**| String | État final de l’exécution de pipeline. Les valeurs possibles de la propriété sont `Succeeded` et `Failed`. | `Succeeded`|

### <a name="trigger-run-log-attributes"></a>Attributs du journal d’exécution de déclencheur

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}
```

| Propriété | Type | Description | Exemple |
| --- | --- | --- | --- |
| **Niveau** |String | Niveau des journaux de diagnostic. Pour les journaux d’exécution d’activité, affectez la valeur 4 à la propriété. | `4` |
| **correlationId** |String | ID unique pour le suivi d’une demande particulière. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | Heure de l’événement au format UTC d’intervalle de temps `YYYY-MM-DDTHH:MM:SS.00000Z`. | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| String| ID de l’exécution du déclencheur. | `08587023010602533858661257311` |
|**resourceId**| String | ID associé à la ressource de fabrique de données. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | Catégorie des journaux de diagnostic. Affectez la valeur `PipelineRuns` à la propriété. | `PipelineRuns` |
|**level**| String | Niveau des journaux de diagnostic. Affectez la valeur `Informational` à la propriété. | `Informational` |
|**operationName**| String | Nom du déclencheur avec son état final, qui indique si le déclencheur a fonctionné correctement. Si la pulsation a réussi, la valeur de la propriété est `MyTrigger - Succeeded`. | `MyTrigger - Succeeded` |
|**triggerName**| String | Nom du déclencheur. | `MyTrigger` |
|**triggerType**| String | Type du déclencheur. Les valeurs possibles de la propriété sont `Manual Trigger` et `Schedule Trigger`. | `ScheduleTrigger` |
|**triggerEvent**| String | Événement du déclencheur. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| String | Heure de début de l’action du défenseur au format UTC d’intervalle de temps. | `2017-06-26T20:55:29.5007959Z`|
|**statut**| String | État final indiquant si le déclencheur a fonctionné correctement. Les valeurs possibles de la propriété sont `Succeeded` et `Failed`. | `Succeeded`|

### <a name="ssis-integration-runtime-log-attributes"></a>Attributs du journal du runtime d’intégration SSIS

Voici les attributs de journal des opérations de démarrage, d’arrêt et de maintenance du runtime d’intégration (IR) de SQL Server Integration Services (SSIS).

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "resultType": "",
   "properties": {
      "message": ""
   },
   "resourceId": ""
}
```

| Propriété                   | Type   | Description                                                   | Exemple                        |
| -------------------------- | ------ | ------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | Heure de l’événement au format UTC :`YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | Nom de votre opération IR SSIS                            | `Start/Stop/Maintenance/Heartbeat` |
| **category**               | String | Catégorie de journaux de diagnostic                               | `SSISIntegrationRuntimeLogs` |
| **correlationId**          | String | ID unique pour le suivi d’une opération particulière             | `f13b159b-515f-4885-9dfa-a664e949f785Deprovision0059035558` |
| **dataFactoryName**        | String | Nom de votre fabrique de données                                          | `MyADFv2` |
| **integrationRuntimeName** | String | Nom de votre IR SSIS                                      | `MySSISIR` |
| **level**                  | String | Niveau des journaux de diagnostic                                  | `Informational` |
| **resultType**             | String | Résultat de votre opération IR SSIS                          | `Started/InProgress/Succeeded/Failed/Healthy/Unhealthy` |
| **message**                | String | Message de sortie de votre opération IR SSIS                  | `The stopping of your SSIS integration runtime has succeeded.` |
| **resourceId**             | String | ID unique de votre ressource de fabrique de données                            | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="ssis-event-message-context-log-attributes"></a>Attributs du journal de contexte des messages d’événements SSIS

Voici les attributs de journal des conditions liées aux messages d’événement générés par des exécutions de package SSIS sur votre runtime d’intégration de SSIS. Ils transmettent des informations similaires à celles [d’une table ou d’une vue contextuelle de messages d’événements du catalogue SSIS (SSISDB)](/sql/integration-services/system-views/catalog-event-message-context) qui indiquent les valeurs d’exécution de nombreuses propriétés de packages SSIS. Ils sont générés lorsque vous sélectionnez le niveau de journalisation `Basic/Verbose` et sont utiles pour le débogage ou la vérification de la conformité.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "contextDepth": "",
      "packagePath": "",
      "contextType": "",
      "contextSourceName": "",
      "contextSourceId": "",
      "propertyName": "",
      "propertyValue": ""
   },
   "resourceId": ""
}
```

| Propriété                   | Type   | Description                                                          | Exemple                        |
| -------------------------- | ------ | -------------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | Heure de l’événement au format UTC :`YYYY-MM-DDTHH:MM:SS.00000Z`        | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | Paramètre à définir sur `YourSSISIRName-SSISPackageEventMessageContext`       | `mysqlmissisir-SSISPackageEventMessageContext` |
| **category**               | String | Catégorie de journaux de diagnostic                                      | `SSISPackageEventMessageContext` |
| **correlationId**          | String | ID unique pour le suivi d’une opération particulière                    | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | Nom de votre fabrique de données                                                 | `MyADFv2` |
| **integrationRuntimeName** | String | Nom de votre IR SSIS                                             | `MySSISIR` |
| **level**                  | String | Niveau des journaux de diagnostic                                         | `Informational` |
| **operationId**            | String | ID unique pour le suivi d’une opération particulière dans SSISDB          | `1` (1 désigne les opérations liées aux packages *non* stockées dans SSISDB/invoquées via T-SQL.) |
| **contextDepth**           | String | Profondeur du contexte de votre message d’événement                              | `0` (0 désigne le contexte avant le démarrage de l’exécution du package, 1 désigne le contexte lorsqu’une erreur se produit et la valeur augmente à mesure que le contexte s’éloigne de l’erreur.) |
| **packagePath**            | String | Chemin d’accès de l’objet du package en tant que source de contexte de message d’événement      | `\Package` |
| **contextType**            | String | Type d’accès de l’objet du package en tant que source de contexte de message d’événement      | `60`(Voir [plus de types de contexte](/sql/integration-services/system-views/catalog-event-message-context#remarks).) |
| **contextSourceName**      | String | Nom d’accès de l’objet du package en tant que source de contexte de message d’événement      | `MyPackage` |
| **contextSourceId**        | String | ID unique d’accès de l’objet du package en tant que source de contexte de message d’événement | `{E2CF27FB-EA48-41E9-AF6F-3FE938B4ADE1}` |
| **propertyName**           | String | Nom de la propriété de l’objet du package en tant que source de contexte de message d’événement   | `DelayValidation` |
| **propertyValue**          | String | Valeur de la propriété de l’objet du package en tant que source de contexte de message d’événement  | `False` |
| **resourceId**             | String | ID unique de votre ressource de fabrique de données                                   | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="ssis-event-messages-log-attributes"></a>Attributs du journal des messages d’événements SSIS

Voici les attributs de journal des messages d’événement générés par les exécutions de package SSIS sur votre runtime d’intégration SSIS. Ils transmettent des informations similaires à celles [d’une table ou d’une vue de messages d’événements SSISDB](/sql/integration-services/system-views/catalog-event-messages) qui montrent le texte détaillé ou les métadonnées des messages d’événements. Ils sont générés à n’importe quel niveau de journalisation, sauf `None`.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "messageTime": "",
      "messageType": "",
      "messageSourceType": "",
      "message": "",
      "packageName": "",
      "eventName": "",
      "messageSourceName": "",
      "messageSourceId": "",
      "subcomponentName": "",
      "packagePath": "",
      "executionPath": "",
      "threadId": ""
   }
}
```

| Propriété                   | Type   | Description                                                        | Exemple                        |
| -------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                   | String | Heure de l’événement au format UTC :`YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | Paramètre à définir sur `YourSSISIRName-SSISPackageEventMessages`           | `mysqlmissisir-SSISPackageEventMessages` |
| **category**               | String | Catégorie de journaux de diagnostic                                    | `SSISPackageEventMessages` |
| **correlationId**          | String | ID unique pour le suivi d’une opération particulière                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | Nom de votre fabrique de données                                               | `MyADFv2` |
| **integrationRuntimeName** | String | Nom de votre IR SSIS                                           | `MySSISIR` |
| **level**                  | String | Niveau des journaux de diagnostic                                       | `Informational` |
| **operationId**            | String | ID unique pour le suivi d’une opération particulière dans SSISDB        | `1` (1 désigne les opérations liées aux packages *non* stockées dans SSISDB/invoquées via T-SQL.) |
| **messageTime**            | String | Heure de création du message d’événement au format UTC          | `2017-06-28T21:00:27.3534352Z` |
| **messageType**            | String | Type du message d'événement                                     | `70`(Voir [plus de types de messages](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks).) |
| **messageSourceType**      | String | Type de source du message d'événement                              | `20`(Voir [plus de types de sources de messages](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks).) |
| **message**                | String | Texte du message d'événement                                     | `MyPackage:Validation has started.` |
| **packageName**            | String | Nom de votre fichier de package exécuté                             | `MyPackage.dtsx` |
| **eventName**              | String | Nom de l’événement au moment de l’exécution                                 | `OnPreValidate` |
| **messageSourceName**      | String | Nom d’accès du composant du package en tant que source du message d’événement         | `Data Flow Task` |
| **messageSourceId**        | String | ID unique d’accès du composant du package en tant que source du message d’événement    | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |
| **subcomponentName**       | String | Nom d’accès du composant de flux de données en tant que source du message d’événement       | `SSIS.Pipeline` |
| **packagePath**            | String | Chemin d’accès de l’objet du package en tant que source du message d’événement            | `\Package\Data Flow Task` |
| **executionPath**          | String | Chemin d’accès complet du package parent au composant exécuté            | `\Transformation\Data Flow Task` (Ce chemin d’accès capture également les itérations d’un composant.) |
| **threadId**               | String | ID unique du thread exécuté lorsque votre message d’événement est journalisé | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |

### <a name="ssis-executable-statistics-log-attributes"></a>Attributs du journal des statistiques exécutables SSIS

Voici les attributs de journal des statistiques exécutables générées par les exécutions de package SSIS sur votre runtime d’intégration SSIS, où les exécutables sont des conteneurs ou des tâches dans les flux de contrôle des packages. Ils transmettent des informations similaires à celles [d’une table ou d’une vue de statistiques sur les exécutables SSISDB](/sql/integration-services/system-views/catalog-executable-statistics) qui présentent une ligne pour chaque exécutable en cours d’exécution, y compris ses itérations. Ils sont générés à n’importe quel niveau de journalisation, sauf `None`, et sont utiles pour identifier les goulots d’étranglement ou les défaillances au niveau des tâches.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "executionPath": "",
      "startTime": "",
      "endTime": "",
      "executionDuration": "",
      "executionResult": "",
      "executionValue": ""
   },
   "resourceId": ""
}
```

| Propriété                   | Type   | Description                                                      | Exemple                        |
| -------------------------- | ------ | ---------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | Heure de l’événement au format UTC :`YYYY-MM-DDTHH:MM:SS.00000Z`    | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | Paramètre à définir sur `YourSSISIRName-SSISPackageExecutableStatistics`  | `mysqlmissisir-SSISPackageExecutableStatistics` |
| **category**               | String | Catégorie de journaux de diagnostic                                  | `SSISPackageExecutableStatistics` |
| **correlationId**          | String | ID unique pour le suivi d’une opération particulière                | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | Nom de votre fabrique de données                                             | `MyADFv2` |
| **integrationRuntimeName** | String | Nom de votre IR SSIS                                         | `MySSISIR` |
| **level**                  | String | Niveau des journaux de diagnostic                                     | `Informational` |
| **executionId**            | String | ID unique pour le suivi d’une exécution particulière dans SSISDB      | `1` (1 désigne les exécutions liées aux packages *non* stockées dans SSISDB/invoquées via T-SQL.) |
| **executionPath**          | String | Chemin d’accès complet du package parent au composant exécuté          | `\Transformation\Data Flow Task` (Ce chemin d’accès capture également les itérations d’un composant.) |
| **startTime**              | String | Heure à laquelle le fichier exécutable entre dans la phase de pré-exécution au format UTC  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | String | Heure à laquelle le fichier exécutable entre dans la phase de post-exécution au format UTC | `2017-06-28T21:00:27.3534352Z` |
| **executionDuration**      | String | Heure d’exécution de l’exécutable en millisecondes                   | `1,125` |
| **executionResult**        | String | Résultat de l’exécution de l’exécutable                                 | `0` (0 désigne la réussite, 1 désigne l’échec, 2 désigne la fin et 3 désigne l’annulation.) |
| **executionValue**         | String | Valeur définie par l’utilisateur retournée par l’exécutable en cours d’exécution            | `1` |
| **resourceId**             | String | ID unique de votre ressource de fabrique de données                               | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="ssis-execution-component-phases-log-attributes"></a>Attributs du journal des phases du composant d’exécution SSIS

Voici les attributs de journal des statistiques de runtime pour les composants de flux de données générés par des exécutions de package SSIS sur votre runtime d’intégration SSIS. Ils transmettent des informations similaires à celles [d’une table ou d’une vue des phases des composants d’exécution SSISDB](/sql/integration-services/system-views/catalog-execution-component-phases) qui indiquent le temps passé par les composants de flux de données dans toutes leurs phases d’exécution. Ils sont générés lorsque vous sélectionnez le niveau de journalisation `Performance/Verbose` qui est utile pour la capture des statistiques d’exécution du flux de données.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "subcomponentName": "",
      "phase": "",
      "startTime": "",
      "endTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| Propriété                   | Type   | Description                                                         | Exemple                        |
| -------------------------- | ------ | ------------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | Heure de l’événement au format UTC :`YYYY-MM-DDTHH:MM:SS.00000Z`       | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | Paramètre à définir sur `YourSSISIRName-SSISPackageExecutionComponentPhases` | `mysqlmissisir-SSISPackageExecutionComponentPhases` |
| **category**               | String | Catégorie de journaux de diagnostic                                     | `SSISPackageExecutionComponentPhases` |
| **correlationId**          | String | ID unique pour le suivi d’une opération particulière                   | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | Nom de votre fabrique de données                                                | `MyADFv2` |
| **integrationRuntimeName** | String | Nom de votre IR SSIS                                            | `MySSISIR` |
| **level**                  | String | Niveau des journaux de diagnostic                                        | `Informational` |
| **executionId**            | String | ID unique pour le suivi d’une exécution particulière dans SSISDB         | `1` (1 désigne les exécutions liées aux packages *non* stockées dans SSISDB/invoquées via T-SQL.) |
| **packageName**            | String | Nom de votre fichier de package exécuté                              | `MyPackage.dtsx` |
| **taskName**               | String | Nom de la tâche de flux de données exécutée                                 | `Data Flow Task` |
| **subcomponentName**       | String | Nom du composant de flux de données                                     | `Derived Column` |
| **phase**                  | String | Nom de la phase d'exécution                                         | `AcquireConnections` |
| **startTime**              | String | Heure de début de la phase d’exécution au format UTC                  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | String | Heure de fin de la phase d’exécution au format UTC                    | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**          | String | Chemin d'exécution de la tâche de flux de données                            | `\Transformation\Data Flow Task` |
| **resourceId**             | String | ID unique de votre ressource de fabrique de données                                  | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="ssis-execution-data-statistics-log-attributes"></a>Attributs du journal des statistiques des données d’exécution SSIS

Voici les attributs de journal des déplacements de données via chaque tronçon de pipelines de flux de données, de l’amont vers les composants en aval, générés par les exécutions de package SSIS sur votre IR SSIS. Ils transmettent des informations similaires à celles [d’une table ou d’une vue de statistiques sur les données d’exécution SSISDB](/sql/integration-services/system-views/catalog-execution-data-statistics) qui indiquent le nombre de lignes de données déplacées par les tâches de flux de données. Ils sont générés lorsque vous sélectionnez le niveau de journalisation `Verbose` qui est utile pour calculer le débit du flux de données.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "dataflowPathIdString": "",
      "dataflowPathName": "",
      "sourceComponentName": "",
      "destinationComponentName": "",
      "rowsSent": "",
      "createdTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| Propriété                     | Type   | Description                                                        | Exemple                        |
| ---------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                     | String | Heure de l’événement au format UTC :`YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**            | String | Paramètre à définir sur `YourSSISIRName-SSISPackageExecutionDataStatistics` | `mysqlmissisir-SSISPackageExecutionDataStatistics` |
| **category**                 | String | Catégorie de journaux de diagnostic                                    | `SSISPackageExecutionDataStatistics` |
| **correlationId**            | String | ID unique pour le suivi d’une opération particulière                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**          | String | Nom de votre fabrique de données                                               | `MyADFv2` |
| **integrationRuntimeName**   | String | Nom de votre IR SSIS                                           | `MySSISIR` |
| **level**                    | String | Niveau des journaux de diagnostic                                       | `Informational` |
| **executionId**              | String | ID unique pour le suivi d’une exécution particulière dans SSISDB        | `1` (1 désigne les exécutions liées aux packages *non* stockées dans SSISDB/invoquées via T-SQL.) |
| **packageName**              | String | Nom de votre fichier de package exécuté                             | `MyPackage.dtsx` |
| **taskName**                 | String | Nom de la tâche de flux de données exécutée                                | `Data Flow Task` |
| **dataflowPathIdString**     | String | ID unique du chemin d’accès du flux de données de suivi                          | `Paths[SQLDB Table3.ADO NET Source Output]` |
| **dataflowPathName**         | String | Nom du chemin d'accès de flux de données                                         | `ADO NET Source Output` |
| **sourceComponentName**      | String | Nom du composant de flux de données qui envoie les données                    | `SQLDB Table3` |
| **destinationComponentName** | String | Nom du composant de flux de données qui reçoit les données                 | `Derived Column` |
| **rowsSent**                 | String | Nombre de lignes envoyées par composant source                        | `500` |
| **createdTime**              | String | Heure à laquelle les valeurs de ligne sont obtenues au format UTC                | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**            | String | Chemin d'exécution de la tâche de flux de données                           | `\Transformation\Data Flow Task` |
| **resourceId**               | String | ID unique de votre ressource de fabrique de données                                 | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

## <a name="log-analytics-schema"></a>Schéma Log Analytics

Log Analytics hérite du schéma d’Azure Monitor avec les exceptions suivantes :

* La première lettre de chaque nom de colonne est majuscule. Par exemple, le nom de colonne « correlationId » dans Monitor est « CorrelationId » dans Log Analytics.
* Il n’y a pas de colonne « Level ».
* La colonne des « properties » des propriétés dynamiques est conservée en tant que type d’objet blob JSON dynamique.

    | Colonne Azure Monitor | Colonne Log Analytics | Type |
    | --- | --- | --- |
    | $.properties.UserProperties | UserProperties | Dynamique |
    | $.properties.Annotations | Annotations | Dynamique |
    | $.properties.Input | Entrée | Dynamique |
    | $.properties.Output | Output | Dynamique |
    | $.properties.Error.errorCode | ErrorCode | int |
    | $.properties.Error.message | ErrorMessage | string |
    | $.properties.Error | Error | Dynamique |
    | $.properties.Predecessors | Predecessors | Dynamique |
    | $.properties.Parameters | Paramètres | Dynamique |
    | $.properties.SystemParameters | SystemParameters | Dynamique |
    | $.properties.Tags | Balises | Dynamique |

## <a name="next-steps"></a>Étapes suivantes

[Installer la solution Azure Data Factory Analytics à partir de la Place de marché Azure](monitor-install-analytics.md)
