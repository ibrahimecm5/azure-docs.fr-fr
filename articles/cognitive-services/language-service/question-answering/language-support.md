---
title: Prise en charge linguistique - Réponses aux questions personnalisées
titleSuffix: Azure Cognitive Services
description: Liste de cultures, langues naturelles prises en charge par les réponses aux questions personnalisées pour votre base de connaissances. Ne mélangez pas différentes langues dans une même base de connaissances.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: reference
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: dbef13bdb39085c650a1fc5cedceb6c143eea4ad
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450112"
---
# <a name="language-support-for-custom-question-answering-and-knowledge-bases"></a>Prise en charge linguistique pour les réponses aux questions personnalisées et les bases de connaissances

Cet article décrit les options de prise en charge linguistique pour les ressources et les bases de connaissances compatibles avec les réponses aux questions personnalisées.

Dans les réponses aux questions personnalisées, vous avez la possibilité de sélectionner la langue à chaque fois que vous ajoutez un nouveau projet à une ressource qui autorise la prise en charge de plusieurs langues, ou vous pouvez sélectionner une langue qui s’appliquera à tous les projets futurs pour une ressource.

## <a name="supporting-multiple-languages-in-one-custom-question-answering-enabled-resource"></a>Prise en charge de plusieurs langues dans une seule ressource de réponses aux questions personnalisées

> [!div class="mx-imgBorder"]
> ![Sélection de base de connaissances multilingue](./media/language-support/choose-language.png)

* Lorsque vous créez le premier projet de votre service, vous avez la possibilité de choisir la langue à chaque fois que vous créez un projet. Sélectionnez cette option pour créer des bases de connaissances appartenant à des langues différentes au sein d’un même service.
* L’option du paramètre de langue n’est pas modifiable pour le service, une fois la première base de connaissances créée.
* Si vous activez plusieurs langues pour la base de connaissances, au lieu d’avoir un seul index de test pour le service, vous aurez un index de test par base de connaissances.

## <a name="supporting-multiple-languages-in-one-knowledge-base"></a>Prise en charge de plusieurs langues dans une seule base de connaissances

Pour prendre en charge un système de base de connaissances comprenant plusieurs langues, vous pouvez :

* Utiliser le [service Translator](../../translator/translator-info-overview.md) pour traduire une question dans une langue individuelle avant de l’envoyer à votre base de connaissances. Cela vous permet de vous concentrer sur la qualité d’une seule langue et la qualité des autres questions et réponses.
* Créez une ressource de langue prenant en charge les réponses aux questions personnalisées, ainsi qu’une base de connaissances à l’intérieur de cette ressource, pour chaque langue. Cela vous permet de gérer d’autres questions et du texte de réponse plus nuancé pour chaque langue. Vous bénéficiez ainsi d’une flexibilité sensiblement accrue, mais devez exposer des frais de maintenance bien plus élevés lorsque les questions ou réponses changent dans toutes les langues.

## <a name="single-language-per-resource"></a>Une seule langue par ressource

Si vous **sélectionnez l’option permettant de définir la langue utilisée par tous les projets associés à la ressource**, tenez compte des points suivants : 
* Une ressource de langue, et tous ses projets/bases de connaissances, ne prend en charge qu’une seule langue.
* La langue est définie explicitement lors de la création du premier projet du service.
* La langue ne peut pas être modifiée pour les autres projets associés à la ressource.
* Les services Recherche cognitive (ranker n°1) et Réponses aux questions personnalisées (ranker n°2) utilisent la langue pour générer la meilleure réponse à une requête.

## <a name="languages-supported"></a>Langues prises en charge

La liste suivante contient les langues prises en charge pour une ressource de réponse aux questions.

| Langage |
|--|
| Arabe |
| Arménien |
| Bangla |
| Basque |
| Bulgare |
| Catalan |
| Chinois simplifié |
| Chinois traditionnel |
| Croate |
| Tchèque |
| Danois |
| Néerlandais |
| Anglais |
| Estonien |
| Finnois |
| Français |
| Galicien |
| Allemand |
| Grec |
| Goudjrati |
| Hébreu |
| Hindi |
| Hongrois |
| Islandais |
| Indonésien |
| Irlandais |
| Italien |
| Japonais |
| Kannada |
| Coréen |
| Letton |
| Lituanien |
| Malayalam |
| Malais |
| Norvégien |
| Polonais |
| Portugais |
| Pendjabi |
| Roumain |
| Russe |
| Serbe (cyrillique) |
| Serbe (latin) |
| Slovaque |
| Slovène |
| Espagnol |
| Suédois |
| Tamoul |
| Télougou |
| Thaï |
| Turc |
| Ukrainien |
| Ourdou |
| Vietnamien |

## <a name="query-matching-and-relevance"></a>Correspondance et pertinence des requêtes
Les réponses aux questions personnalisées dépendent des [analyseurs de langue de Recherche cognitive Azure](/rest/api/searchservice/language-support) pour fournir des résultats.

Même si les fonctionnalités de Recherche cognitive Azure sont à égalité pour les langues prises en charge, la réponses aux questions possède un outil de classement supplémentaire qui se trouve au-dessus des résultats de recherche Azure. Dans ce modèle d’outil de classement, nous utilisons une sémantique spéciale et des fonctionnalités basées sur des mots dans les langues suivantes.

|Langues avec outil de classement supplémentaire|
|--|
|Chinois|
|Tchèque|
|Néerlandais|
|Anglais|
|Français|
|Allemand|
|Hongrois|
|Italien|
|Japonais|
|Coréen|
|Polonais|
|Portugais|
|Espagnol|
|Suédois|

Ce classement supplémentaire est un travail interne du ranker des réponses aux questions personnalisées.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Sélection de la langue](../index.yml)
