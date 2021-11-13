---
title: Qu’est-ce que la reconnaissance d’entité nommée (NER) personnalisée dans Azure Cognitive Services pour Language (préversion)
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser la reconnaissance d’entité nommée (NER) personnalisée.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: bac6068c02ea4f253176a65061d11604104c2bd5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439041"
---
# <a name="what-is-custom-named-entity-recognition-ner-preview"></a>Qu’est-ce que la reconnaissance d’entité nommée (NER) personnalisée (préversion) ?

La NER personnalisée est l’une des fonctionnalités proposées par [Azure Cognitive Services for Langage](../overview.md). Il s’agit d’un service d’API informatique qui applique l’intelligence du Machine Learning pour vous permettre de construire des modèles personnalisés pour les tâches de NER dans du texte.

La NER personnalisée est proposée dans le cadre des fonctionnalités personnalisées d’[Azure Cognitive Services for Language](../overview.md). Cette fonctionnalité permet à ses utilisateurs de créer des modèles IA personnalisés pour extraire des entités spécifiques à un domaine à partir d’un contenu textuel non structuré, par exemple des contrats ou des documents financiers. En créant un projet de NER personnalisée, les développeurs peuvent non seulement étiqueter les données d’un modèle mais également l’entraîner, l’évaluer et améliorer ses performances de manière itérative avant de le rendre disponible pour qu’il soit consommé. La qualité des données étiquetées impacte considérablement les performances du modèle. Pour simplifier la création et la personnalisation de votre modèle, le service offre un portail web personnalisé accessible dans [Langage Studio](https://aka.ms/languageStudio). Vous pouvez facilement commencer à utiliser le service en suivant les étapes de ce [guide de démarrage rapide](quickstart.md). 
 
Cette documentation contient les types d’articles suivants :

* Les [Démarrages rapides](quickstart.md) sont des instructions de prise en main qui vous guident dans la formulation de vos requêtes au service.
* Des [concepts](concepts/evaluation-metrics.md) fournissent des explications sur les fonctionnalités du service.
* Les [Guides pratiques](how-to/tag-data.md) contiennent des instructions sur l’utilisation du service de manière plus spécifique ou personnalisée.

## <a name="example-usage-scenarios"></a>Exemples de scénarios d’usage

### <a name="information-extraction"></a>Extraction d’informations

De nombreuses organisations financières et juridiques extraient et normalisent quotidiennement les données de milliers de textes complexes non structurés, par exemple des relevés bancaires, des contrats juridiques ou des formulaires bancaires. À la place d’un traitement manuel de ces formulaires, une NER personnalisée permet d’automatiser le processus et d’économiser des coûts, du temps et des efforts, etc.

### <a name="knowledge-mining-to-enhanceenrich-semantic-search"></a>Exploration des connaissances pour améliorer/enrichir la recherche sémantique

La recherche est fondamentale pour toute application exposant du contenu aux utilisateurs, avec des scénarios courants comme la recherche dans un catalogue ou dans des documents, la recherche sur un site de vente au détail ou l’exploration de connaissances pour la science des données.De nombreuses entreprises issues de divers secteurs cherchent à créer une expérience de recherche riche à partir d’un contenu privé hétérogène comprenant à la fois des documents structurés et non structurés. Dans le cadre de leur pipeline, les développeurs peuvent utiliser une NER personnalisée afin d’extraire du texte les entités pertinentes pour leur secteur d’activité. Ces entités peuvent être utilisées pour enrichir l’indexation du fichier afin d’offrir une expérience de recherche plus personnalisée. 

### <a name="audit-and-compliance"></a>Audit et conformité

Au lieu d’examiner manuellement des fichiers texte très longs pour effectuer des audits et appliquer des stratégies, les services informatiques des entreprises financières ou juridiques peuvent utiliser la NER personnalisée pour élaborer des solutions automatisées. Ces solutions permettent d’appliquer les stratégies de conformité et de configurer les règles d’entreprise nécessaires en fonction des pipelines d’extraction de connaissances qui traitent des contenus structurés et non structurés.

## <a name="application-development-lifecycle"></a>Cycle de vie du développement d’applications

L’utilisation d’une NER personnalisée implique généralement plusieurs étapes distinctes. 

:::image type="content" source="../custom-classification/media/development-lifecycle.png" alt-text="Cycle de vie du développement" lightbox="../custom-classification/media/development-lifecycle.png":::

1. **Définir le schéma** : familiarisez-vous avec vos données et identifiez les entités à extraire. Évitez les ambiguïtés.

2. **Étiqueter les données** : l’étiquetage des données est un facteur clé pour déterminer les performances du modèle. Étiquetez de manière précise, cohérente et complète.
    1. **Étiqueter avec précision** : étiquetez toujours chaque entité en utilisant le type approprié. N’incluez que ce que vous souhaitez extraire, et évitez les données inutiles dans votre étiquette.
    2. **Étiquetez de manière cohérente** : la même entité doit avoir la même étiquette dans tous les fichiers.
    3. **Étiqueter de manière complète** : étiquetez toutes les instances de l’entité dans tous vos fichiers.

3. **Entraîner le modèle** : votre modèle commence à apprendre à partir de vos données étiquetées.

4. **Visualiser les détails de l’évaluation du modèle** : une fois l’entraînement effectué, visualisez les détails de l’évaluation du modèle et ses performances.

5. **Améliorer le modèle** : après avoir passé en revue les détails de l’évaluation du modèle, vous pouvez aller plus loin pour découvrir comment améliorer le modèle.

6. **Déployer le modèle** : le déploiement d’un modèle consiste à rendre celui-ci disponible.

7. **Extraire les entités** : utilisez vos modèles personnalisés pour les tâches d’extraction d’entités.

## <a name="next-steps"></a>Étapes suivantes

* Utilisez le [guide de démarrage rapide](quickstart.md) pour commencer à utiliser la classification de texte personnalisée.  

* Pendant le cycle de vie du développement de l’application, consultez le [glossaire](glossary.md) pour en savoir plus sur les termes utilisés dans la documentation de cette fonctionnalité. 

* N’oubliez pas de consulter les [limites du service](service-limits.md) pour plus d’informations, comme la [disponibilité régionale](service-limits.md#regional-availability).
