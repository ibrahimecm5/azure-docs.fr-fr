---
title: Erreurs de modèle non valide
description: Décrit comment résoudre les erreurs de modèle non valide quand vous déployez des fichiers Bicep ou des modèles Azure Resource Manager (ARM).
ms.topic: troubleshooting
ms.date: 11/15/2021
ms.openlocfilehash: d5bd7494300ac8861ebd85478e380132acbac007
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550662"
---
# <a name="resolve-errors-for-invalid-template"></a>Résoudre les erreurs de modèle non valide

Cet article décrit comment résoudre les erreurs de modèle non valide pour des fichiers Bicep ou des modèles Azure Resource Manager (ARM).

## <a name="symptom"></a>Symptôme

Quand un modèle est déployé, vous recevez une erreur qui indique :

```Output
Code=InvalidTemplate
Message=<varies>
```

Le message d’erreur varie selon le type d’erreur.

## <a name="cause"></a>Cause

Cette erreur peut résulter de différents types d’erreurs. Elles impliquent généralement une erreur de syntaxe ou de structure dans le modèle.

<a id="syntax-error"></a>

## <a name="solution-1---syntax-error"></a>Solution 1 - erreur de syntaxe

Si vous recevez un message d’erreur qui indique que la validation du modèle a échoué, vous avez peut-être un problème de syntaxe dans votre modèle.

```Output
Code=InvalidTemplate
Message=Deployment template validation failed
```

Des erreurs de syntaxe peuvent se produire, car les expressions de modèle ont de nombreux éléments. Par exemple, l’attribution de nom pour un compte de stockage inclut des paires de guillemets simples ou doubles, d’accolades, de crochets et de parenthèses. Les expressions contiennent également des fonctions et des caractères tels que des signes dollar, des virgules et des points.


# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
name: 'storage${uniqueString(resourceGroup().id)}'
```

# <a name="json"></a>[JSON](#tab/json)

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

---

Lorsque vous recevez ce type d’erreur, examinez la syntaxe d’expression. Pour identifier les erreurs de modèle, vous pouvez utiliser [Visual Studio Code](https://code.visualstudio.com) avec la dernière [extension Bicep](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep) ou l’[extension Outils Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools).

<a id="incorrect-segment-lengths"></a>

## <a name="solution-2---incorrect-segment-lengths"></a>Solution 2 - longueurs de segments incorrectes

Une autre erreur de modèle non valide se produit lorsque le nom de la ressource n’est pas au format approprié. Pour résoudre cette erreur, consultez [Résoudre les erreurs d’incompatibilité de nom et de type](error-invalid-name-segments.md).

<a id="parameter-not-valid"></a>

## <a name="solution-3---parameter-isnt-valid"></a>Solution 3 - le paramètre n’est pas valide

Vous pouvez spécifier les valeurs autorisées d’un paramètre dans un modèle. Pendant le déploiement, si vous fournissez une valeur qui n’est pas une valeur autorisée, vous recevez un message semblable à l’erreur suivante :

```Output
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Consultez le modèle pour connaître les valeurs autorisées du paramètre et utilisez une valeur autorisée pendant le déploiement. Pour plus d’informations, consultez les valeurs autorisées pour les [fichiers Bicep](../bicep/parameters.md#allowed-values) ou les [modèles ARM](../templates/parameters.md#allowed-values).

<a id="too-many-resource-groups"></a>

## <a name="solution-4---too-many-target-resource-groups"></a>Solution 4 : trop de groupes de ressources cibles

Vous pouvez voir cette erreur dans les déploiements antérieurs, car vous étiez limité à cinq groupes de ressources cibles dans un déploiement unique. En mai 2020, cette limite a été augmentée à 800 groupes de ressources. Pour plus d’informations, consultez la procédure de déploiement sur plusieurs groupes de ressources pour [Bicep](../bicep/deploy-to-resource-group.md#deploy-to-multiple-resource-groups) ou les modèles [ARM](../templates/deploy-to-resource-group.md#deploy-to-multiple-resource-groups).

<a id="circular-dependency"></a>

## <a name="solution-5---circular-dependency-detected"></a>Solution 5 : dépendance circulaire détectée

Vous recevez cette erreur lorsque des ressources dépendant les unes des autres empêchent le déploiement de démarrer. À cause de l’effet combiné d’interdépendances, plusieurs ressources attendent d’autres ressources qui sont elles aussi en attente. Par exemple, la ressource 1 dépend de la ressource 3, la ressource 2 de la ressource 1 et la ressource 3 de la ressource 2. Vous pouvez généralement résoudre ce problème en supprimant les dépendances inutiles.

Bicep crée une dépendance implicite lorsqu’une ressource utilise le nom symbolique d’une autre ressource. Une dépendance explicite à l’aide de `dependsOn` n’est généralement pas nécessaire. Pour plus d’informations, consultez [Dépendances Bicep](../bicep/resource-declaration.md#dependencies).

Pour résoudre une dépendance circulaire :

1. Dans votre modèle, recherchez la ressource identifiée dans la dépendance circulaire.
1. Pour cette ressource, examinez la propriété `dependsOn` et les utilisations de la fonction `reference` pour voir de quelles ressources elle dépend.
1. Examinez ces ressources pour voir de quelles ressources elles dépendent. Suivez les dépendances jusqu’à ce que vous trouviez une ressource qui dépend de la ressource d’origine.
1. Pour les ressources impliquées dans la dépendance circulaire, examinez attentivement toutes les utilisations de la propriété `dependsOn` pour identifier les dépendances qui ne sont pas nécessaires. Supprimer ces dépendances. Si vous n’êtes pas certain qu’une dépendance soit nécessaire, essayez de la supprimer.
1. Redéployez le modèle.

La suppression de valeurs de la propriété `dependsOn` peut provoquer des erreurs lors du déploiement du modèle. Si vous obtenez une erreur, rajoutez la dépendance dans le modèle.

Si cette approche ne résout pas le problème de dépendance circulaire, essayez de déplacer une partie de votre logique de déploiement dans les ressources enfants (par exemple, les extensions ou les paramètres de configuration). Configurez ces ressources enfants pour qu’elles se déploient après les ressources impliquées dans la dépendance circulaire. Par exemple, supposons que vous déployiez deux machines virtuelles, mais que vous deviez définir sur chacune d’elles des propriétés faisant référence les unes aux autres. Vous pouvez les déployer dans l’ordre suivant :

1. Machine virtuelle 1
1. Machine virtuelle 2
1. L’extension sur la machine virtuelle 1 dépend des machines virtuelles 1 et 2. L’extension définit sur la machine virtuelle 1 des valeurs qu’elle obtient de la machine virtuelle 2.
1. L’extension sur la machine virtuelle 2 dépend des machines virtuelles 1 et 2. L’extension définit sur la machine virtuelle 2 des valeurs qu’elle obtient de la machine virtuelle 1.

Cette approche fonctionne aussi pour les applications App Service. Essayez de déplacer des valeurs de configuration dans une ressource enfant de la ressource d’application. Vous pouvez déployer deux applications web dans l’ordre suivant :

1. Application web 1
1. Application web 2
1. La configuration de l’application web 1 dépend des applications web 1 et 2. Elle contient des paramètres d’application avec les valeurs de l’application web 2.
1. La configuration de l’application web 2 dépend des applications web 1 et 2. Elle contient des paramètres d’application avec les valeurs de l’application web 1.
