---
title: Création d’un jeu de données d’apprentissage pour un modèle personnalisé - Form Recognizer
titleSuffix: Azure Applied AI Services
description: Découvrez comment vous assurer que votre jeu de données d’apprentissage est optimisé pour l’entraînement d’un modèle Form Recognizer.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: 640cad589343a90912dfe0dba615f1df6baaa6b2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027686"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Créer un jeu de données d’apprentissage pour un modèle personnalisé

Lorsque vous utilisez le modèle personnalisé Form Recognizer, vous fournissez vos propres données d’apprentissage à l’opération [Effectuer l’apprentissage d’un modèle personnalisé](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync) afin que le modèle puis s’entraîner sur des formulaires spécifiques de votre secteur. Suivez ce guide pour apprendre à collecter et préparer des données afin d’effectuer l’apprentissage du modèle efficacement.

Vous avez besoin d’au moins cinq formulaires remplis du même type.

Si vous souhaitez utiliser des données d’apprentissage étiquetées manuellement, il vous faut pour commencer au moins cinq formulaires remplis du même type. Vous pouvez également utiliser des formulaires sans étiquette en plus du jeu de données requis.

## <a name="custom-model-input-requirements"></a>Exigences d’entrée de modèle personnalisé

Commencez par vous assurer que votre jeu de données d’apprentissage respecte les exigences d’entrée pour Form Recognizer.

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="training-data-tips"></a>Conseils relatifs aux données d’entraînement

Suivez les conseils supplémentaires suivants pour optimiser davantage votre jeu de données à des fins d’apprentissage.

* Si possible, utilisez des documents PDF utilisant du texte au lieu d’images. Les fichiers PDF numérisés sont traités comme des images.
* Pour les formulaires remplis, utilisez les exemples dont les champs sont tous renseignés.
* Utilisez des formulaires avec des valeurs différentes dans chaque champ.
* Si vos images de formulaire sont de faible qualité, utilisez un plus grand jeu de données (par exemple, 10 à 15 images).

## <a name="upload-your-training-data"></a>Charger vos données d’entraînement

Une fois que vous avez combiné l’ensemble des documents de formulaire que vous utiliserez pour l’entraînement, vous devez charger cet ensemble sur un conteneur de stockage blob Azure. Si vous ignorez comment créer un compte de stockage Azure avec un conteneur, consultez le [démarrage rapide du stockage Azure pour le portail Azure](../../storage/blobs/storage-quickstart-blobs-portal.md). Utilisez le niveau de performance standard.

Si vous souhaitez utiliser des données étiquetées manuellement, vous devrez également charger les fichiers *.labels.json* et *.ocr.json* correspondant à vos documents d’apprentissage. Vous pouvez vous servir de [l’outil d’étiquetage des exemples](label-tool.md) (ou de votre propre interface utilisateur) pour générer ces fichiers.

### <a name="organize-your-data-in-subfolders-optional"></a>Organiser vos données dans des sous-dossiers (facultatif)

Par défaut, l’API [Entraîner un modèle personnalisé](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync) utilisera uniquement les formulaires situés à la racine de votre conteneur de stockage. Toutefois, l’entraînement peut être effectué avec des données dans les sous-dossiers si vous le spécifiez dans l’appel d’API. Normalement, le corps de l’appel [Entraîner un modèle personnalisé](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync) se présente au format suivant, où `<SAS URL>` correspond à l’URL de signature d’accès partagé du conteneur :

```json
{
  "source":"<SAS URL>"
}
```

Si vous ajoutez le contenu suivant au corps de la demande, l’API sera entraînée en utilisant les documents situés dans les sous-dossiers. Le champ `"prefix"` est facultatif et limitera le jeu de données d’apprentissage de manière à utiliser uniquement les fichiers dont les chemins d’accès commencent par la chaîne donnée. Ainsi, avec une valeur de `"Test"` par exemple, l’API n’examinera que les fichiers ou les dossiers qui commencent par le mot « Test ».

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  },
  "useLabelFile": false
}
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à créer un jeu de données d’apprentissage, suivez un guide de démarrage rapide pour entraîner un modèle Form Recognizer personnalisé et commencer à l’utiliser sur vos formulaires.

* [Entraîner un modèle et extraire des données de formulaire avec la bibliothèque de client ou l’API REST](quickstarts/try-sdk-rest-api.md)
* [Entraînement avec des étiquettes à l’aide de l’outil Exemple d’étiquetage](label-tool.md)

## <a name="see-also"></a>Voir aussi

* [Qu’est-ce que Form Recognizer ?](./overview.md)
