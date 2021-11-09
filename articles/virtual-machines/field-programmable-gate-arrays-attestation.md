---
title: Service Azure FPGFA Attestation
description: Service d’attestation pour les machines virtuelles de la série NP.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: vikancha
ms.openlocfilehash: 110021730ecc91281f2c187a5ef6f1f989fbaf87
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131018467"
---
# <a name="fpga-attestation-for-azure-np-series-vms-preview"></a>Attestation FPGA pour les machines virtuelles de la série Azure NP (préversion)

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Le service d’attestation FPGA effectue une série de validations sur un fichier de point de contrôle de conception (appelé « netlist ») généré par l’ensemble d’outils Xilinx et produit un fichier qui contient l’image validée (appelée « flux binaire ») qui peut être chargée sur la carte FPGA Xilinx U250 dans une machine virtuelle de série NP.  

## <a name="prerequisites"></a>Prérequis  

Vous aurez besoin d’un abonnement Azure et d’un compte de stockage Azure. L’abonnement vous donne accès à Azure et au compte de stockage utilisé pour stocker vos fichiers netlist et de sortie du service d’attestation.  

Nous fournissons des scripts PowerShell et bash pour envoyer les demandes d’attestation.   Les scripts utilisent Azure CLI, qui peut s’exécuter sous Windows et Linux. PowerShell peut s’exécuter sur Windows, Linux et macOS.  

[Téléchargement d’Azure CLI (obligatoire)](/cli/azure/install-azure-cli)

[Téléchargement de PowerShell pour Windows, Linux et macOS (uniquement pour les scripts PowerShell)](/powershell/scripting/install/installing-powershell)

Votre client et votre ID d’abonnement doivent être autorisés à envoyer des demandes au service d’attestation. Rendez-vous sur [https://aka.ms/AzureFPGAAttestationPreview](https://aka.ms/AzureFPGAAttestationPreview) pour demander l’accès. 

## <a name="building-your-design-for-attestation"></a>Création de votre conception pour l’attestation  

L’ensemble d’outils Xilinx préféré pour créer des conceptions est Vitis 2020.2. Les fichiers netlist qui ont été créés avec une version antérieure de l’ensemble d’outils et qui sont toujours compatibles avec 2020.2 peuvent être utilisés. Vérifiez que vous avez chargé le bon interpréteur de commandes pour la génération. La version `xilinx_u250_gen3x16_xdma_2_1_202010_1` est actuellement prise en charge. Les fichiers de support peuvent être téléchargés à partir du salon Xilinx Alveo.

Vous devez inclure l’argument suivant dans Vitis (ligne de commande v++) pour générer un fichier `xclbin` qui contient une netlist au lieu d’un flux binaire.

`--advanced.param compiler.acceleratorBinaryContent=dcp`

## <a name="logging-into-azure"></a>Connexion à Azure  

Avant d’effectuer des opérations avec Azure, vous devez vous connecter à Azure et définir l’abonnement autorisé à appeler le service. Pour ce faire, utilisez les champs `az login` et `az account set –s <Sub ID or Name>`. Pour plus d’informations sur ce processus, consultez : [Se connecter avec Azure CLI](/cli/azure/authenticate-azure-cli). Utilisez l’option **Connexion interactive** ou **Se connecter avec des informations d’identification** sur la ligne de commande.  

## <a name="creating-a-storage-account-and-blob-container"></a>Créer un compte de stockage et un conteneur d’objets blob  

Votre fichier netlist doit être chargé dans un conteneur d’objets Blob du stockage Azure pour permettre l’accès par le service d’attestation.  

Reportez-vous à cette page pour plus d’informations sur la création du compte et d’un conteneur et le chargement de votre netlist en tant qu’objet Blob dans ce conteneur : [https://docs.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-cli](../storage/blobs/storage-quickstart-blobs-cli.md).  

Vous pouvez également utiliser le portail Azure à cette fin.  

## <a name="upload-your-netlist-file-to-azure-blob-storage"></a>Charger votre fichier netlist dans le stockage d’objets Blob Azure  

Il existe plusieurs façons de copier le fichier ; vous trouverez ci-dessous un exemple d’utilisation de l’applet de commande az storage upload. Les commandes az s’exécutent à la fois sur Linux et Windows. Vous pouvez choisir n’importe quel nom pour le nom de l’objet Blob, mais veillez à conserver l’extension `xclbin`.

`az storage blob upload --account-name <storage account to receive netlist> --container-name <blob container name> --name <blob filename> --file <local file with netlist>`

## <a name="download-the-attestation-scripts"></a>Télécharger les scripts d’attestation  

Les scripts de validation peuvent être téléchargés à partir du conteneur d’objets Blob de stockage Azure suivant :  

[https://fpgaattestation.blob.core.windows.net/validationscripts/validate.zip](https://fpgaattestation.blob.core.windows.net/validationscripts/validate.zip)

Le fichier zip contient deux scripts PowerShell, un pour envoyer et l’autre pour surveiller, tandis que le troisième fichier est un script bash qui exécute les deux fonctions.  

## <a name="running-the-attestation-scripts"></a>Exécution des scripts d’attestation  

Pour exécuter les scripts, vous devez fournir le nom de votre compte de stockage, le nom du conteneur d’objets Blob dans lequel le fichier netlist est stocké et le nom du fichier netlist. Vous devrez également créer une signature d’accès partagé (SAP) de service qui accorde l’accès en lecture/écriture à votre conteneur (et non à la netlist). Cette signature d’accès partagé est utilisée par le service d’attestation pour effectuer une copie locale de votre fichier netlist et pour écrire les fichiers de sortie résultant du processus de validation dans votre conteneur.  

Une vue d’ensemble des signatures d’accès partagé est disponible ici avec des informations spécifiques sur les associations de services disponibles ici. La page SAP de service comprend un avertissement important sur la protection des SAP générées.  Lisez l’avertissement pour comprendre la nécessité de protéger la SAP contre toute utilisation malveillante ou involontaire.  

Vous pouvez générer une SAP pour votre conteneur à l’aide de la cmdlet az storage container generate-sas. Spécifiez un délai d’expiration au format UTC qui est au moins de quelques heures après l’heure de l’envoi. Un délai de 6 heures devrait être plus que suffisant.  

Si vous souhaitez utiliser des répertoires virtuels, vous devez inclure la hiérarchie de répertoires dans le cadre de l’argument de conteneur. Par exemple, si vous avez un conteneur nommé «netlists» et un répertoire virtuel nommé « image1 » qui contient le blob netlist, vous spécifiez « netlists/image1 » comme nom de conteneur. Ajoutez des noms de répertoires supplémentaires pour spécifier une hiérarchie plus profonde. 

### <a name="powershell"></a>PowerShell   

```powershell
$sas=$(az storage container generate-sas --account-name <storage acct name> --name <blob container name> --https-only --permissions rwc --expiry <e.g., 2021-01-07T17:00Z> --output tsv)

.\Validate-FPGAImage.ps1 -StorageAccountName <storage acct name> -Container <blob container name> -BlobContainerSAS $sas -NetlistName <netlist blob filename>
```

### <a name="bash"></a>Bash  

```bash
sas=az storage container generate-sas --account-name <storage acct name> --name <blob container name> --https-only --permissions rwc --expiry <2021-01-07T17:00Z> --output tsv  

validate-fpgaimage.sh --storage-account <storage acct name> --container <blob container name> --netlist-name <netlist blob filename> --blob-container-sas $sas
``` 

## <a name="checking-on-the-status-of-your-submission"></a>Vérification de l’état de votre envoi  

Le service d’attestation renverra l’ID d’orchestration de votre envoi. Les scripts d’envoi commencent automatiquement à surveiller l’envoi en interrogeant pour vérifier sa finalisation. L’ID d’orchestration est le principal moyen pour nous de vérifier ce qui est arrivé à votre envoi. Conservez-le en cas de problème. À titre de référence, l’attestation prend environ 30 minutes pour une petite taille de fichier netlist (300 Mo), et une heure pour un fichier de 1,6 Go. 

Vous pouvez appeler le script Monitor-Validation.ps1 à tout moment pour obtenir l’état et les résultats de l’attestation, en fournissant l’ID d’orchestration en tant qu’argument :  

`.\Monitor-Validation.ps1 -OrchestrationId <orchestration ID>`

Vous pouvez également envoyer une requête HTTP post au point de terminaison du service d’attestation :  

`https://fpga-attestation.azurewebsites.net/api/ComputeFPGA_HttpGetStatus`

Le corps de la demande doit contenir votre ID d’abonnement, votre ID de locataire et l’ID d’orchestration de votre demande d’attestation :  

```json
{  
  "OrchestrationId": "<orchestration ID>",  
  "ClientSubscriptionId": "<your subscription ID>",  
  "ClientTenantId": "<your tenant ID>"
}
```

## <a name="post-validation-steps"></a>Étapes post-validation

Le service écrit sa sortie dans votre conteneur. Si le test de validation réussit, votre conteneur aura le fichier netlist d’origine (abc.xclbin), un fichier avec le flux binaire (abc.bit.xclbin), un fichier qui identifie l’emplacement privé de votre flux binaire stocké (abc.azure.xclbin) et quatre fichiers journaux : un pour le processus de démarrage (abc-log.txt) et un autre pour les trois phases parallèles qui effectuent la validation. Ils sont nommés *logPhaseX.txt, où X est un nombre correspondant à la phase. azure.xclbin est utilisé sur votre machine virtuelle pour signaler le chargement de votre image validée dans l’U250. 

En cas d’échec de la validation, un fichier error-*. txt est écrit pour indiquer l’étape qui a échoué. Vérifiez également les fichiers journaux si le journal des erreurs indique que l’attestation a échoué. Quand vous nous contactez pour obtenir de l’aide, veillez à inclure tous ces fichiers dans le cadre de la demande de support, ainsi que l’ID de l’orchestration.  

Vous pouvez utiliser le portail Azure pour créer votre conteneur, ainsi que pour charger votre netlist et télécharger les fichiers binaires et les fichiers journaux. L’envoi d’une demande d’attestation et la surveillance de sa progression via le portail ne sont pas pris en charge pour l’instant et doivent être effectués par le biais de scripts comme décrit ci-dessus.
