---
title: Vue d’ensemble de la résolution des problèmes liés au modèle ARM et au fichier Bicep
description: Décrit la résolution des problèmes liés au déploiement de ressources Azure avec des modèles Azure Resource Manager (modèles ARM) et des fichiers Bicep.
ms.topic: overview
ms.date: 10/26/2021
ms.custom: troubleshooting-overview
ms.openlocfilehash: 604ba2be3fc1adcf22df9774400237a1b07f93e1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097648"
---
# <a name="what-is-deployment-troubleshooting"></a>Qu’est-ce que la résolution des problèmes de déploiement ?

Lorsque vous déployez des fichiers Bicep ou des modèles Azure Resource Manager (modèles ARM), vous pouvez obtenir une erreur. Cette documentation vous aide à trouver les solutions possibles pour l’erreur.

## <a name="error-types"></a>Types d’erreurs

Il existe deux types d’erreurs que vous pouvez obtenir : les **erreurs de validation** et les **erreurs de déploiement**.

Les erreurs de validation surviennent avant le démarrage du déploiement. Ces erreurs peuvent être déterminées sans interagir avec votre environnement Azure actuel. Par exemple, la validation vous informe des erreurs de syntaxe ou des arguments manquants pour une fonction avant le démarrage du déploiement.

Les erreurs de déploiement ne peuvent être déterminées qu’en tentant le déploiement et en interagissant avec votre environnement Azure. Par exemple, une machine virtuelle nécessite une carte réseau (NIC). Si la carte réseau n’existe pas lorsque la machine virtuelle est déployée, vous obtenez une erreur de déploiement.

## <a name="troubleshooting-tools"></a>Outils de résolution des problèmes

Pour faciliter l’identification des erreurs de syntaxe avant un déploiement, utilisez la version la plus récente de [Visual Studio Code](https://code.visualstudio.com). Installez la version la plus récente de l’une ou l’autre des extensions suivantes :

* [Extension Bicep](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep)
* [Extension Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Pour dépanner les déploiements, il est utile d’en savoir plus sur les propriétés d’un fournisseur de ressources ou les versions de l’API. Pour plus d’informations, consultez [Définir des ressources à l’aide de Bicep et de modèles ARM](/azure/templates).

Pour suivre les meilleures pratiques en matière de développement de vos modèles, utilisez l’un ou l’autre :

* [Linter Bicep](../bicep/linter.md)
* [Kit de ressources de test de modèle ARM](../templates/test-toolkit.md)

Lorsque vous effectuez un déploiement, vous pouvez trouver la cause des erreurs à partir du portail Azure dans les section **Déploiements** ou **Journal d’activité** d’un groupe de ressources. Si vous utilisez Azure PowerShell, utilisez des commandes telles que [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation) et [Get-AzActivityLog](/powershell/module/az.monitor/get-azactivitylog). Pour Azure CLI, utilisez des commandes telles que [az deployment operation group](/cli/azure/deployment/operation/group) et [az monitor activity-log list](/cli/azure/monitor/activity-log#az_monitor_activity_log_list).

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des solutions basées sur le code d’erreur, consultez [Résoudre les erreurs courantes de déploiement Azure](common-deployment-errors.md).
- Pour consultez une présentation de la recherche du code d’erreur, consultez [Démarrage rapide : Résoudre les problèmes des déploiements de modèles ARM](quickstart-troubleshoot-arm-deployment.md) ou au [Démarrage rapide : Résoudre les problèmes de déploiement de fichiers Bicep](quickstart-troubleshoot-bicep-deployment.md).
