---
title: Obtenir l’état des documents
titleSuffix: Azure Cognitive Services
description: La méthode get documents status renvoie l'état de tous les documents d'une demande de traduction de document par lot.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.openlocfilehash: 4da15c603028524c0501819c0ecbc3884080ca6f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053459"
---
# <a name="get-documents-status"></a>Obtenir l’état des documents

Si le nombre de documents dans la réponse dépasse notre limite de pagination, la pagination côté serveur est utilisée. Les réponses paginées indiquent un résultat partiel et incluent un jeton de continuation dans la réponse. L’absence de jeton de continuation signifie qu’aucune autre page n’est disponible.

Les paramètres de requête $top, $skip et $maxpagesize peuvent être utilisés pour spécifier un nombre de résultats à retourner et un décalage pour la collection.

$top indique le nombre total d’enregistrements que l’utilisateur souhaite voir retournés dans toutes les pages. $skip indique le nombre d’enregistrements à ignorer dans la liste des états de documents détenus par le serveur en fonction de la méthode de tri spécifiée. Par défaut, le tri est effectué par ordre décroissant de l’heure de début. $maxpagesize est le nombre maximal d’éléments retournés dans une page. Si davantage d’éléments sont demandés via $top (ou si $top n’est pas spécifié et qu’il y a plus d’éléments à retourner), @nextLink contient le lien vers la page suivante.

Le paramètre de requête $orderBy s’utilise pour trier la liste retournée (par exemple, « $orderBy=createdDateTimeUtc asc » ou « $orderBy=createdDateTimeUtc desc »). Le tri par défaut est effectué par ordre décroissant de l’heure de création (createdDateTimeUtc). Certains paramètres de requête peuvent être utilisés pour filtrer la liste retournée (par exemple : « status=Succeeded,Cancelled » retourne uniquement les documents réussis et annulés). createdDateTimeUtcStart etcreatedDateTimeUtcEnd peuvent être utilisés conjointement ou séparément pour spécifier une plage de valeurs DateHeure selon laquelle filtrer la liste retournée. Les paramètres de requête de filtrage pris en charge sont (status, IDs, createdDateTimeUtcStart, createdDateTimeUtcEnd).

Lorsque $top et $skip sont tous deux inclus, le serveur doit d’abord appliquer $skip, puis $top sur la collection. 

> [!NOTE]
> Si le serveur ne peut pas honorer $top et/ou $skip, il doit retourner une erreur au client afin de l’en informer au lieu d’ignorer simplement les options de requête. Cela réduit le risque que le client émette des hypothèses quant aux données retournées.

## <a name="request-url"></a>URL de la demande

Envoyez une demande `GET` à :
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches/{id}/documents
```

Découvrez comment déterminer votre [nom de domaine personnalisé](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Toutes les requêtes d’API adressées au service Traduction de documentation nécessitent un point de terminaison de domaine personnalisé**.
> * Vous ne pouvez pas utiliser le point de terminaison qui se trouve dans la page _Clés et point de terminaison_ de votre ressource du portail Azure, ni le point de terminaison du traducteur global (`api.cognitive.microsofttranslator.com`) pour soumettre des requêtes HTTP au service Traduction de documentation.

## <a name="request-parameters"></a>Paramètres de la demande

Les paramètres de demande transmis à la chaîne de requête sont les suivants :

|Paramètre de requête.|Dans|Obligatoire|Type|Description|
|--- |--- |--- |--- |--- |
|id|path|True|string|ID de l’opération.|
|$maxpagesize|query|Faux|entier int32|$maxpagesize est le nombre maximal d’éléments retournés dans une page. Si davantage d’éléments sont demandés via $top (ou si $top n’est pas spécifié et qu’il y a plus d’éléments à retourner), @nextLink contient le lien vers la page suivante. Les clients PEUVENT demander une pagination basée sur le serveur avec une taille de page spécifique en spécifiant une préférence $maxpagesize. Le serveur DOIT respecter cette préférence si la taille de page spécifiée est inférieure à la taille de page par défaut du serveur.|
|$orderBy|query|Faux|tableau|Requête de tri pour la collection (par exemple : « CreatedDateTimeUtc asc », « CreatedDateTimeUtc desc »).|
|$skip|query|Faux|entier int32|$skip indique le nombre d’enregistrements à ignorer dans la liste des enregistrements détenus par le serveur en fonction de la méthode de tri spécifiée. Par défaut, le tri est effectué par ordre décroissant de l’heure de début. Les clients PEUVENT utiliser les paramètres de requête $top et $skip pour spécifier un nombre de résultats à retourner et un décalage dans la collection. Quand les deux paramètres $top et $skip sont fournis par un client, le serveur DOIT appliquer $skip avant $top sur la collection. Remarque : Si le serveur ne peut pas appliquer $top et/ou $skip, il DOIT retourner une erreur au client afin de l’en informer au lieu d’ignorer simplement les options de requête.|
|$top|query|Faux|entier int32|$top indique le nombre total d’enregistrements que l’utilisateur souhaite voir retournés dans toutes les pages. Les clients PEUVENT utiliser les paramètres de requête $top et $skip pour spécifier un nombre de résultats à retourner et un décalage dans la collection. Quand les deux paramètres $top et $skip sont fournis par un client, le serveur DOIT appliquer $skip avant $top sur la collection. Remarque : Si le serveur ne peut pas appliquer $top et/ou $skip, il DOIT retourner une erreur au client afin de l’en informer au lieu d’ignorer simplement les options de requête.|
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
|200|OK. Demande réussie, et retourne l’état des documents. HeadersRetry-After: integerETag: string|
|400|Demande non valide. Vérifiez les paramètres d’entrée.|
|401|Non autorisé. Vérifiez vos informations d’identification.|
|404|Ressource introuvable.|
|500|Erreur interne du serveur.|
|Autres codes d’état|<ul><li>Trop de demandes</li><li>Serveur temporairement indisponible</li></ul>|


## <a name="get-documents-status-response"></a>Réponse de la méthode get documents status

### <a name="successful-get-documents-status-response"></a>Réponse positive de la méthode get documents status

Les informations suivantes sont retournées dans une réponse positive.

|Nom|Type|Description|
|--- |--- |--- |
|@nextLink|string|URL de la page suivante. Null s’il n’y a plus de page disponible.|
|value|DocumentStatus []|État détaillé des documents individuels listés ci-dessous.|
|value.path|string|Emplacement du document ou du dossier.|
|value.sourcePath|string|Emplacement du document source.|
|value.createdDateTimeUtc|string|Date et heure de création de l’opération.|
|value.lastActionDateTimeUtc|string|Date et heure de mise à jour de l’état de l’opération.|
|value.status|status|Liste des états possibles pour un travail ou un document.<ul><li>Opération annulée</li><li>Cancelling</li><li>Failed</li><li>NotStarted</li><li>En cours d’exécution</li><li>Succès</li><li>ValidationFailed</li></ul>|
|value.to|string|Langue cible.|
|value.progress|nombre|Progression de la traduction, si elle est disponible.|
|value.id|string|Document ID.|
|value.characterCharged|entier|Caractères facturés par l’API.|

### <a name="error-response"></a>Réponse d’erreur

|Nom|Type|Description|
|--- |--- |--- |
|code|string|Enums contenant des codes d’erreur généraux. Valeurs possibles :<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Non autorisé</li></ul>|
|message|string|Obtient un message d’erreur général.|
|target|string|Obtient la source de l’erreur. Par exemple, il s’agirait de « documents » ou « document ID » dans le cas d’un document non valide.|
|innerError|InnerTranslationError|Nouveau format d’erreur interne qui est conforme aux instructions de l’API Cognitive Services. Ce format contient les propriétés obligatoires ErrorCode, message et les propriétés facultatives target, details (paire clé-valeur), et l’erreur interne (qui peut être imbriquée).|
|innerError.code|string|Obtient la chaîne d’erreur de code.|
|innerError.message|string|Obtient un message d’erreur général.|
|innerError.target|string|Obtient la source de l’erreur. Par exemple, « documents » ou « document id » en présence d’un document non valide.|

## <a name="examples"></a>Exemples

### <a name="example-successful-response"></a>Exemple de réponse positive

Voici un exemple de réponse positive :

```JSON
{
  "value": [
    {
      "path": "https://myblob.blob.core.windows.net/destinationContainer/fr/mydoc.txt",
      "sourcePath": "https://myblob.blob.core.windows.net/sourceContainer/fr/mydoc.txt",
      "createdDateTimeUtc": "2020-03-26T00:00:00Z",
      "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
      "status": "Running",
      "to": "fr",
      "progress": 0.1,
      "id": "273622bd-835c-4946-9798-fd8f19f6bbf2",
      "characterCharged": 0
    }
  ],
  "@nextLink": "https://westus.cognitiveservices.azure.com/translator/text/batch/v1.0/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55/documents?$top=5&$skip=15"
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
