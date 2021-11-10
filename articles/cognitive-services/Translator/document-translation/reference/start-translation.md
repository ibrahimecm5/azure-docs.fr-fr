---
title: Commencer la traduction
titleSuffix: Azure Cognitive Services
description: Commencez une demande de traduction de document avec le service Traduction de documentation.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/22/2021
ms.openlocfilehash: eaa12d4fff9331f5170a2a7b7d28b5ebb4d06a74
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061000"
---
# <a name="start-translation"></a>Commencer la traduction

Utilisez cette API pour commencer une demande de traduction avec le service Traduction de documents. Chaque demande peut contenir plusieurs documents, et doit contenir un conteneur source et un conteneur de destination pour chaque document.

Les filtres de préfixe et de suffixe (s’ils sont fournis) sont utilisés pour filtrer les dossiers. Le préfixe est appliqué au sous-chemin après le nom du conteneur.

Des glossaires et une mémoire de traduction peuvent être inclus dans la demande, et sont appliqués par le service lorsque le document est traduit.

Si le glossaire n’est pas valide ou est inaccessible lors de la traduction, une erreur est signalée dans l’état du document. Si un fichier portant le même nom existe déjà dans la destination, le travail échouera. La valeur targetUrl de chaque langue cible doit être unique.

## <a name="request-url"></a>URL de la demande

Envoyez une demande `POST` à :
```HTTP
POST https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches
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

## <a name="request-body-batch-submission-request"></a>Corps de la requête : demande d’envoi par lot

|Nom|Type|Description|
|--- |--- |--- |
|inputs|BatchRequest[]|BatchRequest listée ci-dessous. Liste d’entrée de documents ou de dossiers contenant des documents. Types de médias : « application/json », « text/json », « application/*+json ».|

### <a name="inputs"></a>Entrées

Définition de la demande de traduction par lot d’entrée.

|Nom|Type|Obligatoire|Description|
|--- |--- |--- |--- |
|source|SourceInput[]|Vrai|inputs.source listée ci-dessous. Source des documents d’entrée.|
|storageType|StorageInputType[]|False|inputs.storageType listé ci-dessous. Type de stockage de la chaîne source des documents d’entrée. Obligatoire seulement pour la traduction de documentation unique.|
|cibles|TargetInput[]|Vrai|inputs.target listée ci-dessous. Emplacement de la destination pour la sortie.|

**inputs.source**

Source des documents d’entrée.

|Nom|Type|Obligatoire|Description|
|--- |--- |--- |--- |
|filter|DocumentFilter[]|False|DocumentFilter[] listé ci-dessous.|
|filter.prefix|string|False|Chaîne de préfixe respectant la casse afin de filtrer les documents dans le chemin source pour la traduction. Par exemple, lors de l’utilisation d’un URI d’objet blob Stockage Azure, utilisez le préfixe afin de restreindre les sous-dossiers pour la traduction.|
|filter.suffix|string|False|Chaîne de suffixe respectant la casse afin de filtrer les documents dans le chemin source pour la traduction. Souvent utilisée pour les extensions de fichier.|
|langage|string|False|Code de langue. Si aucun n’est spécifié, nous effectuerons une détection automatique sur le document.|
|sourceUrl|string|True|Emplacement du dossier/conteneur ou fichier unique avec vos documents.|
|storageSource|StorageSource|False|StorageSource listée ci-dessous.|
|storageSource.AzureBlob|string|False||

**inputs.storageType**

Type de stockage de la chaîne source des documents d’entrée.

|Nom|Type|
|--- |--- |
|fichier|string|
|dossier|string|

**inputs.target**

Destination pour les documents traduits terminés.

|Nom|Type|Obligatoire|Description|
|--- |--- |--- |--- |
|catégorie|string|False|Catégorie/système personnalisé pour la demande de traduction.|
|glossaries|Glossary[]|False|Glossary listé ci-dessous. Liste de glossaires.|
|glossaries.format|string|False|Formater.|
|glossaries.glossaryUrl|string|True (si vous utilisez des glossaires)|Emplacement du glossaire. Nous utiliserons l’extension de fichier pour extraire la mise en forme si le paramètre de format n’est pas fourni. Si la paire de langues de traduction n’est pas présente dans le glossaire, elle ne sera pas appliquée.|
|glossaries.storageSource|StorageSource|False|StorageSource listée ci-dessous.|
|glossaries.version|string|False|Version facultative. Si aucune valeur n’est spécifiée, la valeur par défaut est utilisée.|
|targetUrl|string|True|Emplacement du dossier/conteneur avec vos documents.|
|langage|string|True|Code de langue cible à deux lettres. Consultez la [liste des codes de langue](../../language-support.md).|
|storageSource|StorageSource []|False|StorageSource [] listée ci-dessus.|

## <a name="example-request"></a>Exemple de requête

Voici des exemples de demandes de lots :

**Traduction de tous les documents dans un conteneur**

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D"
            },
            "targets": [
                {
                    "targetUrl": "https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D",
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**Traduction de tous les documents d’un conteneur avec application de glossaires**

Vérifiez que vous avez créé une URL de glossaire et un jeton SAS pour l’objet blob/document spécifique (et non pour le conteneur)

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D"
            },
            "targets": [
                {
                    "targetUrl": "https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D",
                    "language": "fr",
                    "glossaries": [
                        {
                            "glossaryUrl": "https://my.blob.core.windows.net/glossaries/en-fr.xlf?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=BsciG3NWoOoRjOYesTaUmxlXzyjsX4AgVkt2AsxJ9to%3D",
                            "format": "xliff",
                            "version": "1.2"
                        }
                    ]

                }
            ]
        }
    ]
}
```

**Traduction d’un dossier spécifique dans un conteneur**

Vérifiez que vous avez spécifié le nom du dossier (respecte la casse) en tant que préfixe dans le filtre, bien que le jeton SAS soit toujours pour le conteneur.

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D",
                "filter": {
                    "prefix": "MyFolder/"
                }
            },
            "targets": [
                {
                    "targetUrl": "https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D",
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**Traduction d’un document spécifique dans un conteneur**

* Vérifiez que vous avez spécifié "storageType": "File"
* Vérifiez que vous avez créé une URL source et un jeton SAS pour l’objet blob/document spécifique (et non pour le conteneur)
* Vérifiez que vous avez spécifié le nom de fichier cible dans le cadre de l’URL cible, bien que le jeton SAS soit toujours pour le conteneur.
* L’exemple de demande ci-dessous montre un document unique qui est traduit en deux langues cibles

```json
{
    "inputs": [
        {
            "storageType": "File",
            "source": {
                "sourceUrl": "https://my.blob.core.windows.net/source-en/source-english.docx?sv=2019-12-12&st=2021-01-26T18%3A30%3A20Z&se=2021-02-05T18%3A30%3A00Z&sr=c&sp=rl&sig=d7PZKyQsIeE6xb%2B1M4Yb56I%2FEEKoNIF65D%2Fs0IFsYcE%3D"
            },
            "targets": [
                {
                    "targetUrl": "https://my.blob.core.windows.net/target/try/Target-Spanish.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D",
                    "language": "es"
                },
                {
                    "targetUrl": "https://my.blob.core.windows.net/target/try/Target-German.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D",
                    "language": "de"
                }
            ]
        }
    ]
}
```

## <a name="response-status-codes"></a>Codes d’état de réponse

Voici les codes d’état HTTP qu’une demande peut retourner.

|Code d’état|Description|
|--- |--- |
|202|Accepté. La demande ayant réussi et la demande de lot sont créées par le service. L’en-tête Operation-Location indique une URL d’état avec la chaîne ID.HeadersOperation-Location: de l’opération|
|400|Demande incorrecte. Demande non valide. Vérifiez les paramètres d’entrée.|
|401|Non autorisé. Vérifiez vos informations d’identification.|
|429|Le taux de demandes est trop élevé.|
|500|Erreur interne du serveur.|
|503|Le service est actuellement indisponible.  Veuillez réessayer plus tard.|
|Autres codes d’état|<ul><li>Trop de demandes</li><li>Serveur temporairement indisponible</li></ul>|

## <a name="error-response"></a>Réponse d’erreur

|Nom|Type|Description|
|--- |--- |--- |
|code|string|Enums contenant des codes d’erreur généraux. Valeurs possibles :<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Non autorisé</li></ul>|
|message|string|Obtient un message d’erreur général.|
|innerError|InnerTranslationError|Nouveau format d’erreur interne qui est conforme aux instructions de l’API Cognitive Services. Ce format contient les propriétés obligatoires ErrorCode, message et les propriétés facultatives target, details (paire clé-valeur), et l’erreur interne (qui peut être imbriquée).|
|inner.Errorcode|string|Obtient la chaîne d’erreur de code.|
|innerError.message|string|Obtient un message d’erreur général.|
|innerError.target|string|Obtient la source de l’erreur. Par exemple, « documents » ou « document id » en présence d’un document non valide.|

## <a name="examples"></a>Exemples

### <a name="example-successful-response"></a>Exemple de réponse positive

Les informations suivantes sont retournées dans une réponse positive.

Vous trouverez l’ID de travail dans la valeur de l’URL de l’en-tête de réponse Operation-Location de la méthode POST. Le dernier paramètre de l’URL est l’ID de travail de l’opération (la chaîne qui suit "/operation/").

```HTTP
Operation-Location: https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55
```

### <a name="example-error-response"></a>Exemple de réponse d’erreur

```JSON
{
  "error": {
    "code": "ServiceUnavailable",
    "message": "Service is temporary unavailable",
    "innerError": {
      "code": "ServiceTemporaryUnavailable",
      "message": "Service is currently unavailable.  Please try again later"
    }
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

Suivez notre guide de démarrage rapide pour en savoir plus sur l’utilisation du service Traduction de document et de la bibliothèque de client.

> [!div class="nextstepaction"]
> [Bien démarrer avec la traduction de documentation](../get-started-with-document-translation.md)
