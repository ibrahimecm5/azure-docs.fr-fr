---
title: Créer et déployer des packages d’application de machine virtuelle (préversion)
description: Découvrez comment créer et déployer des applications de machine virtuelle à l’aide d’une galerie Azure Compute Gallery.
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/02/2021
ms.reviewer: amjads
ms.custom: ''
ms.openlocfilehash: f5978c40acdbcc842400224fb3cdaed7c49255da
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479164"
---
# <a name="create-and-deploy-vm-applications-preview"></a>Créer et déployer des applications de machine virtuelle (préversion)

Les applications de machine virtuelle sont un type de ressource dans Azure Compute Gallery (anciennement Shared Image Gallery), qui simplifie la gestion, le partage et la distribution globale d’applications pour vos machines virtuelles.


> [!IMPORTANT]
> Les **applications de machine virtuelle dans Azure Compute Gallery** sont actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous disposez des éléments suivants :


Cet article part du principe que vous disposez déjà d’une galerie Azure Compute Gallery. Si vous n’avez pas encore de galerie, commencez par en créer une. Pour plus d’informations, consultez [Créer une galerie pour le stockage et le partage de ressources](create-gallery.md).

Vous devez avoir chargé votre application dans un conteneur d’un compte de stockage Azure. Votre application peut être stockée dans un objet blob de blocs ou de pages. Si vous choisissez d’utiliser un objet blob de pages, vous devez aligner les octets des fichiers avant de les charger. Voici un exemple qui va aligner les octets de votre fichier :

```azurepowershell-interactive
$inputFile = <the file you want to pad>

$fileInfo = Get-Item -Path $inputFile

$remainder = $fileInfo.Length % 512

if ($remainder -ne 0){

    $difference = 512 - $remainder

    $bytesToPad = [System.Byte[]]::CreateInstance([System.Byte], $difference)

    Add-Content -Path $inputFile -Value $bytesToPad -Encoding Byte
    }
```

Vous devez vous assurer que les fichiers sont publiquement disponibles. Autrement, vous aurez besoin de l’URI SAP pour les fichiers figurant dans votre compte de stockage. Vous pouvez utiliser l’[Explorateur Stockage](/azure/vs-azure-tools-storage-explorer-blobs) pour créer rapidement un URI SAP si vous n’en avez pas encore.

## <a name="create-the-vm-application"></a>Créer l’application de machine virtuelle

Choisissez une option ci-dessous pour créer vos définition et version d’application de machine virtuelle :

### <a name="portal"></a>[Portail](#tab/portal)


1. Accédez au [portail Azure](https://portal.azure.com), puis recherchez et sélectionnez **Azure Compute Gallery**.
1. Sélectionnez la galerie à utiliser dans la liste.
1. Sur la page de votre nouvelle galerie d’images, sélectionnez **Ajouter** en haut de la page, puis **Définition d’image de machine virtuelle** dans le menu déroulant. La page **Créer une définition d’application de machine virtuelle** s’ouvre.
1. Sous l’onglet **De base**, entrez un nom pour votre application et indiquez si l’application est destinée aux machines virtuelles exécutant Linux ou Windows.
1. Sélectionnez l’onglet **Options de publication** si vous souhaitez spécifier l’un des paramètres facultatifs suivants pour votre définition d’application de machine virtuelle :
    - Description de la définition d’application de machine virtuelle.
    - Date de fin de vie
    - Lien vers un CLUF
    - URI d’une déclaration de confidentialité
    - URI pour des notes de publication
1. Quand vous avez terminé, sélectionnez **Vérifier + créer**.
1. Une fois la validation terminée, sélectionnez **Créer** pour déployer la définition.
1. Une fois le déploiement effectué, sélectionnez **Accéder à la ressource**.


Vous pouvez maintenant créer une machine virtuelle et y déployer l’application de machine virtuelle à l’aide du portail. Il vous suffit de créer la machine virtuelle comme d’habitude, puis, sous l’onglet **Avancé**, de choisir **Sélectionner une application de machine virtuelle à installer**.

:::image type="content" source="media/vmapps/advanced-tab.png" alt-text="Capture d’écran de l’onglet Avancé dans laquelle vous pouvez choisir d’installer une application de machine virtuelle.":::

Sélectionnez l’application de machine virtuelle dans la liste, puis choisissez **Enregistrer** en bas de la page.

:::image type="content" source="media/vmapps/select-app.png" alt-text="Capture d’écran montrant la sélection d’une application de machine virtuelle à installer sur la machine virtuelle.":::

Si vous avez plusieurs applications de machine virtuelle à installer, vous pouvez définir l’ordre d’installation de chaque application de machine virtuelle sous l **onglet Avancé**.

### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Les applications de machine virtuelle requièrent [Azure CLI](/cli/azure/install-azure-cli) version 2.30.0 ou ultérieure.

Créez la définition d’application de machine virtuelle à l’aide de la commande [az sig gallery-application create](/cli/azure/sig/gallery-application#az_sig_gallery_application_create). Dans cet exemple, nous créons une définition d’application de machine virtuelle nommée *MyApp* pour machines virtuelles basées sur Linux.

```azurecli-interactive
az sig gallery-application create \
    --application-name myApp \
    --gallery-name myGallery \
    --resource-group myResourceGroup \
    --os-type Linux \
    --location "East US"
```

Créez une version d’application de machine virtuelle à l’aide de la commande [az sig gallery-application version create](/cli/azure/sig/gallery-application/version#az_sig_gallery_application_version_create). Les caractères autorisés pour la version sont les chiffres et les points. Les nombres doivent être un entier 32 bits. Format: *MajorVersion*.*MinorVersion*.*Patch*.

Remplacez les valeurs des paramètres par vos propres valeurs.

```azurecli-interactive
az sig gallery-application version create \
   --version-name 1.0.0 \
   --application-name myApp \
   --gallery-name myGallery \
   --location "East US" \
   --resource-group myResourceGroup \
   --package-file-link "https://<storage account name>.blob.core.windows.net/<container name>/<filename>" \
   --install-command "mv myApp .\myApp\myApp" \
   --remove-command "rm .\myApp\myApp" \
   --update-command  "mv myApp .\myApp\myApp \
   --default-configuration-file-link "https://<storage account name>.blob.core.windows.net/<container name>/<filename>"\
```


### <a name="powershell"></a>[PowerShell](#tab/powershell)

Créez la définition d’application de machine virtuelle à l’aide de la commande `New-AzGalleryApplication`. Dans cet exemple, nous créons une application Linux nommée *MyApp* dans la galerie Azure Compute Gallery *myGallery*, dans le groupe de ressources *myGallery*, et j’ai fourni une brève description de l’application de machine virtuelle pour mon propre usage. Remplacez les valeurs en fonction des besoins.

```azurepowershell-interactive
$galleryName = myGallery
$rgName = myResourceGroup
$applicationName = myApp
New-AzGalleryApplication `
  -ResourceGroupName $rgName `
  -GalleryName $galleryName `
  -Name $applicationName `
  -SupportedOSType Linux `
  -Description "Backend Linux application for finance."
```

Créez une version de votre application à l’aide de la commande `New-AzGalleryApplicationVersion`. Les caractères autorisés pour la version sont les chiffres et les points. Les nombres doivent être un entier 32 bits. Format: *MajorVersion*.*MinorVersion*.*Patch*.

Dans cet exemple, nous créons le numéro de version *1.0.0*. Remplacez les valeurs des variables en fonction des besoins.

```azurepowershell-interactive
$version = 1.0.0
New-AzGalleryApplicationVersion `
   -ResourceGroupName $rgName `
   -GalleryName $galleryName `
   -GalleryApplicationName $applicationName `
   -Name $version `
   -PackageFileLink "https://<storage account name>.blob.core.windows.net/<containder name>/<filename>" `
   -Location "East US" `
   -Install myApp.exe /silent `
   -Remove myApp.exe /uninstall `
```


Pour ajouter l’application à une machine virtuelle existante, récupérez la version de l’application et utilisez-la pour obtenir l’ID de version de l’application de machine virtuelle. Utilisez l’ID pour ajouter l’application à la configuration de machine virtuelle.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName $rgname -Name myVM
$vmapp = Get-AzGalleryApplicationVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryname `
   -ApplicationName $applicationname `
   -Version $version

$vm = Add-AzVmGalleryApplication `
   -VM $vm `
   -Id $vmapp.Id

Update-AzVm -ResourceGroupName $rgname -VM $vm
```


### <a name="rest"></a>[REST](#tab/rest2)

Créez la définition d’application.


```rest
PUT
/subscriptions/\<**subscriptionId**\>/resourceGroups/\<**resourceGroupName**\>/providers/Microsoft.Compute/galleries/\<**galleryName**\>/applications/\<**applicationName**\>?api-version=2019-03-01

{
    "location": "West US",
    "name": "myApp",
    "properties": {
        "supportedOSType": "Windows | Linux",
        "endOfLifeDate": "2020-01-01"
    }
}

```

| Nom du champ | Description | Limites |
|--|--|--|
| name | Nom unique de l’application de machine virtuelle dans la galerie | Longueur maximale de 117 caractères. Les caractères autorisés sont les lettres majuscules ou minuscules, les chiffres, les traits d’union (-), les points (.) et les traits de soulignement (_). Les noms ne peuvent pas se terminer par un point (.). |
| supportedOSType | Indique s’il s’agit d’une application Windows ou Linux | « Windows » ou « Linux » |
| endOfLifeDate | Date de fin de vie future de l’application. Notez que ces informations sont données à titre indicatif et ne sont pas appliquées. | Date future valide |

Créez une version de l’application de machine virtuelle.

```rest
PUT
/subscriptions/\<**subscriptionId**\>/resourceGroups/\<**resourceGroupName**\>/providers/Microsoft.Compute/galleries/\<**galleryName**\>/applications/\<**applicationName**\>/versions/\<**versionName**\>?api-version=2019-03-01

{
  "location": "$location",
  "properties": {
    "publishingProfile": {
      "source": {
        "mediaLink": "$mediaLink",
        "defaultConfigurationLink": "$configLink"
      },
      "manageActions": {
        "install": "echo installed",
        "remove": "echo removed",
        "update": "echo update"
      },
      "targetRegions": [
        {
          "name": "$location1",
          "regionalReplicaCount": 1 
        },
        { "name": "$location1" }
      ]
    },
    "endofLifeDate": "datetime",
    "excludeFromLatest": "true | false"
  }
}

```

| Nom du champ | Description | Limites |
|--|--|--|
| location | Emplacement source de la version de l’application de machine virtuelle | Région Azure valide |
| MediaLink | URL contenant le package de la version de l’application | URL de stockage valide et existante |
| defaultConfigurationLink | facultatif. URL contenant la configuration par défaut, qui peut être remplacée au moment du déploiement. | URL de stockage valide et existante |
| Installer | Commande pour installer l’application | Commande valide pour le système d’exploitation donné |
| Supprimer | Commande pour supprimer l’application | Commande valide pour le système d’exploitation donné |
| Update | facultatif. Commande permettant de mettre à jour l’application. Si elle n’est pas spécifiée et si une mise à jour est requise, l’ancienne version est supprimée et la nouvelle est installée. | Commande valide pour le système d’exploitation donné |
| targetRegions/name | Nom d’une région vers laquelle effectuer la réplication | Valider la région Azure |
| targetRegions/regionalReplicaCount | facultatif. Nombre de réplicas à créer dans la région. La valeur par défaut est de 1. | Entier compris entre 1 et 3 (inclus) |
| endOfLifeDate | Date de fin de vie future de la version de l’application. Notez que ces informations sont données à titre indicatif pour le client et ne sont pas appliquées. | Date future valide |
| excludeFromLatest | Si elle est spécifiée, cette version n’est pas prise en compte pour la dernière version. | True ou false |




Pour ajouter une version d’application de machine virtuelle à une machine virtuelle, effectuez une requête PUT sur la machine virtuelle.

```rest
PUT
/subscriptions/\<**subscriptionId**\>/resourceGroups/\<**resourceGroupName**\>/providers/Microsoft.Compute/virtualMachines/\<**VMName**\>?api-version=2019-03-01

{
  "properties": {
    "applicationProfile": {
      "galleryApplications": [
        {
          "order": 1,
          "packageReferenceId": "/subscriptions/{subscriptionId}/resourceGroups/<resource group>/providers/Microsoft.Compute/galleries/{gallery name}/applications/{application name}/versions/{version}",
          "configurationReference": "{path to configuration storage blob}"
        }
      ]
    }
  },
  "name": "{vm name}",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resource group}/providers/Microsoft.Compute/virtualMachines/{vm name}",
  "location": "{vm location}"
}
```


Pour appliquer l’application de machine virtuelle à un groupe identique uniforme :

```rest
PUT
/subscriptions/\<**subscriptionId**\>/resourceGroups/\<**resourceGroupName**\>/providers/Microsoft.Compute/
virtualMachineScaleSets/\<**VMSSName**\>?api-version=2019-03-01

{
  "properties": {
    "virtualMachineProfile": {
      "applicationProfile": {
        "galleryApplications": [
          {
            "order": 1,
            "packageReferenceId": "/subscriptions/{subscriptionId}/resourceGroups/<resource group>/providers/Microsoft.Compute/galleries/{gallery name}/applications/{application name}/versions/{version}",
            "configurationReference": "{path to configuration storage blob}"
          }
        ]
      }
    }
  },
  "name": "{vm name}",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resource group}/providers/Microsoft.Compute/virtualMachines/{vm name}",
  "location": "{vm location}"
}
```


| Nom du champ | Description | Limites |
|--|--|--|
| order | facultatif. Ordre dans lequel les applications doivent être déployées. Voir ci-dessous. | Valider l’entier |
| packageReferenceId | Référence de la version de l’application de la galerie | Référence de version d’application valide |
| configurationReference | facultatif. URL complète d’un blob de stockage contenant la configuration pour ce déploiement. Ceci remplace toute valeur fournie pour defaultConfiguration précédemment. | Référence de blob de stockage valide |

Le champ d’ordre peut être utilisé pour spécifier des dépendances entre applications. Les règles pour l’ordre sont les suivantes :

| Cas | Signification de l’installation | Signification de l’échec |
|--|--|--|
| Aucun ordre spécifié | Les applications non ordonnées sont installées après les applications ordonnées. Il n’y a aucune garantie d’ordre d’installation parmi les applications non ordonnées. | Les échecs d’installation d’autres applications, ordonnées ou non, n’affectent pas l’installation des applications non ordonnées. |
| Valeurs d’ordre en double | L’application sera installée dans un ordre quelconque par rapport à d’autres applications du même ordre. Toutes les applications de même ordre sont installées après celles dont l’ordre est inférieur et avant celles dont l’ordre est supérieur. | Si l’installation d’une application précédente dont l’ordre est inférieur échoue, aucune application de cet ordre n’est installée. Si l’installation d’une application quelconque de cet ordre échoue, aucune application dont l’ordre est supérieur n’est installée. |
| Ordres croissants | L’application est installée après celles dont l’ordre est inférieur et avant celles dont l’ordre est supérieur. | Si l’installation d’une application précédente d’ordre inférieur a échoué, cette application n’est pas installée. Si l’installation de cette application échoue, aucune application d’ordre supérieur n’est installée. |

La réponse inclut le modèle de machine virtuelle complet. Voici les parties pertinentes.

```rest
{
  "name": "{vm name}",
  "id": "{vm id}",
  "type": "Microsoft.Compute/virtualMachines",
  "location": "{vm location}",
  "properties": {
    "applicationProfile": {
      "galleryApplications": ""
    },
    "provisioningState": "Updating"
  },
  "resources": [
    {
      "name": "VMAppExtension",
      "id": "{extension id}",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "centraluseuap",
      "properties": "@{autoUpgradeMinorVersion=True; forceUpdateTag=7c4223fc-f4ea-4179-ada8-c8a85a1399f5; provisioningState=Creating; publisher=Microsoft.CPlat.Core; type=VMApplicationManagerLinux; typeHandlerVersion=1.0; settings=}"
    }
  ]
}

```

Si les applications de machine virtuelle n’ont pas encore été installées sur la machine virtuelle, la valeur sera vide. 

---



## <a name="next-steps"></a>Étapes suivantes
Apprenez-en davantage sur les [applications de machine virtuelle](vm-applications.md).
