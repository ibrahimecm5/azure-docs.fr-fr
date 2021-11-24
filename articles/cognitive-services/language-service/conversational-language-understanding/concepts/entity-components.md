---
title: Composants d’entité dans la compréhension du langage courant
titleSuffix: Azure Cognitive Services
description: Découvrez la façon dont la compréhension du langage courant extrait les entités d’un texte
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: c42028b10a71a039a208f82cfacb58e5ba8e07cc
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347724"
---
# <a name="entity-components"></a>Composants de l’entité

Dans la compréhension du langage courant, les entités sont des informations pertinentes qui sont extraites de vos énoncés. Une entité peut être extraite par différentes méthodes. Elles peuvent être apprises par le biais d’un contexte, d’une correspondance à partir d’une liste ou détectées par une entité reconnue prédéfinie. Chaque entité de votre projet est constituée d’une ou de plusieurs de ces méthodes, qui sont définies en tant que composants de votre entité. Quand une entité est définie par plusieurs composants, leurs prédictions peuvent se chevaucher. Vous pouvez déterminer le comportement d’une prédiction d’entité quand ses composants se chevauchent en utilisant un ensemble fixe d’options dans la **méthode de chevauchement**.

## <a name="component-types"></a>Types de composants

Un composant d’entité détermine la façon dont vous pouvez extraire l’entité. Une entité peut contenir un seul composant, qui détermine la seule méthode qui serait utilisée pour extraire l’entité, ou plusieurs composants pour étendre la manière dont l’entité est définie et extraite.

### <a name="learned-component"></a>Composant appris

Le composant appris utilise les étiquettes d’entité avec lesquelles vous étiquetez vos énoncés pour entraîner un modèle issu du machine learning. Le modèle apprend à prédire l’emplacement de l’entité, en fonction du contexte dans l’énoncé. Vos étiquettes fournissent des exemples d’emplacement où l’entité doit être présente dans un énoncé, en fonction de la signification des mots qui l’entourent et des mots qui ont été étiquetés. Ce composant n’est défini que si vous ajoutez des étiquettes en étiquetant des énoncés pour l’entité. Si vous n’étiquetez aucun énoncé avec l’entité, celle-ci n’aura aucun composant appris.

:::image type="content" source="../media/learned-component.png" alt-text="Capture d’écran montrant un exemple de composants appris pour des entités" lightbox="../media/learned-component.png":::

### <a name="list-component"></a>Composant de liste

Le composant de liste représente un ensemble fixe, fermé de mots associés ainsi que leurs synonymes. Le composant effectue une correspondance de texte exacte avec la liste de valeurs que vous fournissez en tant que synonymes. Chaque synonyme appartient à une « clé de liste » qui peut être utilisée comme valeur standard normalisée pour le synonyme retourné dans la sortie si le composant de liste est mis en correspondance. Les clés de liste ne sont **pas** utilisées pour la correspondance.


:::image type="content" source="../media/list-component.png" alt-text="Capture d’écran montrant un exemple de composants de liste pour des entités" lightbox="../media/list-component.png":::

### <a name="prebuilt-component"></a>Composant prédéfini

Le composant prédéfini vous permet d’effectuer une sélection à partir d’une bibliothèque de types courants tels que des nombres, des valeurs de date/heure et des noms. Quand un composant prédéfini est ajouté, il est détecté automatiquement. Vous pouvez avoir jusqu’à 5 composants prédéfinis par entité. Pour plus d’informations, consultez la [liste des composants prédéfinis pris en charge](../prebuilt-component-reference.md).


:::image type="content" source="../media/prebuilt-component.png" alt-text="Capture d’écran montrant un exemple de composants prédéfinis pour des entités" lightbox="../media/prebuilt-component.png":::


## <a name="overlap-methods"></a>Méthodes de chevauchement

Quand plusieurs composants sont définis pour une entité, leurs prédictions peuvent se chevaucher. En cas de chevauchement, la prédiction finale de chaque entité est déterminée par l’une des options suivantes.

### <a name="longest-overlap"></a>Chevauchement le plus long

Quand deux composants ou plus sont trouvés dans le texte et que la méthode de chevauchement est utilisée, le composant avec l’**ensemble de caractères** le plus long est retourné.

Vous pouvez utiliser cette option si vous souhaitez extraire la plus longue prédiction possible par les différents composants. Cette méthode garantit qu’en cas de confusion (chevauchement), le composant retourné est le composant le plus long.

#### <a name="examples"></a>Exemples

Si « Palm Beach » a été mis en correspondance par le composant de liste et que « Palm Beach Extension » a été prédit par le composant appris, « **Palm Beach Extension** » est retourné, car il s’agit de l’ensemble de caractères le plus long dans ce chevauchement.

:::image type="content" source="../media/return-longest-overlap-example-1.svg" alt-text="Capture d’écran montrant un exemple de résultats de chevauchement le plus long pour des composants" lightbox="../media/return-longest-overlap-example-1.svg":::

Si « Palm Beach » a été mis en correspondance par le composant de liste et que « Beach Extension » a été prédit par le composant appris, « **Beach Extension** » est retourné, car il s’agit du composant ayant l’ensemble de caractères le plus long dans ce chevauchement.

:::image type="content" source="../media/return-longest-overlap-example-2.svg" alt-text="Capture d’écran montrant un deuxième exemple de résultats de chevauchement le plus long pour des composants" lightbox="../media/return-longest-overlap-example-2.svg":::

Si « Palm Beach » a été mis en correspondance à partir du composant de liste et que « Extension » a été prédit par le composant appris, 2 instances distinctes des entités sont retournées, car il n’y a aucun chevauchement entre elles : une pour « **Palm Beach** », l’autre pour « **Extension** », car aucun chevauchement n’a eu lieu dans cette instance.

:::image type="content" source="../media/return-longest-overlap-example-3.svg" alt-text="Capture d’écran montrant un troisième exemple de résultats de chevauchement le plus long pour des composants" lightbox="../media/return-longest-overlap-example-3.svg":::

### <a name="exact-overlap"></a>Chevauchement exact

Tous les composants doivent se chevaucher **exactement au niveau des mêmes caractères** dans le texte pour que l’entité soit retournée. Si l’un des composants définis n’est pas mis en correspondance ou prédit, l’entité n’est pas retournée.

Vous pouvez utiliser cette option quand vous avez une entité stricte dont plusieurs composants doivent être détectés en même temps pour qu’elle soit extraite.

#### <a name="examples"></a>Exemples

Si « Palm Beach » a été mis en correspondance par le composant de liste et que « Palm Beach » a été prédit par le composant appris et que seuls ces deux composants sont définis dans l’entité, « **Palm Beach** » est retourné, car tous les composants se chevauchent exactement au niveau des mêmes caractères.

:::image type="content" source="../media/require-exact-overlap-example-1.svg" alt-text="Capture d’écran montrant un exemple de résultats de chevauchement exact pour des composants" lightbox="../media/require-exact-overlap-example-1.svg":::

Si « Palm Beach » a été mis en correspondance par le composant de liste et que « Beach Extension » a été prédit par le composant appris, l’entité n’est **pas** retournée, car tous les composants ne se chevauchent pas exactement au niveau des mêmes caractères.

:::image type="content" source="../media/require-exact-overlap-example-2.svg" alt-text="Capture d’écran montrant un deuxième exemple de résultats de chevauchement exact pour des composants" lightbox="../media/require-exact-overlap-example-2.svg":::

Si « Palm Beach » a été mis en correspondance à partir du composant de liste et que « Extension » a été prédit par le composant appris, l’entité n’est **pas** retournée, car aucun chevauchement n’a eu lieu dans cette instance.

:::image type="content" source="../media/require-exact-overlap-example-3.svg" alt-text="Capture d’écran montrant un troisième exemple de résultats de chevauchement exact pour des composants" lightbox="../media/require-exact-overlap-example-3.svg":::

### <a name="union-overlap"></a>Chevauchement d’union

Quand plusieurs composants sont trouvés dans le texte et se chevauchent, l’**union** des étendues des composants est retournée.

Vous pouvez utiliser cette option quand vous optimisez le rappel et tentez d’obtenir la correspondance la plus longue possible pouvant être combinée.

#### <a name="examples"></a>Exemples

Si « Palm Beach » a été mis en correspondance par le composant de liste et que « Palm Beach Extension » a été prédit par le composant appris, « **Palm Beach Extension** » est retourné, car le premier caractère au début du chevauchement est le « P » de « Palm » et la dernière lettre à la fin des composants qui se chevauchent est le « n » de « Extension ».

:::image type="content" source="../media/return-union-example-1.svg" alt-text="Capture d’écran montrant un exemple de résultats de chevauchement d’union pour des composants" lightbox="../media/return-union-example-1.svg":::

Si « Palm Beach » a été mis en correspondance par le composant de liste et que « Beach Extension » a été prédit par le composant appris, « **Palm Beach Extension** » est retourné, car le premier caractère au début du chevauchement est le « P » de « Palm » et la dernière lettre à la fin des composants qui se chevauchent est le « n » de « Extension ».

:::image type="content" source="../media/return-union-example-2.svg" alt-text="Capture d’écran montrant un deuxième exemple de résultats de chevauchement d’union pour des composants" lightbox="../media/return-union-example-2.svg":::

Si « New York » a été mis en correspondance par le composant prédéfini, que « York Beach » a été mis en correspondance par le composant de liste et que « Beach Extension » a été prédit par le composant appris, « __**New York Beach Extension**__  » est retourné, car le premier caractère au début du chevauchement est le « N » de « New » et la dernière lettre à la fin des composants qui se chevauchent est le « n » de « Extension ».

:::image type="content" source="../media/return-union-example-3.svg" alt-text="Capture d’écran montrant un troisième exemple de résultats de chevauchement d’union pour des composants" lightbox="../media/return-union-example-3.svg":::

### <a name="return-all-separately"></a>Retourner tout séparément

La correspondance ou la prédiction de chaque composant est retournée en tant qu’**instance distincte** de l’entité.

Cette option est recommandée quand vous souhaitez appliquer votre propre logique de chevauchement pour l’entité après la prédiction.

#### <a name="examples"></a>Exemples

Si « Palm Beach » a été mis en correspondance par le composant de liste et que « Palm Beach Extension » a été prédit par le composant appris, l’entité retourne deux instances : une pour « **Palm Beach** », l’autre pour « **Palm Beach Extension** ».

:::image type="content" source="../media/return-all-overlaps-example-1.svg" alt-text="Capture d’écran montrant un exemple de retour de tous les résultats de chevauchement d’union pour des composants" lightbox="../media/return-all-overlaps-example-1.svg":::

Si « New York » a été prédit par le composant prédéfini, que « York Beach » a été mis en correspondance par le composant de liste et que « Beach Extension » a été prédit par le composant appris, l’entité est retournée avec trois instances : une pour « **New York** », une pour « **York Beach** » et une pour « **Beach Extension** ».

:::image type="content" source="../media/return-all-overlaps-example-2.svg" alt-text="Capture d’écran montrant un deuxième exemple de retour de tous les résultats de chevauchement d’union pour des composants" lightbox="../media/return-all-overlaps-example-2.svg":::

## <a name="next-steps"></a>Étapes suivantes

[Composants prédéfinis pris en charge](../prebuilt-component-reference.md)
