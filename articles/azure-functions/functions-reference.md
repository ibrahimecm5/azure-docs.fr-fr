---
title: Conseils relatifs au développement d'Azure Functions
description: Découvrez les concepts et techniques Azure Functions dont vous avez besoin pour développer des fonctions dans Azure, et ce, pour tous les langages de programmation et toutes les liaisons.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 9/02/2021
ms.openlocfilehash: 7aef7301207772711bcce7fbec4bde8937c94cf1
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523445"
---
# <a name="azure-functions-developer-guide"></a>Guide du développeur Azure Functions
Dans Azure Functions, des fonctions spécifiques partagent quelques concepts techniques et composants de base, quels que soient le langage et la liaison que vous utilisez. Avant de passer à l'apprentissage des détails propres à un langage ou une liaison donnés, veillez à lire cette présentation qui s'applique à l’ensemble d’entre eux.

Cet article suppose que vous avez déjà lu la [Présentation d’Azure Functions](functions-overview.md).

## <a name="function-code"></a>Code de fonction
La *fonction* est le principal concept d'Azure Functions. Une fonction contient deux éléments importants - votre code, qui peut être écrit dans un certain nombre de langages, et, dans certaines configurations, le fichier function.json. Pour les langages compilés, ce fichier de configuration est généré automatiquement à partir des annotations de votre code. Pour les langages de script, vous devez fournir le fichier de configuration vous-même.

Le fichier function.json définit le déclencheur, les liaisons de fonction et d’autres paramètres de configuration. Chaque fonction possède un seul déclencheur. Le runtime utilise ce fichier de configuration pour déterminer les événements à surveiller et comment passer des données et renvoyer des données à partir de l’exécution d’une fonction. Voici un exemple de fichier function.json.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Pour plus d’informations, consultez [Concepts des déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md).

La propriété `bindings` vous permet de configurer les liaisons et les déclencheurs. Chaque liaison partage quelques paramètres communs et des paramètres propres à un type de liaison donné. Chaque liaison requiert les paramètres suivants :

| Propriété    | Valeurs | Type | Commentaires|
|---|---|---|---|
| type  | Nom de la liaison.<br><br>Par exemple : `queueTrigger`. | string | |
| direction | `in`, `out`  | string | Indique si la liaison sert à recevoir des données dans la fonction ou à envoyer des données à partir de la fonction. |
| name | Identificateur de fonction.<br><br>Par exemple : `myQueue`. | string | Le nom utilisé pour les données liées dans la fonction. Pour C#, il s’agit d’un nom d'argument ; pour JavaScript, il s’agit de la clé dans une liste de clés/valeurs. |

## <a name="function-app"></a>Conteneur de fonctions
Une application de fonction fournit un contexte d’exécution dans Azure dans lequel vos fonctions s’exécutent. À ce titre, elle constitue l'unité de déploiement et de gestion de vos fonctions. Un conteneur de fonctions est constitué d’une ou de plusieurs des fonctions individuelles qui sont gérées, déployées et mises à l’échelle ensemble. Toutes les fonctions d'une application de fonction partagent le même plan de tarification, la même méthode de déploiement et la même version du runtime. Considérez un conteneur de fonctions comme un moyen d’organiser et de gérer collectivement vos fonctions. Pour en savoir plus, consultez [Gérer une application de fonction](functions-how-to-use-azure-function-app-settings.md). 

> [!NOTE]
> Toutes les fonctions d’une application de fonction doivent être créées dans le même langage. Dans les [version précédentes](functions-versions.md) du runtime Azure Functions, cela n’était pas obligatoire.

## <a name="folder-structure"></a>Structure de dossiers
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

La structure de dossiers ci-dessus est recommandée et utilisée par défaut pour une application de fonction. Si vous souhaitez changer l’emplacement du code d’une fonction, modifiez la section `scriptFile` du fichier _function.json_. Nous vous recommandons également d’utiliser un [package de déploiement](deployment-zip-push.md) pour déployer votre projet sur votre application de fonction dans Azure. Vous pouvez également utiliser des outils existants tels que [l’intégration et le déploiement continus](functions-continuous-deployment.md) et Azure DevOps.

> [!NOTE]
> En cas de déploiement manuel d’un package, veillez à déployer votre fichier _host.json_ et vos dossiers de fonction directement dans le dossier `wwwroot`. N’incluez pas le dossier `wwwroot` dans vos déploiements. Sinon, vous vous retrouverez avec `wwwroot\wwwroot` dossiers.

#### <a name="use-local-tools-and-publishing"></a>Utiliser des outils locaux pour la publication
Vous pouvez créer et publier des applications de fonction à l’aide de différents outils, notamment [Visual Studio](./functions-develop-vs.md), [Visual Studio Code](./create-first-function-vs-code-csharp.md), [IntelliJ](./functions-create-maven-intellij.md), [Eclipse](./functions-create-maven-eclipse.md) et [Azure Functions Core Tools](./functions-develop-local.md). Pour plus d’informations, consultez [Coder et tester Azure Functions localement](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a name="how-to-edit-functions-in-the-azure-portal"></a><a id="fileupdate"></a> Comment modifier des fonctions dans le portail Azure
L’éditeur de fonctions intégré au portail Azure vous permet de mettre à jour votre code et votre fichier *function.json* directement en ligne. Cela n’est recommandé que pour les petites modifications ou les preuves de concept - la meilleure pratique est d’utiliser un outil de développement local comme VS Code.

## <a name="parallel-execution"></a>Exécution parallèle
Lorsque plusieurs événements de déclenchement se produisent plus rapidement qu'un runtime de fonction monothread ne peut les traiter, le runtime peut appeler la fonction plusieurs fois en parallèle.  Si un conteneur de fonctions utilise le [plan d’hébergement de consommation](event-driven-scaling.md), il peut monter automatiquement effectuer un scale-out.  Que l’application s’exécute sur le plan d’hébergement de consommation ou sur un [plan d’hébergement App Service](../app-service/overview-hosting-plans.md) standard, chaque instance de l’application de fonction peut traiter en parallèle des appels de fonction simultanés en utilisant plusieurs threads.  Le nombre maximal d’appels de fonction simultanés dans chaque instance de chaque application de fonction varie en fonction du type de déclencheur utilisé, ainsi que des ressources utilisées par d’autres fonctions au sein de l’application de fonction.

## <a name="functions-runtime-versioning"></a>Contrôle de version du runtime Functions

Vous pouvez configurer la version du runtime Functions en utilisant le paramètre d’application `FUNCTIONS_EXTENSION_VERSION`. Par exemple, la valeur « ~3 » indique que votre application de fonction utilise 3.x comme version principale. Les applications de fonction sont mises à niveau vers chaque nouvelle version mineure lorsqu’elles sont disponibles. Pour obtenir plus d’informations, notamment sur la façon d’afficher la version exacte de votre application de fonction, consultez [Guide pratique pour cibler des versions du runtime Azure Functions](set-runtime-version.md).

## <a name="repositories"></a>Référentiels
Le code pour Azure Fonctions est open source et stocké dans des dépôts GitHub :

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Hôte Azure Functions](https://github.com/Azure/azure-functions-host/)
* [Portail Azure Functions](https://github.com/azure/azure-functions-ux)
* [Modèles Azure Functions](https://github.com/azure/azure-functions-templates)
* [Kit de développement logiciel Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/)
* [Extensions du kit de développement logiciel Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Liaisons
Voici un tableau de toutes les liaisons prises en charge.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Vous rencontrez des problèmes avec des erreurs de liaisons ? Consultez la documentation [Codes d’erreur de liaison d’Azure Functions](functions-bindings-error-pages.md).


## <a name="connections"></a>Connexions

Votre projet de fonction référence des informations de connexion par nom à partir de son fournisseur de configuration. Il n’accepte pas directement les détails de la connexion, ce qui leur permet d’être changés dans l’ensemble des environnements. Par exemple, une définition de déclencheur peut inclure une propriété `connection`. Celle-ci peut faire référence à une chaîne de connexion, mais vous ne pouvez pas définir la chaîne de connexion directement dans un `function.json`. Au lieu de cela, vous devez définir `connection` sur le nom d’une variable d’environnement qui contient la chaîne de connexion.

Le fournisseur de configuration par défaut utilise des variables d’environnement. Celles-ci peuvent être définies par [paramètres d’application](./functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings) lors de l’exécution dans le service Azure Functions, ou à partir du [fichier de paramètres local](functions-develop-local.md#local-settings-file) lors du développement local.

### <a name="connection-values"></a>Valeurs de connexion

Quand le nom de la connexion correspond à une seule valeur exacte, le runtime identifie la valeur en tant que _chaîne de connexion_, qui comprend généralement un secret. Les détails d’une chaîne de connexion sont définis par le service auquel vous souhaitez vous connecter.

Toutefois, un nom de connexion peut également faire référence à un ensemble de plusieurs éléments de configuration, utiles pour la configuration des [connexions basées sur l’identité](#configure-an-identity-based-connection). Les variables d’environnement peuvent être traitées comme une collection à l’aide d’un préfixe partagé qui se termine par deux traits de soulignement `__`. Il est ensuite possible de référencer le groupe en définissant le nom de la connexion sur ce préfixe.

Par exemple, la propriété `connection` d’une définition de déclencheur Blob Azure peut être « Storage1 ». Tant qu’aucune valeur de chaîne unique n’est configurée par une variable d’environnement nommée « Storage1 », une variable d’environnement nommée `Storage1__blobServiceUri` peut être utilisée pour informer la propriété `blobServiceUri` de la connexion. Les propriétés de connexion sont différentes pour chaque service. Reportez-vous à la documentation du composant qui utilise la connexion.

### <a name="configure-an-identity-based-connection"></a>Configurer une connexion basée sur une identité

Dans Azure Functions, certaines connexions peuvent être configurées pour utiliser une identité plutôt qu’un secret. La prise en charge dépend de l’extension qui utilise la connexion. Dans certains cas, une chaîne de connexion peut toujours être nécessaire dans Functions, même si le service auquel vous vous connectez prend en charge les connexions basées sur une identité. Pour obtenir un tutoriel sur la configuration de vos applications de fonction avec des identités managées, consultez le [tutoriel Création d’une application de fonction avec des connexions basées sur l’identité](./functions-identity-based-connections-tutorial.md).

Les connexions basées sur une identité sont prises en charge par les composants suivants :

| Source de connexion                                       | Plans pris en charge | En savoir plus                                                                                                         |
|---------------------------------------------------------|-----------------|--------------------------------------------------------------------------------------------------------------------|
| Déclencheurs et liaisons d’objets blob Azure               | Tous             | [L’extension version 5.0.0 ou ultérieure](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)     |
| Déclencheurs et liaisons de File d’attente Azure            | Tous             | [L’extension version 5.0.0 ou ultérieure](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher)    |
| Déclencheurs et liaisons Azure Event Hubs     | Tous             | [L’extension version 5.0.0 ou ultérieure](./functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher)    |
| Déclencheurs et liaisons Azure Service Bus       | Tous             | [L’extension version 5.0.0 ou ultérieure](./functions-bindings-service-bus.md#service-bus-extension-5x-and-higher)  |
| Déclencheurs et liaisons Azure Cosmos DB - Préversion         | Premium élastique | [Extension version 4.0.0-preview1 ou ultérieure](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher) |
| Stockage exigé par l’hôte (« AzureWebJobsStorage ») - Préversion | Tous             | [Connexion au stockage hôte avec une identité](#connecting-to-host-storage-with-an-identity-preview)                        |

> [!NOTE]
> Les connexions basées sur une identité ne sont pas prises en charge avec Durable Functions.

[!INCLUDE [functions-identity-based-connections-configuration](../../includes/functions-identity-based-connections-configuration.md)]

Choisissez un onglet ci-dessous afin d’en savoir plus sur les autorisations pour chaque composant :

# <a name="azure-blobs-extension"></a>[Extension d’objets blob Azure](#tab/blob)

[!INCLUDE [functions-blob-permissions](../../includes/functions-blob-permissions.md)]

# <a name="azure-queues-extension"></a>[Extension de files d’attente Azure](#tab/queue)

[!INCLUDE [functions-queue-permissions](../../includes/functions-queue-permissions.md)]

# <a name="event-hubs-extension"></a>[Extension Event Hubs](#tab/eventhubs)

[!INCLUDE [functions-event-hubs-permissions](../../includes/functions-event-hubs-permissions.md)]

# <a name="service-bus-extension"></a>[Extension Service Bus](#tab/servicebus)

[!INCLUDE [functions-service-bus-permissions](../../includes/functions-service-bus-permissions.md)]

# <a name="azure-cosmos-db-extension-preview"></a>[Extension Azure Cosmos DB (préversion)](#tab/cosmos)

[!INCLUDE [functions-cosmos-permissions](../../includes/functions-cosmos-permissions.md)]

# <a name="functions-host-storage-preview"></a>[Stockage hôte Functions (préversion)](#tab/azurewebjobsstorage)

[!INCLUDE [functions-azurewebjobsstorage-permissions](../../includes/functions-azurewebjobsstorage-permissions.md)]

---

#### <a name="common-properties-for-identity-based-connections"></a>Propriétés courantes pour les connexions basées sur une identité

Une connexion basée sur une identité pour un service Azure accepte les propriétés courantes suivantes, où `<CONNECTION_NAME_PREFIX>` est la valeur de votre propriété `connection` dans la définition de déclencheur ou de liaison :

| Propriété    |  Modèle de variable d’environnement | Description |
|---|---|---|---|
| Informations d’identification du jeton |  `<CONNECTION_NAME_PREFIX>__credential` | Définit la façon dont un jeton doit être obtenu pour la connexion. Recommandé uniquement lors de la spécification d’une identité attribuée par l’utilisateur, quand elle doit être définie sur « managedidentity ». Valide uniquement si hébergé dans le service Azure Functions. |
| ID client | `<CONNECTION_NAME_PREFIX>__clientId` | Lorsque `credential` est a la valeur « managedidentity », cette propriété spécifie l’identité attribuée par l’utilisateur à utiliser lors de l’obtention d’un jeton. La propriété accepte un ID client correspondant à une identité attribuée par l’utilisateur affectée à l’application. Par défaut, l’identité affectée par le système de l’application est utilisée. Cette propriété est utilisée différemment dans des [scénarios de développement local](#local-development-with-identity-based-connections) lorsque `credential` ne doit pas être défini. |

Des options supplémentaires peuvent être prises en charge pour un type de connexion donné. Reportez-vous à la documentation du composant qui effectue la connexion.

##### <a name="local-development-with-identity-based-connections"></a>Développement local avec connexions basées sur une identité

> [!NOTE]
> Le développement local avec des connexions basées sur l’identité nécessite des versions mises à jour d’[Azure Functions Core Tools](./functions-run-local.md). Vous pouvez vérifier la version installée en exécutant `func -v`. Pour Functions v3, utilisez la version `3.0.3904` ou une version ultérieure. Pour Functions v4, utilisez la version `4.0.3904` ou une version ultérieure. 

Lors d’une exécution locale, la configuration ci-dessus indique au runtime d’utiliser votre identité de développeur locale. La connexion tente d’obtenir un jeton à partir des emplacements suivants, dans l’ordre :

- Un cache local partagé entre les applications Microsoft
- Le contexte utilisateur actuel dans Visual Studio
- Le contexte utilisateur actuel dans Visual Studio Code
- Le contexte utilisateur actuel dans Azure CLI

Si aucune de ces options ne fonctionne, une erreur se produit.

Étant donné que la configuration utilise votre identité de développeur, vous avez peut-être déjà des rôles sur des ressources de développement, mais il est possible qu’ils ne fournissent pas l’accès aux données. Les rôles de gestion comme [Propriétaire](../role-based-access-control/built-in-roles.md#owner) ne sont pas suffisants. Revérifiez quelles autorisations sont nécessaires pour les connexions de chaque composant et vérifiez qu’elles vous sont affectées.

Dans certains cas, vous souhaiterez peut-être spécifier l’utilisation d’une identité différente. Pour la connexion, vous pouvez ajouter des propriétés de configuration qui pointent vers l’identité de substitution en fonction d’un ID client et d’un secret client pour un principal de service Azure Active Directory. **Cette option de configuration n’est pas prise en charge quand elle est hébergée dans le service Azure Functions.** Pour utiliser un ID et un secret sur votre ordinateur local, définissez la connexion avec les propriétés supplémentaires suivantes :

| Propriété    | Modèle de variable d’environnement | Description |
|---|---|---|
| ID client | `<CONNECTION_NAME_PREFIX>__tenantId` | ID du locataire Azure Active Directory (annuaire). |
| ID client | `<CONNECTION_NAME_PREFIX>__clientId` |  ID client (application) d’une inscription d’application dans le locataire. |
| Clé secrète client | `<CONNECTION_NAME_PREFIX>__clientSecret` | Un secret client qui a été généré pour l’inscription de l’application. |

Voici un exemple de propriétés `local.settings.json` obligatoires pour une connexion basée sur une identité à des objets blob Azure : 

```json
{
  "IsEncrypted": false,
  "Values": {
    "<CONNECTION_NAME_PREFIX>__blobServiceUri": "<blobServiceUri>",
    "<CONNECTION_NAME_PREFIX>__queueServiceUri": "<queueServiceUri>",
    "<CONNECTION_NAME_PREFIX>__tenantId": "<tenantId>",
    "<CONNECTION_NAME_PREFIX>__clientId": "<clientId>",
    "<CONNECTION_NAME_PREFIX>__clientSecret": "<clientSecret>"
  }
}
```

#### <a name="connecting-to-host-storage-with-an-identity-preview"></a>Connexion à un stockage hôte avec une identité (préversion)

Par défaut, Azure Functions utilise la connexion « AzureWebJobsStorage » pour les comportements de base comme la coordination de l’exécution unique de déclencheurs de minuteur et du stockage de clés d’application par défaut. Cela peut également être configuré pour tirer parti d’une identité.

> [!CAUTION]
> Dans Functions, d’autres composants reposent sur « AzureWebJobsStorage » pour les comportements par défaut. Vous ne devez pas la déplacer vers une connexion basée sur une identité si vous utilisez des versions antérieures des extensions qui ne prennent pas en charge ce type de connexion, ce qui inclut les déclencheurs et les liaisons pour les objets blob Azure et les hubs d’événement. De même, `AzureWebJobsStorage` est utilisé pour les artefacts de déploiement lors de l’utilisation de la génération côté serveur dans Linux, et si vous l’activez, vous devrez effectuer le déploiement via [un package de déploiement externe](/run-functions-from-deployment-package).
>
> De plus, certaines applications réutilisent « AzureWebJobsStorage » pour d’autres connexions de stockage dans leurs déclencheurs, liaisons et/ou code de fonction. Vérifiez que toutes les utilisations de « AzureWebJobsStorage » peuvent utiliser le format de connexion basée sur une identité avant de modifier cette connexion à partir d’une chaîne de connexion.

Pour utiliser une connexion basée sur une identité pour « AzureWebJobsStorage », configurez les paramètres d’application suivants :

| Paramètre                       | Description                                | Valeur d'exemple                                        |
|-----------------------------------------------------|--------------------------------------------|------------------------------------------------|
| `AzureWebJobsStorage__blobServiceUri`| URI de plan de données du service BLOB du compte de stockage, utilisant le schéma HTTPS. | https://<storage_account_name>.blob.core.windows.net |
| `AzureWebJobsStorage__queueServiceUri` | URI de plan de données du service File d’attente du compte de stockage, utilisant le schéma HTTPS. | https://<storage_account_name>.queue.core.windows.net |

Il est également possible de définir des [propriétés courantes pour les connexions basées sur une identité](#common-properties-for-identity-based-connections).

Si vous utilisez un compte de stockage qui utilise le suffixe DNS et le nom de service par défaut pour Azure global, en respectant le format `https://<accountName>.blob/queue/file/table.core.windows.net`, vous pouvez à la place définir `AzureWebJobsStorage__accountName` sur le nom de votre compte de stockage. Les points de terminaison d’objet blob et de file d’attente seront déduits pour ce compte. Cela ne fonctionnera pas si le compte de stockage se trouve dans un cloud souverain ou s’il a un DNS personnalisé.

| Paramètre                       | Description                                | Valeur d'exemple                                        |
|-----------------------------------------------------|--------------------------------------------|------------------------------------------------|
| `AzureWebJobsStorage__accountName` | Nom de compte d’un compte de stockage, valide uniquement si le compte n’est pas dans un cloud souverain et n’a pas de DNS personnalisé. | <storage_account_name> |

[!INCLUDE [functions-azurewebjobsstorage-permissions](../../includes/functions-azurewebjobsstorage-permissions.md)]

## <a name="reporting-issues"></a>Problèmes liés aux rapports
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez les ressources suivantes :

* [Azure Functions triggers and bindings (Déclencheurs et liaisons Azure Functions)](functions-triggers-bindings.md)
* [Coder et tester Azure Functions localement](./functions-develop-local.md)
* [Meilleures pratiques pour Azure Functions](functions-best-practices.md)
* [Informations de référence pour les développeurs C# sur Azure Functions](functions-dotnet-class-library.md)
* [Informations de référence pour les développeurs Node.js sur Azure Functions](functions-reference-node.md)
