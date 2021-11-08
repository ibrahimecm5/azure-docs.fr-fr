---
title: Personnaliser la prononciation
description: Utilisez des phonèmes pour personnaliser la prononciation des mots dans la reconnaissance vocale.
author: ut-karsh
ms.author: umaheshwari
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/19/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 6b8f2339668656ec067bae234d21ec4bf2d5741c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096281"
---
# <a name="customize-pronunciation"></a>Personnaliser la prononciation

La reconnaissance vocale personnalisée vous permet de fournir des prononciations différentes pour des mots spécifiques à l’aide du jeu de phonèmes universel. Le jeu de phonèmes universel (UPS) est un ensemble de phones lisible par ordinateur basé sur l’Alphabet phonétique international (API). L’API est utilisé par les linguistes du monde entier et est utilisé comme alphabet standard.

Les prononciations UPS se composent d’une chaîne de phones UPS, séparés par un espace blanc. L’ensemble de phones respecte la casse. Les étiquettes des phones UPS sont toutes définies à l’aide de chaînes de caractères ASCII.

Pour plus d’informations sur l’implémentation du jeu de phonèmes universel, consultez [Données de texte structurées pour les jeux de phonèmes d’apprentissage](how-to-custom-speech-test-and-train.md#structured-text-data-for-training-public-preview)

Ces données de texte structurées sont différentes des [fichiers de prononciation](how-to-custom-speech-test-and-train.md#pronunciation-data-for-training), et elles ne peuvent pas être utilisées ensemble.

## <a name="languages-supported"></a>Langues prises en charge

Utilisez le tableau ci-dessous pour accéder au jeu de phonèmes universel de la langue respective.

| Langage                | Paramètres régionaux  |
|-------------------------|---------|
| [Anglais (États-Unis)](phone-sets.md) | `en-US` |


## <a name="next-steps"></a>Étapes suivantes

* [Fournir une prononciation UPS à Custom Speech](how-to-custom-speech-test-and-train.md#structured-text-data-for-training-public-preview)
