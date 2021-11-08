---
title: Comment utiliser des étiquettes de tableau pour effectuer l’apprentissage de votre module de formulaire personnalisé – Form Recognizer
titleSuffix: Azure Applied AI Services
description: Apprenez à utiliser efficacement l’étiquetage supervisé des tableaux.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 07/23/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2d61c48204478cc19be51d5d2c0d2974c674661d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020841"
---
# <a name="use-table-tags-to-train-your-custom-form-model"></a>Utiliser des étiquettes de tableau pour effectuer l’apprentissage de votre modèle de formulaire personnalisé

Dans cet article, vous allez apprendre à effectuer l’apprentissage de votre modèle de formulaire personnalisé à l’aide d’étiquettes de tableau. Certains scénarios nécessitent un étiquetage plus complexe que le simple alignement des paires clé-valeur. Ces scénarios incluent l’extraction d’informations de formulaires aux structures hiérarchiques complexes ou la rencontre d’éléments qui ne sont pas automatiquement détectés et extraits par le service. Dans ces cas, vous pouvez utiliser les étiquettes de tableau pour effectuer l’apprentissage de votre modèle de formulaire personnalisé.

## <a name="when-should-i-use-table-tags"></a>Quand dois-je utiliser des étiquettes de tableau ?

Voici quelques exemples de cas où l’utilisation d’étiquettes de tableau serait appropriée :

- Les données que vous souhaitez extraire sont présentées sous forme de tableaux dans vos formulaires, et la structure des tableaux a une signification. Par exemple, chaque ligne du tableau représente un élément et chaque colonne de la ligne représente une caractéristique spécifique de cet élément. Dans ce cas, vous pouvez utiliser une étiquette de tableau dans laquelle une colonne représente des caractéristiques et une ligne représente des informations sur chaque caractéristique.
- Les données que vous souhaitez extraire ne sont pas présentées dans des champs de formulaire spécifiques, mais, d’un point de vue sémantique, elles pourraient s’insérer dans une grille bidimensionnelle. Par exemple, votre formulaire contient une liste de personnes, avec un prénom, un nom et une adresse e-mail. Vous voulez extraire ces informations. Dans ce cas, vous pouvez utiliser une étiquette de tableau dont les colonnes sont le prénom, le nom et l’adresse e-mail et dont chaque ligne contient des informations sur une personne de votre liste.

> [!NOTE]
> Form Recognizer recherche et extrait automatiquement tous les tableaux de vos documents, que les tableaux soient étiquetés ou non. Par conséquent, il n’est pas nécessaire d’étiqueter chaque tableau de votre formulaire avec une étiquette de tableau ,et vos étiquettes de tableau n’ont pas à reproduire la structure de chaque tableau trouvé dans votre formulaire. Les tableaux extraits automatiquement par Form Recognizer seront inclus dans la section pageResults de la sortie JSON.

## <a name="create-a-table-tag-with-the-form-recognizer-sample-labeling-tool"></a>Créer une étiquette de table à l’aide de l’outil d’étiquetage des exemples Form Recognizer
<!-- markdownlint-disable MD004 -->
* Déterminez si vous souhaitez une étiquette de tableau **dynamique** ou **de taille fixe**. Si le nombre de lignes varie d’un document à l’autre, utilisez une étiquette de tableau dynamique. Si le nombre de lignes est constant d’un document à l’autre, utilisez une étiquette de tableau de taille fixe.
* Si votre étiquette de tableau est dynamique, définissez les noms des colonnes ainsi que le type et le format des données pour chaque colonne.
* Si votre tableau est de taille fixe, définissez le nom de la colonne, le nom de la ligne, le type de données et le format pour chaque étiquette.
:::image type="content" source="./media/table-tag-configure.png" alt-text="Configurer une étiquette de tableau":::

## <a name="label-your-table-tag-data"></a>Étiqueter les données de vos étiquettes de tableau

* Si votre projet comporte une étiquette de tableau, vous pouvez ouvrir le panneau d’étiquetage et remplir l’étiquette comme vous le feriez pour des champs de type clé-valeur.
:::image type="content" source="media/table-labeling.png" alt-text="Étiqueter avec des étiquettes de tableau":::

## <a name="next-steps"></a>Étapes suivantes

Suivez notre démarrage rapide pour effectuer l’apprentissage de votre modèle Form Recognizer personnalisé et l’utiliser :

> [!div class="nextstepaction"]
> [Entraînement avec des étiquettes à l’aide de l’outil d’étiquetage des exemples](label-tool.md)

## <a name="see-also"></a>Voir aussi

* [Entraîner un modèle personnalisé avec l’outil d’étiquetage des exemples](label-tool.md)
>
