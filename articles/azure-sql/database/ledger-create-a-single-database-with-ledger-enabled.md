---
title: Créer une base de données avec le registre activé
description: Créez une base de données dans Azure SQL Database avec le registre activé en utilisant le portail Azure.
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: quickstart
author: rothja
ms.author: jroth
ms.reviewer: vanto
ms.date: 09/09/2021
ms.openlocfilehash: 8538e725583aea6fe765a7afd6e0ac58f176b443
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063738"
---
# <a name="quickstart-create-a-database-in-azure-sql-database-with-ledger-enabled"></a>Démarrage rapide : Créer une base de données dans Azure SQL Database avec le registre activé

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Le Registre Azure SQL Database est actuellement en préversion publique.

Dans ce démarrage rapide, vous allez créer une [base de données de registre](ledger-overview.md#ledger-database) dans Azure SQL Database, et configurer un [stockage de synthèse automatique avec le service Stockage Blob Azure](ledger-digest-management-and-database-verification.md#automatic-generation-and-storage-of-database-digests) en utilisant le portail Azure. Pour plus d’informations sur le registre, consultez [Registre Azure SQL Database](ledger-overview.md).

## <a name="prerequisite"></a>Configuration requise

Vous avez besoin d’un abonnement Azure actif. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/).

## <a name="create-a-ledger-database-and-configure-digest-storage"></a>Créer une base de données de registre et configurer un stockage de synthèse

Créez une base de données de registre dans le [niveau de calcul serverless](serverless-tier-overview.md), et configurez le chargement de synthèses de registre dans un compte de stockage Azure.

### <a name="use-the-azure-portal"></a>Utiliser le portail Azure

Pour créer une base de données dans le portail Azure, ce démarrage rapide commence dans la page Azure SQL.

1. Accédez à la page [Sélectionner l’option de déploiement SQL](https://portal.azure.com/#create/Microsoft.AzureSQL).

1. Sous **Bases de données SQL**, laissez **Type de ressource** défini sur **Base de données unique**, puis sélectionnez **Créer**.

   ![Capture montrant l’ajout à Azure SQL.](./media/single-database-create-quickstart/select-deployment.png)

1. Sous l’onglet **De base** du formulaire **Créer une base de données SQL**, sous **Détails du projet**, sélectionnez l’abonnement Azure que vous souhaitez utiliser.

1. Pour **Groupe de ressources**, sélectionnez **Créer**, entrez **myResourceGroup**, puis sélectionnez **OK**.

1. Pour **Nom de la base de données**, entrez **démo**.

1. Pour **Serveur**, sélectionnez **Créer**. Remplissez le **formulaire de nouveau serveur** avec les valeurs suivantes :
   - **Nom du serveur** : entrez **mysqlserver** et ajoutez quelques caractères pour l’unicité. Nous ne pouvons pas fournir un nom de serveur exact à utiliser, car les noms de serveur doivent être globalement uniques pour tous les serveurs dans Azure, et non pas seulement dans un abonnement. Entrez un nom tel que **mysqlserver12345**, après quoi le portail vous indique s’il est disponible ou non.
   - **Connexion administrateur au serveur** : entrez **azureuser**.
   - **Mot de passe**: entrez un mot de passe répondant aux exigences. Entrez-le une nouvelle fois dans la zone **Confirmer le mot de passe**.
   - **Emplacement** : sélectionnez un emplacement dans la liste déroulante.
   - **Autoriser les services Azure à accéder à ce serveur** : sélectionnez cette option activer l’accès au stockage de synthèse.
   
   Sélectionnez **OK**.
   
1. Laissez **Vous souhaitez utiliser un pool élastique SQL ?** avec la valeur **Non**.

1. Sous **Calcul + stockage**, sélectionnez **Configurer la base de données**.

1. Comme ce guide de démarrage rapide utilise une base de données serverless, sélectionnez **Serverless**, puis **Appliquer**. 

      ![Capture d’écran montrant la configuration d’une base de données serverless.](./media/single-database-create-quickstart/configure-database.png)

1. Sous l’onglet **Réseau**, pour **Méthode de connectivité**, sélectionnez **Point de terminaison public**.
1. Pour **Règles de pare-feu**, affectez la valeur **Oui** à **Ajouter l’adresse IP actuelle du client**. Laissez **Autoriser les services et les ressources Azure à accéder à ce serveur** avec la valeur **Non**.
1. Sélectionnez **Suivant : Sécurité** en bas de la page.

   :::image type="content" source="media/ledger/ledger-create-database-networking-tab.png" alt-text="Capture d’écran montrant l’onglet Mise en réseau de l’écran Créer un base de données SQL Database dans le portail Azure.":::

1. Sous l’onglet **Sécurité**, dans la section **Registre**, sélectionnez l’option **Configurer un registre**.

    :::image type="content" source="media/ledger/ledger-configure-ledger-security-tab.png" alt-text="Capture d’écran montrant la configuration d’un registre dans l’onglet Sécurité du portail Azure.":::

1. Dans le volet **Configurer un registre**, dans la section **Registre**, activez la case à cocher **Activer pour toutes les futures tables dans cette base de données**. Ce paramètre garantit que toutes les futures tables dans la base de données seront des tables de registre. Ainsi, toutes les données de la base de données présentent une preuve de falsification. Par défaut, les nouvelles tables seront créées en tant que tables de registre pouvant être mises à jour, même si vous ne spécifiez pas `LEDGER = ON` dans [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql). Vous pouvez également conserver cette option désélectionnée. Vous devez alors activer la fonctionnalité de registre pour chaque table lorsque vous créez des tables à l’aide de Transact-SQL.

1. Dans la section **Stockage de synthèse**, l’option **Activer le stockage de synthèse automatique** est automatiquement sélectionnée. Ensuite, un nouveau compte Stockage Azure et un nouveau conteneur où vos synthèses sont stockées est créé.

1. Sélectionnez **Appliquer**.

    :::image type="content" source="media/ledger/ledger-configure-ledger-pane.png" alt-text="Capture d’écran montrant le volet Configurer le registre (préversion) dans le portail Azure.":::

1. Au bas de la page, sélectionnez **Examiner et créer**.

    :::image type="content" source="media/ledger/ledger-review-security-tab.png" alt-text="Capture d’écran montrant la révision et la création d’une base de données de registre dans l’onglet Sécurité du portail Azure.":::

1. Dans la page **Vérifier + créer**, après vérification, sélectionnez **Créer**.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Conservez le groupe de ressources, le serveur et la base de données unique pour les étapes suivantes. Vous allez apprendre à utiliser la fonctionnalité de registre de base de données avec différentes méthodes.

Une fois que vous avez fini d’utiliser ces ressources, supprimez le groupe de ressources que vous avez créé. Cette action supprime également le serveur et la base de données unique qu’il contient.

### <a name="use-the-azure-portal"></a>Utiliser le portail Azure

Pour supprimer **myResourceGroup** et toutes ses ressources à l’aide du portail Azure :

1. Dans le portail, recherchez et sélectionnez **Groupes de ressources**. Sélectionnez ensuite **myResourceGroup** dans la liste
1. Sur la page Groupe de ressources, sélectionnez **Supprimer un groupe de ressources**.
1. Sous **Tapez le nom du groupe de ressources**, entrez **myResourceGroup**, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Connectez-vous à votre base de données et interrogez ses données à l’aide de différents outils et langages :

- [Créer et utiliser des tables de registre pouvant être mises à jour](ledger-how-to-updatable-ledger-tables.md)
- [Créer et utiliser des tables de registre d’ajout uniquement](ledger-how-to-append-only-ledger-tables.md) 
