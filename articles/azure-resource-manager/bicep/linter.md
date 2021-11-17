---
title: Utiliser le linter Bicep
description: Découvrez comment utiliser le linter Bicep.
ms.topic: conceptual
ms.date: 10/12/2021
ms.openlocfilehash: 5edf5fbb9553233455e7b24dd482a18d3c7e1c42
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548099"
---
# <a name="use-bicep-linter"></a>Utiliser le linter Bicep

Le linter Bicep vérifie les fichiers Bicep pour rechercher les erreurs de syntaxe et les infractions aux meilleures pratiques. Ce linter facilite l’application des normes de code en proposant des conseils pendant le développement. Vous pouvez personnaliser les meilleures pratiques à utiliser pour vérifier le fichier.

## <a name="linter-requirements"></a>Exigences relatives aux linters

Le linter est intégré à l’interface CLI Bicep et à l’extension Bicep pour Visual Studio Code. Pour l’utiliser, vous devez disposer de la version **0.4 ou d’une version ultérieure**.

## <a name="default-rules"></a>Règles par défaut

L’ensemble par défaut des règles du linter est minimal et extrait des [cas de test arm-ttk](../templates/template-test-cases.md). L’extension et l’interface CLI de Bicep vérifient les règles suivantes, qui sont définies sur le niveau avertissement.

- [adminusername-should-not-be-literal](./linter-rule-admin-username-should-not-be-literal.md)
- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

Vous pouvez personnaliser le mode d’application des règles du linter. Pour remplacer les paramètres par défaut, ajoutez un fichier **bicepconfig.json** et appliquez des paramètres personnalisés. Pour plus d’informations sur l’application de ces paramètres, consultez [Ajouter des paramètres personnalisés dans le fichier de configuration Bicep](bicep-config-linter.md).

## <a name="use-in-visual-studio-code"></a>Utiliser dans Visual Studio Code

La capture d’écran suivante montre le linter dans Visual Studio Code :

:::image type="content" source="./media/linter/bicep-linter-show-errors.png" alt-text="Utilisation du linter Bicep dans Visual Studio Code.":::

Le volet **PROBLÈMES** contient quatre erreurs, un avertissement et un message d’information illustrés sur la capture d’écran.  Le message d’information affiche le fichier de configuration Bicep utilisé. Ce type d’information n’est affichée que lorsque vous définissez **verbose** sur **true** dans le fichier de configuration.

Passez le curseur de la souris sur l’une des zones de problèmes. Le linter donne les détails de l’erreur ou de l’avertissement. Sélectionnez la zone pour afficher également une ampoule bleue :

:::image type="content" source="./media/linter/bicep-linter-show-quickfix.png" alt-text="Utilisation du linter Bicep dans Visual Studio Code - afficher le correctif rapide.":::

Sélectionnez soit l’ampoule, soit le lien **Correctif rapide** pour voir la solution :

:::image type="content" source="./media/linter/bicep-linter-show-quickfix-solution.png" alt-text="Utilisation du linter Bicep dans Visual Studio Code - afficher la solution de correctif rapide.":::

Sélectionnez la solution pour résoudre le problème automatiquement.

## <a name="use-in-bicep-cli"></a>Utiliser dans l’interface CLI Bicep

La capture d’écran suivante montre le linter dans la ligne de commande. La sortie de la commande de compilation affiche toutes les infractions aux règle.

:::image type="content" source="./media/linter/bicep-linter-command-line.png" alt-text="Utilisation du linter Bicep dans la ligne de commande.":::

Vous pouvez intégrer ces vérifications dans le cadre de vos pipelines CI/CD. Vous pouvez utiliser une action GitHub pour tenter une génération de Bicep. Les erreurs font échouer les pipelines.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la personnalisation des règles du linter, consultez [Ajouter des paramètres personnalisés dans le fichier de configuration Bicep](bicep-config-linter.md).
* Pour plus d’informations sur l’utilisation de Visual Studio Code et l’extension Bicep, consultez [Démarrage rapide : Créer des fichiers Bicep avec Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
