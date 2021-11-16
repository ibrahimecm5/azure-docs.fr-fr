---
title: Utilisation des ressources Azure dans la NER personnalisée
titleSuffix: Azure Cognitive Services
description: Apprenez-en davantage sur les étapes d’utilisation des ressources Azure avec la NER personnalisée.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, references_regions, ignite-fall-2021
ms.openlocfilehash: c7dfe80162ab406c6dde32ad54ff251e001f5416
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027676"
---
# <a name="how-to-create-custom-ner-projects"></a>Guide pratique pour créer des projets de NER personnalisée

Avant de commencer à utiliser la NER personnalisée, vous aurez besoin de plusieurs choses :

* Une ressource Azure Language 
* Un compte Stockage Azure dans lequel vous chargerez vos fichiers `.txt` qui seront utilisés pour entraîner un modèle d’intelligence artificielle afin de classer du texte

Utilisez cet article pour apprendre à préparer la configuration requise pour l’utilisation de la NER personnalisée.

## <a name="prerequisites"></a>Prérequis

Un abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/cognitive-services).
Vous devez avoir une idée du [schéma de projet](design-schema.md) que vous utiliserez pour vos données.

Utilisez cet article pour apprendre à préparer la configuration requise pour l’utilisation de la classification de texte personnalisée.

## <a name="azure-resources"></a>Ressources Azure

Avant de commencer à utiliser la NER personnalisée, vous aurez besoin d’une ressource Azure Language. Nous vous recommandons de suivre les étapes du [Guide de démarrage rapide](../quickstart.md) pour en créer une dans le portail Azure. La création d’une ressource dans le portail Azure vous permet de créer un compte Stockage Azure en même temps, avec toutes les autorisations requises préconfigurées. Vous pouvez également consulter la suite de l’article pour savoir comment utiliser une ressource préexistante et la configurer pour qu’elle fonctionne avec la NER personnalisée.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

[!INCLUDE [create a new resource from the Azure portal](../../custom-classification/includes/resource-creation-azure-portal.md)]

# <a name="language-studio"></a>[Language Studio](#tab/studio)

### <a name="create-a-new-resource-from-language-studio"></a>Créer une ressource à partir de Language Studio

Si c’est la première fois que vous vous connectez, vous voyez apparaître une fenêtre dans [Language Studio](https://aka.ms/languageStudio) pour vous permettre de choisir une ressource Language ou d’en créer une nouvelle. Vous pouvez également créer une ressource en cliquant sur l’icône des paramètres dans le coin supérieur droit, en sélectionnant **Ressources**, puis en cliquant sur **Créer une ressource**.

> [!IMPORTANT]
> * Pour utiliser la NER personnalisée, vous aurez besoin d’une ressource Language dans la région **USA Ouest 2** ou **Europe Ouest** avec le niveau tarifaire Standard (**S**).
> * Veillez à sélectionner **Identité managée** lorsque vous créez une ressource. 

:::image type="content" source="../../media/create-new-resource-small.png" alt-text="Capture d’écran montrant l’écran de création de ressource dans Language Studio." lightbox="../../media/create-new-resource.png":::

Pour utiliser la NER personnalisée, vous devez [créer un compte Stockage Azure](/azure/storage/common/storage-account-create) si vous n’en avez pas encore un. 

Ensuite, vous devez affecter les [rôles appropriés](#roles-for-your-storage-account) pour le compte de stockage afin de le connecter à votre ressource Language. 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

### <a name="create-a-new-resource-with-the-azure-powershell"></a>Créer une ressource avec Azure PowerShell

Vous pouvez créer une ressource et un compte de stockage en utilisant les fichiers de [modèles](https://github.com/Azure-Samples/cognitive-services-sample-data-files) et de [paramètres](https://github.com/Azure-Samples/cognitive-services-sample-data-files) CLI suivants, qui sont hébergés sur GitHub.

Modifiez les valeurs suivantes dans le fichier de paramètres :

| Nom du paramètre | Description de la valeur |
|--|--|
|`name`| Nom de votre ressource Language|
|`location`| Région dans laquelle votre ressource est hébergée. La NER personnalisée est uniquement disponible dans les régions **USA Ouest 2** et **Europe Ouest**.|
|`sku`| Niveau tarifaire de votre ressource. Cette fonctionnalité fonctionne uniquement avec le niveau **S**|
|`storageResourceName`| Nom de votre compte de stockage|
|`storageLocation`| Région où votre compte de stockage est hébergé.|
|`storageSkuType`| Référence SKU de votre [compte de stockage](/rest/api/storagerp/srp_sku_types).|
|`storageResourceGroupName`| Groupe de ressources de votre compte de stockage|
<!-- |builtInRoleType| Définissez ce rôle sur **« Contributeur »**| -->

Utilisez la commande PowerShell suivante pour déployer le modèle Azure Resource Manager (ARM) avec les fichiers que vous avez modifiés.

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile <path-to-arm-template> `
  -TemplateParameterFile <path-to-parameters-file>
```

Consultez la documentation du modèle ARM pour plus d’informations sur le [déploiement de modèles](/azure/azure-resource-manager/templates/deploy-powershell#parameter-files) et les [fichiers de paramètres](/azure/azure-resource-manager/templates/parameter-files?tabs=json).

--- 

## <a name="using-a-pre-existing-azure-resource"></a>Utilisation d’une ressource Azure préexistante

Vous pouvez utiliser une ressource Language existante pour commencer à utiliser la NER personnalisée tant que cette ressource répond aux exigences suivantes :

|Condition requise  |Description  |
|---------|---------|
|Régions     | Vérifiez que votre ressource existante est provisionnée dans une des deux régions prises en charge, **USA Ouest 2** ou **Europe Ouest**. Si ce n’est pas le cas, vous devrez créer une ressource dans ces régions.        |
|Niveau tarifaire     | Vérifiez que votre ressource existante est au niveau tarifaire Standard (**S**). Seul ce niveau tarifaire est pris en charge. Si votre ressource n’utilise pas ce niveau tarifaire, vous devrez créer une ressource.        |
|Identité managée     | Vérifiez que le paramètre d’identité managée par la ressource est activé. Si ce n’est pas le cas, lisez la section suivante. |

Pour utiliser la NER personnalisée, vous devez [créer un compte Stockage Azure](/azure/storage/common/storage-account-create) si vous n’en avez pas encore un. 

Ensuite, vous devez affecter les [rôles appropriés](#roles-for-your-storage-account) pour le compte de stockage afin de le connecter à votre ressource Language. 

## <a name="roles-for-your-azure-language-resource"></a>Rôles pour votre ressource Azure Language

Le rôle de **propriétaire** ou de **contributeur** doit vous être attribué sur votre ressource Azure Language.

## <a name="enable-identity-management-for-your-resource"></a>Activer la gestion des identités pour votre ressource

Votre ressource Language doit disposer de la gestion des identités, qui peut être activée en utilisant le portail Azure ou Language Studio. Pour l’activer en utilisant [Language Studio](https://aka.ms/languageStudio) :
1. Cliquez sur l’icône Paramètres dans le coin supérieur droit de l’écran.
2. Sélectionnez **Ressources**
3. Sélectionnez **Identité managée** pour votre ressource Azure.

## <a name="roles-for-your-storage-account"></a>Rôles pour votre compte de stockage

Votre compte Stockage Blob Azure doit avoir les rôles suivants :

* Votre ressource a le rôle **propriétaire** ou **contributeur** sur le compte de stockage.
* Votre ressource a le rôle **Propriétaire des données Blob du stockage** ou **Contributeur aux données blob du stockage** sur le compte de stockage.
* Votre ressource a le rôle **Lecteur** sur le compte de stockage.

Pour définir les rôles appropriés sur votre compte de stockage :

1. Accédez à la page de votre compte de stockage dans le [portail Azure](https://ms.portal.azure.com/).
2. Sélectionnez **Contrôle d’accès (IAM)** dans le menu de navigation de gauche.
3. Sélectionnez **Ajouter** pour **Ajouter une attribution de rôle** et choisissez le rôle **Propriétaire** ou **Contributeur**. Vous pouvez rechercher des noms d’utilisateur dans le champ **Sélectionner**.

[!INCLUDE [Storage connection note](../../custom-classification/includes/storage-account-note.md)]

## <a name="prepare-training-data"></a>Préparer des données d’entraînement

* En guise de prérequis à la création d’un projet de NER personnalisée, vos données d’entraînement doivent être chargées vers un conteneur d’objets blob dans votre compte de stockage. Vous pouvez créer et charger des fichiers d’entraînement à partir d’Azure directement ou par le biais de l’outil Explorateur Stockage Azure. L’utilisation de l’outil Explorateur Stockage Azure vous permet de charger plus de données en moins de temps.

  * [Créer et charger des fichiers à partir d’Azure](/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)
  * [Créer et charger des fichiers en utilisant l’Explorateur Stockage Azure](/azure/vs-azure-tools-storage-explorer-blobs)

* Vous ne pouvez utiliser que des fichiers `.txt` pour la NER personnalisée. Si vos données sont dans un autre format, vous pouvez utiliser l’[outil Cognitive Services Language Utilities](https://aka.ms/CognitiveServicesLanguageUtilities) pour analyser votre fichier au format `.txt`.

* Vous pouvez charger des données étiquetées ou étiqueter vos données dans Language Studio. Les données étiquetées doivent respecter le [format de fichier des étiquettes](../concepts/data-formats.md). 

>[!TIP]
> Passez en revue [Préparer les données et définir un schéma](../how-to/design-schema.md) pour plus d’informations sur la sélection et la préparation des données.

## <a name="create-a-custom-named-entity-recognition-project"></a>Créer un projet de reconnaissance d’entité nommée personnalisée

>[!NOTE]
> Si votre jeu de données contient des fichiers de langues différentes ou si vous prévoyez des langues différentes pendant l’exécution, vous pouvez activer l’option multilingue.

[!INCLUDE [Create custom NER project](../includes/create-project.md)]

## <a name="next-steps"></a>Étapes suivantes

Une fois votre projet créé, vous pouvez commencer à [étiqueter vos données](tag-data.md), ce qui indiquera à votre modèle d’extraction d’entité comment interpréter le texte, et servira à l’entraînement et à l’évaluation.
