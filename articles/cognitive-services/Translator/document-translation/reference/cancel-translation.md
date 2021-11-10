---
title: Méthode cancel translation
titleSuffix: Azure Cognitive Services
description: La méthode cancel translation annule une opération en cours de traitement ou en file d’attente.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/20/2021
ms.openlocfilehash: 8bf718da42dbd29c864736209cf441b03f3c9e1b
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059257"
---
# <a name="cancel-translation"></a>Annuler la traduction

Annule une opération en cours de traitement ou en file d’attente. Une opération ne sera pas annulée si elle est déjà terminée, si elle a échoué ou si elle est en cours d’annulation. Une requête incorrecte sera retournée. Tous les documents dont la traduction est terminée ne seront pas annulés et seront facturés. Tous les documents en attente seront annulés si possible.

## <a name="request-url"></a>URL de la demande

Envoyez une demande `DELETE` à :

```DELETE HTTP
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches/{id}
```

Découvrez comment déterminer votre [nom de domaine personnalisé](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Toutes les requêtes d’API adressées au service Traduction de documentation nécessitent un point de terminaison de domaine personnalisé**.
> * Vous ne pouvez pas utiliser le point de terminaison qui se trouve dans la page _Clés et point de terminaison_ de votre ressource du portail Azure, ni le point de terminaison du traducteur global (`api.cognitive.microsofttranslator.com`) pour soumettre des requêtes HTTP au service Traduction de documentation.

## <a name="request-parameters"></a>Paramètres de la demande

Les paramètres de demande transmis à la chaîne de requête sont les suivants :

|Paramètre de requête.|Obligatoire|Description|
|-----|-----|-----|
|id|Vrai|ID de l’opération.|

## <a name="request-headers"></a>En-têtes de requête

Les en-têtes de requête sont les suivants :

|headers|Description|
|-----|-----|
|Ocp-Apim-Subscription-Key|En-tête de requête obligatoire|

## <a name="response-status-codes"></a>Codes d’état de réponse

Voici les codes d’état HTTP qu’une demande peut retourner.

| Code d’état| Description|
|-----|-----|
|200|OK. La demande d’annulation a été envoyée|
|401|Non autorisé. Vérifiez vos informations d’identification.|
|404|Introuvable. Ressource introuvable. 
|500|Erreur interne du serveur.
|Autres codes d’état|<ul><li>Trop de demandes</li><li>Serveur temporairement indisponible</li></ul>|

## <a name="cancel-translation-response"></a>Réponse de cancel translation

### <a name="successful-response"></a>Réponse correcte

Les informations suivantes sont retournées dans une réponse positive.

|Nom|Type|Description|
|--- |--- |--- |
|id|string|ID de l'opération.|
|createdDateTimeUtc|string|Date et heure de création de l’opération.|
|lastActionDateTimeUtc|string|Date et heure de mise à jour de l’état de l’opération.|
|status|String|Liste des états possibles pour un travail ou un document. <ul><li>Opération annulée</li><li>Cancelling</li><li>Failed</li><li>NotStarted</li><li>En cours d’exécution</li><li>Succès</li><li>ValidationFailed</li></ul>|
|Récapitulatif|StatusSummary|Résumé contenant les détails listés ci-dessous.|
|summary.total|entier|Nombre total de documents.|
|summary.failed|entier|Nombre de documents ayant échoué.|
|summary.success|entier|Nombre de documents traduits avec succès.|
|summary.inProgress|entier|Nombre de documents en cours de traitement.|
|summary.notYetStarted|entier|Nombre de documents dont le traitement n’a pas encore commencé.|
|summary.cancelled|entier|Nombre de documents annulés.|
|summary.totalCharacterCharged|entier|Nombre total de caractères facturés par l’API.|

### <a name="error-response"></a>Réponse d’erreur

|Nom|Type|Description|
|--- |--- |--- |
|code|string|Enums contenant des codes d’erreur généraux. Valeurs possibles :<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Non autorisé</li></ul>|
|message|string|Obtient un message d’erreur général.|
|target|string|Obtient la source de l’erreur. Par exemple, il s’agirait de « documents » ou « document id » pour un document non valide.|
|innerError|InnerTranslationError|Nouveau format d’erreur interne qui est conforme aux instructions de l’API Cognitive Services. Ce format contient les propriétés obligatoires ErrorCode, message et les propriétés facultatives target, details (paire clé-valeur), et l’erreur interne (qui peut être imbriquée).|
|innerError.code|string|Obtient la chaîne d’erreur de code.|
|innerError.message|string|Obtient un message d’erreur général.|
|innerError.target|string|Obtient la source de l’erreur. Par exemple, « documents » ou « document ID » en présence d’un document non valide.|

## <a name="examples"></a>Exemples

### <a name="example-successful-response"></a>Exemple de réponse positive

L’objet JSON suivant est un exemple de réponse positive.

Code d’état : 200

```JSON
{
  "id": "727bf148-f327-47a0-9481-abae6362f11e",
  "createdDateTimeUtc": "2020-03-26T00:00:00Z",
  "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
  "status": "Succeeded",
  "summary": {
    "total": 10,
    "failed": 1,
    "success": 9,
    "inProgress": 0,
    "notYetStarted": 0,
    "cancelled": 0,
    "totalCharacterCharged": 0
  }
}
```

### <a name="example-error-response"></a>Exemple de réponse d’erreur

L’objet JSON suivant est un exemple de réponse d’erreur. Le schéma des autres codes d’erreur est le même.

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
