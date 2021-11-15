---
title: Contrôle d’accès en fonction du rôle LUIS
titleSuffix: Azure Cognitive Services
description: Dans cet article, découvrez comment ajouter un contrôle d’accès à votre ressource LUIS.
author: aahill
ms.author: aahi
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/09/2021
ms.topic: conceptual
ms.openlocfilehash: 5651f4241cb206413dc0a669965c006efce042b6
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894944"
---
# <a name="luis-role-based-access-control"></a>Contrôle d’accès en fonction du rôle LUIS

LUIS prend en charge le contrôle d’accès en fonction du rôle Azure (Azure RBAC), système d’autorisation pour la gestion des accès individuels aux ressources Azure. À l’aide d’Azure RBAC, vous attribuez à différents membres d’équipe différents niveaux d’autorisations pour vos ressources de création LUIS. Pour plus d’informations, consultez la [documentation Azure RBAC](/azure/role-based-access-control/).

## <a name="add-role-assignment-to-language-understanding-authoring-resource"></a>Ajouter une attribution de rôle à la ressource de création Language Understanding

Azure RBAC peut être attribué à une ressource de création Language Understanding. Pour accorder l’accès à une ressource Azure, vous ajoutez une attribution de rôle.
1. Dans le [portail Azure](https://ms.portal.azure.com/), sélectionnez **Tous les services**. 
2. Sélectionnez **Cognitive Services** et accédez à votre ressource de création Language Understanding spécifique.
   > [!NOTE]
   > Vous pouvez également configurer Azure RBAC pour des groupes de ressources, des abonnements ou des groupes d’administration complets. Pour ce faire, sélectionnez le niveau d’étendue souhaité, puis accédez à l’élément souhaité. Par exemple, en sélectionnant **Groupes de ressources**, puis en accédant à un groupe de ressources spécifique.

1. Sélectionnez **Contrôle d’accès (IAM)** dans le menu de navigation de gauche.
1. Sélectionnez **Ajouter**, puis **Ajouter une attribution de rôle**.
1. Dans l’onglet **Rôle** de l’écran suivant, sélectionnez le rôle que vous souhaitez ajouter.
1. Dans l’onglet **Membres**, sélectionnez un utilisateur, un groupe, un principal de service ou une identité managée.
1. Dans l’onglet **Passer en revue + affecter**, sélectionnez **Passer en revue + affecter** pour affecter le rôle.

Au bout de quelques minutes, le rôle sélectionné est attribué à la cible dans l’étendue sélectionnée. Pour recevoir del’aide relative à ces étapes, consultez [Attribuer des rôles Azure à l’aide du Portail Azure](/azure/role-based-access-control/role-assignments-portal).

## <a name="security"></a>Sécurité 

LU prend en charge l’authentification Azure Active Directory (AAD). Pour plus d’informations, consultez [S’authentifier avec Azure Active Directory](/azure/cognitive-services/authentication#authenticate-with-azure-active-directory).

## <a name="luis-role-types"></a>Types de rôles LUIS

Utilisez le tableau suivant pour déterminer les besoins d’accès de votre application LUIS.

Ces rôles personnalisés s’appliquent uniquement aux ressources de création (Création Language Understanding) et non aux ressources de prédiction (Language Understanding).

### <a name="cognitive-services-luis-reader"></a>Lecteur LUIS Cognitive Services

Un utilisateur qui doit uniquement valider et examiner les applications LUIS, généralement un testeur qui s’assure que l’application fonctionne correctement avant de déployer le projet. Il peut souhaiter passer en revue les ressources de l’application (énoncés, intentions, entités) pour informer les développeurs d’applications des modifications qui doivent être apportées, mais sans accès direct pour effectuer ces opérations.


:::row:::
    :::column span="":::
        **Capabilities**
    :::column-end:::
    :::column span="":::
        **Accès à l’interface de programmation d’applications**
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        * Lire des énoncés
        * Intentions 
        * Entités
        * Tester l’application
    :::column-end:::
    :::column span="":::
      * Toutes les API GET sous : 
         * [LUIS Programmatic v3.0-preview](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f)
         * [API LUIS Programmatic v2.0](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)
      * Toutes les API sous : 
         * [API LUIS Endpoint v2.0](https://chinaeast2.dev.cognitive.azure.cn/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)
         * [API LUIS Endpoint v3.0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/operations/5cb0a9459a1fe8fa44c28dd8)
         * [API LUIS Endpoint v3.0-preview](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0-preview/operations/5cb0a9459a1fe8fa44c28dd8)
      * Toutes les API web de test par lot
    :::column-end:::
:::row-end:::

### <a name="cognitive-services-luis-writer"></a>Rédacteur LUIS Cognitive Services

Utilisateur responsable de la création et de la modification de l’application LUIS, agissant en tant que collaborateur dans une équipe de plus grande taille. Le collaborateur peut modifier l’application LUIS comme il le souhaite, effectuer l’apprentissage de ces modifications et valider/tester ces modifications dans le portail. Toutefois, cet utilisateur n’a pas accès au déploiement de cette application dans le runtime, car il peut refléter accidentellement ses modifications dans un environnement de production. Il ne peut pas non plus supprimer l’application ni modifier ses ressources de prédiction et ses paramètres de point de terminaison (en atttribuant ou en désattribuant des ressources de prédiction, rendant ainsi le point de terminaison public). Cela empêche ce rôle de modifier une application en cours d’utilisation dans un environnement de production. Il peut également créer des applications sous cette ressource, mais avec les restrictions mentionnées.

:::row:::
    :::column span="":::
        **Capabilities**
    :::column-end:::
    :::column span="":::
        **Accès à l’interface de programmation d’applications**
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
      * Toutes les fonctionnalités sous le lecteur LUIS Cognitive Services.
      * Possibilité d’ajouter : 
          * Énoncés
          * Intentions
          * Entités
    :::column-end:::
    :::column span="":::
      * Toutes les API sous le lecteur LUIS
      * Toutes les API POST, PUT et DELETE sous :
         * [LUIS Programmatic v3.0-preview](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f)
         * [API LUIS Programmatic v2.0](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2d)

          À l’exception de
          * [Supprimer une application](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c39)
          * [Déplacer une application vers une autre ressource Azure de création LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/apps-move-app-to-another-luis-authoring-azure-resource)
          * [Publier une application](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c3b)
          * [ Mettre à jour les paramètres d’application](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/58aeface39e2bb03dcd5909e)
          * [Attribuer des comptes Azure LUIS à une application](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5be32228e8473de116325515)
          * [Supprimer des comptes Azure LUIS affectés d’une application](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5be32554f8591db3a86232e1)
    :::column-end:::
:::row-end:::

### <a name="cognitive-services-luis-owner"></a>Propriétaire LUIS Cognitive Services

Ces utilisateurs sont les gardiens des applications LUIS dans un environnement de production. Ils doivent avoir un accès complet à toutes les fonctions sous-jacentes et peuvent donc afficher tous les éléments de l’application et disposer d’un accès direct pour éditer des modifications à la fois pour les environnements de création et d’exécution.

:::row:::
    :::column span="":::
        **Fonctionnalité**
    :::column-end:::
    :::column span="":::
        **Accès à l’interface de programmation d’applications**
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
      * Toutes les fonctionnalités sous le rédacteur LUIS Cognitive Services
      * Déployer un modèle
      * Supprimer une application
    :::column-end:::
    :::column span="":::
      * Toutes les API disponibles pour LUIS
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Étapes suivantes

* [Gestion des ressources Azure](/azure/cognitive-services/luis/luis-how-to-azure-subscription?branch=pr-en-us-171715&tabs=portal#authoring-resource)
