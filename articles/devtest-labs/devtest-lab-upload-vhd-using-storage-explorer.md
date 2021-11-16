---
title: Charger un fichier VHD à l’aide de l’Explorateur Stockage
description: Charger un fichier VHD dans un compte de stockage lab DevTest Labs à l’aide de l’Explorateur Stockage Microsoft Azure.
ms.topic: how-to
ms.date: 11/05/2021
ms.openlocfilehash: 2531964c056ddbed38da435e16bde3e0f5e1eff9
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054057"
---
# <a name="upload-a-vhd-file-to-a-labs-storage-account-by-using-storage-explorer"></a>Charger un fichier VHD dans un compte de stockage de laboratoire à l’aide de l’Explorateur Stockage

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Dans Azure DevTest Labs, vous pouvez utiliser des fichiers VHD pour créer des images personnalisées afin d’approvisionner des machines virtuelles. Cet article explique comment utiliser l’[Explorateur Stockage Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) pour charger un fichier de disque dur virtuel vers le compte de stockage d’un laboratoire. Après avoir chargé le fichier VHD dans DevTest Labs, vous pouvez [créer une image personnalisée](devtest-lab-create-custom-image-from-vhd-using-powershell.md) à partir du fichier chargé. Pour plus d’informations sur les disques et les disques durs virtuels dans Azure, consultez [Introduction aux disques managés](../virtual-machines/managed-disks-overview.md).

Ce dernier prend en charge plusieurs options de connexion. Cette article décrit la connexion à un compte de stockage associé à votre abonnement Azure. Pour plus d’informations sur les autres options de connexion de l’Explorateur Stockage, consultez [Prise en main de l’Explorateur Stockage](../vs-azure-tools-storage-manage-with-storage-explorer.md).

## <a name="prerequisites"></a>Prérequis

- [Téléchargez et installez la version la plus récente de l’Explorateur Stockage Microsoft Azure](https://www.storageexplorer.com).

- Obtenez le nom du compte de stockage du laboratoire via le portail Azure :

  1. Dans le [portail Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040), recherchez et sélectionnez **DevTest Labs**, puis sélectionnez votre laboratoire dans la liste.
  1. Dans la page du laboratoire, dans la barre de navigation de gauche, sélectionnez **Configuration et stratégies**. 
  1. Dans la page **Configuration et stratégies**, sous **Bases de machine virtuelle**, sélectionnez **Images personnalisées**.
  1. Dans la page **Images personnalisées**, sélectionnez **Ajouter**. 
  1. Dans la page **image personnalisée**, sous **VHD**, sélectionnez le lien **Charger un VHD à l’aide de PowerShell** .
     ![Capture d’écran montrant le chargement du disque dur virtuel à l’aide du lien PowerShell.](media/devtest-lab-upload-vhd-using-storage-explorer/upload-image-using-psh.png)
  1. Dans la page **Charger une image à l’aide de PowerShell**, dans l’appel à la cmdlet **Add-AzureVhd**, le paramètre `Destination` affiche le nom du compte de stockage du laboratoire au format suivant : `https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/`.
  1. Copiez le nom du compte de stockage à utiliser dans les étapes suivantes.

## <a name="step-by-step-instructions"></a>Instructions pas à pas

1. Lorsque vous ouvrez l’Explorateur Stockage, le volet gauche de l’**Explorateur** affiche tous les abonnements Azure auxquels vous êtes connecté.

   Si vous devez ajouter un autre compte, sélectionnez l’icône **Gestion de compte**, puis, dans le volet **Gestion de compte**, sélectionnez **Ajouter un compte**.

   ![Capture d’écran montrant l’ajout d’un compte dans le volet Gestion de compte.](media/devtest-lab-upload-vhd-using-storage-explorer/add-account-link.png)

   Suivez les invites pour vous connecter avec le compte Microsoft associé à votre abonnement Azure.

1. Lorsque vous êtes connecté, le volet **Explorateur** affiche les abonnements Azure associés à votre compte. Sélectionnez la flèche déroulante en regard de l’abonnement Azure que vous souhaitez utiliser. Le volet gauche affiche les comptes de stockage associés aux abonnements Azure sélectionnés.

   ![Capture d’écran montrant les comptes de stockage pour un abonnement Azure sélectionné.](media/devtest-lab-upload-vhd-using-storage-explorer/storage-accounts-list.png)

1. Sélectionnez la flèche déroulante en regard du nom du compte de stockage Lab que vous avez enregistré précédemment.

1. Développez le nœud **Conteneurs d’objets blob**, puis sélectionnez le conteneur **chargements**.

   ![Capture d’écran qui montre le nœud Conteneurs d’objets blob développé avec le répertoire Chargements.](media/devtest-lab-upload-vhd-using-storage-explorer/upload-dir.png)

1. Dans le volet droit de l’Explorateur Stockage, dans la barre d’outils de l’éditeur d’objet blob, sélectionnez **Charger**, puis **Charger des fichiers**. 

   ![Capture d’écran montrant le bouton Charger et l’option Charger les fichiers.](media/devtest-lab-upload-vhd-using-storage-explorer/upload-button.png)

1. Dans la boîte de dialogue **Charger les fichiers**, sélectionnez **...** en regard du champ **Fichiers sélectionnés**, accédez au fichier VHD sur votre ordinateur, sélectionnez-le, puis choisissez **Ouvrir**.

1. Sous **Type d’objet BLOB**, remplacez **Objet blob de blocs** par **Objet blob de pages**.

1. Sélectionnez **Télécharger**.

   ![Capture d’écran montrant la boîte de dialogue Charger des fichiers.](media/devtest-lab-upload-vhd-using-storage-explorer/upload-file.png)

Le volet **Activités** au bas de l’onglet affiche l’état du chargement. Le chargement du fichier VHD peut prendre beaucoup de temps, selon la taille du fichier VHD et la vitesse de connexion.

![Capture d’écran montrant le volet Activités avec l’état du chargement.](media/devtest-lab-upload-vhd-using-storage-explorer/upload-status.png)

## <a name="next-steps"></a>Étapes suivantes

- [Créer une image personnalisée dans Azure DevTest Labs à partir d’un fichier de disque dur virtuel à l’aide du portail Azure](devtest-lab-create-template.md)
- [Créer une image personnalisée dans Azure DevTest Labs à partir d’un fichier de disque dur virtuel à l’aide de PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

