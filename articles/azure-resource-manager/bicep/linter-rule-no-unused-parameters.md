---
title: 'Règle de linter : aucun paramètre inutilisé'
description: 'Règle de linter : aucun paramètre inutilisé'
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 2e9c602a28b2ba19d8c0a38b1bba3ecc521e6d77
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017841"
---
# <a name="linter-rule---no-unused-parameters"></a>Règle de linter : aucun paramètre inutilisé

Cette règle recherche les paramètres qui ne sont pas référencés dans le fichier Bicep.

## <a name="returned-code"></a>Code renvoyé

`no-unused-params`

## <a name="solution"></a>Solution

Pour améliorer la lisibilité de votre modèle, supprimez tous les paramètres qui sont définis mais qui ne sont pas utilisés. Ce test recherche les paramètres qui ne sont utilisés nulle part dans le modèle. L’élimination des paramètres inutilisés facilite également le déploiement de votre modèle, car vous n’avez pas besoin de fournir des valeurs inutiles.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le linter, consultez [Utiliser le linter Bicep](./linter.md).
