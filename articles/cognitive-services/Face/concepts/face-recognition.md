---
title: Concepts de reconnaissance faciale
titleSuffix: Azure Cognitive Services
description: Découvrez le concept de reconnaissance faciale, les opérations associées et les structures de données sous-jacentes.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: pafarley
ms.openlocfilehash: 3ae6a67ce0f906a4b498abaec703ddd6e64485bf
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426256"
---
# <a name="face-recognition-concepts"></a>Concepts de reconnaissance faciale

Cet article explique le concept de reconnaissance faciale, les opérations associées et les structures de données sous-jacentes. D’une manière générale, la reconnaissance faciale fait référence à la méthode de vérification ou d’identification d’une personne à l’aide de son visage. 

La vérification est une correspondance un-à-un qui prend deux visages et indique s’ils correspondent à la même personne. L’identification est une correspondance un-à-plusieurs qui prend un seul visage comme entrée et retourne un ensemble de candidats correspondants. La reconnaissance faciale est importante pour l’implémentation du scénario de vérification d’identité que des entreprises et applications peuvent utiliser pour vérifier qu’un utilisateur (distant) est bien celui qu’il prétend être.

## <a name="related-data-structures"></a>Structures de données associées

Les opérations de reconnaissance utilisent principalement les structures de données suivantes. Ces objets sont stockés dans le cloud et peuvent être référencés par leurs chaînes d’ID. Les chaînes d’ID sont toujours uniques dans un abonnement, mais des champs de nom peuvent être dupliqués.

|Nom|Description|
|:--|:--|
|DetectedFace| Cette représentation de visage unique est récupérée par l’opération de [détection des visages](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md). Son ID expire 24 heures après sa création.|
|PersistedFace| Lorsque des objets DetectedFace sont ajoutés à un groupe, tel que FaceList ou Person, ils deviennent des objets PersistedFace. Ils peuvent être [récupérés](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) à n’importe quel moment et n’expirent pas.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) ou [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Cette structure de données est une liste assortie d’objets PersistedFace. Un objet FaceList a un ID unique, une chaîne de nom et éventuellement une chaîne de données utilisateur.|
|[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Cette structure de données est une liste d’objets PersistedFace qui appartiennent à la même personne. Elle possède un ID unique, une chaîne de nom et éventuellement une chaîne de données utilisateur.|
|[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) ou [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Cette structure de données est une liste assortie d’objets Person. Elle possède un ID unique, une chaîne de nom et éventuellement une chaîne de données utilisateur. Un PersonGroup doit être [entraîné](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) avant de pouvoir être utilisé dans les opérations de reconnaissance.|
|PersonDirectory | Cette structure de données est similaire à **LargePersonGroup**, mais elle offre une capacité de stockage supplémentaire et d’autres fonctionnalités ajoutées. Pour plus d’informations, consultez [Utiliser la structure PersonDirectory](../Face-API-How-to-Topics/use-persondirectory.md).

## <a name="recognition-operations"></a>Opérations de reconnaissance

Cette section explique en détail comment les opérations sous-jacentes utilisent les structures de données décrites ci-dessus pour identifier et vérifier un visage.

### <a name="persongroup-creation-and-training"></a>Création et entraînement de PersonGroup

Vous devez créer un [PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) ou un [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) pour stocker l’ensemble de personnes avec lequel établir une correspondance. Les PersonGroups contiennent des objets [Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), chacun représentant une personne spécifique et contenant un ensemble de données de visage appartenant à cette personne.

L’opération [Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) prépare le jeu de données à utiliser pour les comparaisons de données de visage.

### <a name="identification"></a>Identification

L’opération [Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) prend un ou plusieurs ID de visage source (à partir d’un objet DetectedFace ou PersistedFace) et un PersonGroup ou un LargePersonGroup. Elle retourne une liste des objets Person auxquels chaque visage source peut appartenir. Les objets Person retournés sont empaquetés en tant qu’objets de candidats et comportent une valeur de niveau de confiance de prédiction.

### <a name="verification"></a>Vérification

L’opération [Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) prend un seul ID de visage (à partir d’un objet DetectedFace ou PersistedFace) et un objet Person. Elle détermine si le visage appartient à la même personne. La vérification est une correspondance un-à-un et peut être utilisée comme contrôle final des résultats de l’appel d’API Identify. Toutefois, si vous le souhaitez, vous pouvez transmettre le PersonGroup auquel appartient l’objet Person candidat pour améliorer les performances de l’API.

## <a name="input-data"></a>Données d’entrée

Utilisez les conseils suivants pour vous assurer que vos images d’entrée fournissent les résultats de reconnaissance les plus précis :

* Les formats d’image d’entrée pris en charge sont JPEG, PNG, GIF (la première image) et BMP.
* La taille du fichier image ne doit pas dépasser 6 Mo.
* Lorsque vous créez des objets Person, utilisez des photos présentant différents types d’éclairage et d’angle.
* Certains visages peuvent ne pas être reconnus en raison de défis techniques, tels que :
  * Des images avec un éclairage extrême, par exemple, un contre-jour marqué.
  * Des obstacles cachant un œil ou les deux yeux.
  * Des différences de type de cheveux ou de pilosité faciale.
  * Des changements d’apparence du visage dus à l’âge.
  * Une très grande expressivité du visage.
* Vous pouvez utiliser l’attribut qualityForRecognition dans l’opération de [détection de visage](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) lorsque vous utilisez des modèles de détection applicables de manière générale pour déterminer si l’image est susceptible d’offrir une qualité suffisante pour tenter une reconnaissance faciale. Seules des images de qualité « élevée » sont recommandées pour l’inscription de personnes, et une qualité supérieure à « moyenne » est recommandée pour les scénarios d’identification.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous êtes familiarisé avec les concepts de reconnaissance faciale, écrivez un script pour identifier les visages par rapport à un objet PersonGroup formé.

* [Démarrage rapide : Bibliothèque de client Visage](../Quickstarts/client-libraries.md)