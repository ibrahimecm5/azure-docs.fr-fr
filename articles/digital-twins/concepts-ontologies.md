---
title: Qu’est-ce qu’une ontologie ?
titleSuffix: Azure Digital Twins
description: Découvrez-en plus sur les ontologies de jumeaux numériques, la façon dont elles sont utilisées dans Azure Digital Twins et la façon dont ces ontologies DTDL peuvent servir à des fins de modélisation dans le contexte de certaines industries.
author: baanders
ms.author: baanders
ms.date: 10/21/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 56ce0815bdcb59e64f6a61cce4d4fb6172abea20
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501625"
---
# <a name="what-is-an-ontology"></a>Qu’est-ce qu’une ontologie ? 

Cet article décrit le concept des ontologies et la façon dont elles peuvent être utilisées dans le contexte d’Azure Digital Twins.

Le vocabulaire d’une solution Azure Digital Twins est défini à l’aide de [modèles](concepts-models.md), lesquels décrivent les types d’entité présents dans votre environnement.

Parfois, quand votre solution est liée à un secteur particulier, il est plus facile et plus efficace de démarrer avec un ensemble de modèles qui existent déjà pour ce secteur, au lieu de créer votre propre ensemble de modèles à partir de zéro. Ces ensembles de modèles préexistants sont appelés **ontologies**.

En général, une ontologie est un ensemble de modèles pour un domaine donné, par exemple une structure de bâtiment, un système IoT, une ville intelligente, un réseau électrique, un contenu web, etc. Des ontologies sont souvent utilisées en tant que schémas pour des graphes de jumeaux, car elles permettent ce qui suit :
* Harmonisation des composants logiciels, de la documentation, des bibliothèques de requêtes, etc.
* Réduction des investissements dans la modélisation conceptuelle et le développement du système
* Simplification de l’interopérabilité des données au niveau sémantique
* Réutilisation des bonnes pratiques, au lieu de partir de zéro ou de « réinventer la roue »

Cet article explique pourquoi utiliser des ontologies pour vos modèles Azure Digital Twins et comment procéder. Il explique également les ontologies et les outils actuellement disponibles pour ceux-ci.

## <a name="using-ontologies-for-azure-digital-twins"></a>Utilisation d’ontologies pour Azure Digital Twins

Les ontologies constituent un excellent point de départ pour les solutions à base de jumeaux numériques. Elles englobent un ensemble de modèles spécifiques à un domaine et de relations entre entités, qui permettent de concevoir, de créer et d’analyser un graphe de jumeaux numériques. Les ontologies permettent aux développeurs de commencer à créer une solution à base de jumeaux numériques à partir d’un point de départ éprouvé et de se concentrer sur la résolution des problèmes métier. Les ontologies fournies par Microsoft sont également conçues pour être facilement extensibles, ce qui vous permet de les personnaliser pour votre solution. 

Par ailleurs, l’utilisation de ces ontologies dans vos solutions entraîne leur intégration de manière plus transparente entre les différents partenaires et fournisseurs, car les ontologies fournissent un vocabulaire commun à toutes les solutions.

Dans la mesure où les modèles dans Azure Digital Twins sont représentés en [langage DTDL (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md), les ontologies à utiliser avec Azure Digital Twins sont également écrites en DTDL. 

## <a name="strategies-for-integrating-ontologies"></a>Stratégies d’intégration des ontologies

Il existe trois stratégies possibles d’intégration des ontologies conformes aux standards d’un secteur en langage DTDL. Vous pouvez choisir celle qui vous convient le mieux en fonction de vos besoins :

| Stratégie | Description | Ressources |
| --- | --- | --- |
| **Adopter** | Vous pouvez démarrer votre solution avec une ontologie DTDL open source qui a été conçue sur des normes du secteur d’activité largement adoptées. Vous pouvez utiliser ces ensembles de modèles prêts à l’emploi, ou les étendre avec vos propres ajouts dans le cadre d’une solution personnalisée. | [Adoption&nbsp;d’ontologies&nbsp;conformes aux standards d’un secteur](concepts-ontologies-adopt.md)<br><br>[Extension&nbsp;d’ontologies](concepts-ontologies-extend.md) |
| **Convert** | Si vous avez déjà des modèles existants représentés dans un autre format standard, vous pouvez les convertir en DTDL pour les utiliser avec Azure Digital Twins. | [Conversion&nbsp;d’ontologies](concepts-ontologies-convert.md)<br><br>[Extension&nbsp;d’ontologies](concepts-ontologies-extend.md) |
| **Auteur** | Vous pouvez toujours développer vos propres modèles DTDL personnalisés à partir de zéro, en vous inspirant des standards applicables au secteur. | [Modèles DTDL](concepts-models.md) |

### <a name="using-ontology-strategies-in-a-model-development-path"></a>Utilisation de stratégies d’ontologie pour le développement de modèles

Quelle que soit la stratégie choisie pour l’intégration d’une ontologie avec Azure Digital Twins, vous pouvez suivre la procédure complète ci-dessous afin de créer et charger votre ontologie sous forme de modèles DTDL.

1. Commencez par examiner et comprendre la [modélisation DTDL dans Azure Digital Twins](concepts-models.md).
1. Continuez avec la stratégie d’intégration d’ontologie que vous avez choisie ci-dessus : [adoptez](concepts-ontologies-adopt.md), [convertissez](concepts-ontologies-convert.md) ou [créez](concepts-models.md) vos modèles en fonction de votre ontologie.
    1. Si nécessaire, [étendez](concepts-ontologies-extend.md) votre ontologie pour la personnaliser selon vos besoins.
1. [Validez](how-to-parse-models.md) vos modèles pour vérifier qu’ils fonctionnent avec des documents DTDL.
1. Une fois que vos modèles sont prêts, chargez-les vers Azure Digital Twins en utilisant les [API](how-to-manage-model.md#upload-models) ou un exemple d’application tel que le [chargeur de modèles Azure Digital Twins](https://github.com/Azure/opendigitaltwins-tools/tree/master/ADTTools#uploadmodels).

La lecture de cette série d’articles vous permet de découvrir progressivement comment utiliser vos modèles dans votre instance d’Azure Digital Twins. 

>[!TIP]
> Vous pouvez visualiser les modèles dans votre ontologie à l’aide d’[Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md) ou du [visualiseur de modèles Azure Digital Twins](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer).

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus en détail les stratégies d’adoption, de conversion et de création d’ontologies :
* [Adoption des ontologies standard](concepts-ontologies-adopt.md)
* [Conversion d’ontologies](concepts-ontologies-convert.md)
* [Gérer des modèles DTDL](how-to-manage-model.md)

Ou découvrez comment les modèles permettent de créer des jumeaux numériques : [Jumeaux numériques et graphe de jumeaux](concepts-twins-graph.md).