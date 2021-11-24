---
title: Fichier include
description: fichier
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 11/10/2021
ms.openlocfilehash: 921e441a783d1f7bf4dc68b9decf03d6b23fbdb8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132354032"
---
## <a name="generalize-the-image"></a>Généraliser l’image

Toutes les images de la Place de marché Azure doivent être réutilisables de façon générale. Pour autoriser cela, le VHD du système d’exploitation doit être généralisé. Cette opération consiste à supprimer d’une machine virtuelle tous les pilotes logiciels et identificateurs propres à une instance.

### <a name="for-windows"></a>Pour Windows

Les disques de système d’exploitation Windows sont généralisés à l’aide de l’outil [Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Si vous mettez à jour ou reconfigurez le système d’exploitation par la suite, vous devrez réexécuter Sysprep.

> [!WARNING]
> Après l’exécution de Sysprep, éteignez la machine virtuelle jusqu’à son déploiement, car les mises à jour peuvent s’exécuter automatiquement. Cet arrêt évite que des mises à jour ultérieures apportent des modifications propres à une instance au système d’exploitation ou aux services installés. Pour plus d’informations sur l’exécution de sysprep, consultez [Généraliser une machine virtuelle Windows](../../virtual-machines/generalize.md#windows).

### <a name="for-linux"></a>Pour Linux

1. Supprimez l’agent Linux Azure.
    1. Connectez-vous à votre machine virtuelle Linux en utilisant un client SSH.
    2. Dans la fenêtre SSH, saisissez la commande suivante : `sudo waagent –deprovision+user`.
    3. Tapez Y pour continuer (vous pouvez ajouter le paramètre -force à la commande précédente pour éviter l’étape de confirmation).
    4. Une fois la commande exécutée, entrez **Exit** pour fermer le client SSH.
2. Arrêtez la machine virtuelle.
    1. Dans le portail Azure, sélectionnez votre groupe de ressources et désallouez la machine virtuelle.
    2. Votre machine virtuelle est désormais généralisée, et vous pouvez créer une autre machine virtuelle à l’aide de ce disque de machine virtuelle.

### <a name="capture-image"></a>Capturer l’image

> [!NOTE]
> L’abonnement Azure contenant Azure Compute Gallery doit être sous le même locataire que le compte de l’éditeur pour pouvoir être publié. En outre, le compte de l’éditeur doit avoir au moins un accès Contributeur à l’abonnement contenant Azure Compute Gallery.

Une fois que votre machine virtuelle est prête, vous pouvez la capturer dans une galerie Azure Compute Gallery (anciennement connue comme Shared Image Gallery). Suivez les étapes ci-dessous pour la capturer :

1. Dans [Portail Azure](https://ms.portal.azure.com/), accédez à la page de votre machine virtuelle.
2. Sélectionnez **Capturer**.
3. Sous **Partager l’image dans Azure Compute Gallery**, sélectionnez **Oui, la partager dans une galerie en tant que version d’image**.
4. Sous **État du système d’exploitation**, sélectionnez Généralisé.
5. Sélectionnez une galerie d’images cible ou **créez-en une nouvelle**.
6. Sélectionnez une définition d’image cible ou **créez-en une nouvelle**.
7. Indiquez un **numéro de version** pour l’image.
8. Sélectionnez **Vérifier + créer** pour passer en revue vos choix.
9. Une fois la validation réussie, sélectionnez **Créer**.

## <a name="set-the-right-permissions"></a>Définir les autorisations appropriées

Si votre compte Espace partenaires est le propriétaire de l’abonnement qui héberge une galerie Azure Compute Gallery, rien d’autre n’est nécessaire pour les autorisations.

Si vous disposez uniquement d’un accès en lecture à l’abonnement, utilisez l’une des deux options suivantes.

### <a name="option-one--ask-the-owner-to-grant-owner-permission"></a>Option 1 : Demander au propriétaire d’accorder son autorisation

Étapes à suivre pour que le propriétaire accorde son autorisation :

1. Accédez à la galerie Azure Compute Gallery.
2. Sélectionnez **Contrôle d’accès** (IAM) dans le volet gauche.
3. Sélectionnez **Ajouter**, puis **Ajouter une attribution de rôle**.<br>
    :::image type="content" source="../media/create-vm/add-role-assignment.png" alt-text="La fenêtre Ajouter une attribution de rôle s’affiche":::.
1. Sous **Rôle**, sélectionnez **Propriétaire**.
1. Pour **Attribuer l’accès à**, sélectionnez **Utilisateur, groupe ou principal du service**.
1. Pour **Sélectionner**, entrez l’adresse e-mail Azure de la personne qui publiera l’image.
1. Sélectionnez **Enregistrer**.

### <a name="option-two--run-a-command"></a>Option 2 : Exécuter une commande

Demandez au propriétaire d’exécuter l’une de ces commandes (dans les deux cas, utilisez le SusbscriptionId de l’abonnement où vous avez créé la galerie Azure Compute Gallery).

```azurecli
az login
az provider register --namespace Microsoft.PartnerCenterIngestion --subscription {subscriptionId}
```
 
```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId {subscriptionId}
Register-AzResourceProvider -ProviderNamespace Microsoft.PartnerCenterIngestion
```

> [!NOTE]
> Vous n’avez pas besoin de générer d’URI de SAP, car vous pouvez désormais publier une image provenant d’une galerie Azure Compute Gallery sur l’Espace partenaires. Toutefois, si vous avez encore besoin de vous référer aux étapes de génération d’un URI de SAP, consultez [Comment générer un URI SAS pour une image de machine virtuelle](../azure-vm-get-sas-uri.md).
