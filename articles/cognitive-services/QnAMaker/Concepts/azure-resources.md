---
title: Ressources Azure – QnA Maker
description: QnA Maker utilise plusieurs sources Azure, dont chacune a un objectif différent. Comprendre comment elles sont utilisées individuellement vous permet de planifier et de sélectionner le niveau tarifaire approprié ou de savoir quand modifier celui-ci. Comprendre comment elles sont utilisées en combinaison vous permet d’épingler et de résoudre des problèmes quand ils se produisent.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/11/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: e421c2e799e7eeb002cffab2748f099f21f81199
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131011995"
---
# <a name="azure-resources-for-qna-maker"></a>Ressources Azure pour QnA Maker

QnA Maker utilise plusieurs sources Azure, dont chacune a un objectif différent. Comprendre comment elles sont utilisées individuellement vous permet de planifier et de sélectionner le niveau tarifaire approprié ou de savoir quand modifier celui-ci. Comprendre comment elles sont utilisées _en combinaison_ vous permet d’épingler et de résoudre des problèmes quand ils se produisent.

[!INCLUDE [Info on question answering GA](../includes/new-version.md)]

## <a name="resource-planning"></a>Planification des ressources

La première fois que vous développez une base de connaissances QnA Maker, lors de la phase prototypage, il est courant d’avoir une seule ressource QnA Maker pour les tests et la production.

Lorsque vous passez à la phase de développement du projet, vous devez prendre en compte les aspects suivants :

* Combien de langues que votre système de base de connaissances doit-il prendre en charge ?
* Dans combien de régions votre base de connaissances doit-elle être accessible ?
* Combien de documents dans chaque domaine votre système doit-il contenir ?

Prévoyez d’avoir une ressource QnA Maker unique contenant toutes les bases de connaissances ayant en commun la langue, la région et la combinaison de domaines.

## <a name="pricing-tier-considerations"></a>Considérations relatives au niveau tarifaire

En général, il existe trois paramètres dont vous devez tenir compte :

* **Débit dont vous avez besoin à partir du service** :
    * sélectionnez le [plan App Service](https://azure.microsoft.com/pricing/details/app-service/plans/) approprié en fonction de vos besoins. Vous pouvez faire [monter en puissance](../../../app-service/manage-scale-up.md) ou faire descendre en puissance l’application.
    * Ceci doit également influencer votre sélection de la référence SKU de la [Recherche cognitive](../../../search/search-sku-tier.md) Azure. Voir **ici** pour plus d’informations. En outre, vous devrez peut-être ajuster la [capacité](../../../search/search-capacity-planning.md) de la Recherche cognitive capacité avec des réplicas.

* **Taille et nombre de bases de connaissances** : Choisissez la [référence SKU Recherche Azure](https://azure.microsoft.com/pricing/details/search/) adaptée à votre scénario. En règle générale, vous fixez le nombre de bases de connaissances dont vous avez besoin en fonction du nombre de domaines différents. Un domaine (pour une langue) doit se trouver dans une base de connaissances.

Votre ressource de service Azure Search doit avoir été créée après le janvier 2019 et ne peut pas se trouver dans le niveau gratuit (partagé). Il n’existe aucune prise en charge pour configurer des clés gérées par le client dans le portail Azure.

> [!IMPORTANT]
> Vous pouvez publier N-1 bases de connaissances dans un niveau spécifique, où N correspond au nombre maximal d’index autorisé dans le niveau. Vérifiez également la taille maximale et le nombre de documents autorisés par niveau.

Par exemple, si votre niveau comporte 15 index autorisés, vous pouvez publier 14 bases de connaissances (un index par base de connaissances publiée). Le quinzième index est utilisé pour toutes les bases de connaissances à des fins de création et de test.

* **Nombre de documents comme sources** : la référence SKU gratuite du service de gestion QnA Maker limite à 3 le nombre de documents (d’une taille de 1 Mo chacun) que vous pouvez gérer via le portail et les API. La référence SKU Standard n’a aucune limite en termes de nombre de documents que vous pouvez gérer. Vous trouverez plus de détails [ici](https://aka.ms/qnamaker-pricing).

Le tableau suivant vous donne des indications d’ordre général.

|                            | Administration de QnA Maker | App Service | Recherche cognitive Azure | Limites                      |
| -------------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| **Expérimentation**        | Référence SKU Gratuite             | Niveau Gratuit   | Niveau Gratuit    | Publier jusqu’à 2 Ko, taille de 50 Mo  |
| **Environnement Dev/Test**   | Référence SKU standard         | Partagé      | De base        | Publier jusqu’à 14 Ko, taille de 2 Go    |
| **Environnement de production** | Référence SKU standard         | De base       | Standard     | Publier jusqu’à 49 Ko, taille de 25 Go |

## <a name="recommended-settings"></a>Paramètres recommandés

|RPS cible | App Service | Recherche cognitive Azure |
| -------------------- | ----------- | ------------ |
| 3             | S1, un réplica   | S1, un réplica    |
| 50         | S3, 10 réplicas       | S1, 12 réplicas         |
| 80         | S3, 10 réplicas      |  S3, 12 réplicas  |
| 100         | P3V2, 10 réplicas  | S3, 12 réplicas, 3 partitions   |
| 200 à 250         | P3V2, 20 réplicas | S3, 12 réplicas, 3 partitions    |

## <a name="when-to-change-a-pricing-tier"></a>Quand modifier un niveau tarifaire

|Mettre à niveau|Motif|
|--|--|
|[Mise à niveau](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) de la référence SKU de gestion de QnA Maker|Vous souhaitez avoir plus de paires de questions-réponses ou sources de documents dans votre base de connaissances.|
|[Mettre à niveau](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) la référence SKU App Service et vérifier le niveau de la Recherche cognitive et [créer des réplicas de Recherche cognitive](../../../search/search-capacity-planning.md)|Votre base de connaissances doit traiter plus de demandes de votre application cliente, comme un bot de conversation.|
|[Mettre à niveau](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) le service Recherche cognitive Azure|Vous envisagez de disposer de nombreuses bases de connaissances.|

Procurez-vous les dernières mises à jour du runtime en [mettant à jour votre App Service dans le portail Azure](../how-to/configure-QnA-Maker-resources.md#get-the-latest-runtime-updates).

## <a name="keys-in-qna-maker"></a>Clés dans QnA Maker

Votre service QnA Maker gère deux types de clés : **clés de création** et **clés de point de terminaison de requête** utilisées avec le runtime hébergé dans App service.

Utilisez ces clés lorsque vous adressez des demandes au service via des API.

![Gestion des clés](../media/authoring-key.png)

|Nom|Emplacement|Objectif|
|--|--|--|
|Clé de création/d’abonnement|[Azure portal](https://azure.microsoft.com/free/cognitive-services/)|ces clés sont utilisées pour accéder aux [API du service d’administration QnA Maker](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase). Ces API vous permettent de modifier les questions et réponses dans votre base de connaissances, et de publier votre base de connaissances. Ces clés sont créées lorsque vous créez un service QnA Maker.<br><br>Recherchez ces clés dans la ressource **Cognitive Services** de la page **Clés et point de terminaison**.|
|Clé de point de terminaison de requête|[Portail QnA Maker](https://www.qnamaker.ai)|Ces clés sont utilisées pour interroger le point de terminaison de base de connaissances publié afin d’obtenir une réponse à une question d’utilisateur. En général, vous utilisez ce point de terminaison de requête dans votre bot conversationnel ou dans le code de l’application cliente qui se connecte au service QnA Maker. Ces clés sont créées lorsque vous publiez votre base de connaissances QnA Maker.<br><br>Recherchez ces clés dans la page **Paramètres du service**. Recherchez cette page à partir du menu de l’utilisateur en haut à droite de la page dans le menu déroulant.|

### <a name="find-authoring-keys-in-the-azure-portal"></a>Rechercher des clés de création dans le portail Azure

Vous pouvez afficher et réinitialiser vos clés de création à partir du portail Azure où la ressource QnA Maker a été créée.

1. Accédez à la ressource QnA Maker dans le portail Azure et sélectionnez la ressource qui a le type _Cognitive Services_ :

    ![Liste des ressources QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Accédez aux **clés et point de terminaison** :

    ![Clé d’abonnement QnA Maker managé (préversion)](../media/qnamaker-how-to-key-management/subscription-key-v2.png)

### <a name="find-query-endpoint-keys-in-the-qna-maker-portal"></a>Rechercher des clés de point de terminaison de requête dans le portail QnA Maker

Le point de terminaison se trouve dans la même région que la ressource, car les clés de point de terminaison sont utilisées pour appeler la base de connaissances.

Les clés de point de terminaison peuvent être gérées à partir du [portail QnA Maker](https://qnamaker.ai).

1. Connectez-vous au [portail QnA Maker](https://qnamaker.ai), accédez à votre profil, puis sélectionnez **Paramètres de service** :

    ![Clé de point de terminaison](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Affichez ou réinitialisez vos clés :

    > [!div class="mx-imgBorder"]
    > ![Gestionnaire de la clé de point de terminaison](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Actualisez vos clés si vous pensez qu’elles ont été compromises. Cette opération peut nécessiter des modifications correspondantes de votre code de bot ou d’application cliente.

## <a name="management-service-region"></a>Région du service de gestion

Le service de gestion de QnA Maker est utilisé uniquement pour le portail QnA Maker et pour le traitement initial des données. Ce service est disponible uniquement dans la région **USA Ouest**. Il n’y a pas de stockage de données de clients dans ce service aux USA Ouest.

## <a name="resource-naming-considerations"></a>Considérations relatives à l’affection de noms aux ressources

Le nom de la ressource QnA Maker, tel que `qna-westus-f0-b`, est également utilisé pour nommer les autres ressources.

La fenêtre Créer du portail Azure vous permet de créer une ressource QnA Maker et de sélectionner les niveaux tarifaires des autres ressources.

> [!div class="mx-imgBorder"]
> ![Capture d’écran du Portail Azure pour la création de ressource QnA Maker](../media/concept-azure-resource/create-blade.png)

Une fois les ressources créées, elles portent le même nom, à l’exception de la ressource facultative Application Insights, qui ajoute des caractères à la fin du nom.

> [!div class="mx-imgBorder"]
> ![Capture d’écran de la liste des ressources sur le portail Azure](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> Créez un groupe de ressources lorsque vous créez une ressource QnA Maker. Cela vous permet de voir toutes les ressources associées à la ressource QnA Maker lors d’une recherche par groupe de ressources.

> [!TIP]
> Utilisez une convention d’affectation de noms pour indiquer les niveaux tarifaires dans le nom de la ressource ou du groupe de ressources. Si vous recevez des erreurs lors de la création d’une base de connaissances ou de l’ajout de documents, la limite de niveau tarifaire de Recherche cognitive est un problème courant.

## <a name="resource-purposes"></a>Objectifs des ressources

Chaque ressource Azure créée avec QnA Maker a un objectif spécifique :

* Ressource QnA Maker
* Ressource Recherche cognitive
* App Service
* Service de plan d’application
* Service Application Insights

### <a name="qna-maker-resource"></a>Ressource QnA Maker

La ressource QnA Maker donne accès aux API de création et de publication, ainsi qu’à la deuxième couche de classement (ranker #2) basé sur le traitement en langage naturel des paires de questions-réponses au moment de l’exécution.

Le deuxième classement applique des filtres intelligents qui peuvent inclure des métadonnées et des invites de suivi.

#### <a name="qna-maker-resource-configuration-settings"></a>Paramètres de configuration de ressources QnA Maker

Lorsque vous créez une base de connaissances dans le [portail QnA Maker](https://qnamaker.ai), le paramètre **Langue** est le seul appliqué au niveau de la ressource. Vous sélectionnez la langue lorsque vous créez la première base de connaissances pour la ressource.

### <a name="cognitive-search-resource"></a>Ressource Recherche cognitive

La ressource [Recherche cognitive](../../../search/index.yml) est utilisée pour :

* Stocker les paires de questions-réponses
* fournir le classement initial (ranker #1) des paires de questions-réponses lors du runtime

#### <a name="index-usage"></a>Utilisation de l’index

La ressource conserve un index faisant office d’index de test. Chacun des index restants est mis en corrélation avec une base de connaissances publiée.

Une ressource tarifée pour contenir 15 index contient 14 bases de connaissances publiées, et un index est utilisé pour tester toutes les bases de connaissances. Cet index de test est partitionné par la base de connaissances afin qu’une requête utilisant le volet de test interactif utilise l’index de test, mais retourne uniquement les résultats de la partition associée à la base de connaissances.

#### <a name="language-usage"></a>Utilisation de la langue

La première base de connaissances créée dans la ressource QnA Maker est utilisée pour déterminer la langue _unique_ définie pour la ressource Recherche cognitive et tous ses index. Vous ne pouvez avoir _qu’une seule langue définie_ pour un service QnA Maker.

#### <a name="using-a-single-cognitive-search-service"></a>Utilisation d’un service Recherche cognitive unique

Si vous créez un service QnA et ses dépendances (par exemple, la recherche) via le portail, un service de recherche est créé pour vous et lié au service QnA Maker. Une fois ces ressources créées, vous pouvez mettre à jour le paramètre App Service pour utiliser un service de recherche existant au préalable et supprimer celui que vous venez de créer.

Découvrez [comment configurer](../How-To/configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource) QnA Maker pour utiliser une ressource Service cognitif différente de celle créée dans le cadre du processus de création de ressource QnA Maker.

### <a name="app-service-and-app-service-plan"></a>App service et plan App service

Votre application cliente utilise l’[App service](../../../app-service/index.yml) pour accéder aux bases de connaissances publiées via le point de terminaison du runtime.

Pour interroger la base de connaissances publiée, toutes les bases de connaissances publiées utilisent le même point de terminaison d’URL, mais spécifient l’**ID de la base de connaissances** dans l’itinéraire.

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

La fonctionnalité [Application Insights](../../../azure-monitor/app/app-insights-overview.md) est utilisée pour collecter les journaux de conversation et la télémétrie. Pour plus d’informations sur votre service, voir les [requêtes Kusto](../how-to/get-analytics-knowledge-base.md) communes.

### <a name="share-services-with-qna-maker"></a>Partager des services avec QnA Maker

QnA Maker crée plusieurs ressources Azure. Pour alléger la gestion et profiter du partage des coûts, utilisez le tableau suivant pour comprendre ce que vous pouvez et ne pouvez pas partager :

|Service|Partager|Motif|
|--|--|--|
|Cognitive Services|X|Impossible à la base|
|Plan App Service|✔|Espace disque fixe alloué à un plan App Service. Si d’autres applications qui partagent le même plan App Service utilisent une quantité d’espace disque significative, l’instance QnAMaker App Service rencontrera des problèmes.|
|App Service|X|Impossible à la base|
|Application Insights|✔|Peut être partagé|
|Service de recherche|✔|1. `testkb` est un nom réservé pour le service QnAMaker ; il ne peut pas être utilisé par d’autres.<br>2. Le nom de carte de synonymes `synonym-map` est réservé au service QnAMaker.<br>3. Le nombre de bases de connaissances publiées est limité par le niveau de service de recherche. Si des index gratuits sont disponibles, les autres services peuvent les utiliser.|

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la [base de connaissances](../How-To/manage-knowledge-bases.md) QnA Maker
* Comprendre un [cycle de vie de base de connaissances](development-lifecycle-knowledge-base.md)
* Examiner les [limites](../limits.md) du service et de la base de connaissances
