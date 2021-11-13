---
title: Utiliser des connexions basées sur l’identité avec des déclencheurs et des liaisons Azure Functions
ms.service: azure-functions
description: Découvrez comment utiliser des connexions basées sur l’identité plutôt que des chaînes de connexion lors de la connexion à une file d’attente Service Bus à l’aide d’Azure Functions.
ms.topic: tutorial
ms.date: 10/20/2021
ms.openlocfilehash: 5a3eb4dc6006d9072abac95b7940692e8af737a5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130271172"
---
# <a name="tutorial-use-identity-based-connections-instead-of-secrets-with-triggers-and-bindings"></a>Tutoriel : Utiliser des connexions basées sur l’identité plutôt que des secrets avec des déclencheurs et des liaisons

Ce tutoriel vous montre comment configurer Azure Functions pour vous connecter à des files d’attente Azure Service Bus à l’aide d’identités managées plutôt que de secrets stockés dans les paramètres de l’application de fonction. Ce tutoriel est la suite du tutoriel [Créer une application de fonction sans secrets de stockage par défaut dans sa définition][tutoriel précédent]. Pour en savoir plus sur les connexions basées sur l’identité, consultez [Configurer une connexion basée sur l’identité](functions-reference.md#configure-an-identity-based-connection).

Même si les procédures indiquées fonctionnent généralement pour tous les langages, ce tutoriel prend plus particulièrement en charge les fonctions de bibliothèque de classes C# sur Windows. 

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
>
> * Créez un espace de noms et une file d’attente Service Bus.
> * Configurer votre application de fonction avec une identité managée
> * Créer une attribution de rôle accordant à cette identité l’autorisation de lire à partir de la file d’attente Service Bus
> * Créer et déployer une application de fonction avec un déclencheur Service Bus.
> * Vérifier votre connexion basée sur l’identité à Service Bus

## <a name="prerequisite"></a>Configuration requise

Suivez le tutoriel précédent : [Créer une application de fonction avec des connexions basées sur l’identité][tutoriel précédent].

## <a name="create-a-service-bus-and-queue"></a>Créer un bus de service et une file d’attente

1. Dans le [portail Azure](https://portal.azure.com), choisissez **Créer une ressource (+)** .

1. Dans la page **Créer une ressource**, sélectionnez **Intégration** > **Service Bus**.

1. Dans la page **Informations de base**, utilisez le tableau suivant pour configurer les paramètres d’espace de noms Service Bus. Utilisez les valeurs par défaut pour les options restantes.

    | Option      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Votre abonnement | Abonnement sous lequel vos ressources sont créées. |
    | **[Groupe de ressources](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Le groupe de ressources que vous avez créé avec votre application de fonction. |
    | **Nom de l’espace de noms** | Nom globalement unique | Espace de noms de votre instance à partir duquel déclencher votre fonction. Étant donné que l’espace de noms est accessible publiquement, vous devez utiliser un nom qui est globalement unique dans Azure. Le nom doit également comporter entre 6 et 50 caractères, contenir uniquement des caractères alphanumériques et des tirets, et ne pas commencer par un chiffre. |
    | **[Emplacement](https://azure.microsoft.com/regions/)** | myFunctionRegion | La région dans laquelle vous avez créé votre application de fonction. |
    | **Niveau tarifaire** | De base | Niveau Service Bus de base. |

1. Sélectionnez **Revoir + créer**. Une fois la validation terminée, sélectionnez **Créer**.

1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.

1. Dans votre espace de noms Service Bus, sélectionnez **+ File d’attente** pour ajouter une file d’attente.

1. Tapez `myinputqueue` comme nom de la nouvelle file d’attente, puis sélectionnez **Créer**.

Maintenant que vous avez une file d’attente, vous allez ajouter une attribution de rôle à l’identité managée de votre espace de noms Service Bus.

## <a name="configure-your-service-bus-trigger-with-a-managed-identity"></a>Configurer votre déclencheur Service Bus avec une identité managée

Pour utiliser des déclencheurs Service Bus avec des connexions basées sur l’identité, vous devez ajouter l’attribution du rôle **Récepteur de données Azure Service Bus** à l’identité managée dans votre application de fonction. Ce rôle est requis lors de l’utilisation d’identités managées à déclencher à partir de votre espace de noms Service Bus. Vous pouvez également ajouter votre propre compte à ce rôle, ce qui permet de se connecter à l’espace de noms Service Bus au cours du test local.

> [!NOTE]
> Les exigences de rôle pour l’utilisation de connexions basées sur l’identité varient en fonction du service et de la façon dont vous vous y connectez. Les besoins varient selon les déclencheurs, les liaisons d’entrée et les liaisons de sortie. Pour plus d’informations sur des exigences de rôle spécifiques, reportez-vous à la documentation du service relative aux déclencheurs et aux liaisons.

1. Dans l’espace de noms Service Bus que vous venez de créer, sélectionnez **Contrôle d’accès (IAM)** . C’est là que vous pouvez voir et configurer qui a accès à la ressource.  

1. Cliquez sur **Ajouter**, puis sélectionnez **Ajouter une attribution de rôle**.

1. Recherchez **Récepteur de données Azure Service Bus**, sélectionnez-le, puis cliquez sur **Suivant**.

1. Dans l’onglet **Membres**, sous **Attribuer l’accès à**, choisissez **Identité managée**

1. Cliquez sur **Sélectionner des membres** pour ouvrir le volet **Sélectionner des identités managées**.

1. Vérifiez que l’**Abonnement** est celui dans lequel vous avez créé les ressources précédemment.

1. Dans le sélecteur **Identité managée**, choisissez **Application de fonction** dans la catégorie **Identité managée affectée par le système**. Un nombre entre parenthèses peut être affiché en regard de l’étiquette « Application de fonction », indiquant le nombre d’applications de l’abonnement ayant des identités affectées par le système.

1. Votre application doit apparaître dans une liste sous les champs d’entrée. Si vous ne la voyez pas, vous pouvez utiliser la zone **Sélectionner** pour filtrer les résultats avec le nom de votre application.

1. Cliquez sur votre application. Elle doit descendre dans la section **Membres sélectionnés**. Cliquez sur **Sélectionner**.

1. De retour dans l’écran **Ajouter une attribution de rôle**, cliquez sur **Vérifier + attribuer**. Vérifiez la configuration, puis cliquez sur **Vérifier + attribuer**.

Vous avez accordé à votre application de fonction l’accès à l’espace de noms Service Bus à l’aide d’identités managées.

## <a name="connect-to-service-bus-in-your-function-app"></a>Se connecter à Service Bus dans votre application de fonction

1. Dans le portail, recherchez l’application de fonction que vous avez créée dans le [tutoriel précédent] ou accédez-y dans la page **Function App**.

1. Dans votre application de fonction, sélectionnez **Configuration** sous **Paramètres**.

1. Dans **Paramètres d’application**, sélectionnez **+ Nouveau paramètre d’application** pour créer le paramètre dans le tableau suivant.

    | Nom      | Value  | Description |
    | ------------ | ---------------- | ----------- |
    | **ServiceBusConnection__fullyQualifiedNamespace** | <ESPACE_DE_NOM_SERVICE_BUS>.servicebus.windows.net | Ce paramètre connecte votre application de fonction au bus des services. Utilisez des connexions basées sur l’identité plutôt que des secrets. |

1. Une fois les deux paramètres créés, sélectionnez **Enregistrer** > **Confirmer**.

Maintenant que vous avez préparé l’application de fonction à se connecter à l’espace de noms Service Bus à l’aide d’une identité managée, vous pouvez ajouter une nouvelle fonction qui utilise un déclencheur Service Bus à votre projet local.

## <a name="add-a-service-bus-triggered-function"></a>Ajouter une fonction déclenchée par Service Bus

1. Exécutez la commande `func init`, de la façon suivante, pour créer un projet Functions dans un dossier nommé LocalFunctionProj avec le runtime spécifié :

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

1. Accédez au dossier du projet :

    ```console
    cd LocalFunctionProj
    ```

1. Dans le dossier racine du projet, exécutez les commandes suivantes :

    ```command
    dotnet remove package Microsoft.Azure.Webjobs.Extensions.ServiceBus
    dotnet add package Microsoft.Azure.Webjobs.Extensions.ServiceBus --prerelease
    ```

    Cela remplace la version par défaut du package d’extension Service Bus par une version qui prend en charge les identités managées.

1. Exécutez la commande suivante pour ajouter une fonction déclenchée par Service Bus au projet :

    ```csharp
    func new --name ServiceBusTrigger --template ServiceBusQueueTrigger 
    ```

    Cela ajoute le code pour un nouveau déclencheur Service Bus et une référence au package d’extension. Vous devez ajouter un paramètre de connexion de l’espace de noms Service Bus pour ce déclencheur.

1. Ouvrez le nouveau fichier de projet ServiceBusTrigger.cs et remplacez la classe `ServiceBusTrigger` par le code suivant :

    ```csharp
    public static class ServiceBusTrigger
    {
        [FunctionName("ServiceBusTrigger")]
        public static void Run([ServiceBusTrigger("myinputqueue", 
            Connection = "ServiceBusConnection")]string myQueueItem, ILogger log)
        {
            log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
        }
    }
    ```

    Cet exemple de code met à jour le nom de la file d’attente de façon à utiliser `myinputqueue`, qui est le même nom que celui de la file d’attente que vous avez créée précédemment. Il définit également le nom de la connexion Service Bus sur `ServiceBusConnection`. Il s’agit de l’espace de noms Service Bus utilisé par la connexion basée sur l’identité `ServiceBusConnection__fullyQualifiedNamespace` que vous avez configuré dans le portail.

> [!NOTE]  
> Si vous essayez d’exécuter vos fonctions maintenant à l’aide de `func start`, une erreur s’affiche. Cela est dû au fait que vous n’avez pas de connexion basée sur l’identité définie localement. Si vous souhaitez exécuter votre fonction localement, définissez le paramètre d’application `ServiceBusConnection__fullyQualifiedNamespace` dans `local.settings.json` comme vous l’avez fait dans [la section précédente](#connect-to-service-bus-in-your-function-app). En outre, vous devez attribuer le rôle à votre identité de développeur. Pour plus d’informations, consultez la [documentation sur le développement local avec des connexions basées sur l’identité](./functions-reference.md#local-development-with-identity-based-connections).

## <a name="publish-the-updated-project"></a>Publier le projet mis à jour

1. Exécutez la commande suivante afin de générer localement les fichiers nécessaires pour le package de déploiement :

    ```console
    dotnet publish --configuration Release
    ```

1. Accédez au sous-dossier `\bin\Release\netcoreapp3.1\publish` et créez un fichier .zip à partir de son contenu.

1. Publiez le fichier .zip. Pour cela, exécutez la commande suivante, en remplaçant les paramètres `FUNCTION_APP_NAME`, `RESOURCE_GROUP_NAME` et `PATH_TO_ZIP`, selon le cas :

    ```azurecli
    az functionapp deploy -n FUNCTION_APP_NAME -g RESOURCE_GROUP_NAME --src-path PATH_TO_ZIP
    ```

Maintenant que vous avez mis à jour l’application de fonction avec le nouveau déclencheur, vous pouvez vérifier qu’elle fonctionne avec l’identité.

## <a name="validate-your-changes"></a>Valider vos changements

1. Dans le portail, recherchez `Application Insights`, puis sélectionnez **Application Insights** sous **Services**.  

1. Dans **Application Insights**, naviguez jusqu’à votre instance nommée ou recherchez-la.

1. Dans votre instance, sélectionnez **Métriques en temps réel** sous **Examiner**.

1. Laissez l’onglet précédent ouvert, puis ouvrez le portail Azure dans un nouvel onglet. Dans ce nouvel onglet, accédez à votre espace de noms Service Bus, puis sélectionnez **Files d’attente** dans le panneau de gauche.

1. Sélectionnez votre file d’attente nommée `myinputqueue`.

1. Sélectionnez **Service Bus Explorer** dans le panneau de gauche.

1. Envoyez un message de test.

1. Sélectionnez votre onglet **Métriques en temps réel**, puis regardez l’exécution de la file d’attente Service Bus.

Félicitations ! Vous avez correctement configuré votre déclencheur de file d’attente Service Bus avec une identité managée.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé une application de fonction avec des connexions basées sur l’identité.

Utilisez les liens suivants pour en savoir plus sur Azure Functions avec des connexions basées sur l’identité :

- [Identité managée dans Azure Functions](../app-service/overview-managed-identity.md)
- [Connexions basées sur l’identité dans Azure Functions](./functions-reference.md#configure-an-identity-based-connection)
- [Documentation sur les fonctions pour le développement local](./functions-reference.md#local-development-with-identity-based-connections)

[tutoriel précédent]: ./functions-identity-based-connections-tutorial.md
