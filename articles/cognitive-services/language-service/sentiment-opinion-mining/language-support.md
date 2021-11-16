---
title: Prise en charge des langages d’exploration de données Analyse des sentiments et opinion
titleSuffix: Azure Cognitive Services
description: Cet article explique les langages naturels pris en charge par les fonctionnalités d’exploration d’Analyse des sentiments et d’opinion d’Azure Cognitive Service pour le langage.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/08/2021
ms.author: aahi
ms.custom: language-service-sentiment-opinion-mining, ignite-fall-2021
ms.openlocfilehash: a0cbc5bab15e80bb18ca3d4a13efa2d05fe5aeff
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027144"
---
# <a name="sentiment-analysis-and-opinion-mining-language-support"></a>Prise en charge des langages d’exploration de données Analyse des sentiments et opinion 

Utilisez cet article pour découvrir les langages naturels pris en charge par Analyse des sentiments et Exploration des opinions.

> [!NOTE]
> Les langages sont ajoutés à mesure que de nouvelles versions de modèle sont publiées. La version actuelle du modèle généralement disponible pour l’Analyse des sentiments est `2020-04-01`. Pour utiliser la dernière version du modèle en préversion (`2021-10-01-preview`), spécifiez-la dans votre appel d’API. Pour plus d’informations, consultez [Comment appeler l’API](how-to/call-api.md#specify-the-sentiment-analysis-model).

## <a name="sentiment-analysis-language-support"></a>Prise en charge des langues Analyse des sentiments

| Langage              | Code langue | À partir de la version de modèle : |              Notes |
|:----------------------|:-------------:|:--------------------------:|-------------------:|
| Chinois simplifié    |   `zh-hans`         |         2019-10-01         | `zh` également accepté |
| Chinois traditionnel   |   `zh-hant`         |         2019-10-01         |                    |
| Néerlandais                 |     `nl`            |         2019-10-01        |                    |
| Anglais               |     `en`            |         2019-10-01         |                    |
| Français                |     `fr`            |         2019-10-01         |                    |
| Allemand                |     `de`            |         2019-10-01         |                    |
| Hindi                 |    `hi`             |         2020-04-01         |                    |
| Italien               |     `it`            |         2019-10-01         |                    |
| Japonais              |     `ja`            |         2019-10-01         |                    |
| Coréen                |     `ko`            |         2019-10-01         |                    |
| Norvégien (Bokmål)   |     `no`            |         2020-04-01         |                    |
| Portugais (Brésil)   |    `pt-BR`          |         2020-04-01         |                    |
| Portugais (Portugal) |    `pt-PT`          |         2019-10-01         | `pt` également accepté |
| Espagnol               |     `es`            |         2019-10-01         |                    |
| Turc               |     `tr`             |         2020-04-01        |                    |

### <a name="opinion-mining-language-support"></a>Prise en charge du langage d’Exploration des opinions

| Langage              | Code langue | À partir de la version de modèle : |              Notes |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Anglais               |     `en`      |  2020-04-01              |                    |
| Français                |     `fr`      |         2021-10-01-preview         |                    |
| Allemand                |     `de`      |         2021-10-01-preview         |                    |
| Italien               |     `it`      |         2021-10-01-preview         |                    |
| Portugais (Brésil)   |    `pt-BR`    |         2021-10-01-preview         |                    |
| Portugais (Portugal) |    `pt-PT`    |         2021-10-01-preview         | `pt` également accepté |
| Espagnol               |     `es`      |         2021-10-01-preview         |                    |

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de l’analyse des sentiments et de l’exploration des opinions](overview.md)
