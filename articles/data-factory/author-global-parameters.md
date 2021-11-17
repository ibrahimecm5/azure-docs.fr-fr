---
title: Paramètres globaux
description: Définir des paramètres globaux pour chacun de vos environnements Azure Data Factory
ms.service: data-factory
ms.subservice: authoring
ms.topic: conceptual
author: joshuha-msft
ms.author: joowen
ms.date: 05/12/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3205d5c3be7fe13cbaa4b881a3de6bffc15e9fd8
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550909"
---
# <a name="global-parameters-in-azure-data-factory"></a>Paramètres globaux dans Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Les paramètres globaux représentent des constantes d’une fabrique de données, qui peuvent être consommées par un pipeline dans n’importe quelle expression. Elles sont utiles quand vous avez plusieurs pipelines avec des valeurs et des noms de paramètres identiques. Lors de la promotion d’une fabrique de données à l’aide du processus d’intégration et de déploiement continus (CI/CD), vous pouvez remplacer ces paramètres dans chaque environnement. 

## <a name="creating-global-parameters"></a>Création de paramètres globaux

Pour créer un paramètre global, accédez à l’onglet *Paramètres globaux* de la section **Gérer**. Sélectionnez **Nouveau** pour ouvrir le volet latéral de navigation.

:::image type="content" source="media/author-global-parameters/create-global-parameter-1.png" alt-text="Capture d’écran mettant en évidence le bouton Nouveau qui permet de créer des paramètres globaux.":::

Dans le volet latéral de navigation, entrez un nom, sélectionnez un type de données, puis spécifiez la valeur de votre paramètre.

:::image type="content" source="media/author-global-parameters/create-global-parameter-2.png" alt-text="Capture d’écran montrant où ajouter le nom, le type de données et la valeur du nouveau paramètre global.":::

Après avoir créé un paramètre global, vous pouvez le modifier en cliquant sur le nom du paramètre. Pour modifier plusieurs paramètres à la fois, sélectionnez **Modifier tout**.

:::image type="content" source="media/author-global-parameters/create-global-parameter-3.png" alt-text="Créer des paramètres globaux":::

## <a name="using-global-parameters-in-a-pipeline"></a>Utilisation de paramètres globaux dans un pipeline

Les paramètres globaux peuvent être utilisés dans n’importe quelle [expression de pipeline](control-flow-expression-language-functions.md). Si un pipeline fait référence à une autre ressource, par exemple un jeu de données ou un workflow, vous pouvez transmettre la valeur de paramètre globale via les paramètres de cette ressource. Les paramètres globaux sont référencés en tant que `pipeline().globalParameters.<parameterName>`.

:::image type="content" source="media/author-global-parameters/expression-global-parameters.png" alt-text="Utilisation des paramètres globaux":::

## <a name="global-parameters-in-cicd"></a><a name="cicd"></a> Paramètres globaux dans CI/CD

Il existe deux façons d’intégrer des paramètres globaux dans votre solution d’intégration et de déploiement continus :

* Inclure des paramètres globaux dans le modèle ARM
* Déployer des paramètres globaux via un script PowerShell

Pour les cas d’utilisation générale, il est recommandé d’inclure des paramètres globaux dans le modèle ARM. Cette intégration à la solution décrite dans [la documentation CI/CD](continuous-integration-delivery.md) se fait en mode natif. Dans le cas de la publication automatique et de la connexion Purview, une méthode de **script PowerShell** est nécessaire. Vous trouverez un complément d’informations sur la méthode de script PowerShell plus tard. Les paramètres globaux seront ajoutés par défaut en tant que paramètres de modèle ARM, car ils changent souvent d’un environnement à l’autre. Vous pouvez activer l’inclusion de paramètres globaux dans le modèle ARM à partir du hub de **gestion**.

:::image type="content" source="media/author-global-parameters/include-arm-template.png" alt-text="Inclure dans le modèle ARM":::

> [!NOTE]
> La configuration **Inclure le modèle ARM** est uniquement disponible en « mode Git ». Actuellement, elle est désactivée en mode « direct » ou en mode « Data Factory ». En cas de publication automatique ou de connexion Purview, n’utilisez pas la méthode d’inclusion des paramètres globaux, préférez la méthode de script PowerShell. 

> [!WARNING]
>Vous ne pouvez pas utiliser « - » dans le nom du paramètre. Vous recevrez un code d’erreur "{"code":"BadRequest","message":"ErrorCode=InvalidTemplate,ErrorMessage=The expression >'pipeline().globalParameters.myparam-dbtest-url' is not valid: .....}". Toutefois, vous pouvez utiliser le caractère « _ » dans le nom du paramètre. 

L’ajout de paramètres globaux au modèle ARM a pour effet d’ajouter un paramètre de niveau fabrique qui peut remplacer d’autres paramètres de niveau fabrique tels qu’une clé gérée par le client ou une configuration Git dans d’autres environnements. Si ces paramètres sont activés dans un environnement avec privilèges élevés tel que UAT ou PROD, il est préférable de déployer des paramètres globaux via un script PowerShell dans les étapes décrites ci-dessous. 


### <a name="deploying-using-powershell"></a>Déploiement à l’aide de PowerShell

Les étapes suivantes décrivent comment déployer des paramètres globaux via PowerShell. Cela est utile lorsque votre fabrique cible a un paramètre de niveau fabrique tel qu’une clé gérée par le client.

Lorsque vous publiez une fabrique ou exportez un modèle ARM avec des paramètres globaux, un dossier appelé *globalParameters* est créé avec un fichier appelé *your-factory-name_GlobalParameters.json*. Ce fichier est un objet JSON qui contient chaque type et valeur de paramètre global dans la fabrique publiée.

:::image type="content" source="media/author-global-parameters/global-parameters-adf-publish.png" alt-text="Publication de paramètres globaux":::

Si vous effectuez un déploiement dans un nouvel environnement tel que TEST ou PROD, il est recommandé de créer une copie de ce fichier de paramètres globaux et de remplacer les valeurs appropriées spécifiques de l’environnement. Lors de la republication, le fichier de paramètres globaux d’origine est remplacé, mais la copie de l’autre environnement restera intacte.

Par exemple, si vous possédez une fabrique nommée « ADF-DEV » et un paramètre global de type chaîne nommé 'environment' avec la valeur 'dev', un fichier nommé *ADF-DEV_GlobalParameters.json* est généré lors de la republication. Si vous déployez sur une fabrique de test nommée 'ADF_TEST', créez une copie du fichier JSON (par exemple, ADF-TEST_GlobalParameters.json) et remplacez les valeurs de paramètre par les valeurs spécifiques à l’environnement. Le paramètre 'environment' peut maintenant avoir la valeur 'test'. 

:::image type="content" source="media/author-global-parameters/powershell-task.png" alt-text="Déploiement des paramètres globaux":::

Utilisez le script PowerShell ci-dessous pour promouvoir des paramètres globaux dans d’autres environnements. Ajoutez une tâche Azure PowerShell DevOps avant votre déploiement de modèle ARM. Dans la tâche DevOps, vous devez spécifier l’emplacement du nouveau fichier de paramètres, le groupe de ressources cible et la fabrique de données cible.

> [!NOTE]
> Pour déployer des paramètres globaux à l’aide de PowerShell, vous devez utiliser au moins la version 4.4.0 du module Az.

```powershell
param
(
    [parameter(Mandatory = $true)] [String] $globalParametersFilePath,
    [parameter(Mandatory = $true)] [String] $resourceGroupName,
    [parameter(Mandatory = $true)] [String] $dataFactoryName
)

Import-Module Az.DataFactory

$newGlobalParameters = New-Object 'system.collections.generic.dictionary[string,Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification]'

Write-Host "Getting global parameters JSON from: " $globalParametersFilePath
$globalParametersJson = Get-Content $globalParametersFilePath

Write-Host "Parsing JSON..."
$globalParametersObject = [Newtonsoft.Json.Linq.JObject]::Parse($globalParametersJson)

foreach ($gp in $globalParametersObject.GetEnumerator()) {
    Write-Host "Adding global parameter:" $gp.Key
    $globalParameterValue = $gp.Value.ToObject([Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification])
    $newGlobalParameters.Add($gp.Key, $globalParameterValue)
}

$dataFactory = Get-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Name $dataFactoryName
$dataFactory.GlobalParameters = $newGlobalParameters

Write-Host "Updating" $newGlobalParameters.Count "global parameters."

Set-AzDataFactoryV2 -InputObject $dataFactory -Force
```

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur le [processus d’intégratioet de déploiement continu](continuous-integration-delivery-improvements.md) d’Azure Data Factory
* Découvrez comment utiliser le [langage d’expression de flux de contrôle](control-flow-expression-language-functions.md)
