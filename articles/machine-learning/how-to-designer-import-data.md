---
title: Importer des données dans le concepteur
titleSuffix: Azure Machine Learning
description: Découvrez comment importer des données dans le concepteur Azure Machine Learning à l’aide de jeux de données Azure Machine Learning et du composant Importer des données.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
author: likebupt
ms.author: keli19
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: designer
ms.openlocfilehash: c434319e48bb2bf1f7d0321a8200e9d42532e659
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289734"
---
# <a name="import-data-into-azure-machine-learning-designer"></a>Importer des données dans le concepteur Azure Machine Learning

Dans cet article, vous allez apprendre à importer vos propres données dans le concepteur afin de créer des solutions personnalisées. Vous pouvez importer des données dans le concepteur de deux manières : 

* **Jeux de données Azure Machine Learning** - Inscrivez des [jeux de données](concept-data.md#datasets) dans Azure Machine Learning pour activer les fonctionnalités avancées qui vous aident à gérer vos données.
* **composant Importer des données** - Utilisez le composant [Importer des données](algorithm-module-reference/import-data.md) pour accéder directement aux données à partir de sources de données en ligne.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-azure-machine-learning-datasets"></a>Utiliser des jeux de données Azure Machine Learning

Nous vous recommandons d’utiliser des [jeux de données](concept-data.md#datasets) pour importer des données dans le concepteur. En inscrivant un jeu de données, vous pouvez tirer pleinement parti des fonctionnalités de données avancées, telles que le [contrôle de version](how-to-version-track-datasets.md) et la [supervision des données](how-to-monitor-datasets.md).

### <a name="register-a-dataset"></a>Inscrire un jeu de données

Vous pouvez inscrire des jeux de données existants [par programmation avec le kit de développement logiciel (SDK)](how-to-create-register-datasets.md#datasets-sdk) ou [visuellement dans Azure Machine Learning Studio](how-to-connect-data-ui.md#create-datasets).

Vous pouvez également inscrire la sortie de n’importe quel composant du concepteur sous forme de jeu de données.

1. Sélectionnez le composant qui génère les données que vous souhaitez inscrire.

1. Dans le volet de propriétés, sélectionnez **Sorties + journaux** > **Inscrire le jeu de données**.

    ![Capture d’écran montrant comment accéder à l'option Inscrire le jeu de données](media/how-to-designer-import-data/register-dataset-designer.png)

Si les données de sortie du composant sont dans un format tabulaire, vous devez choisir d’enregistrer la sortie sous la forme d’un **jeu de données de fichier** ou d’un **jeu de données tabulaire**.

 - Un **jeu de données de fichier** inscrit le dossier de sortie du composant en tant que jeu de données de fichier. Le dossier de sortie contient un fichier de données et des méta-fichiers utilisés en interne par le concepteur. Sélectionnez cette option si vous souhaitez continuer à utiliser le jeu de données inscrit dans le concepteur. 

 - Le **jeu de données tabulaire** inscrit uniquement le fichier de données de sortie du composant sous la forme d’un jeu de données tabulaire. Ce format est facilement utilisé par d’autres outils, par exemple dans le Machine Learning automatisé ou le Kit de développement logiciel (SDK) Python. Sélectionnez cette option si vous envisagez d’utiliser le jeu de données enregistré en dehors du concepteur.  
 

### <a name="use-a-dataset"></a>Utiliser un jeu de données

Vos jeux de données inscrits se trouvent dans la palette de composants, sous **Jeux de données**. Pour utiliser un jeu de données, faites-le glisser et déposez-le sur le canevas du pipeline. Ensuite, connectez le port de sortie du jeu de données à d’autres composants du canevas. 

Si vous inscrivez un jeu de données de fichiers, le type de port de sortie du jeu de données est **AnyDirectory**. Si vous inscrivez un jeu de données tabulaire, le type de port de sortie du jeu de données est **DataFrameDirectory**. Notez que si vous connectez le port de sortie du jeu de données à d’autres composants dans le concepteur, le type de port des jeux de données et des composants doit être aligné.

![Capture d’écran montrant l’emplacement des jeux de données enregistrés dans la palette du concepteur](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> Le concepteur prend en charge le [contrôle de version de jeu de données](how-to-version-track-datasets.md). Spécifiez la version du jeu de données dans le volet des propriétés du composant de jeu de données.

### <a name="limitations"></a>Limites 

- Actuellement, vous pouvez visualiser uniquement le jeu de données tabulaire dans le concepteur. Si vous inscrivez un jeu de données de fichiers en dehors du concepteur, vous ne pouvez pas le visualiser dans le canevas du concepteur.
- Actuellement, le concepteur ne prend en charge que les sorties en préversion qui sont stockées dans le **Stockage Blob Azure**. Vous pouvez vérifier et modifier votre magasin de données de sortie dans les **Paramètres de sortie** sous l’onglet **Paramètres** dans le panneau droit du composant.
- Si vos données sont stockées dans un réseau virtuel (VNet) et que vous souhaitez afficher un aperçu, vous devez activer l’identité managée de l’espace de travail du magasin de données.
    1. Accédez au magasin de données connexe, puis cliquez sur **Mettre à jour l’authentification**
    :::image type="content" source="./media/resource-known-issues/datastore-update-credential.png" alt-text="Mettre à jour les informations d’identification":::.
    1. Sélectionnez **Oui** pour activer l’identité managée de l’espace de travail.
    :::image type="content" source="./media/resource-known-issues/enable-workspace-managed-identity.png" alt-text="Activer l’identité managée de l’espace de travail":::

## <a name="import-data-using-the-import-data-component"></a>Importer des données à l’aide du composant Importer des données

Bien que nous recommandions l’utilisation de jeux de données pour importer des données, vous pouvez également utiliser le composant [Importer des données](algorithm-module-reference/import-data.md). Le composant Importer des données ignore l’inscription de votre jeu de données dans Azure Machine Learning et importe les données directement depuis un [magasin de données](concept-data.md#datastores) ou une URL HTTP.

Pour plus d’informations sur l’utilisation du composant Importer des données, consultez la [page de référence Importer des données](algorithm-module-reference/import-data.md).

> [!NOTE]
> Si votre jeu de données contient trop de colonnes, vous pouvez obtenir l’erreur suivante : « Échec de la validation en raison d’une limitation de taille ». Pour éviter cela, [inscrivez le jeu de données dans l’interface des jeux de données](how-to-connect-data-ui.md#create-datasets).

## <a name="supported-sources"></a>Sources prises en charge

Cette section répertorie les sources de données prises en charge par le concepteur. Les données accèdent au concepteur depuis un magasin de données ou un [jeu de données tabulaires](how-to-create-register-datasets.md#dataset-types).

### <a name="datastore-sources"></a>Sources de magasin de données
Pour obtenir la liste des sources de magasin de données prises en charge, consultez [Accéder aux données dans les services de stockage Azure](how-to-access-data.md#supported-data-storage-service-types).

### <a name="tabular-dataset-sources"></a>Sources du jeu de données tabulaire

Le concepteur prend en charge les jeux de données tabulaires créés à partir des sources suivantes :
 * Fichiers délimités
 * Fichiers JSON
 * Fichiers Parquet
 * Requêtes SQL

## <a name="data-types"></a>Types de données

Le concepteur reconnaît en interne les types de données suivants :

* String
* Integer
* Decimal
* Boolean
* Date

Le concepteur utilise un type de données interne appelé pour transmettre des données entre les composants. Vous pouvez convertir de manière explicite vos données dans un format de table de données à l’aide du composant [Convertir en jeu de données](algorithm-module-reference/convert-to-dataset.md). Tout composant qui accepte des formats autres que le format interne convertit silencieusement les données avant de les transmettre au composant suivant.

## <a name="data-constraints"></a>Contraintes de données

Les modules du concepteur sont limités par la taille de la cible de calcul. En présence de jeux de données plus volumineux, vous devez utiliser une plus grande ressource de calcul Azure Machine Learning. Pour plus d’informations sur le calcul Azure Machine Learning, consultez [Qu’est-ce qu’une cible de calcul dans Azure Machine Learning ?](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="access-data-in-a-virtual-network"></a>Accéder à des données dans un réseau virtuel

Si votre espace de travail se trouve dans un réseau virtuel, vous devez effectuer des étapes de configuration supplémentaires pour visualiser les données dans le concepteur. Pour plus d’informations sur l’utilisation des magasins de données et des jeux de données sur un réseau virtuel, consultez [Utiliser Azure Machine Learning Studio dans un réseau virtuel Azure](how-to-enable-studio-virtual-network.md).

## <a name="next-steps"></a>Étapes suivantes

Découvrez les notions de base du concepteur grâce à ce [tutoriel : Prédire le prix de voitures avec le concepteur](tutorial-designer-automobile-price-train-score.md).
