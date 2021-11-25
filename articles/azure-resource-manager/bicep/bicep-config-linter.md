---
title: Paramètres de linter pour la configuration Bicep
description: Décrit comment personnaliser les valeurs de configuration du linter Bicep.
ms.topic: conceptual
ms.date: 11/16/2021
ms.openlocfilehash: d48ea625d58b35ac18c39bfb3fb60eb4ec3d7edd
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557364"
---
# <a name="add-linter-settings-in-the-bicep-config-file"></a>Ajouter des paramètres de linter dans le fichier config Bicep

Dans un fichier **bicepconfig.json**, vous pouvez personnaliser les paramètres de validation du [linter Bicep](linter.md). Le linter utilise ces paramètres lors de l’évaluation de vos fichiers Bicep selon les meilleures pratiques.

Cet article décrit les paramètres disponibles pour l’utilisation du linter Bicep.

## <a name="customize-linter"></a>Personnaliser le linter

Les paramètres du linter sont accessibles sous l’élément `analyzers`. Vous pouvez activer ou désactiver le linter, fournir des valeurs spécifiques à des règles et définir le niveau de règles.

L’exemple suivant montre les règles qui sont disponibles pour la configuration.

```json
{
  "analyzers": {
    "core": {
      "enabled": true,
      "verbose": true,
      "rules": {
        "no-hardcoded-env-urls": {
          "level": "warning"
        },
        "no-unused-params": {
          "level": "error"
        },
        "no-unused-vars": {
          "level": "error"
        },
        "prefer-interpolation": {
          "level": "warning"
        },
        "secure-parameter-default": {
          "level": "error"
        },
        "simplify-interpolation": {
          "level": "warning"
        }
      }
    }
  }
}
```

Les propriétés sont les suivantes :

- **enabled** : indiquez **true** pour activer le linter, **false** pour le désactiver.
- **verbose** : indiquez **true** pour afficher le fichier bicepconfig.json utilisé par Visual Studio Code.
- **rules** : indiquez des valeurs spécifiques aux règles. Chaque règle a un niveau qui détermine la manière dont le linter répond lorsqu’une violation est trouvée.

Les valeurs disponibles pour **level** sont :

| **level**  | **Comportement lors de la création** | **Comportement de l’éditeur** |
|--|--|--|
| `Error` | Les violations apparaissent sous forme d’erreurs dans la sortie de build de la ligne de commande et entraînent l’échec de la build. | Le code incriminé est souligné d’un tilde rouge et apparaît sous l’onglet Problèmes. |
| `Warning` | Les violations apparaissent sous forme d’avertissements dans la sortie de build de la ligne de commande, mais n’entraînent pas l’échec de la build. | Le code incriminé est souligné d’un tilde jaune et apparaît sous l’onglet Problèmes. |
| `Info` | Les violations n’apparaissent pas dans la sortie de la génération de la ligne de commande. | Le code incriminé est souligné d’un tilde bleu et apparaît sous l’onglet Problèmes. |
| `Off` | Entièrement supprimé. | Entièrement supprimé. |

## <a name="environment-urls"></a>URL d’environnement

Pour la règle relative aux URL d’environnement codées en dur, vous pouvez choisir quelles URL sont vérifiées. Par défaut, les paramètres suivants sont appliqués :

```json
{
  "analyzers": {
    "core": {
      "verbose": false,
      "enabled": true,
      "rules": {
        "no-hardcoded-env-urls": {
          "level": "warning",
          "disallowedhosts": [
            "management.core.windows.net",
            "gallery.azure.com",
            "management.core.windows.net",
            "management.azure.com",
            "database.windows.net",
            "core.windows.net",
            "login.microsoftonline.com",
            "graph.windows.net",
            "trafficmanager.net",
            "vault.azure.net",
            "datalake.azure.net",
            "azuredatalakestore.net",
            "azuredatalakeanalytics.net",
            "vault.azure.net",
            "api.loganalytics.io",
            "api.loganalytics.iov1",
            "asazure.windows.net",
            "region.asazure.windows.net",
            "api.loganalytics.iov1",
            "api.loganalytics.io",
            "asazure.windows.net",
            "region.asazure.windows.net",
            "batch.core.windows.net"
          ],
          "excludedhosts": [
            "schema.management.azure.com"
          ]
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

* [Configurer votre environnement Bicep](bicep-config.md)
* [Ajouter des paramètres de module dans la configuration Bicep](bicep-config-modules.md)
* En savoir plus sur le [linter Bicep](linter.md)
