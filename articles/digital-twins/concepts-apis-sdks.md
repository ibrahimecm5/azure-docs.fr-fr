---
title: API et SDK Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Découvrez les options d’API et de Kit de développement logiciel (SDK) Azure Digital Twins, dont des informations sur les classes d’assistance SDK et des notes d’utilisation générales.
author: baanders
ms.author: baanders
ms.date: 10/25/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 1fcbeeb532e813535cc8f3adcf02b2c2e990287e
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500200"
---
# <a name="azure-digital-twins-apis-and-sdks"></a>API et SDK Azure Digital Twins

Cet article donne une vue d’ensemble des API Azure Digital Twins disponibles, ainsi que des méthodes d’interaction avec elles. Vous pouvez utiliser les API REST directement avec les Swaggers qui y sont associés (via un outil tel que [Postman](how-to-use-postman.md)), ou via un Kit de développement logiciel (SDK).

Azure Digital Twins est fourni avec des **API de plan de contrôle**, des **API de plan de données** et des **kits de développement logiciel (SDK)** pour la gestion de votre instance et de ses éléments. 
* Les API de plan de contrôle sont des API [Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md) qui couvrent les opérations de gestion des ressources, telles que la création et la suppression de votre instance. 
* Les API de plan de données sont des API Azure Digital Twins qui sont utilisées pour les opérations de gestion des données, telles que la gestion des modèles, des représentations et du graphique.
* Les kits de développement logiciel (SDK) tirent parti des API existantes pour faciliter le développement d’applications personnalisées utilisant Azure Digital Twins. Les kits de développement logiciel (SDK) de plan de contrôle sont disponibles en [.NET (C#)](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true) et [Java](/java/api/overview/azure/digitaltwins/resourcemanagement?view=azure-java-stable&preserve-view=true), et les kits de développement logiciel (SDK) de plan de données sont disponibles en [.NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true), [Java](/java/api/overview/azure/digitaltwins/client?view=azure-java-stable&preserve-view=true), [JavaScript](/javascript/api/@azure/digital-twins-core/?view=azure-node-latest&preserve-view=true) et [python](/python/api/azure-digitaltwins-core/azure.digitaltwins.core?view=azure-python&preserve-view=true).

## <a name="overview-control-plane-apis"></a>Vue d’ensemble : API de plan de contrôle

Les API de plan de contrôle sont des API [ARM](../azure-resource-manager/management/overview.md) utilisées pour la gestion de votre instance Azure Digital Twins dans son ensemble. Elles permettent donc d’effectuer des opérations telles que la création ou la suppression de votre instance dans son intégralité. Vous utiliserez également ces API afin de créer et de supprimer des points de terminaison.

La version la plus récente de l’API de plan de contrôle est _**2020-12-01**_.

Pour utiliser les API de plan de contrôle :
* Vous pouvez appeler les API directement en référençant le dossier Swagger le plus récent dans le [référentiel Swagger du plan de contrôle](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/stable). Ce dossier contient également un dossier d’exemples qui en montrent l’utilisation.
* Vous pouvez accéder aux SDK des API de contrôle en...
  - [.NET (C#)](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/) ([référence [générée automatiquement]](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true)) ([source](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins))
  - [Java](https://search.maven.org/search?q=a:azure-mgmt-digitaltwins) ([référence [générée automatiquement]](/java/api/overview/azure/digitaltwins?view=azure-java-stable&preserve-view=true)) ([source](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins))
  - [JavaScript](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([source](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins))
  - [Python](https://pypi.org/project/azure-mgmt-digitaltwins/) ([source](https://github.com/Azure/azure-sdk-for-python/tree/release/v3/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [Go](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/services/digitaltwins/mgmt) ([source](https://github.com/Azure/azure-sdk-for-go/tree/master/services/digitaltwins/mgmt))

Vous pouvez également utiliser des API de plan de contrôle en interagissant avec Azure Digital Twins via le [portail Azure](https://portal.azure.com) et l’[interface de ligne de commande](/cli/azure/dt?view=azure-cli-latest&preserve-view=true).

## <a name="overview-data-plane-apis"></a>Vue d’ensemble : API de plan de données

Les API de plan de données sont les API Azure Digital Twins utilisées pour gérer les éléments compris dans votre instance Azure Digital Twins. Elles incluent des opérations telles que la création d’itinéraires, le chargement de modèles, la création de relations et la gestion de jumeaux, lesquelles peuvent être réparties dans les catégories suivantes :
* **DigitalTwinModels** : la catégorie DigitalTwinModels contient des API permettant de gérer des [modèles](concepts-models.md) dans une instance Azure Digital Twins. Les activités de gestion incluent le chargement, la validation, la récupération et la suppression des modèles créés dans DTDL.
* **DigitalTwins** : la catégorie DigitalTwins contient les API qui permettent aux développeurs de créer, de modifier et de supprimer les [jumeaux numériques](concepts-twins-graph.md) et leurs relations dans une instance Azure Digital Twins.
* **Query** : la catégorie Query permet aux développeurs de [trouver des jeux de jumeaux numériques dans le graphe jumeau](how-to-query-graph.md) parmi les relations.
* **Event Routes** : la catégorie Event Routes contient des API permettant d’[acheminer des données](concepts-route-events.md) via le système et vers les services en aval.

La version la plus récente de l’API de plan de données est _**2020-10-31**_.

Pour utiliser les API de plan de données :
* Vous pouvez appeler les API REST en...
   - Référençant le dossier Swagger le plus récent dans le [référentiel Swagger du plan de données](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Ce dossier contient également un dossier d’exemples qui en montrent l’utilisation. 
   - Consultant la [documentation de référence sur l’API](/rest/api/azure-digitaltwins/).
* Vous pouvez utiliser le **SDK .NET (C#)** . Pour utiliser le SDK .NET...
   - Vous pouvez afficher et ajouter le package de NuGet : [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core). 
   - Vous pouvez consulter la [documentation de référence du Kit de développement logiciel (SDK)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true).
   - Vous pouvez trouver la source du Kit de développement logiciel (SDK), y compris un dossier d’exemples, dans GitHub : [Bibliothèque de client Azure IoT Digital Twins pour .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). 
   - Vous pouvez obtenir des informations détaillées et des exemples d’utilisation en passant à la section [SDK .NET (C#) (plan de données)](#net-c-sdk-data-plane) de cet article.
* Vous pouvez utiliser le **SDK Java**. Pour utiliser le Kit de développement logiciel (SDK) Java…
   - Vous pouvez afficher et installer le package à partir de Maven : [`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar).
   - Vous pouvez consulter la [documentation de référence du Kit de développement logiciel (SDK)](/java/api/overview/azure/digitaltwins/client?view=azure-java-stable&preserve-view=true).
   - Vous pouvez trouver la source du Kit de développement logiciel (SDK) dans GitHub : [Bibliothèque de client Azure IoT Digital Twins pour Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core).
* Vous pouvez utiliser le **SDK JavaScript**. Pour utiliser le SDK JavaScript...
   - Vous pouvez afficher et installer le package à partir de npm : [Bibliothèque de client Core Azure Digital Twins pour JavaScript](https://www.npmjs.com/package/@azure/digital-twins-core).
   - Vous pouvez consulter la [documentation de référence du Kit de développement logiciel (SDK)](/javascript/api/@azure/digital-twins-core/?view=azure-node-latest&preserve-view=true).
   - Vous pouvez trouver la source du Kit de développement logiciel (SDK) dans GitHub : [Bibliothèque de client Core Azure Digital Twins pour JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core).
* Vous pouvez utiliser le **SDK Python**. Pour utiliser le Kit de développement logiciel (SDK) Python…
   - Vous pouvez afficher et installer le package à partir de PyPi : [Bibliothèque de client Core Azure Digital Twins pour Python](https://pypi.org/project/azure-digitaltwins-core/).
   - Vous pouvez consulter la [documentation de référence du Kit de développement logiciel (SDK)](/python/api/azure-digitaltwins-core/azure.digitaltwins.core?view=azure-python&preserve-view=true).
   - Vous pouvez trouver la source du Kit de développement logiciel (SDK) dans GitHub : [Bibliothèque de client Core Azure Digital Twins pour Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core).

Vous pouvez également utiliser des API de plan de données en interagissant avec Azure Digital Twins via l’[interface de ligne de commande](/cli/azure/dt?view=azure-cli-latest&preserve-view=true).

## <a name="net-c-sdk-data-plane"></a>SDK .NET (C#) (plan de données)

Le SDK .NET (C#) Azure Digital Twins fait partie du SDK Azure pour .NET. Il est open source et est basé sur les API de plan de données Azure Digital Twins.

> [!NOTE]
> Pour obtenir des informations détaillées sur la conception des kit de développement logiciel (SDK), consultez les [principes de conception pour les kits de développement logiciel (SDK) Azure](https://azure.github.io/azure-sdk/general_introduction.html) généraux, ainsi que les [instructions de conception de .NET](https://azure.github.io/azure-sdk/dotnet_introduction.html) spécifiques.

Pour utiliser le SDK, incluez le package NuGet **Azure.DigitalTwins.Core** dans votre projet. Vous aurez également besoin de la dernière version du package **Azure.Identity**. Dans Visual Studio, vous pouvez ajouter ces packages à l’aide du gestionnaire de package NuGet (accessible via *Outils > Gestionnaire de package NuGet > Gérer les packages NuGet pour la solution*). Vous pouvez également utiliser l’outil de ligne de commande .NET avec les commandes disponibles dans les liens des packages NuGet ci-dessous pour les ajouter à votre projet :
* [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core): Le package pour le [Kit de développement logiciel (SDK) Azure Digital Twins pour .NET](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). 
* [Azure.Identity](https://www.nuget.org/packages/Azure.Identity) : bibliothèque qui fournit des outils pour faciliter l’authentification auprès d’Azure.

Pour obtenir une procédure détaillée de l’utilisation des API dans la pratique, consultez le [Coder une application cliente](tutorial-code.md). 

### <a name="serialization-helpers"></a>Applications d’assistance de sérialisation

Les assistances de sérialisation sont des fonctions d’assistance disponibles dans le kit de développement logiciel (SDK) pour créer ou désérialiser rapidement des données de jumeaux pour accéder à des informations de base. Étant donné que les méthodes principales du kit de développement logiciel (SDK) retournent des données de jumeaux au format JSON par défaut, il peut être utile d’utiliser ces classes d’assistance pour décomposer les données de jumeaux.

Les classes d’assistance disponibles sont les suivantes :
* `BasicDigitalTwin` : représente de façon générique les données de base d’un jumeau numérique
* `BasicDigitalTwinComponent` : représente de façon générique un composant dans les propriétés `Contents` d’un `BasicDigitalTwin`
* `BasicRelationship` : représente de façon générique les données de base d’une relation
* `DigitalTwinsJsonPropertyName` : contient les constantes de chaîne à utiliser dans la sérialisation et la désérialisation JSON pour les types de jumeaux numériques personnalisés

## <a name="general-apisdk-usage-notes"></a>Remarques générales sur l’utilisation des API et des SDK

> [!NOTE]
> Notez qu’Azure Digital Twins ne prend pas actuellement en charge **Partage des ressources cross-origin (CORS)** . Pour plus d’informations sur les stratégies d’impact et de résolution, consultez la section [Partage des ressources cross-origin (CORS)](concepts-security.md#cross-origin-resource-sharing-cors)  de la rubrique *Concepts : Sécurité pour les solutions Azure Digital Twins*.

La liste suivante fournit des instructions générales et des détails supplémentaires concernant l’utilisation des API et des SDK.

* Vous pouvez utiliser un outil de test REST HTTP comme Postman pour effectuer des appels directs aux API Azure Digital Twins. Pour plus d’informations sur ce processus, consultez [Effectuer des demandes d’API avec Postman](how-to-use-postman.md).
* Pour utiliser le SDK, instanciez la classe `DigitalTwinsClient`. Le constructeur exige des informations d’identification qui peuvent être obtenues par le biais de diverses méthodes d’authentification dans le package `Azure.Identity`. Pour plus d’informations sur `Azure.Identity`, consultez la [documentation sur son espace de noms](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true). 
* Vous pourrez trouver `InteractiveBrowserCredential` utile dans les débuts. Toutefois, il existe plusieurs autres options, comme les informations d’identification pour l’[identité managée](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true), qui permettent d’authentifier les [fonctions Azure configurées avec MSI](../app-service/overview-managed-identity.md?tabs=dotnet) dans Azure Digital Twins. Pour plus d’informations sur `InteractiveBrowserCredential`, consultez la [documentation sur sa classe](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true).
* Les demandes envoyées aux API Azure Digital Twins nécessitent un utilisateur ou un principal de service qui fait partie du même locataire de[Répertoire actif Azure](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) où réside l’instance Azure Digital Twins. Pour empêcher l’analyse malveillante des points de terminaison Azure Digital Twins, les demandes avec des jetons d’accès qui ne proviennent pas du locataire d’origine recevront le message d’erreur « 404 Sous-domaine introuvable ». Cette erreur est retournée *même si* l’utilisateur ou le principal de service a reçu le rôle de Lecteur de données Azure Digital Twins Data ou de Propriétaire de données Azure Digital Twins Data par le biais de la collaboration [Azure AD B2B](../active-directory/external-identities/what-is-b2b.md). Pour savoir comment accéder à plusieurs locataires, consultez [ Écrire le code d’authentification de l’application](how-to-authenticate-client.md#authenticate-across-tenants).
* Tous les appels d’API de service sont exposés comme des fonctions membres dans la classe `DigitalTwinsClient`.
* Toutes les fonctions de service existent dans une version synchrone et une version asynchrone.
* Toutes les fonctions de service lèvent une exception pour tout état de retour de 400 (ou supérieur). Veillez à wrapper les appels dans une section `try` et à intercepter au moins `RequestFailedExceptions`. Pour plus d’informations sur ce type d’exception, consultez sa [documentation de référence](/dotnet/api/azure.requestfailedexception?view=azure-dotnet&preserve-view=true).
* La plupart des méthodes de service retournent `Response<T>` (ou `Task<Response<T>>` pour les appels asynchrones), où `T` correspond à la classe de l’objet de retour pour l’appel de service. La classe [Response](/dotnet/api/azure.response-1?view=azure-dotnet&preserve-view=true) encapsule le retour de service et présente les valeurs de retour dans son champ `Value`.  
* Les méthodes de service ayant des résultats paginés retournent des résultats `Pageable<T>` ou `AsyncPageable<T>`. Pour plus d’informations sur la classe `Pageable<T>`, consultez sa [documentation de référence](/dotnet/api/azure.pageable-1?view=azure-dotnet&preserve-view=true). Pour plus d’informations sur `AsyncPageable<T>`, consultez sa [documentation de référence](/dotnet/api/azure.asyncpageable-1?view=azure-dotnet&preserve-view=true).
* Vous pouvez effectuer une itération sur des résultats paginés à l’aide d’une boucle `await foreach`. Pour plus d’informations sur ce processus, consultez la [documentation correspondante](/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8).
* Le SDK sous-jacent est `Azure.Core`. Pour plus d’informations sur l’infrastructure et les types de SDK, consultez la [documentation sur les espaces de noms Azure](/dotnet/api/azure?view=azure-dotnet&preserve-view=true).


Les méthodes de service retournent des objets fortement typés dès que cela est possible. Toutefois, étant donné qu’Azure Digital Twins est basé sur des modèles personnalisés configurés par l’utilisateur au moment de l’exécution (via des modèles DTDL chargés dans le service), de nombreuses API de service acceptent et retournent des données de jumeau au format JSON.

## <a name="monitor-api-metrics"></a>Superviser les métriques d’API

Les métriques d’API, comme celles concernant les requêtes, la latence et le taux d’échec, peuvent être affichées dans le [portail Azure](https://portal.azure.com/). 

Dans la page d’accueil du portail, recherchez votre instance Azure Digital Twins pour consulter les informations la concernant. Sélectionnez l’option **Métriques** dans le menu de l’instance Azure Digital Twins pour afficher la page *Métriques*.

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Capture d’écran montrant la page des métriques pour Azure Digital Twins.":::

À partir de là, vous pouvez afficher les métriques de votre instance et créer des vues personnalisées.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment adresser des demandes directes aux API à l’aide de Postman :
* [Faire des demandes d’API avec Postman](how-to-use-postman.md)

Ou bien, dans la pratique, utilisez le Kit de développement logiciel (SDK) .NET en créant une application cliente à l’aide de ce tutoriel :
* [Coder une application cliente](tutorial-code.md)
