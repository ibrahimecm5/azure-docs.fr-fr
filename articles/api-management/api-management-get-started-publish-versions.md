---
title: 'Tutoriel : Publier les versions d’une API à l’aide de Gestion des API Azure'
description: Suivez les étapes de ce tutoriel pour apprendre à publier plusieurs versions d’API dans Gestion des API.
author: dlepow
ms.service: api-management
ms.custom: mvc, devx-track-azurecli
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: danlep
ms.openlocfilehash: 0f9d43f0b9abd53f76efb1e29d40667f1daf19b3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128644806"
---
# <a name="tutorial-publish-multiple-versions-of-your-api"></a>Tutoriel : Publier plusieurs versions de votre API 

Il est parfois peu pratique que tous les appelants de votre API utilisent exactement la même version. Quand des appelants souhaitent effectuer une mise à niveau vers une version ultérieure, ils veulent une approche facile à comprendre. Comme indiqué dans ce tutoriel, il est possible de fournir plusieurs *versions* dans Gestion des API Azure. 

Pour plus d’informations, consultez [Versions et révisions](https://azure.microsoft.com/blog/versions-revisions/).

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Ajouter une nouvelle version à une API existante
> * Choisir un schéma de version
> * Ajouter la version à un produit
> * Parcourir le portail des développeurs pour afficher la version

:::image type="content" source="media/api-management-getstarted-publish-versions/azure-portal.png" alt-text="Version affichée dans le portail Azure":::

## <a name="prerequisites"></a>Conditions préalables requises

+ Apprenez la [terminologie relative à Gestion des API Azure](api-management-terminology.md).
+ Suivez ce guide de démarrage rapide : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
+ Suivez également le didacticiel suivant : [Importer et publier votre première API](import-and-publish.md).

## <a name="add-a-new-version"></a>Ajouter une nouvelle version

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre instance APIM.
1. Sélectionnez **API**.
1. Dans la liste des API, sélectionnez **API de conférence de démonstration**. 
1. Sélectionnez le menu contextuel ( **...** ) en regard d’**API de conférence de démonstration**.
1. Sélectionnez **Ajouter une version**.

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version-menu.png" alt-text="Menu contextuel de l’API : ajouter une version":::


> [!TIP]
> Les versions peuvent également être activées quand vous créez une API. Dans l’écran **Ajouter l’API**, sélectionnez **Créer une version pour cette API ?** .

## <a name="choose-a-versioning-scheme"></a>Choisissez un schéma de contrôle de version

Dans Gestion des API Azure, vous choisissez la manière dont les appelants spécifient la version de l’API en sélectionnant un *schéma de gestion de versions* : **chemin d’accès, en-tête** ou **chaîne de requête**. Dans l’exemple suivant, le *chemin d’accès* est utilisé comme schéma de gestion de versions.

Entrez les valeurs du tableau suivant. Sélectionnez ensuite **Créer** pour créer votre version.

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version.png" alt-text="Fenêtre Ajouter une version":::



|Paramètre   |Valeur  |Description  |
|---------|---------|---------|
|**Nom**     |  *demo-conference-api-v1*       |  Nom unique dans votre instance Gestion des API.<br/><br/>Une version étant de fait une nouvelle API basée sur la [révision](api-management-get-started-revise-api.md) d’une API, ce paramètre est le nom de la nouvelle API.   |
|**Schéma de gestion de version**     |  **Chemin d’accès**       |  Façon dont les appelants spécifient la version de l’API.     |
|**Identificateur de version**     |  *v1*       |  Indicateur de la version lié au schéma. Pour **Chemin d’accès**, le suffixe du chemin de l’URL de l’API. <br/><br/> Si **En-tête** ou **Chaîne de requête** est sélectionné, entrez une valeur supplémentaire : le nom du paramètre d’en-tête ou de chaîne de requête.<br/><br/> Un exemple d’utilisation est affiché.        |
|**Produits**     |  **Illimité**       |  Éventuellement, un ou plusieurs produits auxquels la version de l’API est associée. Pour publier l’API, vous devez l’associer à un produit. Vous pouvez également [ajouter la version à un produit](#add-the-version-to-a-product) ultérieurement.      |

Une fois la version créée, elle apparaît sous **API de conférence de démonstration** dans la liste des API. Vous voyez maintenant deux API : **Original** et **v1**.

![Versions répertoriées sous une API dans le portail Azure](media/api-management-getstarted-publish-versions/version-list.png)

Vous pouvez maintenant modifier et configurer **v1** en tant qu’API distincte de l’API **Original**. Les modifications apportées à une version n’affectent pas l’autre.

> [!Note]
> Si vous ajoutez une version à une API sans version, une API **Original** est également automatiquement créée. Cette version répond sur l’URL par défaut. La création d’une version Original permet aux appelants existants de ne pas être interrompus par le processus d’ajout d’une version. Si vous créez une API avec des versions activées au démarrage, une API Original n’est pas créée.

## <a name="add-the-version-to-a-product"></a>Ajouter la version à un produit

Pour que les appelants puissent voir la nouvelle version, elle doit être ajoutée à un *produit*. Si vous n’avez pas encore ajouté la version à un produit, vous pouvez le faire à tout moment.

Par exemple, pour ajouter la version au produit **Illimité** :
1. Dans le portail Azure, accédez à votre instance APIM.
1. Sélectionnez **Produits** > **Illimité** > **API** >  **+ Ajouter**.
1. Sélectionnez **API de conférence de démonstration**, version **v1**.
1. Cliquez sur **Sélectionner**.

:::image type="content" source="media/api-management-getstarted-publish-versions/08-add-multiple-versions-03-add-version-product.png" alt-text="Ajouter une version à un produit":::

## <a name="use-version-sets"></a>Utiliser des ensembles de versions

Lorsque vous créez plusieurs versions, le portail Azure crée un *ensemble de versions*, qui représente un ensemble de versions pour une API logique unique. Sélectionnez le nom d’une API qui a plusieurs versions. Le portail Azure affiche son **ensemble de versions**. Vous pouvez personnaliser le **nom** et la **description** d’un ensemble virtuel.

Vous pouvez interagir directement avec les ensembles de versions à l’aide de l’interface Azure CLI :

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Pour afficher tous vos ensembles de versions, exécutez la commande [az apim api versionset list](/cli/azure/apim/api/versionset#az_apim_api_versionset_list) :

```azurecli
az apim api versionset list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Lorsque le portail Azure crée un ensemble de versions pour vous, il attribue un nom alphanumérique, qui apparaît dans la colonne **Nom** de la liste. Utilisez ce nom dans les autres commandes Azure CLI.

Pour afficher les détails d’un ensemble de versions, exécutez la commande [az apim api versionset show](/cli/azure/apim/api/versionset#az_apim_api_versionset_show) :

```azurecli
az apim api versionset show --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --version-set-id 00000000000000000000000
```

Pour plus d’informations sur les ensembles de versions, consultez [Versions dans Gestion des API Azure](api-management-versions.md#how-versions-are-represented).

## <a name="browse-the-developer-portal-to-see-the-version"></a>Parcourir le portail des développeurs pour afficher la version

Si vous avez essayé le [portail des développeurs](api-management-howto-developer-portal-customize.md), vous pouvez y voir les versions de l’API.

1. Sélectionnez **Portail des développeurs** dans le menu supérieur.
2. Sélectionnez **API**, puis **API de conférence de démonstration**.
3. Vous devez voir une liste déroulante avec plusieurs versions à côté du nom de l’API.
4. Sélectionnez **v1**.
5. Notez l’**URL de la demande** de la première opération de la liste. Il indique que le chemin d’accès de l’URL de l’API inclut **v1**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Ajouter une nouvelle version à une API existante
> * Choisir un schéma de version 
> * Ajouter la version à un produit
> * Parcourir le portail des développeurs pour afficher la version

Passez au tutoriel suivant :

> [!div class="nextstepaction"]
> [Personnaliser le style des pages du portail des développeurs](api-management-howto-developer-portal-customize.md)
