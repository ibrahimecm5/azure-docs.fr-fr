---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: adbcef246e8b027230ec436de9d9a1da838e523f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096969"
---
Pour utiliser Language Studio, vous aurez besoin d’une ressource Azure pour le langage d’authentification. Vous pouvez également utiliser cette ressource pour appeler les API REST de la fonctionnalité et les bibliothèques clientes. Effectuez d’abord ces étapes. 

> [!IMPORTANT] 
> Le processus d’installation et la configuration requise pour les fonctionnalités personnalisées sont différents. Si vous utilisez l’une des fonctionnalités personnalisées, nous vous recommandons de suivre l’un des articles de démarrage rapide ci-dessous pour vous familiariser plus facilement.  
> * [Language Understanding conversationnel](../conversational-language-understanding/quickstart.md)
> * [Classification de texte personnalisée](../custom-classification/quickstart.md)
> * [Reconnaissance d’entité nommée (NER) personnalisée](../custom-named-entity-recognition/quickstart.md) 

1. Créez un abonnement Azure. Vous pouvez en [créer un gratuitement](https://azure.microsoft.com/free/ai/). 

2. [Connectez-vous à Language Studio](https://aka.ms/languageStudio). Si vous vous connectez pour la première fois, vous voyez s’afficher une fenêtre qui vous permet de choisir une ressource de langue. 

   :::image type="content" source="../media/language-resource-small.png" alt-text="Capture d’écran montrant l’écran de sélection de ressource dans Language Studio." lightbox="../media/language-resource.png":::

3. Sélectionnez **+ Créer une ressource linguistique**. Entrez ensuite les informations de votre nouvelle ressource, par exemple un nom, un emplacement et un groupe de ressources.

    
    > [!TIP]
    > * Lorsque vous sélectionnez un emplacement pour votre ressource Azure, choisissez celui qui vous convient le mieux pour une latence faible.
    > * Nous vous recommandons d’activer l’option **Identité managée** **sur**, afin d’authentifier vos demandes sur Azure.
    > * Si vous utilisez le niveau gratuit, vous pouvez continuer à utiliser le service de langage même après l’expiration de votre version d’évaluation gratuite ou de votre crédit de service Azure. 

    :::image type="content" source="../media/create-new-resource-small.png" alt-text="Capture d’écran montrant l’écran de création de ressource dans Language Studio." lightbox="../media/create-new-resource.png":::

4. Sélectionnez **Terminé**. Votre ressource sera créée, et vous pourrez essayer les différentes fonctionnalités offertes par le service de langage. Par exemple, sélectionnez **Rechercher des entités liées**.

    :::image type="content" source="../media/language-studio-main-screen.png" alt-text="Capture d’écran montrant l’écran principal dans Language Studio." lightbox="../media/language-studio-main-screen.png":::


5. Cette fonctionnalité comporte une section permettant d’entrer du texte, de charger un fichier ou de choisir un exemple de texte pour illustrer le fonctionnement de la fonctionnalité. Pour tester la démonstration, vous devrez choisir une ressource et accuser réception d’une utilisation en fonction de votre [niveau tarifaire](https://aka.ms/unifiedLanguagePricing).

    :::image type="content" source="../media/language-studio-feature.png" alt-text="Capture d’écran montrant une fonctionnalité dans Language Studio" lightbox="../media/language-studio-feature.png":::

6. Après avoir envoyé du texte, vous pouvez voir une visualisation, ainsi que la réponse JSON. En bas de la page, vous verrez les étapes suivantes et la commande de la boucle pour la demande d’API que vous venez d’envoyer.

    :::image type="content" source="../media/language-studio-feature-result.png" alt-text="Capture d’écran montrant le résultat d’une fonctionnalité dans Language Studio" lightbox="../media/language-studio-feature-result.png":::
