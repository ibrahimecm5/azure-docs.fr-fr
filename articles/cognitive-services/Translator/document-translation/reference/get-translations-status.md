---
title: Obtenir l’état des traductions
titleSuffix: Azure Cognitive Services
description: La méthode get translations status retourne une liste des demandes de lots soumises et l’état de chaque demande.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/22/2021
ms.openlocfilehash: d16de5ccc4beddef8296211d7e5f0c154f10776d
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059012"
---
# <a name="get-translations-status"></a>Obtenir l’état des traductions

La méthode get translations status retourne une liste des demandes de lots soumises et l’état de chaque demande. Cette liste contient uniquement les demandes de lots soumises par l’utilisateur (en fonction de la ressource).

Si le nombre de demandes dépasse notre limite de pagination, la pagination côté serveur est utilisée. Les réponses paginées indiquent un résultat partiel et incluent un jeton de continuation dans la réponse. L’absence de jeton de continuation signifie qu’aucune autre page n’est disponible.

Les paramètres de requête $top, $skip et $maxpagesize peuvent être utilisés pour spécifier un nombre de résultats à retourner et un décalage pour la collection.

$top indique le nombre total d’enregistrements que l’utilisateur souhaite voir retournés dans toutes les pages. $skip indique le nombre d’enregistrements à ignorer dans la liste des lots en fonction de la méthode de tri spécifiée. Par défaut, le tri est effectué par ordre décroissant de l’heure de début. $maxpagesize est le nombre maximal d’éléments retournés dans une page. Si davantage d’éléments sont demandés via $top (ou si $top n’est pas spécifié et qu’il y a plus d’éléments à retourner), @nextLink contient le lien vers la page suivante.

Le paramètre de requête $orderBy s’utilise pour trier la liste retournée (par exemple, « $orderBy=createdDateTimeUtc asc » ou « $orderBy=createdDateTimeUtc desc »). Le tri par défaut est effectué par ordre décroissant de l’heure de création (createdDateTimeUtc). Certains paramètres de requête peuvent être utilisés pour filtrer la liste retournée (par exemple : « status=Succeeded,Cancelled » retourne uniquement les opérations réussies et annulées). createdDateTimeUtcStart etcreatedDateTimeUtcEnd peuvent être utilisés conjointement ou séparément pour spécifier une plage de valeurs DateHeure selon laquelle filtrer la liste retournée. Les paramètres de requête de filtrage pris en charge sont (status, IDs, createdDateTimeUtcStart, createdDateTimeUtcEnd).

Le serveur honore les valeurs spécifiées par le client. Toutefois, les clients doivent être prêts à gérer les réponses qui contiennent une taille de page différente ou un jeton de continuation.

Lorsque $top et $skip sont tous deux inclus, le serveur doit d’abord appliquer $skip, puis $top sur la collection. 

> [!NOTE]
> Si le serveur ne peut pas honorer $top et/ou $skip, il doit retourner une erreur au client afin de l’en informer au lieu d’ignorer simplement les options de requête. Cela réduit le risque que le client émette des hypothèses quant aux données retournées.

## <a name="request-url"></a>URL de la demande

Envoyez une demande `GET` à :
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches
```

Découvrez comment déterminer votre [nom de domaine personnalisé](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Toutes les requêtes d’API adressées au service Traduction de documentation nécessitent un point de terminaison de domaine personnalisé**.
> * Vous ne pouvez pas utiliser le point de terminaison qui se trouve dans la page _Clés et point de terminaison_ de votre ressource du portail Azure, ni le point de terminaison du traducteur global (`api.cognitive.microsofttranslator.com`) pour soumettre des requêtes HTTP au service Traduction de documentation.

## <a name="request-parameters"></a>Paramètres de la demande

Les paramètres de demande transmis à la chaîne de requête sont les suivants :

|Paramètre de requête.|Dans|Obligatoire|Type|Description|
|--- |--- |--- |---|---|
|$maxpagesize|query|Faux|entier int32|$maxpagesize est le nombre maximal d’éléments retournés dans une page. Si davantage d’éléments sont demandés via $top (ou si $top n’est pas spécifié et qu’il y a plus d’éléments à retourner), @nextLink contient le lien vers la page suivante. Les clients PEUVENT demander une pagination basée sur le serveur avec une taille de page spécifique en spécifiant une préférence $maxpagesize. Le serveur DOIT respecter cette préférence si la taille de page spécifiée est inférieure à la taille de page par défaut du serveur.|
|$orderBy|query|Faux|tableau|Requête de tri pour la collection (par exemple : « CreatedDateTimeUtc asc », « CreatedDateTimeUtc desc »)|
|$skip|query|Faux|entier int32|$skip indique le nombre d’enregistrements à ignorer dans la liste des enregistrements détenus par le serveur en fonction de la méthode de tri spécifiée. Par défaut, le tri est effectué par ordre décroissant de l’heure de début. Les clients PEUVENT utiliser les paramètres de requête $top et $skip pour spécifier un nombre de résultats à retourner et un décalage dans la collection. Quand les deux paramètres $top et $skip sont fournis par un client, le serveur DOIT appliquer $skip avant $top sur la collection. Remarque : si le serveur ne peut pas appliquer $top et/ou $skip, le serveur DOIT retourner une erreur au client afin de l’en informer au lieu d’ignorer simplement les options de requête.|
|$top|query|Faux|entier int32|$top indique le nombre total d’enregistrements que l’utilisateur souhaite voir retournés dans toutes les pages. Les clients PEUVENT utiliser les paramètres de requête $top et $skip pour spécifier un nombre de résultats à retourner et un décalage dans la collection. Quand les deux paramètres $top et $skip sont fournis par un client, le serveur DOIT appliquer $skip avant $top sur la collection. Remarque : si le serveur ne peut pas appliquer $top et/ou $skip, le serveur DOIT retourner une erreur au client afin de l’en informer au lieu d’ignorer simplement les options de requête.|
|createdDateTimeUtcEnd|query|Faux|chaîne date-heure|Date et heure de fin pour l’extraction des éléments.|
|createdDateTimeUtcStart|query|Faux|chaîne date-heure|Date et heure de début pour l’extraction des éléments.|
|ids|query|Faux|tableau|ID à utiliser pour le filtrage.|
|statuses|query|Faux|tableau|États à utiliser pour le filtrage.|

## <a name="request-headers"></a>En-têtes de requête

Les en-têtes de requête sont les suivants :

|headers|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|En-tête de requête obligatoire|

## <a name="response-status-codes"></a>Codes d’état de réponse

Voici les codes d’état HTTP qu’une demande peut retourner.

|Code d’état|Description|
|--- |--- |
|200|OK. Demande réussie, et retourne l’état de toutes les opérations. HeadersRetry-After: integerETag: string|
|400|Demande incorrecte. Demande non valide. Vérifiez les paramètres d’entrée.|
|401|Non autorisé. Vérifiez vos informations d’identification.|
|500|Erreur interne du serveur.|
|Autres codes d’état|<ul><li>Trop de demandes</li><li>Serveur temporairement indisponible</li></ul>|

## <a name="get-translations-status-response"></a>Réponse de get translations status

### <a name="successful-get-translations-status-response"></a>Réponse positive de get translations status

Les informations suivantes sont retournées dans une réponse positive.

|Nom|Type|Description|
|--- |--- |--- |
|@nextLink|string|URL de la page suivante. Null s’il n’y a plus de page disponible.|
|value|TranslationStatus[]|Tableau TranslationStatus [] listé ci-dessous|
|value.id|string|ID de l'opération.|
|value.createdDateTimeUtc|string|Date et heure de création de l’opération.|
|value.lastActionDateTimeUtc|string|Date et heure de mise à jour de l’état de l’opération.|
|value.status|String|Liste des états possibles pour un travail ou un document. <ul><li>Opération annulée</li><li>Cancelling</li><li>Failed</li><li>NotStarted</li><li>En cours d’exécution</li><li>Succès</li><li>ValidationFailed</li></ul>|
|value.summary|StatusSummary[]|Résumé contenant les détails listés ci-dessous.|
|value.summary.total|entier|Nombre total de documents.|
|value.summary.failed|entier|Nombre de documents ayant échoué.|
|value.summary.success|entier|Nombre de documents traduits avec succès.|
|value.summary.inProgress|entier|Nombre de documents en cours de traitement.|
|value.summary.notYetStarted|entier|Nombre de documents dont le traitement n’a pas encore commencé.|
|value.summary.cancelled|entier|Nombre de documents annulés.|
|value.summary.totalCharacterCharged|entier|Nombre total de caractères facturés.|

### <a name="error-response"></a>Réponse d’erreur

|Nom|Type|Description|
|--- |--- |--- |
|code|string|Enums contenant des codes d’erreur généraux. Valeurs possibles :<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Non autorisé</li></ul>|
|message|string|Obtient un message d’erreur général.|
|target|string|Obtient la source de l’erreur. Par exemple, « documents » ou « document ID » en présence d’un document non valide.|
|innerError|InnerTranslationError|Nouveau format d’erreur interne qui est conforme aux instructions de l’API Cognitive Services. Ce format contient les propriétés obligatoires ErrorCode, message et les propriétés facultatives target, details (paire clé-valeur), et l’erreur interne (qui peut être imbriquée).|
|innerError.code|string|Obtient la chaîne d’erreur de code.|
|innerError.message|string|Obtient un message d’erreur général.|
|innerError.target|string|Obtient la source de l’erreur. Par exemple, « documents » ou « document ID » en présence d’un document non valide.|

## <a name="examples"></a>Exemples

### <a name="example-successful-response"></a>Exemple de réponse positive

Voici un exemple de réponse positive :

```JSON
{
    "value": [
        {
            "id": "36724748-f7a0-4db7-b7fd-f041ddc75033",
            "createdDateTimeUtc": "2021-06-18T03:35:30.153374Z",
            "lastActionDateTimeUtc": "2021-06-18T03:36:44.6155316Z",
            "status": "Succeeded",
            "summary": {
                "total": 3,
                "failed": 2,
                "success": 1,
                "inProgress": 0,
                "notYetStarted": 0,
                "cancelled": 0,
                "totalCharacterCharged": 0
            }
        },
        {
            "id": "1c7399a7-6913-4f20-bb43-e2fe2ba1a67d",
            "createdDateTimeUtc": "2021-05-24T17:57:43.8356624Z",
            "lastActionDateTimeUtc": "2021-05-24T17:57:47.128391Z",
            "status": "Failed",
            "summary": {
                "total": 1,
                "failed": 1,
                "success": 0,
                "inProgress": 0,
                "notYetStarted": 0,
                "cancelled": 0,
                "totalCharacterCharged": 0
            }
        },
        {
            "id": "daa2a646-4237-4f5f-9a48-d515c2d9af3c",
            "createdDateTimeUtc": "2021-04-14T19:49:26.988272Z",
            "lastActionDateTimeUtc": "2021-04-14T19:49:43.9818634Z",
            "status": "Succeeded",
            "summary": {
                "total": 2,
                "failed": 0,
                "success": 2,
                "inProgress": 0,
                "notYetStarted": 0,
                "cancelled": 0,
                "totalCharacterCharged": 21899
            }
        }
    ],
    ""@nextLink": "https://westus.cognitiveservices.azure.com/translator/text/batch/v1.0/operations/727BF148-F327-47A0-9481-ABAE6362F11E/documents?$top=5&$skip=15"
}

```

### <a name="example-error-response"></a>Exemple de réponse d’erreur

Voici un exemple de réponse d’erreur : Le schéma des autres codes d’erreur est le même.

Code d’état : 500

```JSON
{
  "error": {
    "code": "InternalServerError",
    "message": "Internal Server Error",
    "target": "Operation",
    "innerError": {
      "code": "InternalServerError",
      "message": "Unexpected internal server error has occurred"
    }
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

Suivez notre guide de démarrage rapide pour en savoir plus sur l’utilisation du service Traduction de document et de la bibliothèque de client.

> [!div class="nextstepaction"]
> [Bien démarrer avec la traduction de documentation](../get-started-with-document-translation.md)
