---
title: Activer les notebooks dans un compte Azure Cosmos DB (préversion)
description: Les notebooks intégrés d’Azure Cosmos DB vous permettent d’analyser et de visualiser vos données sur le portail. Cet article explique comment activer cette fonctionnalité pour des comptes Cosmos.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/24/2021
ms.author: dech
ms.custom: references_regions
ms.openlocfilehash: 19bb4a91a8612feab4218a16eedb2f961b1d0adb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434006"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Activer les notebooks pour un compte Azure Cosmos DB (préversion)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> La fonctionnalité de notebook est temporairement désactivée. L’équipe d’Azure Cosmos DB travaille sur un correctif.  

Les notebooks Jupyter intégrés dans Azure Cosmos DB vous permettent d’analyser et de visualiser vos données via le portail Azure. Cet article décrit comment activer cette fonctionnalité pour votre compte Azure Cosmos DB. Les notebooks pour Azure Cosmos DB sont actuellement disponibles dans [29 régions](#supported-regions). Pour utiliser des notebooks, [créez un compte](#create-a-new-cosmos-account) ou [activez les notebooks sur un compte existant](#enable-notebooks-in-an-existing-cosmos-account) dans l’une de ces régions

## <a name="create-a-new-cosmos-account"></a>Créer un compte Cosmos
À compter du 10 février 2021, les notebooks seront automatiquement activés pour les nouveaux comptes Azure Cosmos créés dans une [région prise en charge](#supported-regions). Aucune configuration supplémentaire n'est requise pour activer les notebooks. Utilisez les instructions suivantes pour créer un compte :
1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Sélectionnez **Créer une ressource** > **Bases de données** > **Azure Cosmos DB**.
1. Entrez les paramètres de base du compte.

   :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail-2.png" alt-text="Page de nouveau compte pour Azure Cosmos DB":::

1. Sélectionnez **Revoir + créer**. Vous pouvez ignorer les options **Réseau** et **Balises**. 
1. Passez en revue les paramètres du compte, puis sélectionnez **Créer**. La création du compte prend quelques minutes. Attendez que la page du portail affiche **Votre déploiement est terminé**.

   :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks-complete.png" alt-text="Volet Notifications du portail Azure":::

1. Sélectionnez **Accéder à la ressource** pour accéder à la page du compte Azure Cosmos DB.

   :::image type="content" source="../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png" alt-text="Page du compte Azure Cosmos DB":::

1. Accédez au volet **Explorateur de données**. Vous devez maintenant voir votre espace de travail Notebooks.

    :::image type="content" source="media/enable-notebooks/new-notebooks-workspace.png" alt-text="Nouvel espace de travail Notebooks d’Azure Cosmos DB":::

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Activer les notebooks dans un compte Cosmos

Vous pouvez également activer les notebooks sur des comptes existants. Cette opération ne doit être effectuée qu’une seule fois par compte.

1. Accédez au volet **Explorateur de données** de votre compte Cosmos.
1. Sélectionnez **Activer les notebooks**.

    :::image type="content" source="media/enable-notebooks/enable-notebooks-workspace.png" alt-text="Créer un espace de travail Notebooks dans l’Explorateur de données":::

1. Vous êtes invité à créer un espace de travail Notebooks. Sélectionnez **Terminer la configuration**.
1. Votre compte est désormais activé pour l’utilisation des notebooks.

## <a name="create-and-run-your-first-notebook"></a>Créer et exécuter votre premier notebook

Pour vérifier que vous pouvez utiliser les notebooks, sélectionnez l’un d’eux sous Exemples de notebooks. Cela a pour effet d’enregistrer une copie du notebook dans votre espace de travail et de l’ouvrir.

Dans cet exemple, nous allons utiliser **gettingstarted.ipynb**.

:::image type="content" source="media/enable-notebooks/select-getting-started-notebook.png" alt-text="Afficher le notebook GettingStarted.ipynb":::

Pour exécuter le notebook :
1. Sélectionnez la première cellule de code contenant du code Python.
1. Sélectionnez **Exécuter** pour exécuter la cellule. Dans la cellule, appuyez sur **Maj+Entrée** pour exécuter la cellule.
1. Actualisez le volet des ressources pour voir la base de données et le conteneur créés.

    :::image type="content" source="media/enable-notebooks/run-first-notebook-cell.png" alt-text="Exécuter le notebook de prise en main":::

Vous pouvez également sélectionner **Nouveau notebook** pour créer un notebook ou charger un fichier de notebook (.ipynb) existant en sélectionnant **Charger le fichier** dans le menu **Mes notebooks**. 

:::image type="content" source="media/enable-notebooks/create-or-upload-new-notebook.png" alt-text="Créer ou charger un nouveau notebook":::

## <a name="supported-regions"></a>Régions prises en charge
Les notebooks intégrés pour Azure Cosmos DB sont actuellement disponibles dans 29 régions Azure. Les notebooks sont automatiquement activés pour les nouveaux comptes Azure Cosmos créés dans ces régions. Les notebooks sont proposés gratuitement avec votre compte. 

- Centre de l’Australie
- Centre de l’Australie 2
- Australie Est
- Australie Sud-Est
- Brésil Sud
- Centre du Canada
- Est du Canada
- Inde centrale
- USA Centre
- USA Est
- USA Est 2
- France Centre
- France Sud
- Allemagne Nord
- Allemagne Centre-Ouest
- OuJapon Est
- Corée du Sud
- Centre-Nord des États-Unis
- Europe Nord
- États-Unis - partie centrale méridionale
- Asie Sud-Est
- Suisse Nord
- Émirats arabes unis Centre
- Sud du Royaume-Uni
- Ouest du Royaume-Uni
- Centre-USA Ouest
- Europe Ouest
- Inde Ouest
- USA Ouest 2

## <a name="next-steps"></a>Étapes suivantes

* Découvrez les avantages des [blocs-notes Jupyter d’Azure Cosmos DB](../cosmosdb-jupyter-notebooks.md)
* [Explorer la galerie d’exemples de notebooks](https://cosmos.azure.com/gallery.html)
* [Publier des notebooks dans la galerie des notebooks Azure Cosmos DB](publish-notebook-gallery.md)
* [Utiliser les fonctionnalités et commandes des notebooks Python](use-python-notebook-features-and-commands.md)
* [Utiliser les fonctionnalités et commandes des notebooks C#](use-csharp-notebook-features-and-commands.md)
* [Importer des notebooks depuis un dépôt GitHub](import-github-notebooks.md)
