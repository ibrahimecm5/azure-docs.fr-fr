---
title: Supprimer des machines d’Azure Automation Update Management
description: Cet article explique comment supprimer des machines Azure et non-Azure gérées avec Update Management.
services: automation
ms.topic: conceptual
ms.date: 10/26/2021
ms.custom: mvc
ms.openlocfilehash: e5e4e80833bc21e5ffc5533d208b1642de359fd5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006731"
---
# <a name="remove-vms-from-update-management"></a>Supprimer des machines virtuelles d’Update Management

Quand vous avez terminé de gérer les mises à jour sur des machines Azure ou non-Azure dans votre environnement, vous pouvez arrêter de les gérer avec la fonctionnalité [Update Management](overview.md). Pour arrêter leur gestion, vous allez modifier la requête de recherche enregistrée `MicrosoftDefaultComputerGroup` dans l’espace de travail Log Analytics qui est lié à votre compte Automation.

## <a name="sign-into-the-azure-portal"></a>Se connecter au portail Azure

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="to-remove-your-machines"></a>Pour supprimer des machines

1. Dans le portail Azure, lancez **Cloud Shell** dans le volet de navigation en haut du portail. Si vous ne connaissez pas Azure Cloud Shell, consultez [Vue d’ensemble d’Azure Cloud Shell](../../cloud-shell/overview.md).

2. Utilisez la méthode suivante pour identifier l’UUID d’une machine virtuelle Azure ou d’une machine non-Azure que vous souhaitez supprimer de la gestion.

   # <a name="azure-vm"></a>[Microsoft Azure](#tab/azure-vm)

   ```azurecli
   az vm show -g MyResourceGroup -n MyVm -d
   ```

   # <a name="non-azure-machine"></a>[Machine non-Azure](#tab/non-azure-machine)

   ```kusto
   Heartbeat
   | where TimeGenerated > ago(30d)
   | where ComputerEnvironment == "Non-Azure"
   | summarize by Computer, VMUUID
   ```

   ---

3. Dans le portail Azure, accédez à **Espaces de travail Log Analytics**. Sélectionnez votre espace de travail dans la liste.

4. Dans votre espace de travail Log Analytics, sélectionnez **Groupes d’ordinateurs** dans le menu de gauche.

5. À partir de **Groupes d’ordinateurs** dans le volet de droite, l’onglet **Groupes enregistrés** s’affiche par défaut.

6. Dans le tableau, cliquez sur l’icône **Exécuter la requête** à droite de l’élément **MicrosoftDefaultComputerGroup** avec la valeur **Catégorie héritée** de **Mises à jour**.

7. Dans l’éditeur de requête, passez en revue la requête et recherchez l’UUID de la machine. Supprimez l’UUID de la machine et répétez les étapes pour toutes les machines que vous souhaitez supprimer.

   > [!NOTE]
   > Pour une protection supplémentaire, avant d’apporter des modifications, veillez à effectuer une copie de la requête. De cette manière, vous pourrez le restaurer si un problème survient.

   Si vous souhaitez commencer avec la requête d’origine et rajouter des machines pour la prise en charge d’une activité de nettoyage ou de maintenance, copiez la requête suivante :

   ```kusto
   Heartbeat
   | where Computer in~ ("") or VMUUID in~ ("")
   | distinct Computer
   ```

8. Sauvegardez la recherche enregistrée une fois que vous avez fini de la modifier en sélectionnant **Enregistrer > Enregistrer sous** dans la barre supérieure. Lorsque vous y êtes invité, spécifiez les éléments suivants :

    * **Name** : Updates__MicrosoftDefaultComputerGroup
    * L’option **Enregistrer en tant que groupe d’ordinateurs** est sélectionnée
    * **Catégorie héritée** : Mises à jour

>[!NOTE]
>Les machines s’affichent toujours une fois que vous les avez désinscrites, car nous effectuons des rapports pour toutes les machines évaluées au cours des dernières 24 heures. Une fois la machine supprimée, vous devez attendre 24 heures avant qu’elle ne soit plus listée.

## <a name="next-steps"></a>Étapes suivantes

Pour réactiver la gestion de votre machine Azure ou non-Azure, consultez [Activer Update Management à partir du portail Azure](enable-from-portal.md) ou [Activer Update Management à partir d’une machine virtuelle Azure](enable-from-vm.md).