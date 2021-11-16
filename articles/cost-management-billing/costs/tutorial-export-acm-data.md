---
title: 'Tutoriel : Créer et gérer des données exportées depuis Azure Cost Management'
description: Cet article explique comment créer et gérer des données Cost Management exportées afin de les utiliser dans des systèmes externes.
author: bandersmsft
ms.author: banders
ms.date: 11/03/2021
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: fdefbf1de5e61d05379dd0cfce07a30038dea4f0
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131502803"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Tutoriel : Créer et gérer des données exportées

Si vous avez lu le tutoriel Analyse du coût, vous êtes familiarisé avec le téléchargement manuel de vos données Cost Management. Cependant, vous pouvez créer une tâche récurrente qui exporte automatiquement sur une base quotidienne, hebdomadaire ou mensuelle vos données Cost Management dans un stockage Azure. Les données exportées sont au format CSV, et elles contiennent toutes les informations collectées par Cost Management. Vous pouvez ensuite utiliser les données exportées dans Stockage Azure avec des systèmes externes et les combiner avec vos propres données personnalisées. Vous pouvez aussi utiliser vos données exportées dans un système externe, comme un tableau de bord ou un autre système financier.

Regardez la vidéo [Comment planifier des exportations à des fins de stockage avec Cost Management](https://www.youtube.com/watch?v=rWa_xI1aRzo) pour créer une exportation planifiée de vos données de coût Azure vers le Stockage Azure. Pour regarder d’autres vidéos, consultez la [chaîne YouTube relative à Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/rWa_xI1aRzo]

Les exemples de ce tutoriel montrent comment exporter vos données de gestion des coûts, puis comment vérifier que les données ont été exportées correctement.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une exportation quotidienne
> * Vérifier que les données sont collectées

## <a name="prerequisites"></a>Prérequis

L’exportation de données est disponible pour divers types de comptes Azure, notamment pour les clients [Contrat Entreprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) et [Contrat client Microsoft](get-started-partners.md). Pour accéder à la liste complète des types de comptes pris en charge, voir [Comprendre les données de Cost Management](understand-cost-mgt-data.md). Les autorisations Azure suivantes, ou étendues, sont prises en charge par abonnement pour l’exportation de données par utilisateur et par groupe. Pour plus d’informations sur les étendues, consultez [Comprendre et utiliser les étendues](understand-work-scopes.md).

- Propriétaire : Peut créer, modifier ou supprimer des exportations planifiées pour un abonnement.
- Contributeur : Peut créer, modifier ou supprimer ses propres exportations planifiées. Peut modifier le nom d’exportations planifiées créées par d’autres utilisateurs.
- Lecteur : Peut planifier des exportations pour lesquelles il dispose des autorisations adéquates.

**Pour plus d’informations sur les étendues, notamment sur l’accès nécessaire pour configurer les exportations pour les étendues Contrat Entreprise et Contrat client Microsoft, consultez [Comprendre et utiliser les étendues](understand-work-scopes.md)** .

Pour les comptes Stockage Azure :
- Des autorisations d’écriture sont nécessaires pour modifier le compte de stockage configuré, indépendamment des autorisations sur l’exportation.
- Votre compte de stockage Azure doit être configuré pour le stockage d’objets blob ou de fichiers.
- Le compte de stockage ne doit pas avoir de pare-feu configuré.

Si vous disposez d’un nouvel abonnement, vous ne pouvez pas utiliser les fonctionnalités de Cost Management tout de suite. Vous risquez de devoir attendre jusqu’à 48 heures avant de pouvoir utiliser toutes les fonctionnalités de Cost Management.

## <a name="sign-in-to-azure"></a>Connexion à Azure
Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Créer une exportation quotidienne

### <a name="portal"></a>[Portail](#tab/azure-portal)

Pour créer ou afficher une exportation de données ou pour planifier une exportation, choisissez une étendue sur le portail Azure et sélectionnez **Analyse du coût** dans le menu. Par exemple, accédez à **Abonnements**, sélectionnez un abonnement dans la liste, puis sélectionnez **Analyse du coût** dans le menu. En haut de la page Analyse des coûts, sélectionnez **Paramètres**, puis **Exportations**.

> [!NOTE]
> - Vous pouvez non seulement créer des exportations sur des abonnements, mais aussi sur des groupes de ressources, des groupes d'administration, des services et des inscriptions. Pour plus d’informations sur les étendues, consultez [Comprendre et utiliser les étendues](understand-work-scopes.md).
> - Quand vous êtes connecté en tant que partenaire dans l’étendue du compte de facturation ou sur le locataire d’un client, vous pouvez exporter des données vers un compte de stockage Azure lié à votre compte de stockage partenaire. Toutefois, vous devez disposer d’un abonnement actif dans votre locataire CSP.

1. Sélectionnez **Ajouter** et entrez un nom pour l'exportation.
1. Pour la **Métrique**, effectuez une sélection :
    - **Coût réel (utilisation et achats)**  : sélectionnez cette option pour exporter l'utilisation standard et les achats.
    - **Coût amorti (utilisation et achats)**  : sélectionnez cette option pour exporter les coûts amortis pour les achats tels que les réservations Azure.
1. Pour le **Type d'exportation**, effectuez une sélection :
    - **Exportation quotidienne des coûts en cumul mensuel à ce jour** – Fournit chaque jour un nouveau fichier d’exportation pour vos coûts en cumul mensuel à ce jour. Les dernières données sont agrégées avec les exportations quotidiennes précédentes.
    - **Exportation hebdomadaire des coûts pour les sept derniers jours** – Crée une exportation hebdomadaire de vos coûts pour les sept derniers jours à compter de la date de début d’exportation sélectionnée.
    - **Exportation mensuelle des coûts du mois précédent** – Fournit une exportation des coûts du mois précédent par rapport au mois en cours. Par la suite, la planification exécute une exportation le cinquième jour de chaque nouveau mois avec vos coûts des mois précédents.
    - **Exportation unique** – Vous permet de choisir une plage de dates pour les données historiques à exporter vers le service Stockage Blob Azure. Vous pouvez exporter un maximum de 90 jours de coûts historiques à partir du jour de votre choix. Cette exportation s'exécute immédiatement et est disponible sur votre compte de stockage dans les deux heures.
        En fonction du type d'exportation, choisissez une date de début ou une date au format **De** et **À**.
1. Spécifiez l'abonnement associé à votre compte de stockage Azure, puis sélectionnez un groupe de ressources ou créez-en un.
1. Sélectionnez le nom du compte de stockage ou créez-en un.
1. Sélectionnez l'emplacement (région Azure).
1. Spécifiez le conteneur de stockage et le chemin du répertoire que vous souhaitez utiliser pour le fichier d’exportation.
    :::image type="content" source="./media/tutorial-export-acm-data/basics_exports.png" alt-text="Nouvel exemple d'exportation" lightbox="./media/tutorial-export-acm-data/basics_exports.png":::
1. Vérifiez vos informations d’exportation, puis sélectionnez **Créer**.

Votre nouvelle exportation apparaît dans la liste des exportations. Par défaut, les nouvelles exportations sont activées. Si vous voulez désactiver ou supprimer une exportation planifiée, sélectionnez n’importe quel élément de la liste, puis sélectionnez **Désactiver** ou **Supprimer**.

Au départ, l'exportation peut prendre 12 à 24 heures. Mais l'affichage des données dans les fichiers exportés peut prendre plus de temps.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Quand vous créez une exportation programmatiquement, vous devez inscrire manuellement le fournisseur de ressources `Microsoft.CostManagementExports` auprès de l’abonnement où se trouve le compte de stockage. L’inscription s’effectue automatiquement quand vous créez l’exportation à l’aide du portail Azure. Pour plus d’informations sur l’inscription d’un fournisseur de ressources, consultez [Inscrire un fournisseur de ressources](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).

Commencez par préparer votre environnement pour Azure CLI :

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

1. Une fois connecté, pour voir vos exportations en cours, utilisez la commande [az costmanagement export list](/cli/azure/costmanagement/export#az_costmanagement_export_list) :

   ```azurecli
   az costmanagement export list --scope "subscriptions/00000000-0000-0000-0000-000000000000"
   ```

   >[!NOTE]
   >
   >* Vous pouvez créer des exportations non seulement pour des abonnements, mais aussi pour des groupes de ressources et des groupes d’administration. Pour plus d’informations sur les étendues, consultez [Comprendre et utiliser les étendues](understand-work-scopes.md).
   >* Quand vous êtes connecté en tant que partenaire dans l’étendue du compte de facturation ou sur le locataire d’un client, vous pouvez exporter des données vers un compte de stockage Azure lié à votre compte de stockage partenaire. Toutefois, vous devez disposer d’un abonnement actif dans votre locataire CSP.

1. Créez un groupe de ressources ou utilisez un groupe existant. Pour créer un groupe de ressources, utilisez la commande [az group create](/cli/azure/group#az_group_create) :

   ```azurecli
   az group create --name TreyNetwork --location "East US"
   ```

1. Créez un compte de stockage pour recevoir les exportations, ou utilisez un compte de stockage existant. Pour créer un compte de stockage, utilisez la commande [az storage account create](/cli/azure/storage/account#az_storage_account_create) :

   ```azurecli
   az storage account create --resource-group TreyNetwork --name cmdemo
   ```

1. Exécutez la commande [az costmanagement export create](/cli/azure/costmanagement/export#az_costmanagement_export_create) pour créer l’exportation :

   ```azurecli
   az costmanagement export create --name DemoExport --type ActualCost \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-account-id cmdemo \
   --storage-container democontainer --timeframe MonthToDate --recurrence Daily \
   --recurrence-period from="2020-06-01T00:00:00Z" to="2020-10-31T00:00:00Z" \
   --schedule-status Active --storage-directory demodirectory
   ```

   Pour le paramètre **--type**, vous pouvez choisir `ActualCost`, `AmortizedCost` ou `Usage`.

   Cet exemple utilise `MonthToDate`. L’exportation crée un fichier d’exportation tous les jours pour les coûts du mois en cours. Les dernières données sont ajoutées aux exportations quotidiennes précédentes pour le mois en question.

1. Pour afficher les détails de votre opération d’exportation, utilisez la commande [az costmanagement export show](/cli/azure/costmanagement/export#az_costmanagement_export_show) :

   ```azurecli
   az costmanagement export show --name DemoExport \
      --scope "subscriptions/00000000-0000-0000-0000-000000000000"
   ```

1. Pour mettre une exportation à jour, exécutez la commande [az costmanagement export update](/cli/azure/costmanagement/export#az_costmanagement_export_update) :

   ```azurecli
   az costmanagement export update --name DemoExport
      --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-directory demodirectory02
   ```

   Cet exemple change le répertoire de sortie.

>[!NOTE]
>Au départ, l'exportation peut prendre 12 à 24 heures. Mais l’affichage des données dans les fichiers exportés peut prendre plus de temps.

Vous pouvez supprimer une exportation à l’aide de la commande [az costmanagement export delete](/cli/azure/costmanagement/export#az_costmanagement_export_delete) :

```azurecli
az costmanagement export delete --name DemoExport --scope "subscriptions/00000000-0000-0000-0000-000000000000"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Quand vous créez une exportation programmatiquement, vous devez inscrire manuellement le fournisseur de ressources `Microsoft.CostManagementExports` auprès de l’abonnement où se trouve le compte de stockage. L’inscription s’effectue automatiquement quand vous créez l’exportation à l’aide du portail Azure. Pour plus d’informations sur l’inscription d’un fournisseur de ressources, consultez [Inscrire un fournisseur de ressources](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).

Commencez par préparer votre environnement pour Azure PowerShell :

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Tant que le module PowerShell **Az.CostManagement** est en préversion, vous devez l’installer séparément à l’aide de l’applet de commande `Install-Module`. Une fois que ce module PowerShell sera en disponibilité générale, il fera partie intégrante des versions futures du module PowerShell Az et sera disponible par défaut dans Azure Cloud Shell.

  ```azurepowershell-interactive
  Install-Module -Name Az.CostManagement
  ```

1. Une fois connecté, pour voir vos exportations en cours, utilisez l’applet de commande [Get-AzCostManagementExport](/powershell/module/Az.CostManagement/get-azcostmanagementexport) :

   ```azurepowershell-interactive
   Get-AzCostManagementExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
   ```

   >[!NOTE]
   >
   >* Vous pouvez créer des exportations non seulement pour des abonnements, mais aussi pour des groupes de ressources et des groupes d’administration. Pour plus d’informations sur les étendues, consultez [Comprendre et utiliser les étendues](understand-work-scopes.md).
   >* Quand vous êtes connecté en tant que partenaire dans l’étendue du compte de facturation ou sur le locataire d’un client, vous pouvez exporter des données vers un compte de stockage Azure lié à votre compte de stockage partenaire. Toutefois, vous devez disposer d’un abonnement actif dans votre locataire CSP.

1. Créez un groupe de ressources ou utilisez un groupe existant. Pour créer un groupe de ressources, utilisez l’applet de commande [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) :

   ```azurepowershell-interactive
   New-AzResourceGroup -Name TreyNetwork -Location eastus
   ```

1. Créez un compte de stockage pour recevoir les exportations, ou utilisez un compte de stockage existant. Pour créer un compte de stockage, utilisez l’applet de commande [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) :

   ```azurepowershell-interactive
   New-AzStorageAccount -ResourceGroupName TreyNetwork -AccountName cmdemo -SkuName Standard_RAGRS -Location eastus
   ```

1. Exécutez l’applet de commande [New-AzCostManagementExport](/powershell/module/Az.CostManagement/new-azcostmanagementexport) pour créer l’exportation :

   ```azurepowershell-interactive
   $Params = @{
     Name = 'DemoExport'
     DefinitionType = 'ActualCost'
     Scope = 'subscriptions/00000000-0000-0000-0000-000000000000'
     DestinationResourceId = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/treynetwork/providers/Microsoft.Storage/storageAccounts/cmdemo'
     DestinationContainer = 'democontainer'
     DefinitionTimeframe = 'MonthToDate'
     ScheduleRecurrence = 'Daily'
     RecurrencePeriodFrom = '2020-06-01T00:00:00Z'
     RecurrencePeriodTo = '2020-10-31T00:00:00Z'
     ScheduleStatus = 'Active'
     DestinationRootFolderPath = 'demodirectory'
     Format = 'Csv'
   }
   New-AzCostManagementExport @Params
   ```

   Pour le paramètre **DefinitionType**, vous pouvez choisir `ActualCost`, `AmortizedCost` ou `Usage`.

   Cet exemple utilise `MonthToDate`. L’exportation crée un fichier d’exportation tous les jours pour les coûts du mois en cours. Les dernières données sont ajoutées aux exportations quotidiennes précédentes pour le mois en question.

1. Pour afficher les détails de votre opération d’exportation, utilisez l’applet de commande `Get-AzCostManagementExport` :

   ```azurepowershell-interactive
   Get-AzCostManagementExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
   ```

1. Mettez à jour une exportation en utilisant l’applet de commande [Update-AzCostManagementExport](/powershell/module/Az.CostManagement/update-azcostmanagementexport) :

   ```azurepowershell-interactive
   Update-AzCostManagementExport -Name DemoExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000' -DestinationRootFolderPath demodirectory02
   ```

   Cet exemple change le répertoire de sortie.

>[!NOTE]
>Au départ, l'exportation peut prendre 12 à 24 heures. Mais l’affichage des données dans les fichiers exportés peut prendre plus de temps.

Vous pouvez supprimer une exportation au moyen de l’applet de commande [Remove-AzCostManagementExport](/powershell/module/Az.CostManagement/remove-azcostmanagementexport) :

```azurepowershell-interactive
Remove-AzCostManagementExport -Name DemoExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
```

---

### <a name="export-schedule"></a>Planification des exportations

Les exportations planifiées dépendent de l’heure et du jour de la semaine de la création initiale des exportations. Quand vous créez une exportation planifiée, l’exportation s’exécute selon la même fréquence pour chaque exportation ultérieure. Par exemple, pour une exportation des coûts en cumul mensuel à ce jour définie sur une fréquence quotidienne, l'exportation s'exécute tous les jours. De même, pour une exportation hebdomadaire, l’exportation s’exécute toutes les semaines le même jour que celui planifié. Le temps de remise exact de l’exportation n’est pas garanti, et les données exportées sont disponibles dans un délai d’exécution de quatre heures.

Chaque exportation crée un fichier, ce qui signifie que les exportations antérieures ne sont pas écrasées.

#### <a name="create-an-export-for-multiple-subscriptions"></a>Création d’une exportation pour plusieurs abonnements

Si vous possédez un Accord Entreprise, vous pouvez utiliser un groupe d’administration pour agréger les informations sur les coûts d’abonnement dans un seul conteneur. Vous pouvez ensuite exporter les données de gestion des coûts pour le groupe d’administration. Les exportations pour les groupes d’administration prennent uniquement en charge les coûts réels.

Les exportations pour les groupes d’administration d’autres types d’abonnement ne sont pas prises en charge.

1. Si vous n’avez pas encore créé de groupe d’administration, créez-en un et attribuez-lui des abonnements.
1. Dans l'analyse des coûts, définissez l'étendue de votre groupe d'administration et sélectionnez **Sélectionner ce groupe d'administration**.
    :::image type="content" source="./media/tutorial-export-acm-data/management-group-scope.png" alt-text="Exemple illustrant l'option Sélectionner ce groupe d'administration" lightbox="./media/tutorial-export-acm-data/management-group-scope.png":::
1. Créez une exportation selon l’étendue pour obtenir les données de gestion des coûts pour les abonnements dans le groupe d’administration.
    :::image type="content" source="./media/tutorial-export-acm-data/new-export-management-group-scope.png" alt-text="Exemple montrant l’option Créer une exportation avec l’étendue Groupe d’administration":::

### <a name="file-partitioning-for-large-datasets"></a>Partitionnement de fichiers pour les jeux de données volumineux

Si vous disposez d’un Contrat client Microsoft, d’un Contrat Partenaire Microsoft ou d’un Contrat Entreprise, vous pouvez activer les exportations pour fragmenter votre fichier en plusieurs partitions de fichiers plus petites en vue de faciliter l’ingestion des données. Lors de la phase initiale de configuration de l’exportation, définissez le paramètre **Partitionnement de fichiers** sur **Activé**. Ce paramètre est défini sur **Désactivé** par défaut.

:::image type="content" source="./media/tutorial-export-acm-data/file-partition.png" alt-text="Capture d’écran montrant l’option Partitionnement de fichiers." lightbox="./media/tutorial-export-acm-data/file-partition.png" :::

Si vous ne disposez pas d’un Contrat client Microsoft, d’un Contrat Partenaire Microsoft ni d’un Contrat Entreprise, l’option **Partitionnement de fichiers** ne s’affiche pas.

#### <a name="update-existing-exports-to-use-file-partitioning"></a>Mettre à jour les exportations existantes pour utiliser le partitionnement de fichiers

Si vous avez des exportations existantes et que vous souhaitez configurer le partitionnement de fichiers, créez une nouvelle exportation. Le partitionnement de fichiers n’est disponible qu’avec la dernière version des exportations. Certains champs contenus dans les fichiers d’utilisation créés peuvent avoir fait l’objet de modifications mineures.

Si vous activez le partitionnement de fichiers pour une exportation existante, vous remarquerez peut-être que des modifications mineures ont été apportées aux champs de la sortie des fichiers. Les modifications sont dues aux mises à jour dont ont fait l’objet les exportations après la configuration initiale des vôtres.

#### <a name="partitioning-output"></a>Sortie du partitionnement

Quand le partitionnement de fichiers est activé, vous obtenez un fichier pour chaque partition de données de l’exportation avec un fichier _manifest.json. Le manifeste contient un résumé du jeu de données complet et des informations pour chaque partition de fichier qu’il contient. Chaque partition de fichier présente des en-têtes et ne contient qu’un sous-ensemble du jeu de données complet. Pour gérer le jeu de données complet, vous devez ingérer chaque partition de l’exportation.

Voici un exemple de fichier manifeste _manifest.json.

```json
{
  "manifestVersion": "2021-01-01",
  "dataFormat": "csv",
  "blobCount": 1,
  "byteCount": 160769,
  "dataRowCount": 136,
  "blobs": [
    {
      "blobName": "blobName.csv",
      "byteCount": 160769,
      "dataRowCount": 136,
      "headerRowCount": 1,
      "contentMD5": "md5Hash"
    }
  ]
}
```

### <a name="export-versions"></a>Versions d’exportation

Lorsque vous créez une exportation planifiée dans le portail Azure ou à l’aide de l’API, l’exécution se fait toujours sur la version d’exportation utilisée au moment de la création. Azure conserve vos exportations créées précédemment sur la même version, à moins que vous ne la mettiez à jour. En procédant ainsi, vous évitez toute modification des frais et des champs CSV si la version d’exportation est modifiée. Lorsque la fonctionnalité d’exportation change au fil du temps, les noms de champs sont parfois modifiés et de nouveaux champs sont ajoutés.

Si vous souhaitez utiliser les données et champs les plus récents à votre disposition, nous vous recommandons de créer une nouvelle exportation dans le portail Azure. Pour mettre à jour une exportation existante vers la version la plus récente, effectuez cette opération dans le portail Azure ou à l’aide de la dernière version de l’API d’exportation. La mise à jour d’une exportation existante peut vous amener à constater de légères différences dans les champs et les frais des fichiers produits par la suite.


## <a name="verify-that-data-is-collected"></a>Vérifier que les données sont collectées

Vous pouvez facilement vérifier que vos données Cost Management sont collectées et visualiser le fichier CSV exporté avec l’Explorateur Stockage Azure.

Dans la liste des exportations, sélectionnez le nom du compte de stockage. Dans la page du compte de stockage, sélectionnez Ouvrir dans l’Explorateur. Si vous voyez une boîte de confirmation, sélectionnez **Oui** pour ouvrir le fichier dans l’Explorateur Stockage Azure.

![Page de compte de stockage affichant des exemples d’informations et un lien pour les ouvrir dans l’Explorateur](./media/tutorial-export-acm-data/storage-account-page.png)

Dans l’Explorateur Stockage, accédez au conteneur que vous voulez ouvrir, puis sélectionnez le dossier correspondant au mois en cours. Une liste de fichiers CSV s’affiche. Sélectionnez-en un, puis sélectionnez **Ouvrir**.

![Exemples d’informations affichées dans l’Explorateur de stockage](./media/tutorial-export-acm-data/storage-explorer.png)

Le fichier s’ouvre avec le programme ou l’application configuré pour ouvrir les fichiers avec l’extension CSV. Voici un exemple dans Excel.

![Exemples de données CSV exportées affichées dans Excel](./media/tutorial-export-acm-data/example-export-data.png)

### <a name="download-an-exported-csv-data-file"></a>Télécharger un fichier de données CSV exporté

Vous pouvez également télécharger le fichier CSV exporté dans le Portail Azure. Les étapes suivantes expliquent comment le trouver dans l’analyse des coûts.

1. Dans l’analyse des coûts, sélectionnez **Paramètres**, puis **Exportations**.
1. Dans la liste des exportations, sélectionnez le compte de stockage pour une exportation.
1. Dans votre compte de stockage, sélectionnez **Conteneurs**.
1. Dans la liste des conteneurs, sélectionnez le conteneur souhaité.
1. Parcourez les répertoires et les objets BLOB de stockage jusqu’à la date de votre choix.
1. Sélectionnez le fichier CSV, puis **Télécharger**.

[![Exemple de téléchargement d’exportation](./media/tutorial-export-acm-data/download-export.png)](./media/tutorial-export-acm-data/download-export.png#lightbox)

## <a name="view-export-run-history"></a>Consulter l'historique des exécutions des exportations

Vous pouvez consulter l'historique d'exécution de votre exportation planifiée en sélectionnant une exportation individuelle sur la page répertoriant les exportations. La page contenant la liste des exportations vous permet également d'accéder rapidement à la durée d'exécution de vos exportations précédentes et de savoir quand la prochaine exportation aura lieu. Voici un exemple illustrant l'historique des exécutions.

:::image type="content" source="./media/tutorial-export-acm-data/run-history.png" alt-text="Capture d’écran montrant le volet Exportations.":::

Sélectionnez une exportation pour afficher l'historique des exécutions de celle-ci.

:::image type="content" source="./media/tutorial-export-acm-data/single-export-run-history.png" alt-text="Capture d’écran montrant l’historique des exécutions d’une exportation.":::

## <a name="access-exported-data-from-other-systems"></a>Accéder à des données exportées à partir d’autres systèmes

Un des objectifs de l’exportation de vos données Cost Management est d’accéder à ces données à partir de systèmes externes. Vous pouvez utiliser un système de tableau de bord ou un autre système financier. Ces systèmes peuvent grandement varier : montrer un exemple ne serait donc pas pratique.  Vous pouvez cependant découvrir comment accéder à vos données à partir de vos applications dans [Introduction à Stockage Azure](../../storage/common/storage-introduction.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer une exportation quotidienne
> * Vérifier que les données sont collectées

Passez au tutoriel suivant pour optimiser et améliorer l’efficacité en identifiant les ressources inactives et sous-utilisées.

> [!div class="nextstepaction"]
> [Consulter des recommandations d’optimisation et agir en fonction](tutorial-acm-opt-recommendations.md)
