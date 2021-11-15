---
title: Utiliser les runbooks et modules Azure Automation dans PowerShell Gallery
description: Cet article explique comment utiliser les runbooks et les modules des dépôts GitHub Microsoft et de PowerShell Gallery.
services: automation
ms.subservice: process-automation
ms.date: 10/29/2021
ms.topic: conceptual
ms.openlocfilehash: 1f6b3a47d7d8bcab77c80b71fcbc06c3381509c9
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131504209"
---
# <a name="use-existing-runbooks-and-modules"></a>Utiliser des runbooks et des modules existants

Au lieu de créer vos propres runbooks et modules dans Azure Automation, vous pouvez accéder à des scénarios déjà générés par Microsoft et la communauté. Vous pouvez obtenir des runbooks PowerShell et Python en rapport avec Azure à partir de la galerie de runbooks dans le portail Azure, ainsi que des [modules](#modules-in-the-powershell-gallery) et des [runbooks](#runbooks-in-the-powershell-gallery) (qui peuvent être propres ou non à Azure) à partir de PowerShell Gallery. Vous pouvez également contribuer à la communauté en partageant les [scénarios que vous développez](#contribute-to-the-community).

> [!NOTE]
> Le centre de scripts TechNet est en retrait. Tous les runbooks du Centre de scripts dans la galerie de runbooks ont été déplacés vers notre [organisation GitHub Automation](https://github.com/azureautomation). Pour plus d'informations, consultez [Azure Automation Runbooks moving to GitHub](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337).

## <a name="import-runbooks-from-github-with-the-azure-portal"></a>Importer des runbooks à partir de GitHub avec le portail Azure

> [!NOTE]
>- L’option **Parcourir la galerie** dans le portail Azure offre une expérience utilisateur améliorée.
>- Dans le panneau **Automatisation de processus** > **Runbook**, vous pouvez importer des runbooks à l’aide de l’option **Importer un runbook** ou **Parcourir la Galerie**, tandis que la page **Runbooks** affiche deux nouvelles colonnes : **Version du runtime** et **Type de runbook**.
 
1. Dans le portail Azure, ouvrez votre compte Automation.
1. Sélectionnez le panneau **Runbooks** sous **Automatisation de processus**.
1. Dans la page **Runbooks**, cliquez sur **Importer un runbook**.

   :::image type="content" source="./media/automation-runbook-gallery/import-runbook.png" alt-text="Capture d’écran de la sélection d’un runbook à partir de l’option Importer un runbook.":::

1. Dans la page **Importer un runbook**, vous pouvez importer un fichier stocké sur votre ordinateur local ou à partir de GitHub à l’aide respectivement de la commande **Rechercher le fichier** ou **Rechercher dans la galerie**.
1. Sélectionnez le fichier.
1. Entrez le **Nom**, la **Version du runtime** et la **Description**.
1. Cliquez sur **Importer**.
   
   :::image type="content" source="./media/automation-runbook-gallery/import-runbook-upload-runbook-file.png" alt-text="Capture d’écran montrant la sélection d’un runbook à partir d’un fichier ou d’une galerie.":::

1. Vous pouvez également sélectionner **Parcourir la galerie** dans la page **Runbooks** pour parcourir les runbooks disponibles.

   :::image type="content" source="./media/automation-runbook-gallery/browse-gallery-option.png" alt-text="Capture d’écran montrant la sélection de Rechercher dans la galerie dans le panneau Runbook.":::

1. Vous pouvez utiliser les filtres situés au-dessus de la liste pour affiner l’affichage par éditeur, par type et par ordre de tri. Recherchez l’élément de la galerie qui vous intéresse et sélectionnez-le pour afficher les informations le concernant.

   :::image type="content" source="./media/automation-runbook-gallery/browse-gallery-github.png" alt-text="Navigation dans la galerie de runbooks." lightbox="./media/automation-runbook-gallery/browse-gallery-github-expanded.png":::

1. Cliquez sur **Sélectionner** pour sélectionner un runbook choisi. 
1. Dans la page **Importer un runbook**, entrez le **nom**, puis sélectionnez les **Versions du runtime**.
1. Le champs **Type de runbook** et **Description** sont remplis automatiquement.
1. Cliquez sur **Importer**.

   :::image type="content" source="./media/automation-runbook-gallery/gallery-item-import.png" alt-text="Importation d’un élément de la galerie.":::

7. Ce runbook apparaît sur l’onglet **Runbooks** du compte Automation.

 
## <a name="runbooks-in-the-powershell-gallery"></a>Runbooks dans PowerShell Gallery

> [!IMPORTANT]
> Vous devez valider le contenu de tous les runbooks que vous obtenez à partir de PowerShell Gallery. Soyez extrêmement vigilant lors de leur installation et de leur exécution dans un environnement de production.

[PowerShell Gallery](https://www.powershellgallery.com/packages) fournit un éventail de runbooks provenant de Microsoft et de la communauté, et que vous pouvez importer dans Azure Automation. Pour utiliser l’un de ces runbooks, vous pouvez en télécharger un à partir de la galerie, ou importer directement des runbooks à partir de la galerie, ou à partir de votre compte Automation sur le portail Azure.

> [!NOTE]
> Les runbooks graphiques ne sont pas pris en charge dans PowerShell Gallery.

Seul le portail Azure permet d’importer des runbooks directement à partir de PowerShell Gallery. Vous ne pouvez pas exécuter cette fonction à l’aide de PowerShell. La procédure est la même que celle présentée dans [Importer des runbooks à partir de GitHub avec le portail Azure](#import-runbooks-from-github-with-the-azure-portal), sauf que la **source** sera **PowerShell Gallery**.

:::image type="content" source="./media/automation-runbook-gallery/source-runbook-gallery-small.png" alt-text="Sélection de la galerie de runbooks comme source." lightbox="./media/automation-runbook-gallery/source-runbook-gallery-large.png":::

## <a name="modules-in-the-powershell-gallery"></a>Modules dans PowerShell Gallery

Les modules PowerShell contiennent des cmdlets que vous pouvez utiliser dans vos runbooks. Des modules existants qui peuvent être installés dans Azure Automation sont disponibles dans [PowerShell Gallery](https://www.powershellgallery.com). Vous pouvez lancer cette galerie sur le Portail Azure et installer les modules directement dans Azure Automation, ou bien les télécharger et les installer manuellement.

Vous trouverez également des modules à importer dans le portail Azure. Ils sont répertoriés pour votre compte Automation dans les **Modules** sous **Ressources partagées**.

> [!IMPORTANT] 
> N’incluez pas le mot clé « AzureRm » dans un script conçu pour être exécuté avec le module AZ. L'inclusion de ce mot clé, même dans un commentaire, peut entraîner le chargement d'AzureRm, puis un conflit avec le module Az.

## <a name="common-scenarios-available-in-the-powershell-gallery"></a>Scénarios courants disponibles dans PowerShell Gallery

La liste ci-dessous contient quelques runbooks qui fournissent des scénarios courants. Pour obtenir la liste complète des runbooks créés par l’équipe Azure Automation, consultez [Profil AzureAutomationTeam](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) : importe la version la plus récente de tous les modules dans un compte Automation à partir de PowerShell Gallery.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) : configure Diagnostics Azure et Log Analytics pour recevoir les journaux Azure Automation contenant l’état des travaux et les flux des travaux.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) : copie un fichier distant à partir d’une machine virtuelle Microsoft Azure.
   * [Copy-ItemToAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) : copie un fichier local vers une machine virtuelle Azure.

## <a name="contribute-to-the-community"></a>Contribuer à la communauté

Nous vous encourageons vivement à contribuer au développement de la communauté Azure Automation. Partagez les runbooks exceptionnels que vous avez créés avec la communauté. Vos contributions seront appréciées !

### <a name="add-a-runbook-to-the-github-runbook-gallery"></a>Ajouter un runbook à la galerie de runbooks GitHub

Vous pouvez ajouter de nouveaux runbooks PowerShell ou Python à la galerie de runbooks avec ce workflow GitHub.

1. Créez un dépôt public sur GitHub, puis ajoutez le runbook et tous les autres fichiers nécessaires (par exemple readme.md, description, et ainsi de suite).
1. Ajoutez la rubrique `azureautomationrunbookgallery` pour être sûr que le dépôt soit découvert par notre service et puisse être affiché dans la galerie de runbooks Automation.
1. Si le runbook que vous avez créé est un workflow PowerShell, ajoutez la rubrique `PowerShellWorkflow`. S’il s’agit d’un runbook Python 3, ajoutez `Python3`. Aucune autre rubrique spécifique n’est requise pour les runbooks Azure, mais nous vous encourageons à ajouter d’autres rubriques susceptibles d’être utilisées pour la catégorisation et la recherche dans la galerie de runbooks.

   >[!NOTE]
   >Consultez les runbooks existants dans la galerie pour en savoir plus sur des éléments tels que la mise en forme, les en-têtes et les étiquettes existantes (comme `Azure Automation` ou `Linux Azure Virtual Machines`) que vous pouvez utiliser.

Pour suggérer des modifications à un runbook existant, soumettez une demande de tirage (pull request). 

Si vous décidez de cloner et de modifier un runbook existant, la bonne pratique consiste à lui attribuer un nom différent. Si vous réutilisez l’ancien nom, il sera affiché deux fois dans la galerie de runbooks.

>[!NOTE]
>La synchronisation entre GitHub et la galerie de runbooks Automation, pour les runbooks nouveaux et mis à jour, prend au moins 12 heures.

### <a name="add-a-powershell-runbook-to-the-powershell-gallery"></a>Ajouter un runbook PowerShell à PowerShell Gallery

Microsoft vous invite à ajouter à PowerShell Gallery des runbooks dont vous pensez qu’ils pourraient être utiles à d’autres utilisateurs. PowerShell Gallery accepte les modules PowerShell et les scripts PowerShell. Vous pouvez ajouter un runbook en le [chargeant dans PowerShell Gallery](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-modules-gallery-in-the-azure-portal"></a>Importer un module à partir de la galerie des modules dans le portail Azure

1. Dans le portail Azure, ouvrez votre compte Automation.
1. Sous **Ressources partagées**, sélectionnez **Modules**.
1. Dans la page **Modules**, sélectionnez **Parcourir la galerie** pour ouvrir la liste des modules.

      :::image type="content" source="media/automation-runbook-gallery/modules-blade-sm.png" alt-text="Vue de la galerie de modules." lightbox="media/automation-runbook-gallery/modules-blade-lg.png":::

1. Sur la page Parcourir la galerie, vous pouvez effectuer des recherches en fonction des champs suivants :

   * Nom du module
   * Balises
   * Auteur
   * Nom Applet de commande/Ressource DSC

1. Recherchez un module qui vous intéresse et sélectionnez-le pour en afficher les détails.

   Lorsque vous explorez un module spécifique, vous pouvez afficher plus d’informations. Ces informations incluent un lien vers PowerShell Gallery, toutes les dépendances nécessaires et toutes les applets de commande ou ressources DSC que contient le module.

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-details-blade-sm.png" alt-text="Vue détaillée d’un module dans la galerie." lightbox="media/automation-runbook-gallery/gallery-item-details-blade-lg.png":::

1. Pour installer le module directement dans Azure Automation, cliquez sur **Importer**.
1. Dans le volet Importer, vous pouvez voir le nom du module à importer. Si toutes les dépendances sont installées, le bouton **OK** est activé. S'il manque des dépendances, vous devez les importer avant de pouvoir importer ce module.
1. Dans le volet Importer, cliquez sur **OK** pour importer le module. Quand il importe un module dans votre compte, le logiciel Azure Automation extrait les métadonnées sur le module et les cmdlets. Cette action peut prendre quelques minutes, car chaque activité doit être extraite.
1. Vous recevez une première notification indiquant que le module est en cours de déploiement, puis une autre, signalant que le processus est terminé.
1. Après avoir importé le module, vous pouvez voir les activités disponibles. Vous pouvez utiliser les ressources du module dans vos runbooks et ressources DSC.

> [!NOTE]
> Les modules qui prennent uniquement en charge PowerShell Core ne sont pas gérés dans Azure Automation et ne sont pas importables dans le Portail Azure, ni déployables directement à partir de PowerShell Gallery.

## <a name="request-a-runbook-or-module"></a>Demander un runbook ou un module

Vous pouvez envoyer des demandes à [User Voice](https://feedback.azure.com/d365community/forum/8ddd03a2-0225-ec11-b6e6-000d3a4f0858).  Si vous avez besoin d’aide pour écrire un runbook ou si vous avez une question à propos de PowerShell, publiez une question sur notre [page de questions Microsoft Q&A](/answers/topics/azure-automation.html).

## <a name="next-steps"></a>Étapes suivantes

* Pour commencer à utiliser des runbooks PowerShell, consultez [Tutoriel : Créer un runbook PowerShell](./learn/powershell-runbook-managed-identity.md).
* Pour savoir comment utiliser des runbooks, voir [Gérer les runbooks dans Azure Automation](manage-runbooks.md).
* Pour plus d’informations sur les scripts PowerShell, consultez la [documentation PowerShell](/powershell/scripting/overview).
* Pour obtenir des informations de référence sur les applets de commande PowerShell, consultez [Az.Automation](/powershell/module/az.automation).
