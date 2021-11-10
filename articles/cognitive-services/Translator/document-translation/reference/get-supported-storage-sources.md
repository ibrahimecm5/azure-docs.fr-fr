---
title: Méthode Get supported storage sources
titleSuffix: Azure Cognitive Services
description: La méthode get supported storage sources retourne une liste des sources de stockage prises en charge.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.openlocfilehash: 4dae58196bda8978d503593241f3e74c9ef14c69
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056774"
---
# <a name="get-supported-storage-sources"></a>Obtenir les sources de stockage prises en charge

La méthode get supported storage sources retourne une liste des options/sources de stockage prises en charge par le service Traduction de documentation.

## <a name="request-url"></a>URL de la demande

Envoyez une demande `GET` à :
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/storagesources
```

Découvrez comment déterminer votre [nom de domaine personnalisé](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Toutes les requêtes d’API adressées au service Traduction de documentation nécessitent un point de terminaison de domaine personnalisé**.
> * Vous ne pouvez pas utiliser le point de terminaison qui se trouve dans la page _Clés et point de terminaison_ de votre ressource du portail Azure, ni le point de terminaison du traducteur global (`api.cognitive.microsofttranslator.com`) pour soumettre des requêtes HTTP au service Traduction de documentation.

## <a name="request-headers"></a>En-têtes de requête

Les en-têtes de requête sont les suivants :

|headers|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|En-tête de requête obligatoire|

## <a name="response-status-codes"></a>Codes d’état de réponse

Voici les codes d’état HTTP qu’une demande peut retourner.

|Code d’état|Description|
|--- |--- |
|200|OK. Demande réussie, et retourne la liste des sources de stockage.|
|500|Erreur interne du serveur.|
|Autres codes d’état|<ul><li>Trop de demandes</li><li>Serveur temporairement indisponible</li></ul>|

## <a name="get-supported-storage-sources-response"></a>Réponse de get supported storage sources

### <a name="successful-get-supported-storage-sources-response"></a>Réponse positive de get supported storage sources
Type de base pour le retour de liste dans l’API get supported storage sources.

|Nom|Type|Description|
|--- |--- |--- |
|value|string []|Liste d’objets.|


### <a name="error-response"></a>Réponse d’erreur

|Nom|Type|Description|
|--- |--- |--- |
|code|string|Enums contenant des codes d’erreur généraux. Valeurs possibles :<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Non autorisé</li></ul>|
|message|string|Obtient un message d’erreur général.|
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
    "AzureBlob"
  ]
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
