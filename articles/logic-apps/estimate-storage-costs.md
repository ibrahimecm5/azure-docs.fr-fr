---
title: Estimer les coûts de stockage pour Azure Logic Apps monolocataire
description: Estimez les coûts de stockage de vos workflows en utilisant la calculatrice de stockage Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/10/2021
ms.openlocfilehash: 995c620eeafc5a627a9f9d94733c80de47f2928d
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132369395"
---
# <a name="estimate-storage-costs-for-workflows-in-single-tenant-azure-logic-apps"></a>Estimer les coûts de stockage des workflows dans Azure Logic Apps monolocataire

Azure Logic Apps utilise [Stockage Azure](../storage/index.yml) pour toutes les opérations de stockage. Dans le modèle Azure Logic Apps *multilocataire* traditionnel, toute utilisation de stockage et tout coût sont attachés à l’application logique. Désormais, dans Azure Logic Apps *monolocataire*, vous pouvez utiliser votre propre compte de stockage. Ces coûts de stockage sont listés séparément dans votre facture Azure. Cette fonctionnalité vous offre plus de souplesse et de contrôle sur vos données d’application logique.

> [!NOTE]
> Cet article s’applique aux workflows de l’environnement Azure Logic Apps monolocataire. Ces workflows se trouvent dans la même application logique et dans un locataire unique qui partagent le même stockage. Pour plus d’informations, consultez [Architecture monolocataire ou multilocataire et environnement de service d’intégration](single-tenant-overview-compare.md).

Les coûts de stockage changent en fonction du contenu de vos workflows. Différents déclencheurs, actions et charges utiles entraînent différents besoins et opérations de stockage. Cet article explique comment estimer vos coûts de stockage quand vous utilisez votre propre compte de stockage Azure avec des applications logiques monolocataires. Tout d’abord, vous pouvez [estimer le nombre d’opérations de stockage que vous effectuerez](#estimate-storage-needs) avec la calculatrice de stockage Logic Apps. Ensuite, vous pouvez [estimer les coûts de stockage possibles](#estimate-storage-costs) en utilisant ces chiffres dans la calculatrice de prix Azure.

## <a name="prerequisites"></a>Prérequis

* Un compte et un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/).

* Un workflow Logic Apps monolocataire. Vous pouvez créer un workflow avec [le portail Azure](create-single-tenant-workflows-azure-portal.md) ou [Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md). Si vous n’avez pas encore de workflow, vous pouvez utiliser les exemples de workflow petit, moyen et grand dans la calculatrice de stockage.

* Un compte de stockage Azure que vous souhaitez utiliser avec votre workflow. Si vous ne possédez pas encore de compte de stockage, [créez-en un](../storage/common/storage-account-create.md).

## <a name="get-your-workflows-json-code"></a>Récupérer le code JSON de votre workflow

Si vous avez un workflow à estimer, récupérez son code JSON :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Accédez au service **Logic Apps**, puis sélectionnez votre workflow.

1. Dans le menu de l’application logique, sous **Outils de développement**, choisissez **Affichage du code de l’application logique**.

1. Copiez le code JSON du workflow.

## <a name="estimate-storage-needs"></a>Estimer les besoins de stockage

1. Accédez à la [calculatrice de stockage Logic Apps](https://logicapps.azure.com/calculator).

   :::image type="content" source="./media/estimate-storage-costs/storage-calculator.png" alt-text="Capture d’écran de la calculatrice de stockage Logic Apps, montrant l’interface d’entrée." lightbox="./media/estimate-storage-costs/storage-calculator.png":::

1. Entrez, chargez ou sélectionnez le code JSON pour un workflow d’application logique monolocataire.

   * Si vous avez copié le code dans la section précédente, collez-le dans la zone **Paste or upload workflow.json** (Coller ou charger workflow.json). 
   * Si votre fichier **workflow.json** est enregistré localement, choisissez **Browse Files** (Parcourir les fichiers) dans la liste déroulante de **sélection de workflow**. Choisissez votre fichier, puis sélectionnez **Ouvrir**.
   * Si vous n’avez pas encore de workflow, choisissez l’un des exemples de workflows dans la liste déroulante de **sélection de workflow**.

1. Passez en revue les options sous **Advanced Options** (Options avancées). Ces paramètres dépendent de votre type de workflow et peuvent inclure :

   * Une option permettant d’entrer le nombre d’exécutions de vos boucles.
   * Une option permettant de sélectionner toutes les actions avec des charges utiles de plus de 32 Ko.

1. Pour **Monthly runs** (Exécutions mensuelles), entrez le nombre d’exécutions mensuelles de votre workflow.

1. Sélectionnez **Calculate** et attendez que le calcul s’exécute.

1. Sous **Storage operations breakdown and pricing calculation steps** (Détails des opérations de stockage et étapes du calcul du prix), examinez les estimations **Operation Counts** (Nombres d’opérations).

    Vous pouvez voir le nombre d’opérations estimé par exécution et par mois dans les deux tableaux. Les opérations suivantes sont indiquées :

    * **Blob (read)** (Blob (lecture)), pour les opérations de lecture du Stockage Blob Azure.
    * **Blob (write)** (Blob (écriture)), pour les opérations d’écriture du Stockage Blob Azure.
    * **Queue** (File d’attente), pour les opérations de files d’attente Azure de classe 2.
    * **Tables**, pour les opérations de Stockage Table Azure.

    Chaque opération a un nombre minimal, maximal et le nombre de « meilleure estimation ». En fonction de votre propre scénario, choisissez le nombre le plus pertinent à utiliser pour [estimer vos coûts d’opérations de stockage](#estimate-storage-costs). Nous vous recommandons d’utiliser le nombre de « meilleure estimation » pour plus de précision. Toutefois, vous pouvez également utiliser le nombre maximal pour vous assurer que l’estimation de vos coûts ait une marge.

    :::image type="content" source="./media/estimate-storage-costs/storage-calculator-results.png" alt-text="Capture d’écran de la calculatrice de stockage Logic Apps, montrant la sortie avec des opérations estimées." lightbox="./media/estimate-storage-costs/storage-calculator-results.png":::

## <a name="estimate-storage-costs"></a>Estimer les coûts de stockage

Une fois que vous avez [calculé les besoins de stockage de votre workflow d’application logique](#estimate-storage-needs), vous pouvez estimer les coûts de stockage mensuels possibles. Vous pouvez estimer les prix pour les types d’opérations de stockage suivants :

* [Opérations de lecture et d’écriture du stockage d’objets blob](#estimate-blob-storage-operations-costs)
* [Opération de stockage de file d’attente](#estimate-queue-operations-costs)
* [Opération de stockage de table](#estimate-table-operations-costs)

### <a name="estimate-blob-storage-operations-costs"></a>Estimer les coûts des opérations de stockage d’objets blob

Pour estimer les coûts mensuels des opérations de stockage d’objets blob de votre application logique :

1. Accédez à la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/).

1. Sous l’onglet **Produits**, sélectionnez **Stockage** &gt; **Comptes de stockage**. Ou, dans la zone de recherche de la **barre de recherche**, entrez **Comptes de stockage** et sélectionnez la vignette.

   :::image type="content" source="./media/estimate-storage-costs/pricing-calculator-storage-tile.png" alt-text="Capture d’écran de la calculatrice de prix Azure, avec la vignette permettant d’ajouter une vue Comptes de stockage." lightbox="./media/estimate-storage-costs/pricing-calculator-storage-tile.png":::

1. Sur la notification **Comptes de stockage a été ajouté**, sélectionnez **Afficher** pour afficher la section **Comptes de stockage** de la calculatrice. Ou, accédez à la section **Comptes de stockage** manuellement.

1. Pour **Région**, sélectionnez la région de votre application logique.

1. Pour **Type**, sélectionnez **Stockage d’objets blob de bloc**.

1. Pour **Niveau de performance**, sélectionnez votre niveau de performance.

1. Pour **Redondance**, sélectionnez votre niveau de redondance.

1. Ajustez les autres paramètres en fonction de vos besoins.

1. Sous **Opérations d'écriture**, entrez le nombre d'opérations **Blob (write)** tel qu'indiqué dans la calculatrice de stockage Logic Apps *divisé par 10 000*. Cette étape est nécessaire, car la calculatrice fonctionne en unités transactionnelles pour les opérations de stockage.

1. Sous **Opérations de lecture**, entrez le nombre d'opérations **Blob (read)** tel qu'indiqué dans la calculatrice de stockage Logic Apps *divisé par 10 000*. Cette étape est nécessaire, car la calculatrice fonctionne en unités transactionnelles pour les opérations de stockage.

1. Passez en revue les coûts d’opérations de stockage d’objets blob estimés.

### <a name="estimate-queue-operations-costs"></a>Estimer les coûts des opérations de file d’attente

Pour estimer les coûts mensuels des opérations de file d’attente de votre application logique :

1. Accédez à la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/).

1. Sous l’onglet **Produits**, sélectionnez **Stockage** &gt; **Comptes de stockage**. Ou, dans la zone de recherche de la **barre de recherche**, entrez **Comptes de stockage** et sélectionnez la vignette.

   :::image type="content" source="./media/estimate-storage-costs/pricing-calculator-storage-tile.png" alt-text="Capture d’écran de la calculatrice de prix Azure, avec la vignette permettant d’ajouter une vue Comptes de stockage." lightbox="./media/estimate-storage-costs/pricing-calculator-storage-tile.png":::

1. Sur la notification **Comptes de stockage a été ajouté**, sélectionnez **Afficher** pour afficher la section **Comptes de stockage** de la calculatrice. Ou, accédez à la section **Comptes de stockage** manuellement.

1. Pour **Région**, sélectionnez la région de votre application logique.

1. Pour **Type**, sélectionnez **Stockage File d’attente**.

1. Pour **Type de compte de stockage**, sélectionnez votre type de compte de stockage.

1. Pour **Redondance**, sélectionnez votre niveau de redondance.

1. Sous **Opérations de file d’attente de classe 2**, entrez le nombre d’opérations de **file d’attente** issu de la calculatrice de stockage Logic Apps *divisé par 10 000*. Cette étape est nécessaire, car la calculatrice fonctionne en unités transactionnelles pour les opérations de file d’attente.

1. Passez en revue les coûts d’opérations de file d’attente estimés.

### <a name="estimate-table-operations-costs"></a>Estimer les coûts d’opérations de table

Pour estimer les coûts mensuels des opérations de stockage de table de votre application logique :

1. Accédez à la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/).

1. Sous l’onglet **Produits**, sélectionnez **Stockage** &gt; **Comptes de stockage**. Ou, dans la zone de recherche de la **barre de recherche**, entrez **Comptes de stockage** et sélectionnez la vignette.

   :::image type="content" source="./media/estimate-storage-costs/pricing-calculator-storage-tile.png" alt-text="Capture d’écran de la calculatrice de prix Azure, avec la vignette permettant d’ajouter une vue Comptes de stockage." lightbox="./media/estimate-storage-costs/pricing-calculator-storage-tile.png":::

1. Sur la notification **Comptes de stockage a été ajouté**, sélectionnez **Afficher** pour afficher la section **Comptes de stockage** de la calculatrice. Ou, accédez à la section **Comptes de stockage** manuellement.

1. Pour **Région**, sélectionnez la région de votre application logique.

1. Pour **Type**, sélectionnez **Stockage Table**.

1. Pour **Niveau**, sélectionnez votre niveau de performance.

1. Pour **Redondance**, sélectionnez votre niveau de redondance.

1. Sous **Transactions de stockage**, entrez le nombre d’opérations de **table** issu de la calculatrice de stockage Logic Apps *divisé par 10 000*. Cette étape est nécessaire, car la calculatrice fonctionne en unités transactionnelles pour les opérations de file d’attente.

1. Passez en revue les coûts d’opérations de stockage de table estimés.

## <a name="next-step"></a>Étape suivante

> [!div class="nextstepaction"]
> [Prévoir et gérer les coûts de Logic Apps](plan-manage-costs.md)