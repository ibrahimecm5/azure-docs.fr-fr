---
title: Modules Bicep
description: Décrit comment définir un module dans un fichier Bicep et comment utiliser des étendues de module.
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: a8a0b9c1eeeb56c12926774a78d45bb58e7d8437
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087378"
---
# <a name="bicep-modules"></a>Modules Bicep

Bicep vous permet d’organiser les déploiements en modules. Un module est simplement un fichier Bicep qui est déployé à partir d’un autre fichier Bicep. Les modules permettent d’améliorer la lisibilité de vos fichiers Bicep en encapsulant les détails complexes de votre déploiement. Vous pouvez également facilement réutiliser des modules pour différents déploiements.

Pour partager des modules avec d’autres personnes de votre organisation, [créez un registre privé](private-module-registry.md). Les modules du registre ne sont accessibles qu’aux utilisateurs disposant des autorisations adéquates.

Les modules Bicep sont convertis en un seul modèle Azure Resource Manager avec des [modèles imbriqués](../templates/linked-templates.md#nested-template).

### <a name="microsoft-learn"></a>Microsoft Learn

Pour en savoir plus sur les modules et obtenir des conseils pratiques, consultez [Créer des fichiers Bicep composables en utilisant des modules](/learn/modules/create-composable-bicep-files-using-modules/) sur **Microsoft Learn**.

## <a name="definition-syntax"></a>Définition de la syntaxe

La syntaxe de base pour définir un module est la suivante :

```bicep
module <symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}
```

Ainsi, un exemple simple et concret ressemblerait à ce qui suit :

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/local-file-definition.bicep" :::

Utilisez le nom symbolique pour référencer le module dans une autre partie du fichier Bicep. Par exemple, vous pouvez utiliser le nom symbolique pour obtenir la sortie d’un module. Le nom symbolique peut contenir les caractères a à z, A à Z, 0 à 9, et « _ ». Le nom ne peut pas commencer par un chiffre. Un module ne peut pas avoir le même nom qu’un paramètre, un module ou une ressource.

Le chemin d’accès peut être un fichier local ou un fichier dans un registre. Pour plus d’informations, consultez [Chemin d’accès au module](#path-to-module).

La propriété **nom** est obligatoire. Elle devient le nom de la ressource de déploiement imbriquée dans le modèle généré.

Si vous devez **spécifier une étendue** qui est différente de celle du fichier principal, ajoutez la propriété étendue. Pour plus d’informations, consultez [Définir l’étendue du module](#set-module-scope).

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/scope-definition.bicep" highlight="4" :::

Pour **déployer un module de manière conditionnelle**, ajoutez une expression `if`. L’utilisation est similaire au [déploiement conditionnel d’une ressource](conditional-resource-deployment.md).

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/conditional-definition.bicep" highlight="2" :::

Pour déployer **plus d’une instance** d’un module, ajoutez l’expression `for`. Pour plus d’informations, consultez [Boucles itératives dans Bicep](loops.md).

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/iterative-definition.bicep" highlight="3" :::

À l’instar des ressources, les modules sont déployés en parallèle, sauf s’ils dépendent d’autres modules ou ressources. En général, vous n’avez pas besoin de définir des dépendances car elles sont déterminées de manière implicite. Si vous devez définir une dépendance explicite, vous pouvez ajouter `dependsOn` à la définition du module. Pour en savoir plus sur les dépendances, consultez [Dépendances des ressources](resource-declaration.md#dependencies).

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/dependsOn-definition.bicep" highlight="6-8" :::

## <a name="path-to-module"></a>Chemin d’accès au module

Le fichier du module peut être soit un fichier local, soit un fichier externe dans un registre de modules Bicep. Ces deux options sont expliquées ci-dessous.

### <a name="local-file"></a>Fichier local

Si le module est un **fichier local**, fournissez un chemin d’accès relatif à ce fichier. Tous les chemins d’accès en code Bicep doivent être spécifiés en utilisant le séparateur de répertoires de la barre oblique (/) pour garantir une compilation cohérente entre les plateformes. Le caractère barre oblique inverse (\\) de Windows n’est pas pris en charge. Les chemins d’accès peuvent contenir des espaces.

Par exemple, pour déployer un fichier qui se trouve à un niveau supérieur dans le répertoire de votre fichier principal, utilisez :

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/local-file-definition.bicep" highlight="1" :::

### <a name="file-in-registry"></a>Fichier dans le registre

Si vous avez [publié un module dans un registre](bicep-cli.md#publish), vous pouvez établir un lien à ce module. Indiquez le nom du registre de conteneurs Azure et un chemin d’accès au module. Spécifiez le chemin d’accès au module avec la syntaxe suivante :

```bicep
module <symbolic-name> 'br:<registry-name>.azurecr.io/<file-path>:<tag>' = {
```

- **br** est le nom de schéma d’un registre Bicep.
- Le **chemin d’accès au fichier** est appelé `repository` dans Azure Container Registry. Le **chemin d’accès au fichier** peut contenir des segments séparés par le caractère `/`.
- Une **balise** est utilisée pour spécifier une version du module.

Par exemple :

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/registry-definition.bicep" highlight="1" :::

Lorsque vous référencez un module dans un registre, l’extension Bicep de Visual Studio Code appelle automatiquement la fonction [bicep restore](bicep-cli.md#restore) pour copier le module externe dans le cache local. La restauration du module externe prend quelques instants. Si IntelliSense pour le module ne fonctionne pas immédiatement, attendez la fin de la restauration.

Le chemin d’accès complet d’un module dans un registre peut être long. Au lieu de fournir le chemin d’accès complet chaque fois que vous souhaitez utiliser le module, vous pouvez [configurer des alias dans le fichier bicepconfig.json](bicep-config.md#aliases-for-modules). Les alias permettent de référencer plus facilement le module. Par exemple, grâce à un alias, vous pouvez raccourcir le chemin d’accès à :

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/alias-definition.bicep" highlight="1" :::

## <a name="parameters"></a>Paramètres

Les paramètres que vous fournissez dans votre définition de module correspondent aux paramètres du fichier Bicep.

L’exemple Bicep suivant comporte trois paramètres : storagePrefix, storageSKU et location. Le paramètre storageSKU a une valeur par défaut, de sorte que vous n’avez pas à fournir une valeur pour ce paramètre lors du déploiement.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/create-storage-account/main.bicep" highlight="3,15,17" :::

Pour utiliser l’exemple précédent comme module, fournissez des valeurs pour ces paramètres.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/modules/parent-output.bicep" highlight="14-17" :::

## <a name="set-module-scope"></a>Définir l’étendue du module

Lors de la déclaration d’un module, vous pouvez définir une étendue pour le module qui est différente de l’étendue pour le fichier Bicep conteneur. Utilisez la propriété `scope` pour définir l’étendue du module. Lorsque la propriété d’étendue n’est pas fournie, le module est déployé au niveau de l’étendue cible du parent.

Le fichier Bicep suivant crée un groupe de ressources et un compte de stockage dans ce groupe de ressources. Le fichier est déployé dans un abonnement, mais le module est limité au nouveau groupe de ressources.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/modules/rg-and-storage.bicep" highlight="2,12,19" :::

L’exemple suivant déploie des comptes de stockage sur deux groupes de ressources différents. Ces deux groupes de ressources doivent déjà exister.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/modules/scope-two-resource-groups.bicep" highlight="1,13,22" :::

Définissez la propriété étendue sur un objet d’étendue valide. Si votre fichier Bicep déploie un groupe de ressources, un abonnement ou un groupe d’administration, vous pouvez définir l’étendue d’un module sur le nom symbolique de cette ressource. Vous pouvez également utiliser les fonctions d’étendue pour obtenir une étendue valide.

Ces fonctions sont les suivantes :

- [resourceGroup](bicep-functions-scope.md#resourcegroup)
- [subscription](bicep-functions-scope.md#subscription)
- [managementGroup](bicep-functions-scope.md#managementgroup)
- [client](bicep-functions-scope.md#tenant)

L’exemple suivant utilise la fonction `managementGroup` pour définir l’étendue.

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/function-scope.bicep" highlight="5" :::

## <a name="output"></a>Output

Vous pouvez obtenir des valeurs d’un module et les utiliser dans le fichier Bicep principal. Pour obtenir une valeur de sortie d’un module, utilisez la propriété `outputs` sur l’objet de module.

Le premier exemple crée un compte de stockage et renvoie les points de terminaison principaux.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/create-storage-account/main.bicep" highlight="33" :::

Lorsqu’il est utilisé en tant que module, vous pouvez récupérer cette valeur de sortie.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/modules/parent-output.bicep" highlight="20" :::

## <a name="next-steps"></a>Étapes suivantes

- Pour voir un tutoriel, consultez [Déployer des ressources Azure à l’aide de modèles Bicep](/learn/modules/deploy-azure-resources-by-using-bicep-templates/).
- Pour passer une valeur sensible à un module, utilisez la fonction [getSecret](bicep-functions-resource.md#getsecret) .
