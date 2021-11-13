---
title: Consulter et télécharger l’utilisation et les frais Azure
description: Découvrez comment télécharger ou voir vos frais et utilisation quotidienne d’Azure, et affichez les ressources disponibles supplémentaires.
keywords: facturation de l’utilisation, frais d’utilisation, téléchargement des informations d’utilisation, afficher l’utilisation, facture azure, utilisation d’azure
author: bandersmsft
ms.author: banders
ms.reviewer: adwise
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.date: 10/22/2021
ms.openlocfilehash: e7f4a5f12ec9e1be5c7129d12ed519bc4c781d61
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130255400"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Afficher et télécharger vos informations d’utilisation et vos frais Azure

Vous pouvez télécharger une répartition quotidienne de votre utilisation d’Azure et de vos frais dans le portail Azure. Vous pouvez également accéder à vos données d’utilisation à l’aide d’Azure CLI. Seuls certains rôles, par exemple l’administrateur de compte ou l’administrateur d’entreprise, sont autorisés à obtenir les informations d’utilisation Azure. Pour en savoir plus sur l’accès aux informations de facturation, consultez [Manage access to Azure billing using roles](../manage/manage-billing-access.md) (Utiliser des rôles pour gérer l’accès à la facturation Azure).

Si vous avez un Contrat Client Microsoft (MCA), vous devez être Gestionnaire de factures, ou Propriétaire, Contributeur ou Lecteur de profil de facturation pour voir votre utilisation d’Azure et les frais encourus.  Si vous avez un Contrat Partenaire Microsoft (MPA), seul le rôle Administrateur général ou Agent d’administration dans l’organisation partenaire peut voir et télécharger les informations sur l’utilisation et les frais Azure.

Selon le type d’abonnement que vous utilisez, les options de téléchargement des informations sur votre utilisation et vos frais varient.

## <a name="download-usage-from-the-azure-portal-csv"></a>Télécharger l’utilisation à partir du portail Azure (.csv)

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Recherchez *Gestion des coûts + facturation*.  
    ![Capture d’écran montrant la recherche de « gestion des coûts + facturation » dans le Portail Azure.](./media/download-azure-daily-usage/portal-cm-billing-search.png)
1. Selon votre type d’accès, vous devrez peut-être sélectionner un compte de facturation ou un profil de facturation.
1. Dans le menu de gauche, sélectionnez **Factures** sous **Facturation**.
1. Dans la grille des factures, recherchez la ligne de la période de facturation correspondant aux informations d’utilisation que vous souhaitez télécharger.
1. Sélectionnez l’**icône de téléchargement** ou les points de suspension (`...`) à droite.  
  ![Capture d’écran montrant la page Factures de Cost Management + Billing avec l’option de téléchargement.](./media/download-azure-daily-usage/download-usage-others.png)  
1. Le volet de téléchargement s’ouvre à droite. Sélectionnez **Télécharger** dans la section **Détails d’utilisation**.  

## <a name="download-usage-for-ea-customers"></a>Télécharger l’utilisation pour les clients EA

Pour voir et télécharger les données d’utilisation en tant que client Contrat Entreprise, vous devez être administrateur d’entreprise, propriétaire de compte ou administrateur de service, et la stratégie d’affichage des coûts doit être activée.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Recherchez *Gestion des coûts + facturation*.  
    ![Capture d’écran montrant une recherche dans le Portail Azure.](./media/download-azure-daily-usage/portal-cm-billing-search.png)
1. Si vous avez accès à plusieurs comptes de facturation, sélectionnez l’étendue de facturation pour votre compte de facturation EA.
1. Sélectionnez **Utilisation + frais**.
1. Pour le mois que vous souhaitez télécharger, sélectionnez **Télécharger**.  
    ![Capture d’écran montrant la page Factures de Cost Management + Billing pour les clients E A.](./media/download-azure-daily-usage/download-usage-ea.png)
1. Dans la page Télécharger l’utilisation + les frais, sous Détails d’utilisation, sélectionnez le type de frais à télécharger dans la liste. En fonction de votre sélection, le fichier CSV fournit tous les frais (utilisation et achats), y compris les achats RI (réservation). Ou les frais amortis (utilisation et achats), y compris les achats de réservation. 
    :::image type="content" source="./media/download-azure-daily-usage/select-usage-detail-charge-type.png" alt-text="Capture d’écran montrant la sélection du type de frais Détails d’utilisation à télécharger." :::
1. Sélectionnez **Préparer le document**.
1.  Azure peut prendre un certain temps pour préparer votre téléchargement, en fonction de votre utilisation mensuelle. Quand le rapport est prêt à être téléchargé, sélectionnez **Télécharger le fichier CSV**.

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>Télécharger les informations d’utilisation pour votre Contrat client Microsoft

Pour afficher et télécharger les données d’utilisation pour un profil de facturation, vous devez être Gestionnaire de factures, Propriétaire, Contributeur ou Lecteur de profil de facturation.

### <a name="download-usage-for-billed-charges"></a>Télécharger les informations d’utilisation pour les frais facturés

1. Recherchez **Gestion des coûts + facturation**.
2. Sélectionnez un profil de facturation.
3. Sélectionnez **Factures**.
4. Dans la grille des factures, recherchez la ligne de la facture correspondant aux informations d’utilisation que vous souhaitez télécharger.
5. Cliquez sur les points de suspension (`...`) à la fin de la ligne.
6. Dans le menu contextuel du téléchargement, sélectionnez **Utilisation et frais Azure**.

### <a name="download-usage-for-open-charges"></a>Télécharger les informations d’utilisation pour les frais en cours

Vous pouvez également télécharger les informations d’utilisation cumulée pour le mois en cours concernant la période de facturation actuelle, ce qui signifie que les frais n’ont pas encore été facturés.

1. Recherchez **Gestion des coûts + facturation**.
2. Sélectionnez un profil de facturation.
3. Dans le panneau **Vue d’ensemble**, cliquez sur **Télécharger l’utilisation et les frais Azure**.

### <a name="download-usage-for-pending-charges"></a>Télécharger les informations d’utilisation et les frais en attente

Si vous avez un Contrat Client Microsoft, vous pouvez télécharger l’utilisation en cumul mensuel à ce jour pour la période de facturation actuelle. Il s’agit des frais d’utilisation qui n’ont pas encore été facturés.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Recherchez *Gestion des coûts + facturation*.
3. Sélectionnez un profil de facturation. Selon votre accès, vous devrez peut-être sélectionner au préalable un compte de facturation.
4. Dans la zone **Vue d’ensemble**, recherchez les liens de téléchargement sous les frais récents.
5. Sélectionnez **Télécharger l’utilisation et les prix**.

## <a name="get-usage-data-with-azure-cli"></a>Accéder à des données d’utilisation avec Azure CLI

Commencez par préparer votre environnement pour Azure CLI :

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Une fois connecté, utilisez la commande [az costmanagement query](/cli/azure/costmanagement#az_costmanagement_query) pour interroger les informations sur l’utilisation du mois en cours pour votre abonnement :

```azurecli
az costmanagement query --timeframe MonthToDate --type Usage --dataset-aggregation '{\"totalCost\":{\"name\":\"PreTaxCost\",\"function\":\"Sum\"}}' --dataset-grouping name="ResourceGroup" type="Dimension"
   --scope "subscriptions/00000000-0000-0000-0000-000000000000"
```

Vous pouvez également affiner la requête à l’aide du paramètre **--dataset-filter** ou d’autres paramètres :

```azurecli
'{\"totalCost\":{\"name\":\"PreTaxCost\",\"function\":\"Sum\"}}' --dataset-grouping name="ResourceGroup" type="Dimension"
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" --dataset-filter "{\"and\":[{\"or\":[{\"dimension\":{\"name\":\"ResourceLocation\",\"operator\":\"In\",\"values\":[\"East US\",\"West Europe\"]}},{\"tag\":{\"name\":\"Environment\",\"operator\":\"In\",\"values\":[\"UAT\",\"Prod\"]}}]},{\"dimension\":{\"name\":\"ResourceGroup\",\"operator\":\"In\",\"values\":[\"API\"]}}]}"
```

Le paramètre **--dataset-filter** prend une chaîne JSON ou `@json-file`.

Vous avez également la possibilité d’utiliser les commandes [az costmanagement export](/cli/azure/costmanagement/export) pour exporter des données d’utilisation vers un compte Stockage Azure. Vous pouvez télécharger les données à partir de là.

1. Créez un groupe de ressources ou utilisez un groupe existant. Exécutez la commande [az group create](/cli/azure/group#az_group_create) pour créer un groupe de ressources :

   ```azurecli
   az group create --name TreyNetwork --location "East US"
   ```

1. Créez un compte de stockage pour recevoir les exportations, ou utilisez un compte de stockage existant. Pour créer un compte, utilisez la commande [az storage account create](/cli/azure/storage/account#az_storage_account_create) :

   ```azurecli
   az storage account create --resource-group TreyNetwork --name cmdemo
   ```

1. Exécutez la commande [az costmanagement export create](/cli/azure/costmanagement/export#az_costmanagement_export_create) pour créer l’exportation :

   ```azurecli
   az costmanagement export create --name DemoExport --type Usage \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-account-id cmdemo \
   --storage-container democontainer --timeframe MonthToDate --storage-directory demodirectory
   ```

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur vos facture et frais d’utilisation, consultez :

- [Comprendre les termes figurant dans le détail de votre utilisation de Microsoft Azure](understand-usage.md)
- [Comprendre votre facture Microsoft Azure](review-individual-bill.md)
- [Afficher et télécharger votre facture Microsoft Azure](download-azure-invoice.md)
- [Afficher et télécharger les tarifs Azure de votre organisation](../manage/ea-pricing.md)

Si vous avez un Contrat client Microsoft, consultez :

- [Comprendre les termes figurant dans les informations d’utilisation Azure détaillées associées à votre Contrat client Microsoft](mca-understand-your-usage.md)
- [Comprendre les frais indiqués sur votre facture du Contrat client Microsoft](review-customer-agreement-bill.md)
- [Afficher et télécharger votre facture Microsoft Azure](download-azure-invoice.md)
- [Afficher et télécharger les documents fiscaux pour votre Contrat client Microsoft](mca-download-tax-document.md)
- [Afficher et télécharger les tarifs Azure de votre organisation](../manage/ea-pricing.md)
