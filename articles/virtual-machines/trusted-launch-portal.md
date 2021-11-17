---
title: Déployer une machine virtuelle de lancement fiable
description: Déployez une machine virtuelle qui utilise le lancement fiable.
author: khyewei
ms.author: khwei
ms.reviewer: cynthn
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: how-to
ms.date: 10/25/2021
ms.custom: template-how-to
ms.openlocfilehash: 1bf17761ed7ddba74ea62f5545f44f1c4b57c8d3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456501"
---
# <a name="deploy-a-vm-with-trusted-launch-enabled"></a>Déployer une machine virtuelle avec le lancement fiable activé

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles

Le [lancement fiable](trusted-launch.md) est un moyen d’améliorer la sécurité des machines virtuelles de [génération 2](generation-2.md). Le lancement fiable protège contre les techniques d’attaque avancées et persistantes en combinant des technologies d’infrastructure comme vTPM et l’amorçage sécurisé.

## <a name="prerequisites"></a>Prérequis 

- Vous devez [intégrer votre abonnement à Azure Security Center](https://azure.microsoft.com/services/security-center/?&ef_id=CjwKCAjwwsmLBhACEiwANq-tXHeKhV--teH6kIijnBTmP-PgktfvGr5zW9TAx00SR7xsGUc3sTj5sBoCkEoQAvD_BwE:G:s&OCID=AID2200277_SEM_CjwKCAjwwsmLBhACEiwANq-tXHeKhV--teH6kIijnBTmP-PgktfvGr5zW9TAx00SR7xsGUc3sTj5sBoCkEoQAvD_BwE:G:s&gclid=CjwKCAjwwsmLBhACEiwANq-tXHeKhV--teH6kIijnBTmP-PgktfvGr5zW9TAx00SR7xsGUc3sTj5sBoCkEoQAvD_BwE#overview) si ce n’est déjà fait. Azure Security Center (ASC) dispose d’un niveau gratuit qui offre des Insights très utiles pour diverses ressources Azure et hybrides. Le lancement fiable tire parti d’ASC pour proposer plusieurs recommandations concernant l’intégrité de la machine virtuelle. 

- Attribuez des initiatives de stratégie Azure à votre abonnement. Ces initiatives de stratégie ne doivent être attribuées qu’une seule fois par abonnement. Cette opération installe automatiquement toutes les extensions requises sur toutes les machines virtuelles prises en charge. 
    - Configurer les prérequis pour activer l’attestation d’invité sur les machines virtuelles compatibles avec le Lancement fiable 

    - Configurer des machines pour installer automatiquement les agents Azure Monitor et Azure Security sur des machines virtuelles 

 
## <a name="deploy-a-trusted-vm"></a>Déployer une machine virtuelle fiable
Créez une machine virtuelle avec le lancement fiable activé. Choisissez l’une des options suivantes :

### <a name="portal"></a>[Portail](#tab/portal)

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Recherchez **Machines virtuelles**.
3. Sous **Services**, sélectionnez **Machines virtuelles**.
4. Dans la page **Machines virtuelles**, sélectionnez **Ajouter**, puis **Machine virtuelle**.
5. Sous **Détails du projet**, vérifiez que l’abonnement approprié est sélectionné.
6. Sous **Groupe de ressources**, sélectionnez **Créer** et spécifiez un nom pour votre groupe de ressources ou sélectionnez un groupe de ressources existant dans la liste déroulante.
7. Sous **Détails de l’instance**, spécifiez un nom pour la machine virtuelle et choisissez une région qui prend en charge le [lancement fiable](trusted-launch.md#limitations).
1. Pour **Type de sécurité**, sélectionnez **Machines virtuelles de lancement fiable**. Cela a pour effet d’afficher deux options : **Amorçage sécurisé** et **vTPM**. Sélectionnez les options appropriées pour votre déploiement.
    :::image type="content" source="media/trusted-launch/security.png" alt-text="Capture d’écran montrant les options pour le lancement fiable.":::
3. Sous **Image**, sélectionnez une image parmi les **images de génération 2 recommandées compatibles avec le lancement fiable**. Pour en obtenir la liste, consultez [Images qui prennent en charge le lancement fiable](trusted-launch.md#limitations). 
   > [!TIP]
   > Si vous ne voyez pas la version génération 2 de l’image que vous souhaitez dans la liste déroulante, sélectionnez **Afficher toutes les images**, puis définissez le filtre **Type de sécurité** sur **Lancement fiable**.
13. Sélectionnez une taille de machine virtuelle qui prend en charge le lancement fiable. Consultez la liste des [tailles prises en charge](trusted-launch.md#limitations).
14. Renseignez les informations du **Compte d’administrateur**, puis les **Règles de port entrant**.
15. Au bas de la page, sélectionnez **Vérifier + créer**
16. Sur la page **Créer une machine virtuelle**, vous pouvez voir les détails de la machine virtuelle que vous allez déployer. Une fois la validation réussie, sélectionnez **Créer**.

    :::image type="content" source="media/trusted-launch/validation.png" alt-text="Capture d’écran montrant la page de validation, indiquant que les options de lancement fiable sont incluses.":::


Quelques minutes sont nécessaires pour le déploiement de votre machine virtuelle.

### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Vérifiez que vous utilisez bien la dernière version d’Azure CLI 

Connectez-vous au portail Azure à l’aide de `az login`.  

```azurecli-interactive
az login 
```

Créez une machine virtuelle avec lancement fiable. 

```azurecli-interactive
az group create -n myresourceGroup -l eastus 
az vm create \ 
   --resource-group myResourceGroup \ 
   --name myVM \ 
   --image UbuntuLTS \ 
   --admin-username azureuser \ 
   --generate-ssh-keys \ 
   --SecurityType trustedLaunch \ 
   --EnableSecureBoot true \  
   --EnableVtpm true \
```
 
Pour des machines virtuelles existantes, vous pouvez activer ou désactiver les paramètres d’amorçage sécurisé et de vTPM. La mise à jour de la machine virtuelle avec les paramètres d’amorçage sécurisé et de vTPM déclenche un redémarrage automatique.

```azurecli-interactive
az vm update \  
   --resource-group myResourceGroup \ 
   --name myVM \ 
   --EnableSecureBoot \  
   --EnableVtpm 
```  

### <a name="powershell"></a>[PowerShell](#tab/powershell)


Pour pouvoir approvisionner une machine virtuelle avec un lancement fiable, il faut que celle-ci ait été activée avec le paramètre `TrustedLaunch` à l’aide de la cmdlet `Set-AzVmSecurityType`. Vous pouvez ensuite utiliser la cmdlet Set-AzVmUefi pour définir la configuration de vTPM et SecureBoot. Utilisez l’extrait de code ci-dessous comme démarrage rapide, en veillant à remplacer les valeurs de cet exemple par les vôtres. 

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$location = "West US"
$vmName = "myTrustedVM"
$vmSize = Standard_B2s
$publisher = "MicrosoftWindowsServer"
$offer = "WindowsServer"
$sku = "2019-Datacenter"
$version = latest
$cred = Get-Credential `
   -Message "Enter a username and password for the virtual machine."

$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize 

$vm = Set-AzVMOperatingSystem `
   -VM $vm -Windows `
   -ComputerName $vmName `
   -Credential $cred `
   -ProvisionVMAgent `
   -EnableAutoUpdate 

$vm = Add-AzVMNetworkInterface -VM $vm `
   -Id $NIC.Id 

$vm = Set-AzVMSourceImage -VM $vm `
   -PublisherName $publisher `
   -Offer $offer `
   -Skus $sku `
   -Version $version 

$vm = Set-AzVMOSDisk -VM $vm `
   -StorageAccountType "StandardSSD_LRS" `
   -CreateOption "FromImage" 

$vm = Set-AzVmSecurityType -VM $vm `
   -SecurityType "TrustedLaunch" 

$vm = Set-AzVmUefi -VM $vm `
   -EnableVtpm $true `
   -EnableSecureBoot $true 

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vm 
```
 


### <a name="template"></a>[Modèle](#tab/template)

Vous pouvez déployer des machines virtuelles de lancement fiable à l’aide d’un modèle de démarrage rapide :

**Linux**

[![Déployer sur Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-linux%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-linux%2FcreateUiDefinition.json)

**Windows**

[![Déployer sur Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-windows%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-windows%2FcreateUiDefinition.json)

---




## <a name="verify-or-update-your-settings"></a>Vérifier ou mettre à jour vos paramètres

Vous pouvez afficher la configuration de lancement fiable pour une machine virtuelle existante en visitant la page  **Vue d’ensemble** de la machine virtuelle dans le portail. L’onglet **Propriétés** affiche l’état des fonctionnalités de lancement fiable :

:::image type="content" source="media/trusted-launch/overview-properties.png" alt-text="Capture d’écran des propriétés de lancement fiable de la machine virtuelle.":::

Pour modifier la configuration de lancement fiable, dans le menu de gauche, sélectionnez **Configuration** dans la section **Paramètres**. Vous pouvez activer ou désactiver l’amorçage sécurisé et vTPM dans la section Type de sécurité du Lancement fiable. Lorsque vous avez terminé, sélectionnez Enregistrer en haut de la page. 

:::image type="content" source="media/trusted-launch/update.png" alt-text="Capture d’écran montrant les cases à cocher permettant de modifier les paramètres de lancement fiable.":::

Si la machine virtuelle est en cours d’exécution, vous recevez un message indiquant que la machine virtuelle va être redémarrée. Sélectionnez  **Oui**, puis attendez que la machine virtuelle redémarre pour que les modifications prennent effet. 


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [lancement fiable](trusted-launch.md) et les machines virtuelles [Gen 2](generation-2.md).
