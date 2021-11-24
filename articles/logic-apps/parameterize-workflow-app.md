---
title: Créer des paramètres pour les valeurs de flux de travail monolocataires
description: Définir des paramètres pour les valeurs qui diffèrent dans les flux de travail entre les environnements de déploiement pour Azure Logic Apps monolocataires.
services: logic-apps
ms.suite: integration
ms.reviewer: azla
ms.topic: how-to
ms.date: 08/09/2021
ms.openlocfilehash: a29eda23d12ca07057ff1081ae8d9bc4cfdc56ed
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137027"
---
# <a name="create-parameters-to-use-in-workflows-across-environments-in-single-tenant-azure-logic-apps"></a>Créer des paramètres à utiliser dans les flux de travail entre environnements dans les environnements Azure Logic Apps monolocataires

Dans Azure Logic Apps, vous pouvez extraire des valeurs qui peuvent changer dans vos flux de travail au sein des environnements de développement, de test et de production en définissant des *paramètres*. Lorsque vous utilisez des paramètres à la place, vous pouvez vous concentrer davantage sur la conception de vos flux de travail et insérer ultérieurement vos variables spécifiques à l’environnement.

Cet article explique comment les paramètres fonctionnent dans les instances Azure Logic Apps monolocataires et comment modifier, référencer et gérer les variables d’environnement à l’aide de l’expérience des paramètres monolocataires.

<a name="parameters-introduction"></a>

## <a name="parameters-in-single-tenant-versus-multi-tenant"></a>Paramètres monolocataires et multilocataires

Si vous êtes familiarisé avec Azure Logic Apps *multilocataires*, vous connaissez peut-être également déjà les paramètres. Le paramétrage des entrées de votre flux de travail dans les instances Azure Logic Apps monolocataires fonctionne de la même façon que dans les envrionnements Azure Logic Apps multilocataires, avec cependant une importante différence.

Par exemple, dans les services monolocataires et multilocataires, vous pouvez définir des paramètres lorsque vous travaillez dans le concepteur de flux de travail. Une fois que vous avez défini le paramètre, vous pouvez référencer ce paramètre à partir de n’importe quel flux de travail ou connexion dans la *même* ressource d’application logique. Toutefois, dans le service *multilocataire*, après avoir créé et utilisé des paramètres dans le concepteur, vous définissez les variables d’environnement dans les fichiers de modèle Azure Resource Manager (modèle ARM) et de paramètres du modèle. Dans ce scénario, vous devez définir les paramètres *au moment du déploiement*, ce qui signifie que même si vous ne devez modifier qu’une seule variable, vous devez redéployer le modèle ARM de votre application logique.

À titre de comparaison, avec le service *monolocataire*, vous pouvez utiliser des variables d’environnement au moment de l’exécution et du déploiement à l’aide des paramètres d’application *et* . Dans Azure Logic Apps monolocataire, les paramètres d’application contiennent des options de configuration globale pour *tous les workflows* dans la même application logique. Pour plus d’informations, consultez [Modifier les paramètres de l’hôte et de l’application pour les applications logiques monolocataires](edit-app-settings-host-settings.md).

Par exemple, vous pouvez utiliser les paramètres d’application pour intégrer Azure Key Vault et [référencer directement des chaînes sécurisées\](../app-service/app-service-key-vault-references.md), telles que des chaînes de connexion et des clés. Comme pour les modèles ARM, où vous pouvez définir des variables d’environnement au moment du déploiement, vous pouvez définir des paramètres d’application dans la [définition de workflow de votre application logique](/azure/templates/microsoft.logic/workflows). Vous pouvez ensuite capturer des valeurs d’infrastructure générées dynamiquement, comme des points de terminaison de connexion, des chaînes de stockage, etc.

Toutefois, les paramètres d’application ont des limitations de taille et ne peuvent pas être référencés à partir de certaines zones dans Azure Logic Apps. Les paramètres offrent un plus grand nombre de cas d’utilisation que les paramètres d’application, tels que la prise en charge des tailles de valeur élevée et des objets complexes.

Par exemple, si vous utilisez Visual Studio Code comme outil de développement local pour exécuter des flux de travail localement, dans votre projet d’application logique, vous pouvez définir des paramètres à l’aide du fichier **parameters.json**. Vous pouvez ensuite référencer n’importe quel paramètre dans ce fichier de paramètres à partir de n’importe quel flux de travail dans le fichier **workflow.json** de votre projet ou à partir de n’importe quel objet de connexion dans le fichier **connections.json** de votre projet. La liste suivante décrit quelques cas d’utilisation courants :

* Avoir un fichier de paramètres de test qui comprend toutes les valeurs que vous utilisez pendant le test. Au moment du déploiement, vous pouvez remplacer votre fichier de paramètres de test par votre fichier de paramètres de production.

* Paramétrer différentes parties de votre fichier **connections.json**. Vous pouvez ensuite vérifier votre fichier **connections.json** dans le contrôle de code source, puis gérer les connexions par le biais de votre fichier **parameters.json**.

* Paramétrer des objets complexes, tels que l’objet JSON `authentication`. Par exemple, vous pouvez remplacer la valeur de l’objet `authentication` par une chaîne qui contient une expression de paramètres unique, telle que `@parameters('api-auth')`.

* Évaluer et modifier les paramètres d’application dans le fichier **local.settings.json** de votre projet. Vous pouvez ensuite référencer ces paramètres d’application dans vos paramètres.

> [!NOTE]
> En guise de recommandation générale, envisagez d’utiliser des paramètres comme méthode par défaut pour paramétrer des valeurs, et non des paramètres d’application. Ainsi, quand vous devez stocker des clés ou des chaînes sécurisées, vous pouvez suivre la recommandation pour référencer les paramètres de l’application à partir de vos paramètres. Si vous le souhaitez, vous pouvez utiliser les deux options dans votre solution, en utilisant des paramètres pour référencer les paramètres d’application.

## <a name="prerequisites"></a>Prérequis

* Un compte et un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [workflow d’application logique hébergé dans Azure Logic Apps monlocataire](single-tenant-overview-compare.md).

  Si vous n’avez pas d’application logique, [créez votre application logique (Standard) dans le portail Azure](create-single-tenant-workflows-azure-portal.md) ou [dans Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md).

## <a name="define-use-and-edit-parameters"></a>Définir, utiliser et modifier des paramètres

### <a name="azure-portal"></a>Portail Azure

1. Dans le [Portail Azure](https://portal.azure.com), ouvrez votre application logique monolocataire. Sous **Flux de travail**, sélectionnez et ouvrez votre flux de travail dans le concepteur.

1. Dans la barre d’outils du concepteur, sélectionnez **Paramètres**.

   ![Capture d’écran montrant le Portail Azure, le concepteur de flux de travail et les « Paramètres » dans la barre d’outils du concepteur sélectionnée.](./media/parameterize-workflow-app/portal-designer-select-parameters.png)

1. Dans le volet **Paramètres**, sélectionnez **Créer un paramètre**.

1. Fournissez les informations suivantes sur le paramètre à créer :

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Nom** | Oui | Nom du paramètre à créer. |
   | **Type** | Oui | Type de données pour le paramètre, tel que **Groupe**, **Bool**, **Float**, **Int**, **Objet**, et **Chaîne**. <p><p>**Remarque** : dans les flux de travail monolocataires, les types de données sécurisées, tels que `securestring` et `secureobject` ne sont pas pris en charge. |
   | **Valeur** | Oui | Valeur du paramètre. <p><p>Dans les Applications Azure Logic Apps monolocataires, vous devez spécifier la valeur de paramètre, car la logique de flux de travail, les informations de connexion et les valeurs de paramètre n’existent pas dans un emplacement unique. Le concepteur doit être en mesure de résoudre la valeur de paramètre avant le chargement. |
   |||

   L’exemple suivant illustre une définition pour un paramètre de chaîne :

   ![Capture d’écran montrant le Portail Azure, le concepteur de flux de travail et le volet « Paramètres » avec un exemple de définition de paramètre.](./media/parameterize-workflow-app/define-parameter.png)

1. Lorsque vous avez terminé, fermez le volet **Paramètres**, mais veillez à enregistrer votre flux de travail pour enregistrer votre nouvelle définition de paramètre.

1. Pour référencer le paramètre à partir d’un déclencheur ou d’une action qui se trouve dans un flux de travail au sein de la même application logique, procédez comme suit :

   1. Dans le concepteur, ouvrez le flux de travail de votre choix, puis développez le déclencheur ou l’action.

   1. Dans la propriété où vous souhaitez utiliser le paramètre, cliquez à l’intérieur de la zone d’édition de cette propriété.

   1. Dans la liste de contenu dynamique qui s’ouvre, sous **Paramètres**, choisissez le paramètre que vous avez préalablement créé, par exemple :

      ![Capture d’écran montrant un exemple d’action avec le curseur dans la zone d’édition de la propriété, la liste de contenu dynamique développée et le paramètre précédemment créé sélectionné.](./media/parameterize-workflow-app/reference-parameter.png)

1. Pour afficher ou modifier des paramètres dans la même application logique, effectuez l’une des étapes suivantes :

   * Ouvrez un flux de travail dans cette application logique. Dans le menu du flux de travail, sélectionnez **Concepteur**. Dans la barre d’outils du concepteur, sélectionnez **Paramètres**.

     Le volet **Paramètres** s’ouvre et affiche tous les paramètres que vous avez définis à partir de flux de travail dans cette application logique.

   * Pour afficher ou modifier en bloc JSON, dans le menu principal de votre application logique, sélectionnez **Paramètres**.

     La vue **Paramètres** JSON s’ouvre et affiche tous les paramètres que vous avez définis à partir de flux de travail dans cette application logique.

### <a name="visual-studio-code"></a>Visual Studio Code

1. Dans le fichier JSON racine d’un projet appelé **parameters.json**, définissez *tous* les paramètres et leurs valeurs. Ce fichier possède un objet qui inclut les paires *clé-valeur*. Chaque *clé* est le nom pour chaque paramètre, tandis que chaque *valeur* est la structure pour chaque paramètre. Chaque structure doit inclure à la fois une déclaration `type` et `value`.

   > [!IMPORTANT]
   > Votre fichier **parameters.json** doit définir et inclure tous les paramètres et leurs valeurs que vous référencez ou utilisez ailleurs dans votre projet, par exemple dans les définitions de flux de travail ou les connexions.

   L’exemple suivant montre un fichier de paramètres de base :

   ```json
   {
        "responseString": { 
            "type": "string", 
            "value": "hello" 
        },
        "functionAuth": { 
            "type": "object", 
            "value": { 
                "type": "QueryString", 
                "name": "Code", 
                "value": "@appsetting('<AzureFunctionsOperation-FunctionAppKey>')" 
            }
        }
    }
    ```

    > [!NOTE]
    > Le seul type d’expression valide dans le fichier **parameters.json** est `@appsetting`.

1. Pour référencer des paramètres dans vos entrées de déclencheur ou d’action, utilisez l’expression `@parameters('<parameter-name>')`.

#### <a name="parameterize-connections-file"></a>Paramétrer le fichier de connexions

Pour paramétriser votre fichier **connections.json**, remplacez les valeurs des littéraux, tels que `ConnectionRuntimeUrl`, par une expression unique `parameters()`, par exemple, `@parameters('api-runtimeUrl')`. Les seuls types d’expressions valides dans le fichier **connections.json** sont `@parameters` et `@appsetting`.

> [!IMPORTANT]
>
> Pour le développement, si vous paramétrez le fichier **connections.json**, l’expérience du concepteur devient restreinte, à la fois localement et dans le Portail Azure. Si vous devez utiliser le concepteur pour le développement, utilisez un fichier **connections.json** non paramétré. Ensuite, dans vos pipelines de déploiement, remplacez-le par le fichier paramétrisé. 
> Le runtime fonctionne toujours avec la paramétrisation. Les améliorations du concepteur sont en cours de développement.

L’exemple suivant illustre un fichier **connections.json** paramétré qui utilise à la fois les paramètres d’application et les configurations. Bien que vous souhaitiez utiliser des paramètres dans la mesure du possible, ce scénario est une exception ou un cas limite dans lequel vous utilisez des paramètres d’application sur des paramètres, car les paramètres d’application sont générés au cours du déploiement et sont plus faciles à remplir dynamiquement dans un pipeline de développement. Cet exemple de fichier utilise un paramètre pour l’`blob_auth`objet d’authentification complexe et les paramètres d’application pour les autres valeurs. Dans ce cas, vous pouvez utiliser un paramètre pour l’objet d’authentification, car vous risquez de ne pas référencer le paramètre dans votre flux de travail :

```json
{
   "serviceProviderConnections": {
      "serviceBus": {
         "parameterValues": {
            "connectionString": "@appsetting('serviceBus_connectionString')"
        },
        "serviceProvider": {
           "id": "/serviceProviders/serviceBus"
        },
        "displayName": "servicebus"
     }
   },
   "managedApiConnections": {
      "azureblob": {
         "api": {
            "id": "/subscriptions/@{appsetting('WORKFLOWS_SUBSCRIPTION_ID')}/providers/Microsoft.Web/locations/@{appsetting('WORKFLOWS_LOCATION_NAME')}/managedApis/azureblob"
         },
         "connection": {
            "id": "/subscriptions/@{appsetting('WORKFLOWS_SUBSCRIPTION_ID')}/resourceGroups/@{appsetting('WORKFLOWS_RESOURCE_GROUP_NAME')}/providers/Microsoft.Web/connections/azureblob"
         },
         "connectionRuntimeUrl": "@appsetting('BLOB_CONNECTION_RUNTIMEURL')",
         "authentication": "@parameters('blob_auth')"
      }
   }
}
```

> [!NOTE]
> Lorsque vous avez une expression en ligne avec du texte brut, assurez-vous d’utiliser le format interpolé pour cette expression en l’entourant d’accolades ({}). Ce format permet d’éviter les problèmes d’analyse.
>
> Par exemple, si vous avez `"<text>/@<function-name>('<parameter-name>')/<text>"`, utilisez plutôt la version suivante : `"<text>/@{<function-name>('<parameter-name>')}/<text>"`. 
>
> Pour plus d’informations, consultez [Considérations relatives à l’utilisation des fonctions](workflow-definition-language-functions-reference.md#function-considerations).
     

## <a name="manage-parameters-files"></a>Gérer les fichiers de paramètres

En règle générale, vous devez gérer plusieurs versions de fichiers de paramètres. Vous pouvez avoir des valeurs ciblées pour différents environnements de déploiement, tels que les environnements de développement, de test et de production. La gestion de ces fichiers de paramètres fonctionne souvent comme la gestion des fichiers de paramètres de modèle ARM. Quand vous effectuez un déploiement sur un environnement spécifique, vous promouvez le fichier de paramètres correspondant, généralement par le biais d’un pipeline pour DevOps.

Pour remplacer dynamiquement les fichiers de paramètres en utilisant Azure CLI, exécutez la commande suivante :

```azurecli
az functionapp deploy --resource-group MyResourceGroup --name MyLogicApp --src-path C:\parameters.json --type static --target-path parameters.json
```

Si vous avez un projet d’application logique NuGet, vous devez mettre à jour votre fichier projet ( **&lt;logic-app-name&gt;.csproj**) pour inclure le fichier de paramètres dans la sortie de la génération, par exemple :

```csproj
<ItemGroup>
  <None Update="parameters.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
  </None>
</ItemGroup>
```

> [!NOTE]
> La possibilité de remplacer dynamiquement les fichiers de paramètres n’est pas encore disponible dans le Portail Azure ou dans le concepteur de flux de travail.

Pour plus d’informations sur la configuration de vos applications logiques pour les déploiements DevOps, consultez la documentation suivante :

* [Présentation du déploiement DevOps pour les applications logiques monolocataires](devops-deployment-single-tenant-azure-logic-apps.md)
* [Configurer le déploiement DevOps pour les applications logiques monolocataires](set-up-devops-deployment-single-tenant-azure-logic-apps.md)

## <a name="manage-app-settings"></a>Gérer les paramètres d’application

Dans Azure Logic Apps monolocataire, les paramètres d’application contiennent des options de configuration globale pour *tous les workflows* dans la même application logique. Quand vous exécutez des flux de travail localement dans Visual Studio Code, ces paramètres d’application sont accessibles en tant que variables d’environnement local dans le fichier **local.settings.json**. Vous pouvez ensuite référencer ces paramètres d’application dans vos paramètres.

Pour ajouter, mettre à jour ou supprimer des paramètres d’application, sélectionnez et consultez les sections suivantes selon que vous utilisez Visual Studio Code, le portail Azure, Azure CLI ou un modèle ARM (Bicep).

### <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

Pour passer en revue les paramètres d’application de votre application logique dans le portail Azure, procédez comme suit :

1. Dans le [Portail Azure](https://portal.azure.com/), ouvrez votre application logique monolocataire.

1. Dans le menu de votre application logique, sous **Paramètres**, sélectionnez **Configuration**.

1. Dans la page **Configuration**, sous l’onglet **Paramètres de l’application**, examinez les paramètres de l’application pour votre application logique.

1. Pour voir toutes les valeurs, sélectionnez **Afficher les valeurs**. Ou, pour voir une valeur unique, sélectionnez cette valeur.

Pour ajouter un nouveau paramètre, effectuez les étapes suivantes :

1. Sous l’onglet **Paramètres de l’application**, sous **Paramètres de l’application**, sélectionnez **Nouveau paramètre d’application**.

1. Pour **Nom**, entrez la *clé* ou le nom de votre nouveau paramètre.

1. Pour **Valeur**, entrez la valeur de votre nouveau paramètre.

1. Quand vous êtes prêt à créer votre paire *clé-valeur*, sélectionnez **OK**.

:::image type="content" source="./media/parameterize-workflow-app/portal-app-settings-values.png" alt-text="Capture d’écran montrant le portail Azure et le volet de configuration avec les paramètres et les valeurs d’application pour une application logique monolocataire." lightbox="./media/parameterize-workflow-app/portal-app-settings-values.png":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour passer en revue les paramètres actuels de votre application avec Azure CLI, exécutez la commande `az logicapp config appsettings list`. Assurez-vous que votre commande comprend les paramètres `--name -n` et `--resource-group -g`, par exemple :

```azurecli
az logicapp config appsettings list --name MyLogicApp --resource-group MyResourceGroup
```

Pour ajouter ou mettre à jour un paramètre d’application avec Azure CLI, exécutez la commande `az logicapp config appsettings set`. Assurez-vous que votre commande comprend les paramètres `--name n` et `--resource-group -g`. Par exemple, la commande suivante crée un paramètre avec une clé nommée `CUSTOM_LOGIC_APP_SETTING` et la valeur `12345` :

```azurecli
az logicapp config appsettings set --name MyLogicApp --resource-group MyResourceGroup --settings CUSTOM_LOGIC_APP_SETTING=12345 
```

### <a name="resource-manager-or-bicep-template"></a>[Modèle Resource Manager ou Bicep](#tab/azure-resource-manager)

Pour examiner et définir vos paramètres d’application dans un modèle ARM ou un modèle Bicep, recherchez la définition des ressources de votre application logique et mettez à jour l’objet JSON `appSettings`. Pour obtenir la définition complète des ressources, consultez les [informations de référence sur le modèle ARM](/azure/templates/microsoft.web/sites).

Cet exemple montre les paramètres de fichier pour les modèles ARM ou Bicep :

```json
"appSettings": [
    {
        "name": "string",
        "value": "string"
    },
    {
        "name": "string",
        "value": "string"
    },
    <...>
], 
```

---

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Architecture monolocataire ou multilocataire et environnement de service d’intégration pour Azure Logic Apps](single-tenant-overview-compare.md)
