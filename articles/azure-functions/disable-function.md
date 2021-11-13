---
title: Guide pratique pour désactiver des fonctions dans Azure Functions
description: Découvrez comment désactiver et activer des fonctions dans Azure Functions.
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: devx-track-csharp, devx-track-azurepowershell
ms.openlocfilehash: e2df38182dd8b199ca13d7e5a9b7484aee004f52
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130265378"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Guide pratique pour désactiver des fonctions dans Azure Functions

Cet article explique comment désactiver une fonction dans Azure Functions. Quand vous *désactivez* une fonction, le runtime ignore le déclencheur automatique défini pour la fonction. Ceci vous permet d’empêcher une fonction spécifique de s’exécuter sans arrêter toute l’application de fonction.

La façon recommandée de désactiver une fonction est d’utiliser un paramètre d’application au format `AzureWebJobs.<FUNCTION_NAME>.Disabled` défini sur `true`. Vous pouvez créer et modifier ce paramètre d’application de plusieurs façons, notamment à l’aide de l’interface [Azure CLI](/cli/azure/) et à partir de l’onglet **Vue d’ensemble** de votre fonction sur le [portail Azure](https://portal.azure.com). 

## <a name="disable-a-function"></a>Désactiver une fonction

# <a name="portal"></a>[Portail](#tab/portal)

Utilisez les boutons **Activer** et **Désactiver** sur la page **Vue d’ensemble** de la fonction. Ces boutons fonctionnent en modifiant la valeur du paramètre d’application `AzureWebJobs.<FUNCTION_NAME>.Disabled`. Ce paramètre propre à la fonction est créé la première fois qu’il est désactivé. 

![Commutateur d’état de la fonction](media/disable-function/function-state-switch.png)

Même lorsque vous publiez dans votre application de fonction à partir d’un projet local, vous pouvez toujours utiliser le portail pour désactiver des fonctions dans l’application de fonction. 

> [!NOTE]  
> La fonctionnalité de test intégré au portail ignore le paramètre `Disabled`. Cela signifie qu’une fonction désactivée continue à s’exécuter quand elle est démarrée à partir de la fenêtre **Test** dans le portail. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Dans l'interface de ligne de commande Azure, vous utilisez la commande [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set) pour créer et modifier le paramètre d'application. La commande suivante désactive une fonction nommée `QueueTrigger` en créant un paramètre d’application nommé `AzureWebJobs.QueueTrigger.Disabled` et en le définissant sur `true`. 

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Pour réactiver la fonction, réexécutez la même commande avec la valeur `false`.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

La commande [`Update-AzFunctionAppSetting`](/powershell/module/az.functions/update-azfunctionappsetting) ajoute ou met à jour un paramètre d’application. La commande suivante désactive une fonction nommée `QueueTrigger` en créant un paramètre d’application nommé `AzureWebJobs.QueueTrigger.Disabled` et en le définissant sur `true`. 

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "true"}
```

Pour réactiver la fonction, réexécutez la même commande avec la valeur `false`.

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "false"}
```
---

## <a name="functions-in-a-slot"></a>Fonctions dans un emplacement

Par défaut, les paramètres d’application s’appliquent également aux applications qui s’exécutent dans les emplacements de déploiement. Toutefois, vous pouvez remplacer le paramètre d’application utilisé par l’emplacement en définissant un paramètre d’application spécifique à un emplacement. Par exemple, vous pouvez vouloir qu’une fonction soit active en production, mais pas pendant les tests de déploiement, par exemple une fonction déclenchée par un minuteur. 

Pour désactiver une fonction uniquement dans l’emplacement de préproduction :

# <a name="portal"></a>[Portail](#tab/portal)

Accédez à l’instance d’emplacement de votre application de fonction en sélectionnant **Emplacements de déploiement** sous **Déploiement**, en choisissant votre emplacement, puis en sélectionnant **Fonctions** dans l’instance d’emplacement.  Choisissez votre fonction, puis utilisez les boutons **Activer** et **Désactiver** sur la page **Vue d’ensemble** de la fonction. Ces boutons fonctionnent en modifiant la valeur du paramètre d’application `AzureWebJobs.<FUNCTION_NAME>.Disabled`. Ce paramètre propre à la fonction est créé la première fois qu’il est désactivé. 

Vous pouvez également ajouter directement le paramètre d’application nommé `AzureWebJobs.<FUNCTION_NAME>.Disabled` avec la valeur `true` dans la **configuration** de l’instance d’emplacement. Lorsque vous ajoutez un paramètre d’application spécifique à un emplacement, veillez à cocher la case **Paramètre de l’emplacement de déploiement**. Cela permet de conserver la valeur du paramètre avec l’emplacement pendant les échanges.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=true
```
Pour réactiver la fonction, réexécutez la même commande avec la valeur `false`.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell ne prend actuellement pas en charge cette fonctionnalité.

---

Pour plus d’informations, consultez [Emplacements de déploiement Azure Functions](functions-deployment-slots.md).

## <a name="localsettingsjson"></a>local.settings.json

Les fonctions peuvent être désactivées de la même façon lors de l’exécution locale. Pour désactiver une fonction nommée `HttpExample`, ajoutez une entrée à la collection Values dans le fichier local.settings.json, comme suit :

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "UseDevelopmentStorage=true", 
    "AzureWebJobs.HttpExample.Disabled": true
  }
}
``` 

## <a name="other-methods"></a>Autres méthodes

Bien que la méthode via un paramètre d’application soit recommandée pour tous les langages et toutes les versions du runtime, il existe plusieurs autres méthodes pour désactiver des fonctions. Ces méthodes, qui varient selon le langage et la version du runtime, sont conservées à des fins de compatibilité descendante. 

### <a name="c-class-libraries"></a>Bibliothèque de classes C#

Dans une fonction de bibliothèque de classes, vous pouvez aussi utiliser l’attribut `Disable` pour empêcher le déclenchement de la fonction. Cet attribut vous permet de personnaliser le nom du paramètre utilisé pour désactiver la fonction. Utilisez la version de l’attribut qui vous permet de définir un paramètre de constructeur faisant référence à un paramètre d’application booléen, comme indiqué dans l’exemple suivant :

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Cette méthode vous permet d’activer et de désactiver la fonction en changeant le paramètre d’application, sans recompiler ou redéployer quoi que soit. Le fait de changer un paramètre d’application entraîne le redémarrage de l’application de fonction. Le changement de l’état désactivé est donc reconnu immédiatement.

Il existe également un constructeur pour le paramètre qui n’accepte pas de chaîne pour le nom du paramètre. Cette version de l’attribut n’est pas recommandée. Si vous utilisez cette version, vous devez recompiler et redéployer le projet pour modifier l’état désactivé de la fonction.

### <a name="functions-1x---scripting-languages"></a>Functions 1.x - Langages de script

Dans la version 1.x, vous pouvez aussi utiliser la propriété `disabled` du fichier *function.json* pour indiquer au runtime de ne pas déclencher une fonction. Cette méthode fonctionne seulement pour les langages de script, comme un script C# et JavaScript. La propriété `disabled` peut être définie sur `true` ou sur le nom d’un paramètre de l’application :

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
or 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

Dans le deuxième exemple, la fonction est désactivée quand un paramètre d’application nommé IS_DISABLED existe et a la valeur `true` ou 1.

>[!IMPORTANT]  
>Le portail utilise les paramètres d’application pour désactiver les fonctions v1.x. Lorsqu’un paramètre d’application est en conflit avec le fichier function.json, une erreur peut se produire. Vous devez supprimer la propriété `disabled` du fichier function.json pour éviter les erreurs. 

## <a name="considerations"></a>Considérations

Gardez à l’esprit les considérations suivantes lorsque vous désactivez des fonctions :

+ Lorsque vous désactivez une fonction déclenchée par HTTP à l’aide des méthodes décrites dans cet article, le point de terminaison peut toujours être accessible en cas d’exécution sur votre ordinateur local.  

+ À ce stade, les noms de fonctions qui contiennent un trait d’Union (`-`) ne peuvent pas être désactivés lors de l’exécution sur un plan Linux. Si vous avez besoin de désactiver vos fonctions lors de l’exécution sur un plan Linux, n’utilisez pas de traits d’union dans les noms de vos fonctions.

## <a name="next-steps"></a>Étapes suivantes

Cet article traite de la désactivation des déclencheurs automatiques. Pour plus d’informations sur les déclencheurs, consultez [Déclencheurs et liaisons](functions-triggers-bindings.md).
