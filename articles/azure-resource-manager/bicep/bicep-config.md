---
title: Fichier de configuration Bicep
description: Décrit le fichier de configuration pour vos déploiements Bicep
ms.topic: conceptual
ms.date: 11/16/2021
ms.openlocfilehash: 8679daf65402c939f9eea7651b57b1198970fb8d
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548137"
---
# <a name="configure-your-bicep-environment"></a>Configurer votre environnement Bicep

Bicep prend en charge un fichier de configuration nommé **bicepconfig.json**. Dans ce fichier, vous pouvez ajouter des valeurs qui personnalisent votre expérience de développement Bicep. Si vous n’ajoutez pas ce fichier, Bicep utilise les valeurs par défaut.

Pour personnaliser les valeurs, créez ce fichier dans le répertoire où vous stockez les fichiers Bicep. Vous pouvez ajouter des fichiers bicepconfig.json dans plusieurs répertoires. Le fichier de configuration le plus proche dans l’arborescence du répertoire est utilisé.

## <a name="available-settings"></a>Paramètres disponibles

Lorsque vous utilisez des [modules](modules.md), vous pouvez ajouter des alias pour les chemins d’accès des modules. Ces alias simplifient votre fichier Bicep, car vous n’avez pas à répéter les chemins compliqués. Vous pouvez également configurer la priorité des informations d’identification pour l’authentification auprès du registre. Les informations d’identification sont utilisées pour restaurer les modules externes dans le cache local. Pour plus d’informations, consultez [Ajouter des paramètres de module à Bicep config](bicep-config-modules.md).

Lorsque vous travaillez avec le [Bicep linter](linter.md), vous pouvez remplacer les paramètres par défaut de la validation du fichier Bicep. Pour plus d’informations, consultez [Ajouter des paramètres de module à la configuration Bicep](bicep-config-linter.md).

## <a name="intellisense"></a>Intellisense

L’extension Bicep pour Visual Studio Code prend en charge intellisense pour votre fichier **bicepconfig.json**. Utilisez la fonctionnalité IntelliSense pour découvrir les propriétés et les valeurs disponibles.

:::image type="content" source="./media/bicep-config/bicep-linter-configure-intellisense.png" alt-text="Prise en charge d’IntelliSense dans le fichier de configuration bicepconfig.json.":::

## <a name="next-steps"></a>Étapes suivantes

* [Ajouter des paramètres de module dans la configuration Bicep](bicep-config-modules.md)
* [Ajouter des paramètres de Linter à la configuration Bicep](bicep-config-linter.md)
* En savoir plus sur le [Bicep linter](linter.md)
