---
title: 'Démarrage rapide : Déployer le connecteur Azure IoT pour FHIR (préversion) à l’aide d’un modèle ARM'
description: dans ce guide de démarrage rapide, décrit comment déployer Azure IoT Connector pour FHIR (version préliminaire) à l’aide d’un modèle de Azure Resource Manager (modèle ARM).
author: rbhaiya
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.author: rabhaiya
ms.date: 09/10/2021
ms.custom: devx-track-azurepowershell, mode-other
ms.openlocfilehash: 332b7aeaa9368dbdd0c4c863dfbaf174b2037331
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133061514"
---
# <a name="quickstart-use-an-azure-resource-manager-arm-template-to-deploy-azure-iot-connector-for-fhir-preview"></a>Démarrage rapide : Utiliser un modèle ARM (Azure Resource Manager) pour déployer le connecteur Azure IoT pour FHIR (préversion)

Dans ce guide de démarrage rapide, vous allez découvrir comment utiliser un modèle ARM (Azure Resource Manager) pour déployer le connecteur Azure IoT pour Fast Healthcare Interoperability Resources (FHIR&#174;)*, une fonctionnalité d’API Azure pour FHIR. Pour déployer une instance opérationnelle du connecteur Azure IoT pour FHIR, ce modèle déploie également un service API Azure pour FHIR parent et une application Azure IoT Central qui exporte des données de télémétrie d’un simulateur d’appareil vers le connecteur Azure IoT pour FHIR. Vous pouvez exécuter le modèle ARM afin de déployer le connecteur Azure IoT pour FHIR par le biais du portail Azure, de PowerShell ou de l’interface CLI.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Si votre environnement remplit les prérequis et que vous êtes déjà familiarisé avec l’utilisation des modèles ARM, sélectionnez le bouton **Déployer sur Azure**. Le modèle s’ouvre dans le portail Azure une fois que vous êtes connecté.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Déployer sur Azure un connecteur Azure IoT pour FHIR en utilisant un modèle ARM dans le portail Azure.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prérequis

# <a name="portal"></a>[Portail](#tab/azure-portal)

Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/).
* Si vous souhaitez exécuter le code localement, [Azure PowerShell](/powershell/azure/install-az-ps).

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

* Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/).
* Si vous voulez exécuter le code localement :
    * Un shell Bash (comme Git Bash, qui est inclus dans [Git pour Windows](https://gitforwindows.org)).
    * [Azure CLI](/cli/azure/install-azure-cli).

---

## <a name="review-the-template"></a>Vérifier le modèle

Le [modèle](https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json) définit les ressources Azure suivantes :

* **Microsoft.HealthcareApis/services**
* **Microsoft.HealthcareApis/services/iomtconnectors**
* **Microsoft.IoTCentral/IoTApps**

## <a name="deploy-the-template"></a>Déployer le modèle

# <a name="portal"></a>[Portail](#tab/azure-portal)

Sélectionnez le lien suivant afin de déployer le connecteur Azure IoT pour FHIR en utilisant le modèle ARM dans le portail Azure :

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Déployer sur Azure un service de connecteur Azure IoT pour FHIR en utilisant le modèle ARM dans le portail Azure.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

Dans la page **Déployer l’API Azure pour FHIR** :

1. Si vous le souhaitez, remplacez la valeur par défaut de **Abonnement** par un autre abonnement.

2. Pour **Groupe de ressources**, sélectionnez **Créer**, entrez un nom pour le nouveau groupe de ressources, puis sélectionnez **OK**.

3. Si vous avez créé un groupe de ressources, sélectionnez une **Région** pour le groupe de ressources.

4. Entrez un nom pour votre nouvelle instance d’API Azure pour FHIR dans **FHIR Service Name**.

5. Choisissez l’emplacement (**Location**) de votre API Azure pour FHIR. L’emplacement peut être identique ou différent de la région du groupe de ressources.

6. Donnez un nom à votre instance de connecteur Azure IoT pour FHIR dans **Iot Connector Name**.

7. Donnez un nom à une connexion créée dans le connecteur Azure IoT pour FHIR dans **Connection Name**. Cette connexion est utilisée par l’application Azure IoT Central pour envoyer des données de télémétrie d’appareils simulés au connecteur Azure IoT pour FHIR.

8. Entrez un nom pour votre nouvelle application Azure IoT Central dans **Iot Central Name**. Cette application utilisera le modèle *Continuous patient monitoring* pour simuler un appareil.

9. Choisissez l’emplacement de votre application IoT Central dans la liste déroulante **IoT Central Location**. 

10. Sélectionnez **Revoir + créer**.

11. Lisez les conditions générales, puis sélectionnez **Créer**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Si vous voulez exécuter les scripts PowerShell localement, entrez d’abord `Connect-AzAccount` pour configurer vos informations d’identification Azure.

Si le fournisseur de ressources `Microsoft.HealthcareApis` n’est pas déjà inscrit pour votre abonnement, vous pouvez l’inscrire avec le code interactif suivant. Pour exécuter le code dans Azure Cloud Shell, sélectionnez **Essayer** dans le coin supérieur de n’importe quel bloc de code.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

Si le fournisseur de ressources `Microsoft.IoTCentral` n’est pas déjà inscrit pour votre abonnement, vous pouvez l’inscrire avec le code interactif suivant. Pour exécuter le code dans Azure Cloud Shell, sélectionnez **Essayer** dans le coin supérieur de n’importe quel bloc de code.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.IoTCentral
```

Utilisez le code suivant pour déployer le service de connecteur Azure IoT pour FHIR en utilisant le modèle ARM.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$fhirServiceName = Read-Host -Prompt "Enter a name for the new Azure API for FHIR service"
$location = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$iotConnectorName = Read-Host -Prompt "Enter a name for the new Azure IoT Connector for FHIR"
$connectionName = Read-Host -Prompt "Enter a name for the connection with Azure IoT Connector for FHIR"
$iotCentralName = Read-Host -Prompt "Enter a name for the new Azure IoT Central Application"
$iotCentralLocation = Read-Host -Prompt "Enter a location for the new Azure IoT Central Application"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion
Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure IoT Connector for FHIR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json `
    -fhirServiceName $fhirServiceName `
    -location $location
    -iotConnectorName $iotConnectorName
    -connectionName $connectionName
    -iotCentralName $iotCentralName
    -iotCentralLocation $iotCentralLocation
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

Si le fournisseur de ressources `Microsoft.HealthcareApis` n’est pas déjà inscrit pour votre abonnement, vous pouvez l’inscrire avec le code interactif suivant. Pour exécuter le code dans Azure Cloud Shell, sélectionnez **Essayer** dans le coin supérieur de n’importe quel bloc de code.

```azurecli-interactive
az extension add --name healthcareapis
```

Si le fournisseur de ressources `Microsoft.IoTCentral` n’est pas déjà inscrit pour votre abonnement, vous pouvez l’inscrire avec le code interactif suivant.

```azurecli-interactive
az extension add --name azure-iot
```

Utilisez le code suivant pour déployer le service de connecteur Azure IoT pour FHIR en utilisant le modèle ARM.

```azurecli-interactive
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter a name for the new Azure API for FHIR service: " fhirServiceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " location &&
read -p "Enter a name for the new Azure IoT Connector for FHIR: " iotConnectorName &&
read -p "Enter a name for the connection with Azure IoT Connector for FHIR: " connectionName &&
read -p "Enter a name for the new Azure IoT Central Application: " iotCentralName &&
read -p "Enter a location for the new Azure IoT Central Application: " iotCentralLocation &&

params='fhirServiceName='$fhirServiceName' location='$location' iotConnectorName='$iotConnectorName' connectionName='$connectionName' iotCentralName='$iotCentralName' iotCentralLocation='$iotCentralLocation &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure IoT Connector for FHIR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> Le déploiement prend quelques minutes. Notez les noms du service API Azure pour FHIR, de l’application Azure IoT Central et du groupe de ressources, que vous utiliserez plus tard pour examiner les ressources déployées.

## <a name="review-deployed-resources"></a>Vérifier les ressources déployées

# <a name="portal"></a>[Portail](#tab/azure-portal)

Suivez ces étapes pour obtenir une vue d’ensemble de votre nouveau service API Azure pour FHIR :

1. Dans le [portail Azure](https://portal.azure.com), recherchez et sélectionnez **API Azure pour FHIR**.

2. Dans la liste FHIR, sélectionnez votre nouveau service. La page **Vue d’ensemble** pour le nouveau service API Azure pour FHIR apparaît.

3. Pour vérifier que le compte de l’API FHIR est provisionné, sélectionnez le lien en regard de **Point de terminaison de métadonnées FHIR** pour récupérer la déclaration de capacité de l’API FHIR. Le format du lien est `https://<service-name>.azurehealthcareapis.com/metadata`. Si le compte est provisionné, un grand fichier JSON est affiché.

4. Pour vérifier que le nouveau connecteur Azure IoT pour FHIR est provisionné, sélectionnez **Connecteur IoT (préversion)** dans le menu de navigation de gauche pour ouvrir la page **Connecteurs IoT**. La page doit afficher le connecteur Azure IoT pour FHIR provisionné avec *En ligne* comme valeur d’*État* et *1* comme valeur de *Connexions*. *Mappage d’appareil* et *Mappage FHIR* doivent avoir une icône de *réussite*.

5. Dans le [portail Azure](https://portal.azure.com), recherchez et sélectionnez **Applications IoT Central**.

6. Dans la liste des applications IoT Central, sélectionnez votre nouveau service. La page **Vue d’ensemble** de la nouvelle application IoT Central s’affiche.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Exécutez le code interactif suivant pour visualiser les détails de votre service API Azure pour FHIR. Vous devrez entrer le nom du nouveau service FHIR et du groupe de ressources.

```azurepowershell-interactive
$fhirServiceName = Read-Host -Prompt "Enter the name of your Azure API for FHIR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new FHIR service has been provisioned"

$requestUri="https://" + $fhirServiceName + ".azurehealthcareapis.com/metadata"
$metadata = Invoke-WebRequest -Uri $requestUri
$metadata.RawContent
Read-Host "Press [ENTER] to continue"
```

> [!NOTE]
> Le connecteur Azure IoT pour FHIR ne fournit pas de commande PowerShell pour l’instant. Pour vérifier que votre connecteur Azure IoT pour FHIR a été provisionné correctement, utilisez le processus de validation fourni sous l’onglet **Portail**.

Exécutez le code interactif suivant pour visualiser les détails de votre application Azure IoT Central. Vous devrez entrer le nom de la nouvelle application IoT Central et du groupe de ressources.

```azurepowershell-interactive
$iotCentralName = Read-Host -Prompt "Enter the name of your Azure IoT Central application"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName" -Verbose
Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName
```

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

Exécutez le code interactif suivant pour visualiser les détails de votre service API Azure pour FHIR. Vous devrez entrer le nom du nouveau service FHIR et du groupe de ressources.

```azurecli-interactive
read -p "Enter the name of your Azure API for FHIR service: " fhirServiceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName" &&
az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName &&
read -p "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned: " &&
requestUrl='https://'$fhirServiceName'.azurehealthcareapis.com/metadata' &&
curl --url $requestUrl &&
read -p "Press [ENTER] to continue: "
```

> [!NOTE]
> Le connecteur Azure IoT pour FHIR ne fournit pas de commande CLI pour l’instant. Pour vérifier que votre connecteur Azure IoT pour FHIR a été provisionné correctement, utilisez le processus de validation fourni sous l’onglet **Portail**.

Exécutez le code interactif suivant pour visualiser les détails de votre application Azure IoT Central. Vous devrez entrer le nom de la nouvelle application IoT Central et du groupe de ressources.

```azurecli-interactive
read -p "Enter the name of your IoT Central application: " iotCentralName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az iot central app show -g $resourceGroupName -n $iotCentralName" &&
az iot central app show -g $resourceGroupName -n $iotCentralName &&
```

---

## <a name="connect-your-iot-data-with-the-azure-iot-connector-for-fhir-preview"></a>Connecter vos données IoT à l’aide de la fonctionnalité Connecteur Azure IoT pour FHIR (préversion)
> [!IMPORTANT]
> Le modèle de mappage d’appareil fourni dans ce guide est conçu pour fonctionner avec l’exportation de données (héritée) au sein d’IoT Central.

L’application IoT Central ne fournit pas actuellement de modèle ARM ou de commandes PowerShell et CLI pour définir l’exportation des données. Vous devez donc suivre les instructions ci-dessous dans le portail Azure.  

Une fois que vous avez déployé votre application IoT Central, vos deux appareils simulés prêts à l’emploi commencent à générer des données de télémétrie. Pour ce tutoriel, nous allons ingérer les données de télémétrie du simulateur *Smart Vitals Patch* dans FHIR via la fonctionnalité Connecteur Azure IoT pour FHIR. Pour exporter vos données IoT vers le connecteur Azure IoT pour FHIR, vous devez [configurer une exportation de données (héritée) au sein d’IoT Central](../../iot-central/core/howto-export-data-legacy.md). Dans la page Exportation de données (héritée) :
- Sélectionnez *Azure Event Hubs* comme destination d’exportation.
- Sélectionnez *Utiliser une chaîne de connexion* pour le champ **Espace de noms Event Hubs**.
- Fournissez la chaîne de connexion de la fonctionnalité Connecteur Azure IoT pour FHIR obtenue à l’étape précédente pour le champ **Chaîne de connexion**.
- Conservez l’option **Télémétrie** sur *Activé* pour le champ **Données à exporter**.

---

## <a name="view-device-data-in-azure-api-for-fhir"></a>Voir les données d’appareil dans l’API Azure pour FHIR

vous pouvez afficher la ou les ressources d’Observation FHIR créées par Azure IoT Connector pour FHIR sur votre service FHIR à l’aide de la publication. Pour plus d’informations, consultez [accéder au service FHIR à l’aide](./../use-postman.md) de l’affranchissement et effectuer une `GET` requête pour `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` afficher les ressources FHIR d’observation avec une valeur de fréquence cardiaque.

> [!TIP]
> Vérifiez que votre utilisateur dispose de l’accès approprié au plan de données de l’API Azure pour FHIR. Utilisez le [contrôle d’accès en fonction du rôle Azure](configure-azure-rbac.md) (RBAC Azure) pour attribuer les rôles de plan de données nécessaires.

---

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’en avez plus besoin, supprimez le groupe de ressources, ce qui supprime également les ressources qu’il contient.

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Dans le [portail Azure](https://portal.azure.com), recherchez et sélectionnez **Groupes de ressources**.

2. Dans la liste des groupes de ressources, choisissez le nom de votre groupe de ressources.

3. Dans la page **Vue d’ensemble** de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.

4. Dans la boîte de dialogue de confirmation, entrez le nom de votre groupe de ressources, puis sélectionnez **Supprimer**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to delete"
Write-Verbose "Remove-AzResourceGroup -Name $resourceGroupName" -Verbose
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

```azurecli-interactive
read -p "Enter the name of the resource group to delete: " resourceGroupName &&
echo "DELETE A RESOURCE GROUP (AND ITS RESOURCES):  az group delete --name $resourceGroupName" &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

Pour obtenir un tutoriel pas à pas expliquant comment créer un modèle ARM, consultez le [tutoriel sur la création et le déploiement de votre premier modèle ARM](../../azure-resource-manager/templates/template-tutorial-create-first-template.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé la fonctionnalité Connecteur Azure IoT pour FHIR dans votre ressource API Azure pour FHIR. Pour en savoir plus sur le fonctionnalité Connecteur Azure IoT pour FHIR, sélectionnez l’une des étapes suivantes :

Appréhendez les différentes étapes du workflow dans le Connecteur Azure IoT pour FHIR.

>[!div class="nextstepaction"]
>[Flux de données du Connecteur Azure IoT pour FHIR](iot-data-flow.md)

Découvrez comment configurer le connecteur IoT à l’aide de modèles de mappage d’appareil et FHIR.

>[!div class="nextstepaction"]
>[Modèles de mappage du Connecteur Azure IoT pour FHIR](iot-mapping-templates.md)

*Sur le Portail Azure, le Connecteur Azure IoT pour FHIR est appelé Connecteur IoT (préversion). Le sigle FHIR est une marque déposée de HL7 et est utilisé avec l’autorisation de HL7.
