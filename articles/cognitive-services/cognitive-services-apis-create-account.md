---
title: Créer un compte Cognitive Services dans le portail Azure
titleSuffix: Azure Cognitive Services
description: Commencez à utiliser Azure Cognitive Services en créant et en vous abonnant à une ressource dans le Portail Azure.
services: cognitive-services
author: aahill
manager: nitinme
keywords: services cognitifs, intelligence cognitive, solutions cognitives, services ia
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: aahi
ms.openlocfilehash: 5ebccc07b4816237f12363e7729175dde5537e50
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458339"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-portal"></a>Démarrage rapide : Créer un compte Cognitive Services dans le portail Azure

Ce guide de démarrage rapide vous montre comment commencer à utiliser Azure Cognitive Services. Après avoir créé une ressource Cognitive Services dans le portail Azure, vous obtenez un point de terminaison et une clé pour authentifier vos applications.

La solution Azure Cognitive Services correspond à des services cloud avec des API REST et des kits SDK de bibliothèque de client destinés à aider les développeurs à intégrer une intelligence cognitive dans des applications sans connaissances ni compétences directes en intelligence artificielle (IA) ou en science des données. Azure Cognitive Services permet aux développeurs d’ajouter facilement des fonctionnalités cognitives dans leurs applications à l’aide de solutions cognitives capables de voir, entendre, parler, comprendre et même commencer à raisonner.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure valide - [Créer un abonnement gratuitement](https://azure.microsoft.com/free/cognitive-services/)
* [!INCLUDE [contributor-requirement](./includes/quickstarts/contributor-requirement.md)]


## <a name="create-a-new-azure-cognitive-services-resource"></a>Créer une ressource Azure Cognitive Services

1. Crée une ressource.

### <a name="multi-service-resource"></a>[Ressource multiservice](#tab/multiservice)

La ressource multiservice est nommée **Cognitive Services** dans le portail. [Créez une ressource Cognitive Services](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).

À ce stade, la ressource multiservice permet d’accéder aux services Cognitive Services suivants :

* **Vision** - Vision par ordinateur, vision personnalisée, visage
* **Speech** – Speech
* **Langue** – Langue, Traducteur
* **Décision** - Content Moderator

### <a name="single-service-resource"></a>[Ressource monoservice](#tab/singleservice)

Utilisez les liens ci-dessous afin de créer une ressource pour les services Cognitive Services disponibles :

| Vision                      | Speech                  | Langage                          | Décision             |
|-----------------------------|-------------------------|-----------------------------------|----------------------|
| [Vision par ordinateur](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Services Speech](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Lecteur immersif](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Détecteur d’anomalies](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | 
| [Service Custom Vision](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) |  | [Language Understanding (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | 
| [Visage](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     |
|        |                         | [Service Language](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |  [Metrics Advisor](https://go.microsoft.com/fwlink/?linkid=2142156)                    |
| | | [Translator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) | |

---

2. Sur la page **Créer**, renseignez les informations suivantes :
<!-- markdownlint-disable MD024 -->

### <a name="multi-service-resource"></a>[Ressource multiservice](#tab/multiservice)

|Détails du projet| Description   |
|--|--|
| **Abonnement** | Sélectionnez l’un de vos abonnements Azure disponibles. |
| **Groupe de ressources** | Groupe de ressources Azure comprenant votre ressource Cognitive Services. Vous pouvez créer un groupe ou l’ajouter à un groupe préexistant. |
| **Région** | Emplacement de votre instance Cognitive Services. Des emplacements différents peuvent entraîner une latence. Toutefois, cela n’aura pas d’impact sur la disponibilité d’exécution de votre ressource. |
| **Nom** | Nom descriptif de votre ressource Cognitive Services. Par exemple, *MyCognitiveServicesResource*. |
| **Niveau tarifaire** | Le coût associé à votre compte Cognitive Services dépend des options que vous choisissez, ainsi que de votre utilisation. Pour plus d'informations, consultez le [détail des tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/) de l’API.

<!--![Multi-service resource creation screen](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)-->
:::image type="content" source="media/cognitive-services-apis-create-account/resource_create_screen-multi.png" alt-text="Écran de création de ressource multiservice":::

Lisez et acceptez les conditions (qui vous concernent), puis sélectionnez **Vérifier + créer**.

### <a name="single-service-resource"></a>[Ressource monoservice](#tab/singleservice)

|Détails du projet| Description   |
|--|--|
| **Abonnement** | Sélectionnez l’un de vos abonnements Azure disponibles. |
| **Groupe de ressources** | Groupe de ressources Azure comprenant votre ressource Cognitive Services. Vous pouvez créer un groupe ou l’ajouter à un groupe préexistant. |
| **Région** | Emplacement de votre instance Cognitive Services. Des emplacements différents peuvent entraîner une latence. Toutefois, cela n’aura pas d’impact sur la disponibilité d’exécution de votre ressource. |
| **Nom** | Nom descriptif de votre ressource Cognitive Services. Par exemple, *MyCognitiveServicesResource*. |
| **Niveau tarifaire** | Le coût associé à votre compte Cognitive Services dépend des options que vous choisissez, ainsi que de votre utilisation. Pour plus d'informations, consultez le [détail des tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/) de l’API.

<!--![Single-service resource creation screen](media/cognitive-services-apis-create-account/resource_create_screen.png)-->
:::image type="content" source="media/cognitive-services-apis-create-account/resource_create_screen.png" alt-text="Écran Création de ressource pour la ressource d’un seul service":::

Sélectionnez **Suivant : Réseau virtuel** et choisissez le type d’accès réseau que vous souhaitez autoriser pour votre ressource, puis sélectionnez **Vérifier + créer**.

---

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>Obtenir les clés pour votre ressource

1. Une fois votre ressource déployée avec succès, cliquez sur **Accéder à la ressource** sous **Étapes suivantes**.

    ![Rechercher Cognitive Services](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. Dans le volet de démarrage rapide qui s’ouvre, vous pouvez accéder à votre clé et à votre point de terminaison.

    ![Obtenir la clé et le point de terminaison](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources supprime également toutes les autres ressources se trouvant dans le groupe.

1. Sur le portail Azure, développez le menu de gauche pour ouvrir le menu des services, et sélectionnez **Groupes de ressources** pour afficher la liste de vos groupes de ressources.
2. Localisez le groupe de ressources contenant la ressource à supprimer.
3. Cliquez avec le bouton droit sur la liste des groupes de ressources. Sélectionnez **Supprimer le groupe de ressources** et confirmez.

Si vous devez récupérer une ressource supprimée, consultez [Récupérer des ressources Cognitive Services supprimées](manage-resources.md).

## <a name="see-also"></a>Voir aussi

* Consultez **[Authentifier des requêtes auprès d’Azure Cognitive Services](authentication.md)** qui explique la façon de travailler en toute sécurité avec Cognitive Services.
* Consultez **[Présentation d’Azure Cognitive Services](./what-are-cognitive-services.md)** pour obtenir la liste des différentes catégories présentes dans Cognitive Services.
* Consultez **[Prise en charge du langage naturel](language-support.md)** pour afficher la liste des langages naturels pris en charge par Cognitive Services.
* Consultez **[Utiliser Cognitive Services en tant que conteneurs](cognitive-services-container-support.md)** pour comprendre comment utiliser Cognitive Services en local.
* Consultez **[Planifier et gérer les coûts pour Cognitive Services](plan-manage-costs.md)** afin d’estimer le coût d’utilisation de Cognitive Services.
