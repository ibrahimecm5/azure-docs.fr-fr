---
title: Créer une ressource pour Translator
titleSuffix: Azure Cognitive Services
description: Cet article vous montre comment créer une ressource Azure Cognitive Services Translator et obtenir une clé d’abonnement ainsi qu’une URL de point de terminaison.
services: cognitive-services
author: laujan
ms.author: lajanuar
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: 02d1e509068c2332164fea7236fe1fe554b3ef02
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439269"
---
# <a name="create-a-translator-resource"></a>Créer une ressource pour Translator

Dans cet article, vous allez apprendre à créer une ressource Translator au sein du portail Azure. [Azure Translator](translator-overview.md) est un service de traduction automatique cloud, qui fait partie de la famille [Azure Cognitive Services](../what-are-cognitive-services.md) des API REST. Les ressources Azure sont des instances de services que vous créez. Toutes les requêtes d’API adressées aux services Azure nécessitent une URL de **point de terminaison** et une **clé d’abonnement** en lecture seule pour l’authentification de l’accès.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous avez besoin d’un [**compte Azure**](https://azure.microsoft.com/free/cognitive-services/) actif.  Si vous n’en avez pas, vous pouvez [**créer un abonnement gratuit de 12 mois**](https://azure.microsoft.com/free/).

## <a name="translator-resource-types"></a>Types de ressource Translator

Le service Translator est accessible via deux types de ressource distincts :

* Les types de ressource **monoservice** permettent d’accéder à une seule clé API et un seul point de terminaison de service.  

* Les types de ressource **multiservices** permettent d’accéder à plusieurs instances de Cognitive Services à l’aide d’une seule clé API et d’un seul point de terminaison. La ressource Cognitive Services est disponible pour les services suivants :
  * Langage ([Translator](../translator/translator-overview.md), [Language Understanding (LUIS)](../luis/what-is-luis.md), [Service de langage](../text-analytics/overview.md))  
  * Vision ([Vision par ordinateur](../computer-vision/overview.md)), ([Visage](../face/overview.md))  
  * Décision ([Content Moderator](../content-moderator/overview.md))  

## <a name="create-your-resource"></a>Créer votre ressource

* Accédez directement à la page [**Créer Translator**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) dans le portail Azure pour compléter les détails de votre projet.

* Accédez directement à la page [**Créer Cognitive Services**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) dans le portail Azure pour compléter les détails de votre projet.

>[!TIP]
>Si vous préférez, vous pouvez lancer le processus **Créer** à partir de la page d’accueil du portail Azure en effectuant les actions suivantes :
>
> 1. Accédez à la page d’accueil du [**portail Azure**](https://ms.portal.azure.com/#home).
> 1. Dans le menu des services Azure, sélectionnez ➕**Créer une ressource**.
>1. Dans la zone de recherche **Rechercher dans la Place de marché**, entrez et sélectionnez **Translator** (ressource monoservice) ou **Cognitive Services** (ressource multiservice).  *Consultez* [Créer votre ressource](#create-your-resource), ci-dessus.
> 1. Sélectionnez **Créer**, et vous serez redirigé vers la page des détails du projet.
><br/><br/>

## <a name="complete-your-project-and-instance-details"></a>Complétez les détails relatifs à votre projet et votre instance

1. **Abonnement**. Sélectionnez l’un de vos abonnements Azure disponibles.

1. **Groupe de ressources**. Le groupe de ressources Azure que vous choisissez sert de conteneur virtuel pour votre nouvelle ressource. Vous pouvez créer un groupe de ressources ou ajouter votre ressource à un groupe de ressources préexistant qui partage le même cycle de vie, les mêmes autorisations et les mêmes stratégies.

1. **Région de la ressource**. Choisissez **Globale**, sauf si votre entreprise ou votre application nécessite une région spécifique. Translator est un service non régional. Il n’existe aucune dépendance par rapport à une région Azure spécifique. *Consultez* [Régions et zones de disponibilité dans Azure](../../availability-zones/az-overview.md).

1. **Nom**. Entrez le nom que vous avez choisi pour votre ressource. Le nom choisi doit être unique dans Azure.

> [!NOTE]
> Si vous utilisez une fonctionnalité de Translator qui nécessite un point de terminaison de domaine personnalisé, la valeur que vous entrez dans le champ Nom correspond au paramètre de nom de domaine personnalisé du point de terminaison.

5. **Niveau tarifaire**. Sélectionnez un [niveau tarifaire](https://azure.microsoft.com/pricing/details/cognitive-services/translator) qui répond à vos besoins :

   * Chaque abonnement a un niveau gratuit.
   * Le niveau Gratuit a les mêmes caractéristiques et fonctionnalités que les plans payants. Il n’a pas de date d’expiration.
   * Un seul abonnement gratuit par compte est autorisé.</li></ul>

1. Si vous avez créé une ressource multiservice, vous devez vérifier les détails d’utilisation supplémentaires à l’aide des cases à cocher.

1. Sélectionnez **Vérifier + créer**.

1. Passez en revue les conditions du service, puis sélectionnez **Créer** pour déployer votre ressource.

1. Une fois votre ressource correctement déployée, sélectionnez **Accéder à la ressource**.

### <a name="authentication-keys-and-endpoint-url"></a>Clés d’authentification et URL de point de terminaison

Toutes les requêtes d’API Cognitive Services nécessitent une URL de point de terminaison et une clé en lecture seule pour l’authentification.

* **Clés d’authentification**. Votre clé est une chaîne unique qui est passée à chaque requête adressée au service de traduction. Vous pouvez passer votre clé via un paramètre de chaîne de requête ou en la spécifiant dans l’en-tête de requête HTTP.

* **URL de point de terminaison**. Utilisez le point de terminaison global dans votre requête d’API, sauf si vous avez besoin d’une région Azure spécifique. *Consultez* [URL de base](reference/v3-0-reference.md#base-urls). L’URL de point de terminaison global est `api.cognitive.microsofttranslator.com`.

## <a name="get-your-authentication-keys-and-endpoint"></a>Obtenir vos clés d’authentification et votre point de terminaison

1. Une fois votre nouvelle ressource déployée, sélectionnez **Accéder à la ressource**, ou accédez directement à votre page de ressources.
1. Dans le rail de gauche, sous *Gestion des ressources*, sélectionnez **Clés et point de terminaison**.
1. Copiez et collez vos clés d’abonnement et votre URL de point de terminaison dans un emplacement pratique, par exemple le *Bloc-notes Microsoft*.

:::image type="content" source="../media/cognitive-services-apis-create-account/get-cog-serv-keys.png" alt-text="Obtenir la clé et le point de terminaison.":::

## <a name="how-to-delete-a--resource-or-resource-group"></a>Comment supprimer une ressource ou un groupe de ressources

> [!Warning]
> La suppression d’un groupe de ressources entraîne la suppression de toutes les ressources qu’il contient.

Pour supprimer une ressource Cognitive Services ou Translator, vous pouvez **supprimer la ressource** ou **supprimer le groupe de ressources**.

Pour supprimer la ressource :

1. Accédez à votre groupe de ressources dans le portail Azure.
1. Sélectionnez les ressources à supprimer en cochant la case adjacente.
1. Dans le menu supérieur près du bord droit, sélectionnez **Supprimer**.
1. Tapez *oui* dans la boîte de dialogue **Ressources supprimées**.
1. Sélectionnez **Supprimer**.

Pour supprimer le groupe de ressources :

1. Accédez à votre groupe de ressources dans le portail Azure.
1. Dans la barre de menus supérieure près du bord gauche, sélectionnez **Supprimer le groupe de ressources**.
1. Confirmez la demande de suppression en entrant le nom du groupe de ressources et en sélectionnant **Supprimer**.

## <a name="how-to-get-started-with-translator"></a>Bien démarrer avec Translator

Dans notre guide de démarrage rapide, vous allez apprendre à utiliser le service Translator avec les API REST.

> [!div class="nextstepaction"]
> [Bien démarrer avec Translator](quickstart-translator.md)

## <a name="more-resources"></a>Plus de ressources

* [Exemples de code liés à Microsoft Translator](https://github.com/MicrosoftTranslator).  Des exemples de code multilangage liés à Translator sont disponibles sur GitHub.
* [Forum de support pour Microsoft Translator](https://www.aka.ms/TranslatorForum)
* [Bien démarrer avec Azure (vidéo de 3 minutes)](https://azure.microsoft.com/get-started/?b=16.24)