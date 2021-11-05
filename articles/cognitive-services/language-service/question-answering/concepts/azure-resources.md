---
title: 'Ressources Azure : réponses aux questions'
description: Les réponses aux questions utilisent plusieurs sources Azure, chacune avec un objectif différent. Comprendre comment elles sont utilisées individuellement vous permet de planifier et de sélectionner le niveau tarifaire approprié ou de savoir quand modifier celui-ci. Comprendre comment elles sont utilisées en combinaison vous permet d’épingler et de résoudre des problèmes quand ils se produisent.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 10/10/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 0ed037e44ff202ec1fd0cacfbbbcfb16ee9be273
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097749"
---
# <a name="azure-resources-for-question-answering"></a>Ressources Azure pour les réponses aux questions

Les réponses aux questions utilisent plusieurs sources Azure, chacune avec un objectif différent. Comprendre comment elles sont utilisées individuellement vous permet de planifier et de sélectionner le niveau tarifaire approprié ou de savoir quand modifier celui-ci. Comprendre comment les ressources sont utilisées _en combinaison_ vous permet d’épingler et de résoudre des problèmes quand ils se produisent.

## <a name="resource-planning"></a>Planification des ressources

La première fois que vous développez une base de connaissances, dans la phase prototype, il est courant de n’avoir qu’une seule ressource pour les tests et la production.

> [!TIP]
> « Base de connaissances » et « projet » sont des termes équivalents en question et peuvent être utilisés indifféremment.

Lorsque vous passez à la phase de développement du projet, vous devez prendre en compte les aspects suivants :

* Combien de langues que votre système de base de connaissances doit-il prendre en charge ?
* Dans combien de régions votre base de connaissances doit-elle être accessible ?
* Combien de documents dans chaque domaine votre système doit-il contenir ?

## <a name="pricing-tier-considerations"></a>Considérations relatives au niveau tarifaire

En général, il existe trois paramètres dont vous devez tenir compte :

* **Débit dont vous avez besoin** :
    * La fonctionnalité Réponses aux questions est gratuite. Son débit est actuellement plafonné à 10 transactions par seconde tant pour les API de gestion que pour les API de prédiction.
    * Ceci doit également influencer votre sélection de la référence SKU de la [Recherche cognitive](../../../../search/search-sku-tier.md) Azure. Voir **ici** pour plus d’informations. En outre, vous devrez peut-être ajuster la [capacité](../../../../search/search-capacity-planning.md) de la Recherche cognitive capacité avec des réplicas.

* **Taille et nombre de bases de connaissances** : Choisissez la [référence SKU Recherche Azure](https://azure.microsoft.com/pricing/details/search/) adaptée à votre scénario. En règle générale, vous fixez le nombre de bases de connaissances dont vous avez besoin en fonction du nombre de domaines différents. Un domaine (pour une langue) doit se trouver dans une base de connaissances.

    Avec la réponse à une question personnalisée, vous avez la possibilité de configurer votre ressource de langue dans une seule langue ou dans plusieurs langues. Vous pouvez effectuer cette sélection lorsque vous créez votre premier projet dans [Language Studio](https://language.azure.com/).

    > [!IMPORTANT]
    > Vous pouvez publier N-1 bases de connaissances d’une langue unique ou N/2 bases de connaissances de langues différentes dans un niveau particulier, où N est le nombre maximal d’index autorisés dans le niveau. Vérifiez également la taille maximale et le nombre de documents autorisés par niveau.

    Par exemple, si votre niveau comporte 15 index autorisés, vous pouvez publier 14 bases de connaissances de la même langue (un index par base de connaissances publiée). Le 15e index est utilisé pour toutes les bases de connaissances à des fins de création et de test. Si vous choisissez d’utiliser des bases de connaissances dans différentes langues, vous pouvez uniquement en publier sept.

* **Nombre de documents comme sources** : la fonctionnalité Réponses aux questions est gratuite. Le nombre de documents que vous pouvez ajouter en tant que sources est illimité. 

Le tableau suivant vous donne des indications d’ordre général.

|                            |Recherche cognitive Azure | Limites                      |
| -------------------------- |------------ | -------------------------------- |
| **Expérimentation**        |Niveau Gratuit    | Publier jusqu’à 2 Ko, taille de 50 Mo  |
| **Environnement Dev/Test**   |De base        | Publier jusqu’à 14 Ko, taille de 2 Go    |
| **Environnement de production** |standard     | Publier jusqu’à 49 Ko, taille de 25 Go |

## <a name="recommended-settings"></a>Paramètres recommandés

La fonctionnalité Réponses aux questions personnalisées est gratuite. Son débit est actuellement plafonné à 10 transactions par seconde tant pour les API de gestion que pour les API de prédiction. Pour cibler 10 transactions par seconde pour votre service, nous vous recommandons d’utiliser la référence (SKU) S1 (une instance) du service Recherche cognitive Azure.

## <a name="keys-in-question-answering"></a>Clés des réponses aux questions

Votre fonctionnalité Réponses aux questions personnalisées traite deux types de clés : les **clés de création** et les **clés de Recherche cognitive Azure** utilisées pour accéder au service dans l’abonnement du client.

Utilisez ces clés lorsque vous adressez des demandes au service via des API.

> [!div class="mx-imgBorder"]
> ![Capture d’écran de la version préliminaire gérée de gestion de clés.](../../../qnamaker/media/qnamaker-how-to-key-management/custom-question-answering-key-management.png)

|Nom|Emplacement|Objectif|
|--|--|--|
|Clé de création/d’abonnement|[Azure portal](https://azure.microsoft.com/free/cognitive-services/)|Ces clés sont utilisées pour accéder aux API du service de langage. Ces API vous permettent de modifier les questions et réponses dans votre base de connaissances, et de publier votre base de connaissances. Ces clés sont créées lorsque vous créez une ressource.<br><br>Recherchez ces clés dans la ressource **Cognitive Services** de la page **Clés et point de terminaison**.|
|Clé d’administration de Recherche cognitive Azure|[Azure portal](../../../../search/search-security-api-keys.md)|Ces clés sont utilisées pour communiquer avec le service de recherche cognitive Azure déployé dans l’abonnement Azure de l’utilisateur. Lorsque vous associez une ressource de Recherche Cognitive Azure à la fonctionnalité de réponses aux questions personnalisées, la clé d’administration est automatiquement transmise aux réponses aux questions. <br><br>Vous pouvez trouver ces clés sur la ressource **Recherche cognitive Azure** dans la page **Clés**.|

### <a name="find-authoring-keys-in-the-azure-portal"></a>Rechercher des clés de création dans le portail Azure

Vous pouvez afficher et réinitialiser vos clés de création à partir du portail Azure, là où vous avez ajouté la fonctionnalité Réponses aux questions personnalisées (préversion) dans la ressource Analyse de texte.

1. Accédez à la ressource Analyse de texte dans le portail Azure et sélectionnez la ressource dont le type est *Cognitive Services* :

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la liste des ressources de réponse aux questions.](../../../qnamaker/media/qnamaker-how-to-setup-service/resources-created-question-answering.png)

2. Accédez aux **clés et point de terminaison** :

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la clé d’abonnement.](../../../qnamaker/media/qnamaker-how-to-key-management/custom-qna-keys-and-endpoint.png)

### <a name="management-service-region"></a>Région du service de gestion

Dans la fonctionnalité Réponses aux questions personnalisées, les services de gestion et de prédiction sont localisés dans la même région.

## <a name="resource-purposes"></a>Objectifs des ressources

Chaque ressource Azure créée avec la fonctionnalité Réponses aux questions personnalisées a un objectif spécifique :

* Ressource de langue (également appelée ressource Analyse de texte en fonction du contexte d’évaluation de la ressource.)
* Ressource Recherche cognitive

### <a name="language-resource"></a>Ressource de langue

La ressource de langue avec la fonctionnalité de réponses aux questions personnalisées permet d’accéder aux API de création et de publication, d’héberger le runtime de classement et de fournir des données de télémétrie.

### <a name="azure-cognitive-search-resource"></a>Ressource Recherche cognitive Azure

La ressource [Recherche cognitive](../../../../search/index.yml) est utilisée pour :

* Stocker les paires de questions et réponses
* Fournir le classement initial (Rang 1) des paires de questions et réponses au moment de l’exécution

#### <a name="index-usage"></a>Utilisation de l’index

Vous pouvez publier N-1 bases de connaissances d’une langue unique ou N/2 bases de connaissances de langues différentes dans un niveau particulier, où N est le nombre maximal d’index autorisés dans le niveau Recherche cognitive Azure. Vérifiez également la taille maximale et le nombre de documents autorisés par niveau.

Par exemple, si votre niveau comporte 15 index autorisés, vous pouvez publier 14 bases de connaissances de la même langue (un index par base de connaissances publiée). Le 15e index est utilisé pour toutes les bases de connaissances à des fins de création et de test. Si vous choisissez d’utiliser des bases de connaissances dans différentes langues, vous pouvez uniquement en publier sept.

#### <a name="language-usage"></a>Utilisation de la langue

La fonctionnalité Réponses aux questions personnalisées, vous permet de configurer votre service pour des bases de connaissances dans une ou plusieurs langues. Vous faites ce choix pendant la création de la première base de connaissances dans votre ressource de langue.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la question relative à la réponse aux [bases de connaissances](../How-To/manage-knowledge-base.md)
