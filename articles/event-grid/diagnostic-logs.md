---
title: Azure Event Grid - Journaux de diagnostic pour les rubriques Azure Event Grid et les domaines Event Grid
description: Cet article fournit des informations conceptuelles sur les journaux de diagnostic pour une rubrique ou un domaine Azure Event Grid.
ms.topic: conceptual
ms.date: 11/11/2021
ms.openlocfilehash: df3fe9eaab544e3e52ff3a2da24fe7b624292367
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546820"
---
# <a name="diagnostic-logs-for-event-grid-topics-and-event-grid-domains"></a>Journaux de diagnostic pour les rubriques Event Grid et les domaines Event Grid

Les paramètres de diagnostic permettent aux utilisateurs d’Event Grid de capturer et d’afficher les journaux d’**échec de publication et de remise** dans un compte de stockage, un Event Hub ou espace de travail Log Analytics. Cet article fournit un schéma pour les journaux et un exemple d’entrée de journal.

## <a name="schema-for-publishdelivery-failure-logs"></a>Schéma pour les journaux d’échec de publication/remise

| Nom de la propriété | Type de données | Description |
| ------------- | --------- | ----------- |
| Temps | DateTime | Heure de génération de l’entrée du journal <p>**Exemple de valeur :**  01-29-2020 09:52:02.700</p> |
| EventSubscriptionName | String | Nom de l’abonnement aux événements <p>**Exemple de valeur :** "EVENTSUB1"</p> <p>Cette propriété existe uniquement pour les journaux d’échec de remise.</p>  |
| Category | String | Nom de la catégorie de journal. <p>**Exemples de valeurs :** « DeliveryFailures » ou « PublishFailures » | 
| NomOpération | String | Nom de l’opération à l’origine de l’échec.<p>**Exemples de valeurs :** « Deliver » pour les échecs de remise. |
| Message | String | Message du journal destiné à l’utilisateur expliquant la raison de l’échec et fournissant d’autres détails. |
| ResourceId | String | ID de ressource pour la ressource de rubrique/domaine<p>**Exemples de valeurs :** `/SUBSCRIPTIONS/SAMPLE-SUBSCRIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/TOPIC1` |

## <a name="example---schema-for-publishdelivery-failure-logs"></a>Exemple - Schéma pour les journaux d’échec de publication/remise

```json
{
    "time": "2019-11-01T00:17:13.4389048Z",
    "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
    "eventSubscriptionName": "SAMPLEDESTINATION",
    "category": "DeliveryFailures",
    "operationName": "Deliver",
    "message": "Message:outcome=NotFound, latencyInMs=2635, id=xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx, systemId=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
}
```

Les valeurs possibles de `Outcome` sont les suivantes : `NotFound`, `Aborted`, `TimedOut`, `GenericError` et `Busy`. Event Grid consigne toute information qu’il reçoit du gestionnaire d’événements dans le `message`. Par exemple, pour `GenericError`, il consigne le code d’état HTTP, le code d’erreur et le message d’erreur.

## <a name="schema-for-data-plane-requests"></a>Schéma pour les demandes de plan de données

| Nom de la propriété | Type de données | Description |
| ------------- | --------- | ----------- |
| NetworkAccess | String | **PublicAccess** - lors d’une connexion via une adresse IP publique <br /> **PrivateAccess**- lors d’une connexion via une liaison privée |
| ClientIpAddress | String | Adresse IP source des demandes entrantes |
| TlsVersion | String | Version TLS utilisée par la connexion cliente. Les valeurs possibles sont **1.0**, **1.1** et **1.2** |
| Authentification/Type | String | Type de secret utilisé pour l’authentification lors de la publication des messages. <br /> **Inconnu** : ce n’est pas l’un des autres types d’authentification. Les demandes d’OPTIONS auront ce type d’authentification <br /> **Key** – la demande utilise la clé SAS <br /> **SASToken** – la demande utilise un jeton SAP généré à partir de la clé SAS <br /> **AADAccessToken** – jeton JWT émis par AAD |
| Authentification/ObjectId | String | ObjectId du principal de service utilisé par le type d’authentification AADAccessToken |
| OperationResult | String | Résultat de la publication. **Success**, **Unauthorized**, **Forbidden**, **RequestEntityTooLarge**, **BadRequest** & **InternalServerError** |
| TotalOperations | String | Ces traces ne sont pas émises pour chaque demande de publication. Un agrégat pour chaque combinaison unique des valeurs ci-dessus est émis toutes les minutes |

## <a name="example---schema-for-data-plane-requests"></a>Exemple - Schéma pour les demandes de plan de données

```json
{
    "time": "2021-10-26T21:44:16.8117322Z",
    "resourceId": "/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/RESOURCEGROUPS/BMT-TEST/PROVIDERS/MICROSOFT.EVENTGRID/DOMAINS/BMTAUDITLOGDOMAIN",
    "operationName": "Microsoft.EventGrid/events/send",
    "category": "DataPlaneRequests",
    "level": "Information",
    "region": "CENTRALUSEUAP",
    "properties": {
        "aggregatedRequests": [
            {
                "networkAccess": "PublicAccess",
                "clientIpAddress": "xx.xx.xx.xxx",
                "tlsVersion": "1.2",
                "authentication": {
                            "type": "AADAccessToken",
                            "objectId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx"
                },
                "operationResult": "Success",
                "totalOperations": 1
            }
        ]
    }
}
```

Une fois que le paramètre de diagnostic `DataPlaneRequests` est sélectionné, les ressources Event Grid commencent à publier les suivis d’audit pour les opérations de plan de données, y compris l’accès public et privé, ce suivi peut journaliser une ou plusieurs requêtes si nécessaire.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment activer les journaux de diagnostic pour les rubriques ou les domaines, consultez [Activer les journaux de diagnostic](enable-diagnostic-logs-topic.md).
