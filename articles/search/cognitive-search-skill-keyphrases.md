---
title: Compétence cognitive Extraction de phrases clés
titleSuffix: Azure Cognitive Search
description: Évalue un texte non structuré puis, pour chaque enregistrement, retourne une liste de phrases clés dans un pipeline d’enrichissement de l’IA dans la Recherche cognitive Azure.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 7d364cd73a866bfa3e40542329ddfc5a6dffcf13
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532973"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Compétence cognitive Extraction de phrases clés

La compétence **Extraction de phrases clés** évalue un texte non structuré puis, pour chaque enregistrement, retourne une liste de phrases clés. Cette compétence utilise les modèles Machine Learning fournis par [Analyse de texte](../cognitive-services/text-analytics/overview.md) dans Cognitive Services.

Cette fonctionnalité est utile si vous avez besoin d’identifier rapidement les principaux points de discours dans l’enregistrement. Par exemple, si nous considérons le texte d’entrée « la nourriture était délicieuse et le personnel était merveilleux », le service retourne « nourriture » et « personnel merveilleux ».

> [!NOTE]
> Cette compétence est liée à Cognitive Services et nécessite [une ressource facturable](cognitive-search-attach-cognitive-services.md) pour les transactions dépassant 20 documents par indexeur et par jour. L'exécution des compétences intégrées est facturée au prix actuel du [paiement à l'utilisation de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).
>

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Limites de données
La taille maximale d’un enregistrement doit être de 50 000 caractères telle que mesurée par [`String.Length`](/dotnet/api/system.string.length). Si vous devez subdiviser vos données avant de les envoyer à l’extracteur de phrases clés, envisagez d’utiliser la [compétence Fractionnement de texte](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Paramètres de la compétence

Les paramètres respectent la casse.

| Entrées | Description |
|---------------------|-------------|
| `defaultLanguageCode` | (Facultatif) Code de langue à appliquer aux documents qui ne spécifient pas explicitement une langue.  Si le code de langue par défaut n’est pas spécifié, l’anglais (en) est utilisé comme code de langue par défaut. <br/> Voir la [Liste complète des langues prises en charge](../cognitive-services/text-analytics/language-support.md). |
| `maxKeyPhraseCount`   | (Facultatif) Nombre maximal de phrases clés à produire. |
| `modelVersion`   | (Facultatif) Version du modèle à utiliser pour appeler le service Analyse de texte. Si rien n’est spécifié, la dernière version disponible est utilisée par défaut. Nous vous recommandons de ne pas spécifier cette valeur, sauf nécessité absolue. Pour plus d’informations, consultez [Contrôle de version de modèle dans l’API Analyse de texte](../cognitive-services/text-analytics/concepts/model-versioning.md). |

## <a name="skill-inputs"></a>Entrées de la compétence

| Entrée  | Description |
|--------------------|-------------|
| `text` | Texte à analyser.|
| `languageCode`    |  Chaîne indiquant la langue des enregistrements. Si ce paramètre n’est pas spécifié, le code de langue par défaut est utilisé pour l’analyse des enregistrements. <br/>Voir la [Liste complète des langues prises en charge](../cognitive-services/text-analytics/language-support.md).|

## <a name="skill-outputs"></a>Sorties de la compétence

| Output     | Description |
|--------------------|-------------|
| `keyPhrases` | Liste des expressions clés extraites du texte d’entrée. Les expressions clés sont retournées par ordre d’importance. |


##  <a name="sample-definition"></a>Exemple de définition

Prenons l’exemple d’un enregistrement SQL qui contient les champs suivants :

```json
{
    "content": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
    "language": "en"
}
```

Votre définition de compétence peut se présenter comme suit :

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      },
      {
        "name": "languageCode",
        "source": "/document/language" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
      }
    ]
  }
```

##  <a name="sample-output"></a>Exemple de sortie

Dans l’exemple ci-dessus, la sortie de votre compétence sera écrite dans un nouveau nœud dans l’arborescence enrichie nommé « document/myKeyPhrases », car il s’agit du `targetName` que nous avons spécifié. Si vous ne spécifiez pas de `targetName`, le nom du nœud sera « document/keyPhrases ».

#### <a name="documentmykeyphrases"></a>document/myKeyPhrases 
```json
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
```

Vous pouvez utiliser « document/myKeyPhrases » comme entrée dans d’autres compétences, ou comme source d’un [mappage de champs de sortie](cognitive-search-output-field-mapping.md).

## <a name="warnings"></a>Avertissements
Si vous indiquez un code de langue non pris en charge, un avertissement est généré, et les phrases clés ne sont pas extraites.
Si votre texte est vide, un avertissement est généré.
Si votre texte comprend plus de 50 000 caractères, seuls les 50 000 premiers caractères sont analysés et un avertissement est émis.

## <a name="see-also"></a>Voir aussi

+ [Compétences prédéfinies](cognitive-search-predefined-skills.md)
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)
+ [Guide pratique pour définir des mappages de champs de sortie](cognitive-search-output-field-mapping.md)