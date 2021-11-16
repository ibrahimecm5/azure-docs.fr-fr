---
title: Planifier votre application - réponses aux questions
description: Découvrez comment planifier votre application de réponses aux questions. Comprenez comment les réponses aux questions fonctionnent et interagissent avec d’autres services Azure et certains concepts de la base de connaissances.
ms.service: cognitive-services
ms.subservice: language-service
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 0dbd8e670f652ff598c9b65c07687ab38a097050
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479007"
---
# <a name="plan-your-question-answering-app"></a>Planifier votre application de réponses aux questions

Pour planifier votre application de réponses aux questions, vous devez comprendre comment les réponses aux questions fonctionnent et interagissent avec d’autres services Azure. Vous devez également avoir une bonne compréhension des concepts de la base de connaissances.

## <a name="azure-resources"></a>Ressources Azure

Chaque [ressource Azure](azure-resources.md#resource-purposes) créée avec la fonctionnalité de réponses aux questions a un objectif spécifique. Chaque ressource a sa propre finalité, ses propres limites et son propre [niveau tarifaire](azure-resources.md#pricing-tier-considerations). Il est important de comprendre la fonction de ces ressources afin de pouvoir utiliser cette connaissance dans votre processus de planification.

| Ressource | Objectif |
|--|--|
| Ressource [ressource de langue](azure-resources.md) | Création, point de terminaison de prédiction de requête et télémétrie|
| Ressource [Recherche cognitive](azure-resources.md#azure-cognitive-search-resource) | Stockage et recherche de données |

### <a name="resource-planning"></a>Planification des ressources

Le débit des réponses aux questions est actuellement limité à 10 transactions par seconde pour à la fois les API de gestion et les API de prédiction. Pour cibler 10 transactions par seconde pour votre service, nous vous recommandons d’utiliser la référence (SKU) S1 (une instance) du service Recherche cognitive Azure.

### <a name="language-resource"></a>Ressource de langue

Une seule ressource de langue avec la fonctionnalité Réponse aux questions personnalisées activée peut héberger plusieurs projets/bases de connaissances. Le nombre de projets/bases de connaissances est déterminé par la quantité d’index pris en charge du niveau tarifaire Recherche cognitive. En savoir plus sur la [relation entre les index et les bases de connaissances](azure-resources.md#index-usage).

### <a name="knowledge-base-size-and-throughput"></a>Taille et débit des bases de connaissances

Lorsque vous créez une application réelle, vous devez prévoir des ressources suffisantes pour la taille de votre base de connaissances et pour la quantité de demandes de prédiction des requêtes auxquelles vous vous attendez.

La taille d’une base de connaissances est déterminée par les éléments suivants :
* Les limites du niveau tarifaire des [ressources Recherche cognitive](../../../../search/search-limits-quotas-capacity.md)
* [Limites des réponses aux questions](./limits.md)

La demande de prédiction des requêtes de la base de connaissances est déterminée par le plan d’application web et l’application web. Reportez-vous aux [paramètres recommandés](azure-resources.md#recommended-settings) pour planifier votre niveau tarifaire.

### <a name="understand-the-impact-of-resource-selection"></a>Comprendre l’impact du choix des ressources

En choisissant des ressources adaptées, vous permettez à la base de connaissances de répondre correctement aux prédictions de requête.

Si votre base de connaissances ne fonctionne pas correctement, le problème est généralement lié à une mauvaise gestion des ressources.

Un tel problème nécessite une investigation afin de déterminer quelle [ressource doit être changée](azure-resources.md#pricing-tier-considerations).

## <a name="project"></a>Project

Un projet/une base de connaissances est directement lié à sa ressource de langue. Elle contient les paires de questions et réponses (QnA) utilisées pour répondre aux demandes de prédiction des requêtes.

### <a name="language-considerations"></a>Observations relatives au langage

Vous pouvez désormais disposer de projets dans différentes langues au sein d’une même ressource de langue, où la fonctionnalité de réponses aux questions est activée. Lorsque vous créez le premier projet, vous pouvez choisir d’utiliser la ressource pour les projets/bases de connaissances dans une seule langue qui s’appliquera à tous les projets suivants ou d’effectuer une sélection de langue chaque fois qu’un projet est créé.

### <a name="ingest-data-sources"></a>Ingestion de sources de données

Le service de réponses aux questions prend également en charge le contenu non structuré. Vous pouvez charger un fichier au contenu non structuré.

Actuellement, nous ne prenons pas en charge les URL pour du contenu non structuré.

Le processus d’ingestion convertit les types de contenu pris en charge en Markdown. Toutes les modifications apportées à la *réponse* sont effectuées avec Markdown. Après avoir créé votre base de connaissances, vous pouvez modifier les paires question/réponse dans le portail Language Studio à l’aide de la création de texte enrichi.

### <a name="data-format-considerations"></a>Considérations relatives aux formats des données

Étant donné que le format final d’une paire question/réponse est le format Markdown, il est important de connaître la prise en charge du format Markdown.

### <a name="bot-personality"></a>Personnalité de bot

Ajoutez une personnalité de bot à votre projet/base de connaissances avec [chit-chat](../how-to/chit-chat.md). Cette personnalité comprend des réponses fournies pour un certain type de conversation, par exemple *professionnelle* ou *amicale*. Chit-chat est fourni sous la forme d’un ensemble de conversations, que vous pouvez ajouter, modifier ou supprimer.

Une personnalité de bot est recommandée si votre bot se connecte à votre base de connaissances. Vous pouvez choisir d’utiliser chit-chat dans votre base de connaissances même si vous vous connectez aussi à d’autres services. Toutefois, vous devrez examiner la façon dont le service bot interagit pour savoir si la conception architecturale est adaptée à votre utilisation.

### <a name="conversation-flow-with-a-project"></a>Flux de conversation avec un projet

Le flux de conversation commence généralement par la salutation d’un utilisateur, comme `Hi` ou `Hello`. Votre base de connaissances peut répondre avec une réponse générique, comme `Hi, how can I help you`, puis elle peut afficher des invites de suivi pour poursuivre la conversation.

Vous devez concevoir votre flux de conversation en utilisant une boucle pour qu’un utilisateur sache comment utiliser votre bot et ne soit pas abandonné par le bot au cours de la conversation. Les [invites de suivi](../tutorials/guided-conversations.md) fournissent une liaison entre les paires de questions-réponses, et c’est ce qui crée le flux de conversation.

### <a name="authoring-with-collaborators"></a>Création d’une base de connaissances avec des collaborateurs

Les collaborateurs peuvent être des développeurs qui partagent la totalité de la pile de développement de l’application de base de connaissances, ou ils peuvent uniquement être affectés à la création de la base de connaissances.

Lorsque vous créez une base de connaissances, vous pouvez appliquer différentes autorisations d’accès en fonction du rôle dans le portail Azure afin de limiter les possibilités d’un collaborateur.

## <a name="integration-with-client-applications"></a>Intégration aux applications clientes

L’intégration aux applications clientes s’effectue en envoyant une requête au point de terminaison du runtime de prédiction. Une requête est envoyée à votre projet/base de connaissances, en même temps qu’une requête SDK ou REST envoyée au point de terminaison de l’application web de réponses aux questions.

Afin d’authentifier correctement une requête du client, l’application cliente doit envoyer les bonnes informations d’identification et le bon ID de la base de connaissances. Si vous utilisez Azure Bot Service, configurez ces paramètres dans le cadre de la configuration du bot dans le portail Azure.

### <a name="conversation-flow-in-a-client-application"></a>Flux de conversation dans une application cliente

Dans une application cliente telle qu’un bot Azure, le flux de conversation peut nécessiter des fonctionnalités avant et après l’interaction avec la base de connaissances.

Votre application cliente prend-elle en charge le flux conversationnel, soit en fournissant d’autres moyens pour traiter les invites de suivi, soit en incluant chit-chat ? Si c’est le cas, concevez-les tôt et assurez-vous que la requête de l’application cliente est correctement traitée par un autre service ou lorsqu’elle est envoyée à votre base de connaissances.

### <a name="active-learning-from-a-client-application"></a>Apprentissage actif à partir d’une application cliente

Les réponses aux questions utilisent l’_apprentissage actif_ pour améliorer la qualité de votre base de connaissances en suggérant des questions alternatives pour votre réponse. L’application cliente est responsable en partie de cet [apprentissage actif](../tutorials/active-learning.md). Par le biais d’invites conversationnels, l’application cliente peut détecter que la base de connaissances a renvoyé une réponse qui n’est pas utile à l’utilisateur et peut déterminer une meilleure réponse. L’application cliente doit renvoyer ces informations à la base de connaissances pour améliorer la qualité de la prédiction.

### <a name="providing-a-default-answer"></a>Fournir une réponse par défaut

Si votre base de connaissances ne trouve pas de réponse, elle retourne une _réponse par défaut_. Cette réponse est configurable sur la page **Paramètres**.

Cette réponse par défaut est différente de la réponse par défaut du bot Azure. Vous configurez la réponse par défaut de votre bot Azure dans le portail Azure dans le cadre des paramètres de configuration. Elle est retournée lorsque le seuil de score n’est pas atteint.

## <a name="prediction"></a>Prédiction

La prédiction correspond à la réponse de votre base de connaissances et contient plus d’informations que la simple réponse. Pour obtenir une réponse de prédiction de requête, utilisez l’API de réponses aux questions.

### <a name="prediction-score-fluctuations"></a>Fluctuations du score de prédiction

Un score peut changer en fonction de plusieurs facteurs :

* Nombre de réponses que vous avez demandées en réponse à la propriété `top`
* Diversité des questions alternatives disponibles
* Filtrage des métadonnées
* Requête envoyée au projet/base de connaissances `test` ou `production`.

### <a name="analytics-with-azure-monitor"></a>Analytics avec Azure Monitor

Pour le service de réponses aux questions, la télémétrie provient du [service Azure Monitor](../../../../azure-monitor/index.yml). Utilisez nos [requêtes les plus fréquentes](../how-to/analytics.md) pour comprendre vos métriques.

## <a name="development-lifecycle"></a>Cycle de vie de développement

Le cycle de vie de développement d’une base de connaissances comprend sa modification, son test et sa publication.

### <a name="knowledge-base-development-of-question-answer-pairs"></a>Développement de la base de connaissances des paires de réponses aux questions

Vos paires de questions-réponses doivent être conçues et développées en fonction de l’utilisation de votre application cliente.

Chaque paire peut contenir les éléments suivants :
* Métadonnées : filtrables lors de l’interrogation pour vous permettre d’étiqueter vos paires question/réponse avec des informations supplémentaires sur la source, le contenu, le format et l’objectif de vos données.
* Invites de suivi : permettent de déterminer un chemin au sein de votre base de connaissances afin que l’utilisateur obtienne la bonne réponse.
* Questions alternatives : importantes pour trouver une réponse à partir de plusieurs formulations d’une même question. Les [suggestions d’apprentissage actif](../tutorials/active-learning.md) deviennent des questions alternatives.

### <a name="devops-development"></a>Développement DevOps

Le développement d’une base de connaissances à insérer dans un pipeline DevOps nécessite que la base de connaissances soit isolée pendant les tests par lots.

Une base de connaissances partage l’index Recherche cognitive avec toutes les autres bases de connaissances de la ressource de langue. Si la base de connaissances est isolée par une partition, le partage de l’index peut entraîner une différence de score par rapport à celui de la base de connaissances publiée.

Pour obtenir le _même score_ pour les bases de connaissances `test` et `production`, isolez une ressource de langue dans une base de connaissances unique. Dans cette architecture, la ressource ne doit vivre que pendant la durée du test par lots isolé.

## <a name="next-steps"></a>Étapes suivantes

* [Ressources Azure](./azure-resources.md)
