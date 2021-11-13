---
title: Qu’est-ce que Réponse aux questions ?
description: Réponse aux questions est un service cloud de traitement en langage naturel (NLP, Natural Language Processing) qui permet de créer facilement une couche conversationnelle naturelle sur vos données. Il peut être utilisé pour trouver la réponse la plus appropriée à une entrée donnée en langage naturel à partir de votre base de connaissances personnalisée (base d’informations).
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
keywords: qna maker, chatbot avec peu de code, invites multitours
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 8249fa4f276b7474740e2c8d882abc4e69c9487d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476487"
---
# <a name="what-is-question-answering"></a>Qu’est-ce que Réponse aux questions ?

Réponse aux questions fournit un traitement en langage naturel (NLP, Natural Language Processing) qui vous permet de créer une couche conversationnelle naturelle sur vos données. Il est utilisé pour trouver la réponse la plus appropriée à une entrée à partir de votre base de connaissances personnalisée (base d’informations).

Réponse aux questions est couramment utilisé pour créer des applications clientes conversationnelles, notamment des applications de réseaux sociaux, des chatbots et des applications de bureau à reconnaissance vocale. Plusieurs nouvelles fonctionnalités ont été ajoutées, notamment une pertinence accrue grâce à un ranker de Deep Learning, des réponses précises et une prise en charge régionale de bout en bout.

Cette documentation contient les types d’articles suivants :

* Les [guides de démarrage rapide](./quickstart/sdk.md) sont des instructions pas à pas qui vous permettent d’effectuer des appels au service et d’obtenir des résultats en peu de temps.
* Les [guides patiques](./how-to/manage-knowledge-base.md) contiennent des instructions sur l’utilisation du service de manière plus spécifique ou personnalisée.
* Les [articles conceptuels](./concepts/precise-answering.md) fournissent des explications approfondies sur les fonctions et fonctionnalités du service.
* Les [**Tutoriels**](./tutorials/bot-service.md) sont des guides plus longs qui montrent comment utiliser le service en tant que composant dans des solutions métier élargies. 

## <a name="when-to-use-question-answering"></a>Quand utiliser Réponse aux questions ?

* **Quand vous avez des informations statiques** : utilisez Réponse aux questions quand vous avez des informations statiques dans votre base de connaissances de réponses. Cette base de connaissances est personnalisée en fonction de vos besoins, que vous avez créés avec des documents tels que des PDF et des URL.
* **Quand vous souhaitez fournir la même réponse à une requête, une question ou une commande** : quand différents utilisateurs soumettent la même question, la même réponse est retournée.
* **Quand vous souhaitez filtrer des informations statiques en fonction de méta-informations** : ajoutez des balises de [métadonnées](./tutorials/multiple-domains.md) pour fournir des options de filtrage supplémentaires relatives aux utilisateurs et informations de votre application cliente. Les [échanges](./how-to/chit-chat.md), types ou formats de contenu, objets de contenu et actualisations de contenu représentent des informations de métadonnées courantes. <!--TODO: Fix Link-->
* **Quand vous souhaitez gérer une conversation de bot incluant des informations statiques** : votre base de connaissances répond à une commande ou au texte conversationnel d’un utilisateur. Si la réponse fait partie d’un flux de conversation prédéterminé, représenté dans votre base de connaissances avec un [contexte multitour](./tutorials/guided-conversations.md), le bot peut facilement fournir ce flux.

## <a name="what-is-a-knowledge-base"></a>Qu’est-ce qu’une base de connaissances ?

Réponse aux questions [importe votre contenu](./how-to/manage-knowledge-base.md) dans une base de connaissances comprenant des paires question/réponse. Le processus d’importation extrait des informations sur la relation entre les différentes parties de votre contenu structuré et semi-structuré pour définir des relations entre les paires question/réponse. Vous pouvez modifier ces paires question/réponse ou en ajouter de nouvelles.

Le contenu de la paire question/réponse comprend les éléments suivants :
* Toutes les autres formes de la question
* Les étiquettes de métadonnées utilisées pour filtrer les choix de réponse lors de la recherche
* Des invites de suivi pour poursuivre le perfectionnement de la recherche

Une fois que vous avez publié votre base de connaissances, une application cliente envoie la question d’un utilisateur au point de terminaison. Votre service Réponse aux questions traite la question et y répond avec la meilleure réponse.

## <a name="create-a-chat-bot-programmatically"></a>Créer un chatbot programmatiquement

Une fois qu’une base de connaissances Réponse aux questions est publiée, une application cliente envoie une question au point de terminaison de votre base de connaissances et reçoit les résultats sous forme de réponse JSON. Un chatbot est un exemple d’application cliente courante pour Réponse aux questions.

![Poser une question à un bot et obtenir une réponse à partir du contenu de la base de connaissances](../../qnamaker/media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Étape|Action|
|:--|:--|
|1|L’application cliente envoie la _question_ de l’utilisateur (texte dans ses propres mots) « How do I programmatically update my Knowledge Base? » au point de terminaison de votre base de connaissances.|
|2|Réponse aux questions utilise la base de connaissances entraînée pour fournir la réponse correcte et les invites de suivi qui peuvent être utilisées pour affiner la recherche de la meilleure réponse. Réponse aux questions renvoie une réponse au format JSON.|
|3|L’application cliente utilise la réponse JSON pour prendre des décisions concernant la manière de poursuivre la conversation. Ces décisions peuvent inclure l’affichage de la réponse principale et la présentation de choix supplémentaires pour affiner la recherche de la meilleure réponse. |
|||

## <a name="build-low-code-chat-bots"></a>Créer des chatbots avec peu de code

Le portail Language Studio offre une expérience de création de projet/base de connaissances complète. Vous pouvez importer des documents sous leur forme actuelle dans votre base de connaissances. Ces documents (p.ex., FAQ, manuel de produit, feuille de calcul ou page web) sont convertis en paires question/réponse. Chaque paire est analysée pour identifier des invites de suivi et est connectée à d’autres paires. Le format _Markdown_ final prend en charge les présentations riches, notamment les images et les liens.

Une fois votre base de connaissances modifiée, publiez-la sur un [bot Azure Web App](https://azure.microsoft.com/services/bot-service/) de travail sans écrire le moindre code. Testez votre bot dans le [portail Azure](https://portal.azure.com) ou téléchargez-le et poursuivez le développement.

## <a name="high-quality-responses-with-layered-ranking"></a>Réponses de haute qualité avec classement par couches

Le système de réponses aux questions utilise une approche de classement par couches. Les données sont stockées dans la Recherche Azure, qui sert également de première couche de classement. Les meilleurs résultats de la Recherche Azure sont ensuite transmis par le biais du modèle de reclassement NLP de Réponse aux questions pour produire les résultats finaux et le score de confiance.

## <a name="multi-turn-conversations"></a>Conversations multitours

Réponse aux questions propose des invites multitours et un apprentissage actif pour vous aider à améliorer vos paires question/réponse de base.

Les **invites multitours** vous donnent la possibilité d’associer les paires de questions et réponses. Cette association permet à l’application cliente de fournir une réponse principale et fournit davantage de questions pour affiner la recherche d’une réponse finale.

Dès lors que la base de connaissances reçoit des questions des utilisateurs au point de terminaison publié, Réponse aux questions applique l’**apprentissage actif** à ces questions réelles pour suggérer des modifications à apporter à la base de connaissances afin d’en améliorer la qualité.

## <a name="development-lifecycle"></a>Cycle de vie de développement

Réponse aux questions offre des fonctionnalités de création, d’entraînement et de publication ainsi que des autorisations de collaboration, s’intégrant à l’ensemble du cycle de vie de développement.

> [!div class="mx-imgBorder"]
> ![Image conceptuelle du cycle de développement](../../qnamaker/media/qnamaker-overview-learnabout/development-cycle.png)

## <a name="complete-a-quickstart"></a>Suivre un guide de démarrage rapide

Nous proposons des guides de démarrage rapide pour la plupart des langages de programmation. Chaque guide est conçu pour vous montrer des modèles de conception de base et vous permettre d’exécuter du code en moins de 10 minutes.

* [Bien démarrer avec la bibliothèque de client Réponse aux questions](./quickstart/sdk.md)

## <a name="next-steps"></a>Étapes suivantes
Réponse aux questions fournit tout ce dont vous avez besoin pour créer, gérer et déployer une base de connaissances personnalisée.

> [!div class="nextstepaction"]
> [Passer en revue les derniers changements](../whats-new.md)
