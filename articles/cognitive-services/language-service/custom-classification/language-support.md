---
title: Prise en charge linguistique dans la classification de texte personnalisée
titleSuffix: Azure Cognitive Services
description: En savoir plus sur les langues prises en charge par l’extraction d’entité personnalisée.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 13e031c502538fa2126e6d4ab4e9e4eb49616142
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131509078"
---
# <a name="language-support"></a>Support multilingue

Utilisez cet article pour connaître les langues prises en charge par la classification de texte personnalisée.

## <a name="multiple-language-support"></a>Prise en charge de plusieurs langues

Avec la classification de texte personnalisée, vous pouvez effectuer l’apprentissage d’un modèle dans un langage et un test dans un autre langage. Cette fonctionnalité est très intéressante, car elle vous permet de gagner du temps : au lieu de créer des projets distincts pour chaque langage, vous pouvez gérer des jeux de données multilingues dans un seul projet.  Votre jeu de données n’a pas besoin d’être entièrement dans la même langue, mais vous devez spécifier cette option lors de la création du projet. Si vous remarquez que votre modèle fonctionne mal dans certains langages pendant le processus d’évaluation, envisagez d’ajouter des données dans cette langue à votre jeu d’apprentissage.

> [!NOTE]
> Pour activer la prise en charge multilingue, vous devez activer cette option lors de la [création de votre projet](how-to/create-project.md). Vous pouvez également l’activer ultérieurement à partir de la page des paramètres du projet.

## <a name="languages-supported-by-custom-text-classification"></a>Langues prises en charge par la classification de texte personnalisée

La classification personnalisée du texte prend en charge les fichiers `.txt` dans les langues suivantes :

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

* [Vue d’ensemble de la classification de texte personnalisée](overview.md)
* [Limites de données](service-limits.md)
