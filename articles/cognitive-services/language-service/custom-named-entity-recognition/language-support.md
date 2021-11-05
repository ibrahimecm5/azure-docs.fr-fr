---
title: Prise en charge des langues et des régions pour la reconnaissance d’entités nommées personnalisées (NER)
titleSuffix: Azure Cognitive Services
description: En savoir plus sur les langues et les régions prises en charge par la reconnaissance d’entité nommée personnalisée (NER).
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: references_regions, language-service-custom-ner, ignite-fall-2021
ms.author: aahi
ms.openlocfilehash: 499fa25a09699e21610fb2c1812612616fb818da
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096994"
---
# <a name="language-support-for-custom-named-entity-recognition-ner"></a>Prise en charge linguistique pour la reconnaissance d’entités nommées personnalisées (NER)

Utilisez cet article pour en savoir plus sur les langues et les régions actuellement prises en charge par la reconnaissance d’entité nommée personnalisée (NER).

## <a name="multiple-language-support"></a>Prise en charge de plusieurs langues

Avec les NER personnalisées, vous pouvez effectuer l’apprentissage d’un modèle dans un langage et le tester dans un autre langage. Cette fonctionnalité est très intéressante, car elle vous permet de gagner du temps : au lieu de créer des projets distincts pour chaque langage, vous pouvez gérer des jeux de données multilingues dans un seul projet. Votre jeu de données n’a pas besoin d’être entièrement dans la même langue, mais vous devez spécifier cette option lors de la création du projet. Si vous remarquez que votre modèle fonctionne mal dans certains langages pendant le processus d’évaluation, envisagez d’ajouter des données dans cette langue à votre jeu d’apprentissage.

> [!NOTE]
> Pour activer la prise en charge de plusieurs langues, vous devez activer cette option lors de [la création de votre projet](how-to/create-project.md).

## <a name="language-support"></a>Support multilingue

La NER personnalisée prend en charge les fichiers `.txt` dans les langues suivantes :

| Langage | Paramètres régionaux |  
|--|--|
| Anglais (États-Unis) |`en-US` |
| Français (France) |`fr-FR` |
| Allemand |`de-DE` |
| Italien |`it-IT` |
| Espagnol (Espagne) |`es-ES` |
| Portugais (Portugal) | `pt-PT` |
| Portugais (Brésil) | `pt-BR` |

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de NER personnalisée](overview.md)
