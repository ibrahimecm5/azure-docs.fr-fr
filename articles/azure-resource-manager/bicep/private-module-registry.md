---
title: Créer un registre privé pour le module Bicep
description: Découvrez comment configurer un registre de conteneurs Azure pour les modules Bicep privés
ms.topic: conceptual
ms.date: 10/22/2021
ms.openlocfilehash: b667a80a155355ea206c4804d6eb98200491e3f8
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551365"
---
# <a name="create-private-registry-for-bicep-modules-preview"></a>Créer un registre privé pour les modules Bicep (préversion)

Pour partager des [modules](modules.md) au sein de votre organisation, vous pouvez créer un registre de modules privé. Vous publiez des modules dans ce registre et accordez un accès en lecture aux utilisateurs qui ont besoin de déployer les modules. Une fois les modules partagés dans les registres, vous pouvez les référencer à partir de vos fichiers Bicep.

Pour utiliser des registres de module, vous devez disposer de Bicep CLI version **0.4.1008 ou ultérieure**.

## <a name="configure-private-registry"></a>Configurer un registre privé

Un registre Bicep est hébergé sur [Azure Container Registry (ACR)](../../container-registry/container-registry-intro.md). Procédez comme suit afin de configurer votre registre pour les modules.

1. Si vous disposez déjà d’un registre de conteneurs, vous pouvez l’utiliser. Si vous devez créer un registre de conteneurs, consultez [Démarrage rapide : Créer un registre de conteneurs à l’aide d’un fichier Bicep](../../container-registry/container-registry-get-started-bicep.md). 

   Vous pouvez utiliser l’une des références SKU disponibles pour le registre du module. La [géoréplication](../../container-registry/container-registry-geo-replication.md) du registre fournit aux utilisateurs une présence locale ou une sauvegarde à chaud.

1. Obtenez le nom du serveur de connexion. Vous avez besoin de ce nom lors de la liaison au registre à partir de vos fichiers Bicep. 

   Pour obtenir le nom du serveur de connexion, utilisez [Get-AzContainerRegistry](/powershell/module/az.containerregistry/get-azcontainerregistry).

   ```azurepowershell
   Get-AzContainerRegistry -ResourceGroupName "<resource-group-name>" -Name "<registry-name>"  | Select-Object LoginServer
   ```

   Ou utilisez [az acr show](/cli/azure/acr#az_acr_show).

   ```azurecli
   az acr show --resource-group <resource-group-name> --name <registry-name> --query loginServer
   ```

   Le format du nom du serveur de connexion est : `<registry-name>.azurecr.io`.

- Pour publier des modules dans un registre, vous devez avoir l’autorisation **d’envoyer (push)** une image. Pour déployer un module à partir d’un registre, vous devez avoir l’autorisation de **tirer (pull)** l’image. Pour plus d’informations sur les rôles qui accordent l’accès approprié, consultez [Rôles et autorisations Azure Container Registry](../../container-registry/container-registry-roles.md).

- Selon le type de compte que vous utilisez pour déployer le module, vous devrez peut-être personnaliser les informations d’identification qui sont utilisées. Ces informations d’identification sont nécessaires pour récupérer les modules à partir du registre. Par défaut, les informations d’identification sont obtenues à partir d’Azure CLI ou d’Azure PowerShell. Vous pouvez personnaliser la priorité d’obtention des informations d’identification dans le fichier **bicepconfig.json**. Pour plus d’informations, consultez [Informations d’identification pour la restauration des modules](bicep-config-modules.md#credentials-for-restoring-modules).

- Pour plus de sécurité, vous pouvez demander l’accès via un point de terminaison privé. Pour plus d’informations, consultez [Connexion privée à un registre de conteneurs Azure à l’aide de Azure Private Link](../../container-registry/container-registry-private-link.md).

## <a name="publish-files-to-registry"></a>Publier des fichiers dans le registre

Après avoir configuré le registre de conteneurs, vous pouvez y publier des fichiers. Utilisez la commande [publish](bicep-cli.md#publish) et fournissez tous les fichiers Bicep que vous avez l’intention d’utiliser comme modules. Indiquez l’emplacement cible du module dans votre registre.

```azurecli
az bicep publish storage.bicep --target br:exampleregistry.azurecr.io/bicep/modules/storage:v1
```

## <a name="view-files-in-registry"></a>Afficher les fichiers dans le registre

Pour afficher le module publié dans le portail :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Recherchez **registres de conteneurs**.
1. Sélectionnez votre registre.
1. Sélectionnez **Référentiels** dans le menu de gauche.
1. Sélectionnez le chemin d’accès au module (référentiel).  Dans l’exemple précédent, le nom du chemin d’accès au module est **bicep/modules/storage**.
1. Sélectionnez la balise. Dans l’exemple précédent, la balise est **v1**.
1. La **référence Artefact** correspond à la référence que vous allez utiliser dans le fichier Bicep.

   ![Référence d’artefact du registre du module Bicep](./media/private-module-registry/bicep-module-registry-artifact-reference.png)

Vous êtes maintenant prêt à référencer le fichier dans le registre à partir d’un fichier Bicep. Pour obtenir des exemples de la syntaxe à utiliser pour référencer un module externe, consultez [Modules Bicep](modules.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les modules, consultez [Modules Bicep](modules.md).
* Pour configurer des alias pour un registre de modules, consultez [Ajouter des paramètres de module dans le fichier config Bicep](bicep-config-modules.md).
* Pour plus d’informations sur la publication et la restauration des modules, consultez [Commandes CLI Bicep](bicep-cli.md).
