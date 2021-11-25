---
title: Paramétrage du module pour la configuration Bicep
description: Décrit comment personnaliser les valeurs de configuration des modules dans les déploiements Bicep.
ms.topic: conceptual
ms.date: 11/16/2021
ms.openlocfilehash: e130695532d470a154acc0b35bca864fcdbcba9d
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557331"
---
# <a name="add-module-settings-in-the-bicep-config-file"></a>Ajouter des paramètres de module dans le fichier config Bicep

Dans un fichier **bicepconfig.json**, vous pouvez créer des alias pour les chemins d’accès des modules et configurer la priorité des informations d’identification pour la restauration d’un module.

Cet article décrit les paramètres disponibles pour l’utilisation des [modules](modules.md).

## <a name="aliases-for-modules"></a>Alias pour les modules

Afin de simplifier le chemin pour la liaison aux modules, créez des alias dans le fichier config. Un alias fait référence à un registre de modules ou à un groupe de ressources qui contient des specs de modèle.

Le fichier de configuration a une propriété pour `moduleAliases`. Cette propriété contient tous les alias que vous définissez. Sous cette propriété, les alias sont divisés selon qu’ils font référence à un registre ou à un spec de modèle.

Pour créer un alias pour un **registre Bicep**, ajoutez une propriété `br`. Pour ajouter un alias pour un **spec de modèle**, utilisez la propriété `ts`.

```json
{
  "moduleAliases": {
    "br": {
      <add-registry-aliases>
    },
    "ts": {
      <add-template-specs-aliases>
    }
  }
}
```

Dans la propriété `br`, ajoutez autant d’alias que nécessaire. Attribuez un nom et les propriétés suivantes à chaque alias :

- **registry** (obligatoire) : nom de serveur de connexion au registre
- **modulePath** (facultatif) : référentiel du registre dans lequel les modules sont stockés

Dans la propriété `ts`, ajoutez autant d’alias que nécessaire. Attribuez un nom et les propriétés suivantes à chaque alias :

- **subscription** (obligatoire) : ID de l’abonnement qui héberge les spécifications de modèle
- **resourceGroup** (obligatoire) : nom du groupe de ressources qui contient les spécifications de modèle

L’exemple suivant montre un fichier de configuration qui définit deux alias pour un registre de modules et un alias pour un groupe de ressources qui contient des spécifications de modèle.

```json
{
  "moduleAliases": {
    "br": {
      "ContosoRegistry": {
        "registry": "contosoregistry.azurecr.io"
      },
      "CoreModules": {
        "registry": "contosoregistry.azurecr.io",
        "modulePath": "bicep/modules/core"
      }
    },
    "ts": {
      "CoreSpecs": {
        "subscription": "00000000-0000-0000-0000-000000000000",
        "resourceGroup": "CoreSpecsRG"
      }
    }
  }
}
```

Lorsque vous utilisez un alias dans la référence de module, vous devez utiliser les formats suivants :

```bicep
br/<alias>:<file>:<tag>
ts/<alias>:<file>:<tag>
```

Définissez vos alias sur le dossier ou le groupe de ressources qui contient les modules, pas sur le fichier lui-même. Le nom de fichier doit être inclus dans la référence au module.

**Sans les alias**, vous créez un lien vers un module dans un registre avec le chemin d’accès complet.

```bicep
module stgModule 'br:contosoregistry.azurecr.io/bicep/modules/core/storage:v1' = {
```

**Avec les alias**, vous pouvez simplifier le lien à l’aide de l’alias du registre.

```bicep
module stgModule 'br/ContosoRegistry:bicep/modules/core/storage:v1' = {
```

Ou vous pouvez simplifier le lien à l’aide de l’alias qui spécifie le chemin d’accès au registre et au module.

```bicep
module stgModule  'br/CoreModules:storage:v1' = {
```

Pour une spécification de modèle, utilisez :

```bicep
module stgModule  'ts/CoreSpecs:storage:v1' = {
```

## <a name="credentials-for-restoring-modules"></a>Informations d’identification pour la restauration des modules

Pour [restaurer](bicep-cli.md#restore) des modules externes dans le cache local, le compte doit disposer des autorisations appropriées pour accéder au registre. Vous pouvez configurer la priorité des informations d’identification pour l’authentification auprès du registre. Par défaut, Bicep utilise les informations d’identification de l’utilisateur authentifié dans Azure CLI ou Azure PowerShell. Pour personnaliser la priorité des informations d’identification, ajoutez les éléments `cloud` et `credentialPrecedence` dans le fichier de configuration.

```json
{
    "cloud": {
      "credentialPrecedence": [
        "AzureCLI",
        "AzurePowerShell"
      ]
    }
}
```

Les informations d’identification disponibles sont les suivantes :

* AzureCLI
* AzurePowerShell
* Environnement
* ManagedIdentity
* VisualStudio
* VisualStudioCode

## <a name="next-steps"></a>Étapes suivantes

* [Configurer votre environnement Bicep](bicep-config.md)
* [Ajouter des paramètres de linter à la configuration Bicep](bicep-config-linter.md)
* En savoir plus sur les [modules](modules.md)
