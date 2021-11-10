---
title: Appeler l’API Analyse d’image
titleSuffix: Azure Cognitive Services
description: Découvrez comment appeler l’API Analyse d’image et configurer son comportement.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 46334015d932f667e5bbb7a10153e9b70aaa0764
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058596"
---
# <a name="call-the-image-analysis-api"></a>Appeler l’API Analyse d’image

Cet article explique comment appeler l’API Analyse d’image pour retourner des informations sur les caractéristiques visuelles d’une image.

Ce guide part du principe que vous avez déjà <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="créé une ressource Vision par ordinateur"  target="_blank">créer une ressource Vision par ordinateur</a>, et obtenu une clé d’abonnement et une URL de point de terminaison. Si vous ne l'avez pas encore fait, suivez un guide de [démarrage rapide](../quickstarts-sdk/image-analysis-client-library.md) pour commencer.
  
## <a name="submit-data-to-the-service"></a>Envoyer des données au service

Vous soumettez une image locale ou une image distante à l’API Analyser. Pour une image locale, vous placez les données image binaires dans le corps de la requête HTTP. Pour une image distante, vous spécifiez l’URL de l’image en mettant en forme le corps de la requête comme suit : `{"url":"http://example.com/images/test.jpg"}`.

## <a name="determine-how-to-process-the-data"></a>Déterminer le mode de traitement des données

###  <a name="select-visual-features"></a>Sélectionner des caractéristiques visuelles

L’[API Analyser](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b) vous permet d’accéder à toutes les fonctionnalités d’analyse d’image du service. Vous devez spécifier les fonctionnalités que vous voulez utiliser en définissant les paramètres de requête d’URL. Un paramètre peut avoir plusieurs valeurs, séparées par des virgules. Chaque fonctionnalité que vous spécifiez nécessitera un temps de calcul supplémentaire. Par conséquent, spécifiez uniquement ce dont vous avez besoin.

|Paramètre d’URL | Valeur | Description|
|---|---|--|
|`visualFeatures`|`Adult` | détecte si l’image est de nature pornographique (nudité ou acte sexuel) ou si elle est choquante (violence extrême ou sang). Le contenu sexuellement suggestif (ou contenu osé) est également détecté.|
||`Brands` | détecte les différentes marques dans une image, notamment leur emplacement approximatif. L’argument Brands est disponible uniquement en anglais.|
||`Categories` | classe le contenu de l’image en fonction d’une taxonomie définie dans la documentation. Il s'agit de la valeur par défaut de `visualFeatures`.|
||`Color` | détermine la couleur d’accentuation, la couleur dominante et si une image est en noir et blanc.|
||`Description` | décrit le contenu de l’image avec une phrase complète dans les langues prises en charge.|
||`Faces` | détecte si des visages sont présents. Si tel est le cas, génère des coordonnées, ainsi que l’âge et le sexe.|
||`ImageType` | détecte si l’image est de type clipart ou un dessin au trait.|
||`Objects` | détecte les différents objets dans une image, notamment leur emplacement approximatif. L’argument Objects est disponible uniquement en anglais.|
||`Tags` | étiquette l’image avec une liste détaillée de mots liés au contenu de l’image.|
|`details`| `Celebrities` | identifie les célébrités éventuellement détectées dans l’image.|
||`Landmarks` |identifie les paysages éventuellement détectés dans l’image.|

Une URL remplie peut se présenter comme suit :

`https://{endpoint}/vision/v2.1/analyze?visualFeatures=Description,Tags&details=Celebrities`

### <a name="specify-languages"></a>Spécifier les langues

Vous pouvez également spécifier la langue des données retournées. Le paramètre de requête d’URL suivant spécifie la langue. La valeur par défaut est `en`.

|Paramètre d’URL | Valeur | Description|
|---|---|--|
|`language`|`en` | Anglais|
||`es` | Espagnol|
||`ja` | Japonais|
||`pt` | Portugais|
||`zh` | Chinois simplifié|

Une URL remplie peut se présenter comme suit :

`https://{endpoint}/vision/v2.1/analyze?visualFeatures=Description,Tags&details=Celebrities&language=en`

> [!NOTE]
> **Appels d’API étendues**
>
> Certaines des fonctionnalités de l’Analyse d’image peuvent être appelées directement, ainsi que par le biais d’un appel de l’API Analyser. Par exemple, vous pouvez effectuer une analyse étendue des étiquettes d’image uniquement en effectuant une requête à `https://{endpoint}/vision/v3.2/tag`. Consultez la [documentation de référence](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b) pour découvrir d’autres fonctionnalités qui peuvent être appelées séparément.

## <a name="get-results-from-the-service"></a>Obtenir les résultats du service

Le service retourne une réponse HTTP `200`, et le corps contient les données retournées sous la forme d’une chaîne JSON. Voici un exemple de réponse JSON.

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

Pour obtenir des explications sur les champs de cet exemple, consultez le tableau suivant :

Champ | Type | Contenu
------|------|------|
Balises  | `object` | Objet de plus haut niveau pour un tableau d’étiquettes.
tags[].Name | `string`    | Mot clé du classifieur d’étiquettes.
tags[].Score    | `number`    | Score de confiance, compris entre 0 et 1.
description     | `object`    | Objet de niveau supérieur pour la description d’une description.
description.tags[] |    `string`    | Liste d’étiquettes. En cas confiance insuffisante pour pouvoir produire une légende, les étiquettes peuvent être les seules informations disponibles pour l’appelant.
description.captions[].text    | `string`    | Expression décrivant l’image.
description.captions[].confidence    | `number`    | Score de confiance pour l’expression.

### <a name="error-codes"></a>Codes d’erreur

Consultez la liste suivante des erreurs possibles et leurs causes :

* 400
    * InvalidImageUrl : le format de l’URL de l’image est incorrect ou l’URL est inaccessible.
    * InvalidImageFormat : les données d’entrée ne sont pas une image valide.
    * InvalidImageSize : l’image d’entrée est trop grande.
    * NotSupportedVisualFeature : le type de caractéristique spécifié n’est pas valide.
    * NotSupportedImage : image non prise en charge, par exemple pédopornographie.
    * InvalidDetails : valeur du paramètre `detail` non prise en charge.
    * NotSupportedLanguage : l’opération demandée n’est pas prise en charge dans la langue spécifiée.
    * BadArgument : des détails supplémentaires sont fournis dans le message d’erreur.
* 415 : erreur de type de support non pris en charge. Le type Content-Type ne figure pas dans les types autorisés :
    * Pour une URL d’image : Content-Type doit avoir la valeur application/json
    * Pour des données image binaires : Content-Type doit avoir la valeur application/octet-stream ou multipart/form-data
* 500
    * FailedToProcess
    * Timeout : le traitement de l’image a expiré.
    * InternalServerError

> [!TIP]
> Quand vous utilisez l’API Vision par ordinateur, vous pouvez rencontrer des défaillances temporaires causées par des [limites du taux de transfert](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) appliquées par le service ou d’autres problèmes passagers comme des pannes du réseau. Pour plus d’informations sur la gestion de ces types d’échecs, consultez le [modèle Nouvelle tentative](/azure/architecture/patterns/retry) dans le guide des modèles de conception de cloud et le [modèle Disjoncteur](/azure/architecture/patterns/circuit-breaker) connexe.


## <a name="next-steps"></a>Étapes suivantes

Pour essayer l’API REST, consultez les [Informations de référence sur l’API Analyse d’images](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b).