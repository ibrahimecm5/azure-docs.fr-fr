---
title: Créer et ajouter une machine virtuelle à un lab
description: Découvrez comment ajouter le portail Azure pour ajouter une machine virtuelle à un labo dans Azure DevTest Labs. Vous pouvez choisir une base qui est soit une image personnalisée, soit une formule.
ms.topic: how-to
ms.date: 10/20/2021
ms.openlocfilehash: 326d8923b27abff2ee480f6b981392a311be4f30
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130235285"
---
# <a name="create-and-add-virtual-machines-to-a-lab-in-azure-devtest-labs"></a>Créer et ajouter des machines virtuelles à un lab dans Azure DevTest Labs

Cet article vous guide tout au long de la création et de l’ajout de machines virtuelles Azure dans votre lab DevTest Labs existant à l’aide du portail Azure.

## <a name="create-and-add-virtual-machines"></a>Créer et ajouter des machines virtuelles

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Accédez à votre lab dans **DevTest Labs**.

1. Dans la page **Vue d’ensemble**, sélectionnez **+ Ajouter**.

   :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-add-vm.png" alt-text="Page de présentation du lab montrant le bouton Ajouter.":::

1. Dans la page **Choisir une base**, sélectionnez une image de la Place de marché pour la machine virtuelle. Ce guide utilise **Windows 11 Pro**. Certaines options peuvent différer si vous utilisez une autre image.

1. Sous l’onglet **Paramètres de base**, fournissez les informations suivantes :

    |Propriété |Description |
    |---|---|
    |Nom de la machine virtuelle| La zone de texte est préremplie avec un nom unique généré automatiquement. Le nom correspond au nom d’utilisateur de votre adresse e-mail, suivi d’un numéro unique à trois chiffres. Laissez la valeur par défaut ou entrez un nom unique de votre choix.|
    |User Name| La zone de texte est préremplie avec un nom unique généré automatiquement. Le nom correspond au nom d’utilisateur de votre adresse e-mail. Laissez la valeur par défaut ou entrez un nom de votre choix. L’utilisateur obtient des privilèges d’**administrateur** sur la machine virtuelle.|
    |Utiliser un secret enregistré| Pour cette procédure pas à pas, laissez la case décochée. Vous pouvez d’abord enregistrer des secrets dans Azure Key Vault, puis les utiliser ici. Pour plus d’informations, consultez [Stocker des secrets dans un coffre de clés](devtest-lab-store-secrets-in-key-vault.md). Si vous préférez utiliser un secret enregistré, cochez la case et sélectionnez le secret dans la liste déroulante **Secret**.|
    |Mot de passe|Entrez un mot de passe entre 8 et 123 caractères.|
    |Enregistrer comme mot de passe par défaut| Cochez la case pour enregistrer le mot de passe dans le coffre Azure Key Vault associé au lab.|
    |Taille de la machine virtuelle| Laissez la valeur par défaut ou sélectionnez **Changer la taille** pour sélectionner des composants physiques différents. Cette procédure pas à pas utilise **Standard_B2**.|
    |Type de disque du système d’exploitation|Laissez la valeur par défaut ou sélectionnez une option différente dans la liste déroulante.|
    |Artifacts| Sélectionnez **Ajouter ou supprimer des artefacts**. Sélectionnez et configurez les artefacts que vous souhaitez ajouter à l’image de base. Chaque lab comprend des artefacts provenant du dépôt d’artefacts DevTest Labs public, ainsi que les artefacts que vous avez créés et ajoutés à votre propre dépôt d’artefacts. Pour obtenir des instructions détaillées, consultez [Ajouter des artefacts pendant l’installation](#add-artifacts-during-installation), plus loin dans cet article.|

   :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-vm-basic-settings.png" alt-text="Page des paramètres de base d’une machine virtuelle.":::

1. Sélectionnez l’onglet **Paramètres avancés** et fournissez les informations suivantes :

    |Propriété |Description |
    |---|---|
    |Réseau virtuel| Laissez la valeur par défaut ou sélectionnez un autre réseau dans la liste déroulante.|
    |Sélecteur de sous-réseau| Laissez la valeur par défaut ou sélectionnez un autre sous-réseau dans la liste déroulante.|
    |Adresse IP| Pour cette procédure pas à pas, laissez la valeur par défaut (**Partagée**).|
    |Date d'expiration| Laissez la valeur par défaut pour ne pas définir de date d’expiration ou sélectionnez l’icône de calendrier pour en définir une.|
    |Autoriser la revendication de cette machine| Pour autoriser la revendication de la machine virtuelle par un utilisateur lab, sélectionnez **Oui**. L’autorisation de la revendication de la machine signifie qu’elle ne reçoit pas de propriétaire au moment de la création. Cette procédure pas à pas sélectionne **Oui**.|
    |Nombre d’instances| Pour cette procédure pas à pas, entrez **2**. Nombre d’instances de machine virtuelle à créer.|
    |Automation | facultatif. Sélectionnez **Voir le modèle ARM** pour ouvrir le modèle dans une nouvelle page. Vous pouvez copier et enregistrer le modèle pour créer la même machine virtuelle plus tard. Une fois enregistré, vous pouvez utiliser le modèle Azure Resource Manager pour [déployer de nouvelles machines virtuelles avec Azure PowerShell](../azure-resource-manager/templates/overview.md).|

   :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-vm-advanced-settings.png" alt-text="Page des paramètres avancés de la machine virtuelle.":::

1. Revenez à l’onglet **Paramètres de base** et sélectionnez **Créer**.

1. Dans la page **DevTest Lab**, sous **Mon lab**, sélectionnez **Machines virtuelles pouvant être revendiquées**.

   :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-vm-creation-status.png" alt-text="Page d’état de la création des machines virtuelles.":::

1. Au bout de quelques minutes, sélectionnez **Actualiser** si vos machines virtuelles n’apparaissent pas. Les durées d’installation varient en fonction du matériel, de l’image de base et des artefacts sélectionnés. L’installation des configurations utilisées dans cette procédure pas à pas a pris environ 25 minutes.

## <a name="add-artifacts-during-installation"></a>Ajouter des artefacts pendant l’installation

Ces étapes sont des instructions développées issues de la section précédente. Les étapes commencent après que vous avez sélectionné **Ajouter ou supprimer des artefacts** sous l’onglet **Paramètres de base**. Pour plus d’informations sur les artefacts, consultez [Découvrez comment créer vos propres artefacts pour les utiliser avec DevTest Labs](devtest-lab-artifact-author.md).

1. Dans la page **Ajouter des artefacts**, identifiez un artefact, puis sélectionnez **>** (symbole supérieur à). Sélectionnez ensuite **OK**.

   :::image type="content" source="./media/devtest-lab-add-vm/portal-add-artifact-during.png" alt-text="Ajoutez un artefact à la machine virtuelle.":::

1. Sélectionnez un autre artefact : **Installer le module PowerShell**. Cet artefact nécessite des informations supplémentaires, notamment le nom d’un module PowerShell. Entrez **Az**, puis sélectionnez **OK**.

1. Continuez à ajouter des artefacts en fonction des besoins de votre machine virtuelle.

1. Sélectionnez **...** (points de suspension) à partir de l’un de vos artefacts sélectionnés et observez les différentes options, notamment la possibilité de modifier l’ordre d’installation.

1. Quand vous avez terminé d’ajouter des artefacts, sélectionnez **OK** pour revenir à l’onglet **Paramètres de base**.

## <a name="add-artifacts-after-installation"></a>Ajouter des artefacts après l’installation

Vous pouvez aussi ajouter des artefacts après avoir créé la machine virtuelle. 

1. Dans la page **DevTest Lab**, sous **Mon lab**, sélectionnez **Toutes les ressources**. La page **Toutes les ressources** liste à la fois les machines virtuelles revendiquées et non revendiquées.

    :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-vm-all-resources.png" alt-text="Lab présentant l’état de toutes les ressources.":::

1. Sélectionnez votre machine virtuelle une fois que l’**état** indique **Disponible**.

1. À partir de la page de votre **machine virtuelle**, sélectionnez **Démarrer** pour démarrer la machine virtuelle.

1. Quelques instants après que la page indique **En cours d’exécution**, sous **Opérations**, sélectionnez **Artifacts**.

   :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-vm-overview.png" alt-text="Vue d’ensemble de la machine virtuelle montrant le bouton Démarrer.":::

1. Sélectionnez **Appliquer des artefacts** pour ouvrir la page **Ajouter des artefacts**.

1. À partir de cette page, les étapes sont grosso modo les mêmes que celles de la section [Ajouter des artefacts pendant l’installation](#add-artifacts-during-installation), ci-dessus.

## <a name="next-steps"></a>Étapes suivantes

* Une fois la machine virtuelle créée, vous pouvez vous y connecter en sélectionnant **Connexion** dans le volet de la machine virtuelle.
* Découvrez comment [créer des artefacts personnalisés pour vos machines virtuelles DevTest Labs](devtest-lab-artifact-author.md).
* Explorez la [Galerie de modèles de démarrage rapide d’Azure Resource Manager DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
