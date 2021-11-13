---
title: Concepts d’enrichissement de l’IA
titleSuffix: Azure Cognitive Search
description: L’extraction de contenu, le traitement du langage naturel et le traitement des images sont utilisés pour créer du contenu pouvant faire l’objet de recherches dans les index Recherche cognitive Azure à l’aide de compétences cognitives et d’algorithmes d’IA personnalisés prédéfinis.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.custom: references_regions
ms.openlocfilehash: 8010de7d6aa0af0d096a0c7ed23740f23734cda2
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131554390"
---
# <a name="ai-enrichment-in-azure-cognitive-search"></a>Enrichissement de l’IA dans Recherche cognitive Azure

Dans Recherche cognitive Azure, l’enrichissement par IA fait référence aux compétences cognitives intégrées et aux compétences personnalisées qui ajoutent des analyses, des transformations et la génération de contenu pendant l’indexation. Les enrichissements créent de nouvelles informations là où aucune n’existait précédemment : extraction d’informations à partir d’images, détection de sentiments, d’expressions clés et d’entités à partir de texte, pour n’en nommer que quelques-uns. Les enrichissements ajoutent également une structure à du texte non différencié. Tous ces processus ont pour effet de mettre à la disposition des scénarios de recherche en texte intégral des contenus qui ne pouvaient pas faire l’objet d’une recherche auparavant. Dans de nombreux cas, les documents enrichis sont utiles pour des scénarios autres que la recherche, pour l’exploration de connaissances par exemple.

L’enrichissement est défini par un [**ensemble de compétences**](cognitive-search-working-with-skillsets.md) qui est attaché à un [**indexeur**](search-indexer-overview.md). L’indexeur extrait et configure le contenu, tandis que l’ensemble de compétences identifie, analyse et crée de nouvelles informations et structures à partir d’images, de blobs et d’autres sources de données non structurées. La sortie d’un pipeline d’enrichissement est soit un [**index de recherche**](search-what-is-an-index.md), soit une [**base de connaissances**](knowledge-store-concept-intro.md).

![Diagramme de pipeline d’enrichissement](./media/cognitive-search-intro/cogsearch-architecture.png "vue d’ensemble du pipeline d’enrichissement")

Un ensemble de compétences peut contenir des compétences intégrées de Recherche cognitive ou incorporer un traitement externe que vous fournissez dans une [*compétence personnalisée*](cognitive-search-create-custom-skill-example.md). Un module d’entité ou un classifieur de documents ciblant un domaine spécifique comme la finance, les publications scientifiques ou la médecine sont des exemples de compétence personnalisée.

Les compétences intégrées se répartissent en fonction des catégories suivantes :

+ Les compétences de **traitement du langage naturel** incluent la [reconnaissance d’entité](cognitive-search-skill-entity-recognition-v3.md), la [détection de la langue](cognitive-search-skill-language-detection.md), l’[extraction de phrases clés](cognitive-search-skill-keyphrases.md), la manipulation de texte, la [détection de sentiments (y compris l’exploration des opinions)](cognitive-search-skill-sentiment-v3.md) et la [détection d’informations d’identification personnelle](cognitive-search-skill-pii-detection.md). Grâce à ces compétences, un texte non structuré est mappé sous la forme de champs pouvant être interrogés et filtrés dans un index.

+ Les compétences de **traitement d’image** incluent la [reconnaissance optique de caractères (OCR)](cognitive-search-skill-ocr.md) et l’identification des [caractéristiques visuelles](cognitive-search-skill-image-analysis.md), comme la détection des visages, l’interprétation des images, la reconnaissance des images (monuments et personnes célèbres) ou des attributs tels que l’orientation des images. Ces compétences créent des représentations textuelles du contenu des images, ce qui rend les recherches possibles grâce aux capacités d’interrogation de Recherche cognitive Azure.

Les compétences intégrées de la Recherche cognitive Azure sont basées sur les modèles Machine Learning préentraînés des API Cognitive Services : [Vision par ordinateur](../cognitive-services/computer-vision/index.yml) et [Analyse de texte](../cognitive-services/text-analytics/overview.md). Vous pouvez attacher une ressource Cognitive Services si vous souhaitez tirer parti de ces ressources lors du traitement du contenu.

Le traitement en langage naturel et le traitement des images sont appliqués durant la phase d’ingestion des données. Les résultats sont intégrés à la composition d’un document au sein d’un index pouvant faire l’objet de recherches dans la Recherche cognitive Azure. Les données sont fournies en tant que jeu de données Azure, puis transmises via un pipeline d’indexation à l’aide des [compétences intégrées](cognitive-search-predefined-skills.md) dont vous avez besoin.  

## <a name="feature-availability"></a>Disponibilité des fonctionnalités

L’enrichissement par IA est disponible dans les régions où Azure Cognitive Services est également disponible. Vous pouvez vérifier la disponibilité actuelle de l’enrichissement par IA sur la page [Produits Azure disponibles par région](https://azure.microsoft.com/global-infrastructure/services/?products=search). L’enrichissement par IA est disponible dans toutes les régions prises en charge, à l’exception des suivantes :

+ Sud-Australie Est
+ Chine Nord 2
+ Norvège Est
+ Allemagne Centre-Ouest

Si votre service de recherche se trouve dans l’une de ces régions, vous ne serez pas en mesure de créer et d’utiliser des ensembles de compétences, mais toutes les autres fonctionnalités du service de recherche sont disponibles et entièrement prises en charge.

## <a name="when-to-use-ai-enrichment"></a>Quand utiliser l’enrichissement par IA

Vous devez envisager d’utiliser l’enrichissement si votre contenu brut est du texte non structuré, du contenu d’image ou du contenu qui nécessite la détection de la langue et une traduction. L’application de l’intelligence artificielle via les compétences cognitives intégrées peut déverrouiller ce contenu, en renforçant sa valeur et son utilité dans vos applications de science des données et de recherche. 

En outre, vous pouvez envisager d’ajouter une compétence personnalisée si vous avez du code open source, tiers ou interne que vous souhaitez intégrer dans le pipeline. Les modèles de classification qui identifient les caractéristiques importantes de différents types de documents appartiennent à cette catégorie, mais tout package qui ajoute de la valeur à votre contenu peut être utilisé.

### <a name="use-cases-for-built-in-skills"></a>Cas d’usage des compétences intégrées

Un [ensemble de compétences](cognitive-search-defining-skillset.md) assemblé à l’aide de compétences intégrées convient parfaitement aux scénarios d’application suivants :

+ La [reconnaissance optique de caractères (OCR)](cognitive-search-skill-ocr.md) qui reconnaît la police et le texte manuscrit dans les documents numérisés (JPEG) est peut-être la compétence la plus couramment utilisée. L’ajout de la compétence OCR permet d’identifier, d’extraire et d’ingérer du texte à partir de fichiers JPEG.

+ La [traduction](cognitive-search-skill-text-translation.md) de contenu multilingue est une autre compétence couramment utilisée. La détection de la langue est intégrée à Traduction de texte, mais vous pouvez également exécuter [Détection de langue](cognitive-search-skill-language-detection.md) indépendamment si vous souhaitez simplement connaître les codes de langue du contenu de votre corpus.

+ PDF avec image et texte combinés. Le texte des fichiers PDF peut être extrait durant l’indexation sans recourir aux étapes d’enrichissement. Toutefois, l’ajout du traitement des images et du traitement en langage naturel permet souvent de produire un résultat supérieur à celui fourni par une indexation standard.

+ Documents non structurés ou semi-structurés contenant du contenu qui a une signification ou un contexte intrinsèquement masqué dans le document plus volumineux. 

  En particulier, les blobs contiennent souvent un grand corps de contenu qui est compressé dans un « champ » unique. En associant des compétences en traitement des images et en langage naturel à un indexeur, vous pouvez créer de nouvelles informations qui sont existantes dans le contenu brut, mais qui ne sont pas affichées en tant que champs distincts. Voici quelques compétences cognitives intégrées prêtes à l’emploi qui peuvent vous aider : [Extraction de phrases clés](cognitive-search-skill-keyphrases.md) et [Reconnaissance d’entité](cognitive-search-skill-entity-recognition-v3.md) (personnes, organisations et lieux, pour n’en citer que quelques-unes).

  De plus, les compétences prédéfinies peuvent également être utilisées pour restructurer du contenu via des opérations de découpage, de fusion et de mise en forme du texte.

### <a name="use-cases-for-custom-skills"></a>Cas d’usage des compétences personnalisées

Les compétences personnalisées peuvent prendre en charge des scénarios plus complexes, tels que la reconnaissance de formulaires ou la détection d’entité personnalisée à l’aide d’un modèle que vous fournissez et encapsulez dans l’[interface web des compétences personnalisées](cognitive-search-custom-skill-interface.md). Plusieurs exemples de compétences personnalisées incluent [Form Recognizer](../applied-ai-services/form-recognizer/overview.md), l’intégration de l’[API Recherche d’entités Bing](./cognitive-search-create-custom-skill-example.md) et la [reconnaissance d’entité personnalisée](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).

## <a name="enrichment-steps"></a>Étapes d’enrichissement <a name="enrichment-steps"></a>

Un pipeline d’enrichissement se compose d’[*indexeurs*](search-indexer-overview.md) dotés d’[*ensembles de compétences*](cognitive-search-working-with-skillsets.md). Un ensemble de compétences définit les étapes d’enrichissement, et l’indexeur pilote l’ensemble de compétences. Lorsque vous configurez un indexeur, vous pouvez inclure des propriétés telles que les mappages de champs de sortie qui envoient le contenu enrichi à un [index de recherche](search-what-is-an-index.md) ou des projections qui définissent des structures de données dans une [base de connaissances](knowledge-store-concept-intro.md).

Après l’indexation, vous pouvez accéder au contenu via des requêtes de recherche ainsi qu’à l’aide de tous les [types de requête pris en charge par Recherche cognitive Azure](search-query-overview.md).

### <a name="step-1-connection-and-document-cracking-phase"></a>Étape 1 : Phase de connexion et de décodage du document

Les indexeurs se connectent à des sources externes à l’aide des informations fournies dans une source de données d’indexation. Lorsque l’indexeur se connecte à la ressource, il [« craque les documents »](search-indexer-overview.md#document-cracking) pour en extraire le texte et les images. Le contenu des images peut être acheminé vers des compétences qui effectuent le traitement des images, tandis que le contenu texte est mis en file d’attente pour le traitement du texte. 

![Phase de craquage de document](./media/cognitive-search-intro/document-cracking-phase-blowup.png "craquage de document")

Cette étape rassemble tout le contenu initial ou brut qui sera soumis à un enrichissement par IA. Pour chaque document, une arborescence d’enrichissement est créée. Initialement, l’arborescence est simplement une représentation de nœud racine, mais elle s’agrandit et gagne en structure lors de l’exécution du jeu de compétences.

### <a name="step-2-skillset-enrichment-phase"></a>Étape 2 : Phase d’enrichissement du jeu de compétences

Un ensemble de compétences définit les opérations atomiques qui sont effectuées sur chaque document. Par exemple, pour le texte et les images extraits d’un PDF, un ensemble de compétences peut appliquer la reconnaissance d’entité, la détection de la langue ou l’extraction de phrases clés pour créer des champs (qui ne sont pas disponibles en natif dans la source) dans votre index. 

![Phase d’enrichissement](./media/cognitive-search-intro/enrichment-phase-blowup.png "phase d’enrichissement")

 Un ensemble de compétences peut être minimal ou très complexe, et détermine non seulement le type de traitement, mais également l’ordre des opérations. La plupart des ensembles de compétences contiennent environ trois à cinq compétences.

Un ensemble de compétences, plus les [mappages de champs de sortie](cognitive-search-output-field-mapping.md) définis dans un indexeur, spécifient entièrement le pipeline d’enrichissement. Pour plus d’informations sur l’extraction de tous ces éléments ensemble, consultez [How to create a skillset in an enrichment pipeline](cognitive-search-defining-skillset.md) (Créer un ensemble de compétences dans un pipeline d’enrichissement).

En interne, le pipeline génère une collection de documents enrichis. Vous pouvez décider quelles parties des documents enrichis doivent être mappées aux champs indexables dans votre index de recherche. Par exemple, si vous avez appliqué les compétences de reconnaissance d’entité et d’extraction de phrases clés, ces nouveaux champs vont faire partie du document enrichi et peuvent donc être mappés aux champs de votre index. Consultez [How to reference annotations in a cognitive search skillset](cognitive-search-concept-annotations-syntax.md) (Référencer des annotations dans un ensemble de compétences de recherche cognitive) pour en savoir plus sur les formations entrée/sortie.

### <a name="step-3-indexing"></a>Étape 3 : Indexation

L’indexation est le processus par lequel le contenu brut et enrichi est ingéré comme champs dans un index de recherche, et comme [projections](knowledge-store-projection-overview.md) si vous créez également une base de connaissances. Le même contenu enrichi peut apparaître dans les deux, en utilisant des mappages de champs implicites ou explicites pour envoyer le contenu aux champs appropriés.

Le contenu enrichi est généré pendant l’exécution de l’ensemble de compétences, et il est temporaire à moins que vous ne le sauvegardiez. Pour que le contenu enrichi apparaisse dans un index de recherche, l’indexeur doit disposer d’informations de mappage afin de pouvoir envoyer le contenu enrichi à un champ dans un index de recherche. Les [mappages de champs de sortie](cognitive-search-output-field-mapping.md) configurent ces associations.

## <a name="saving-enriched-output"></a>Enregistrement d’une sortie enrichie

Dans Recherche cognitive Azure, l’indexeur enregistre la sortie qu’il crée.

Un [**index avec recherche possible**](search-what-is-an-index.md) est l’une des sorties qui est toujours créée par un indexeur. La spécification d’un index est une exigence de l’indexeur, et lorsque vous attachez un ensemble de compétences, la sortie de l’ensemble de compétences, plus tous les champs mappés directement de la source, sont utilisés pour alimenter l’index. En règle générale, les sorties de compétences spécifiques, comme les expressions clés ou les scores de sentiment, sont ingérées dans l’index d’un champ créé à cet effet.

Une [**base de connaissances**](knowledge-store-concept-intro.md) est une sortie facultative, utilisée pour des applications en aval comme l’exploration des connaissances. Une base de connaissances est définie dans un ensemble de compétences. Sa définition détermine si vos documents enrichis sont projetés sous forme de tables ou d’objets (fichiers ou blobs). Les projections tabulaires sont bien adaptées à l’analyse interactive dans des outils tels que Power BI, tandis que les fichiers et les blobs sont généralement utilisés en science des données ou dans des processus similaires.

Enfin, un indexeur peut [**mettre en cache les documents enrichis**](cognitive-search-incremental-indexing-conceptual.md) dans Stockage Blob Azure pour une réutilisation potentielle dans des exécutions ultérieures d’ensembles de compétences. Le cache est réservé à un usage interne. Les enrichissements mis en cache sont consommables par le même ensemble de compétences que vous réexécutez à une date ultérieure. La mise en cache est utile si votre ensemble de compétences inclut l’analyse d’images ou la reconnaissance optique de caractères, et si vous voulez éviter de consacrer du temps et de l’argent au retraitement des fichiers d’image.

Les index et les bases de connaissances sont totalement indépendants les uns des autres. Bien que vous deviez joindre un index pour satisfaire aux exigences de l’indexeur, si votre seul objectif est une base de connaissances, vous pouvez ignorer l’index une fois qu’il est rempli. Évitez toutefois de le supprimer. Si vous souhaitez réexécuter l’indexeur et l’ensemble de compétences, vous aurez besoin de l’index pour que l’indexeur puisse fonctionner.

## <a name="using-enriched-content"></a>Utilisation du contenu enrichi

Une fois le traitement terminé, vous disposez d’un [index de recherche](search-what-is-an-index.md) constitué de documents enrichis, avec recherche possible du texte dans Recherche cognitive Azure. [**L’interrogation de l’index**](search-query-overview.md) est la façon dont les développeurs et les utilisateurs accèdent au contenu enrichi généré par le pipeline. L’index est semblable à tous ceux que vous pouvez créer pour Recherche cognitive Azure : vous pouvez compléter l’analyse de texte avec des analyseurs personnalisés, appeler des requêtes de recherche partielle, ajouter des filtres ou expérimenter des profils de scoring pour ajuster la pertinence de la recherche.

Vous pouvez également disposer d’une [base de connaissances](knowledge-store-concept-intro.md). La base de connaissances contient des données qui peuvent être utilisées dans des scénarios d’exploration des connaissances tels que l’analyse ou l’apprentissage automatique. Vous pouvez utiliser [Explorateur Stockage](knowledge-store-view-storage-explorer.md), [Power BI](knowledge-store-connect-power-bi.md) ou toute autre application qui se connecte à Stockage Azure.

## <a name="checklist-a-typical-workflow"></a>Liste de vérification : workflow classique

1. Au début d’un projet, il est utile de travailler avec un sous-ensemble de données. La conception d’indexeurs et d’ensembles de compétences est un processus itératif, et vous itérerez plus rapidement si vous travaillez avec un petit jeu de données représentatif.

1. Créez une [source de données](/rest/api/searchservice/create-data-source) qui spécifie une connexion à vos données.

1. Créez un [ensemble de compétences](/rest/api/searchservice/create-skillset) pour ajouter un enrichissement.

1. Créez un [schéma d’index](/rest/api/searchservice/create-index) qui définit un index de recherche.

1. Créez un [indexeur](/rest/api/searchservice/create-indexer) pour rassembler tous les composants ci-dessus. La création ou l’exécution d’un indexeur permet de récupérer les données, d’exécuter l’ensemble de compétences et de charger l’index.

1. Exécutez des requêtes pour évaluer les résultats et modifiez le code pour mettre à jour les ensembles de compétences, le schéma ou la configuration de l’indexeur.

Pour itérer sur les étapes ci-dessus, [réinitialisez l’indexeur](search-howto-reindex.md) avant de reconstruire le pipeline, ou supprimez et recréez les objets à chaque exécution (recommandé si vous utilisez le niveau gratuit). Vous devez également [activer la mise en cache des enrichissements](cognitive-search-incremental-indexing-conceptual.md) pour réutiliser les enrichissements existants dans la mesure du possible.

## <a name="next-steps"></a>Étapes suivantes

+ [Démarrage rapide : créer une traduction de texte et un ensemble de compétences d’entité](cognitive-search-quickstart-blob.md)
+ [Démarrage rapide : créer un ensemble de compétences d’image d’OCR](cognitive-search-quickstart-ocr.md)
+ [Tutoriel : En savoir plus sur les API REST d’enrichissement par IA](cognitive-search-tutorial-blob.md)
+ [Concepts des ensembles de compétences](cognitive-search-working-with-skillsets.md)
+ [Concepts de base de connaissances](knowledge-store-concept-intro.md)
+ [Créer un ensemble de compétences](cognitive-search-defining-skillset.md)
+ [Créer une base de connaissances](knowledge-store-create-rest.md)