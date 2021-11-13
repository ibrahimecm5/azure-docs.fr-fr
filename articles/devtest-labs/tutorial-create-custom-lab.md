---
title: Créer un tutoriel de labo
description: Dans ce tutoriel, vous créez un laboratoire dans Azure DevTest Labs à l’aide du portail Azure. Un administrateur de laboratoire définit un laboratoire, crée dans celui-ci des machines virtuelles et configure des stratégies.
ms.topic: tutorial
ms.date: 11/03/2021
ms.openlocfilehash: 4a26501511adf65d2db8509120a1d4a014ef73ba
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578582"
---
# <a name="tutorial-set-up-a-lab-in-devtest-labs-using-the-azure-portal"></a>Tutoriel : Configurer un lab dans DevTest Labs en utilisant le portail Azure

Dans ce didacticiel, vous créez un laboratoire en utilisant le portail Azure. Un administrateur lab définit un lab dans une organisation, crée des machines virtuelles Azure dans le lab et configure des stratégies. Les utilisateurs du laboratoire (les développeurs et les testeurs par exemple) revendiquent des machines virtuelles dans le laboratoire, se connectent à eux et les utilisent. 

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Création d’un laboratoire
> * Ajouter une machine virtuelle Azure au lab
> * Ajoutez un utilisateur et attribuez-lui le rôle **Utilisateur DevTest Labs**

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-a-lab"></a>Création d’un laboratoire

Ces étapes montrent comment utiliser le portail Azure pour créer un lab dans Azure DevTest Labs. 

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Entrez `DevTest Labs` dans la zone de texte de recherche, puis sélectionnez **DevTest Labs** dans les résultats.

    :::image type="content" source="./media/tutorial-create-custom-lab/portal-search-devtest-labs.png" alt-text="Capture d’écran de la recherche de DevTest Labs dans le portail.":::

1. Dans la page **DevTest Labs**, sélectionnez **+ Créer**.

1. Dans la page **Créer un lab Devtest**, sous l’onglet **Paramètres de base**, fournissez les informations suivantes :

    |Propriété | Description |
    |---|---|
    |Abonnement| Dans la liste déroulante, sélectionnez l’abonnement Azure à utiliser pour le lab.|
    |Groupe de ressources| Dans la liste déroulante, sélectionnez votre groupe de ressources existant ou **Créer**.|
    |Nom du lab| Entrez un nom pour le laboratoire.|
    |Location| Dans la liste déroulante, sélectionnez un emplacement utilisé pour le lab.|
    |Environnements publics| Laissez la valeur par défaut (**Activé**). Le dépôt d’environnements publics contient une liste de modèles Azure Resource Manager organisés qui permettent aux utilisateurs lab de créer des ressources PaaS dans Labs.|

    :::image type="content" source="./media/tutorial-create-custom-lab/create-custom-lab-blade.png" alt-text="Capture d’écran de l’onglet Paramètres de base pour Créer un lab DevTest.":::

1. Sélectionnez **Vérifier + créer** pour valider la configuration, puis sélectionnez **Créer**. Pour ce tutoriel, les valeurs par défaut des autres onglets sont suffisantes.

1. Quand vous avez terminé le processus de création, sélectionnez **Accéder à la ressource** dans la notification de déploiement.

    :::image type="content" source="./media/tutorial-create-custom-lab/creation-notification.png" alt-text="Capture d’écran de la notification de déploiement de DevTest Labs.":::

1. La page **Vue d’ensemble** du lab ressemble à l’image suivante :

    :::image type="content" source="./media/tutorial-create-custom-lab/lab-home-page.png" alt-text="Capture d’écran de la page de présentation de DevTest Labs.":::

## <a name="add-a-vm-to-the-lab"></a>Ajouter une machine virtuelle au laboratoire

1. Sur la page **DevTest Lab**, sélectionnez **+ Ajouter** sur la barre d’outils.

    :::image type="content" source="./media/tutorial-create-custom-lab/add-vm-to-lab-button.png" alt-text="Capture d’écran de la page de présentation de DevTest Labs et du bouton Ajouter.":::

1. Dans la page **Choisir une base**, sélectionnez une image de la Place de marché pour la machine virtuelle. Ce guide utilise **Windows Server 2019 Datacenter**. Certaines options peuvent différer si vous utilisez une autre image.

1. Sous l’onglet **Paramètres de base**, fournissez les informations suivantes :

    |Propriété |Description |
    |---|---|
    |Nom de la machine virtuelle| La zone de texte est préremplie avec un nom unique généré automatiquement. Le nom correspond au nom d’utilisateur de votre adresse e-mail, suivi d’un numéro unique à trois chiffres. Laissez la valeur par défaut ou entrez un nom unique de votre choix.|
    |User Name| La zone de texte est préremplie avec un nom unique généré automatiquement. Le nom correspond au nom d’utilisateur de votre adresse e-mail. Laissez la valeur par défaut ou entrez un nom de votre choix. L’utilisateur obtient des privilèges d’**administrateur** sur la machine virtuelle.|
    |Utiliser un secret enregistré| Pour cette procédure pas à pas, laissez la case décochée. Vous pouvez d’abord enregistrer des secrets dans Azure Key Vault, puis les utiliser ici. Pour plus d’informations, consultez [Stocker des secrets dans un coffre de clés](devtest-lab-store-secrets-in-key-vault.md). Si vous préférez utiliser un secret enregistré, cochez la case et sélectionnez le secret dans la liste déroulante **Secret**.|
    |Mot de passe|Entrez un mot de passe entre 8 et 123 caractères.|
    |Enregistrer comme mot de passe par défaut| Cochez la case pour enregistrer le mot de passe dans le coffre Azure Key Vault associé au lab.|
    |Taille de la machine virtuelle| Laissez la valeur par défaut ou sélectionnez **Changer la taille** pour sélectionner des composants physiques différents. Cette procédure pas à pas utilise **Standard_D4_v3**.|
    |Type de disque du système d’exploitation|Laissez la valeur par défaut ou sélectionnez une option différente dans la liste déroulante.|
    |Artifacts| Non utilisés dans ce tutoriel.|

   :::image type="content" source="./media/tutorial-create-custom-lab/portal-lab-vm-basic-settings.png" alt-text="Capture d’écran de la page des paramètres de base de la machine virtuelle.":::

1. Sélectionnez l’onglet **Paramètres avancés** et fournissez les informations suivantes :

    |Propriété |Description |
    |---|---|
    |Réseau virtuel| Laissez la valeur par défaut ou sélectionnez un autre réseau dans la liste déroulante.|
    |Sélecteur de sous-réseau| Laissez la valeur par défaut ou sélectionnez un autre sous-réseau dans la liste déroulante.|
    |Adresse IP| Pour cette procédure pas à pas, laissez la valeur par défaut (**Partagée**). Quand **Partagée** est sélectionné, Azure DevTest Labs active automatiquement RDP pour les machines virtuelles Windows et SSH pour les machines virtuelles Linux. Si vous sélectionnez **Publique**, RDP et SSH sont activés sans changement à partir de DevTest Labs.  |
    |Date d'expiration| Laissez la valeur par défaut pour ne pas définir de date d’expiration ou sélectionnez l’icône de calendrier pour en définir une.|
    |Autoriser la revendication de cette machine| Laissez la valeur **Non**. Pour autoriser la revendication de la machine virtuelle par un utilisateur lab, sélectionnez **Oui**. L’autorisation de la revendication de la machine signifie qu’elle ne reçoit pas de propriétaire au moment de la création. |
    |Nombre d’instances| Laissez la valeur **1**. Nombre d'instances de machine virtuelle à créer.|
    |Automatisation | facultatif. Sélectionnez **Voir le modèle ARM** pour ouvrir le modèle dans une nouvelle page. Vous pouvez copier et enregistrer le modèle pour créer la même machine virtuelle plus tard. Une fois enregistré, vous pouvez utiliser le modèle Azure Resource Manager pour [déployer de nouvelles machines virtuelles avec Azure PowerShell](../azure-resource-manager/templates/overview.md).|

   :::image type="content" source="./media/tutorial-create-custom-lab/portal-lab-vm-advanced-settings.png" alt-text="Page des paramètres avancés de la machine virtuelle.":::

1. Revenez à l’onglet **Paramètres de base** et sélectionnez **Créer**.

1. Vous êtes redirigé vers la page **Lab DevTest**. Sous **Mon lab**, sélectionnez **Machines virtuelles pouvant être revendiquées**.

   :::image type="content" source="./media/tutorial-create-custom-lab/portal-lab-vm-creation-status.png" alt-text="Capture d’écran de la page d’état de création de la machine virtuelle lab.":::

1. Au bout de quelques minutes, sélectionnez **Actualiser** si vos machines virtuelles n’apparaissent pas. Les durées d’installation varient en fonction du matériel, de l’image de base et des artefacts sélectionnés. L’installation des configurations utilisées dans cette procédure pas à pas a pris environ 12 minutes.

## <a name="add-a-user-to-the-devtest-labs-user-role"></a>Ajouter un utilisateur au rôle d’utilisateur DevTest Labs

1. Accédez au groupe de ressources qui contient le lab que vous avez créé. Vous devez avoir le rôle [Administrateur de l’accès utilisateur](../role-based-access-control/built-in-roles.md#user-access-administrator) ou [Propriétaire](../role-based-access-control/built-in-roles.md#owner).

1. Dans le menu de gauche, sélectionnez **Contrôle d’accès (IAM)** .

1. Sélectionnez **+ Ajouter** > **Ajouter une attribution de rôle**.

    ![Page Contrôle d’accès (IAM) avec le menu Ajouter une attribution de rôle ouvert.](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. Sous l’onglet **Rôle**, sélectionnez le rôle **Utilisateur DevTest Labs**.

    ![Page Ajouter une attribution de rôle avec l’onglet Rôle sélectionné.](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. Sous l’onglet **Membres**, sélectionnez l’utilisateur à qui attribuer le rôle.

1. Dans l’onglet **Passer en revue + affecter**, sélectionnez **Passer en revue + affecter** pour affecter le rôle.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Supprimez des ressources pour éviter les frais liés à l’exécution du lab et de la machine virtuelle sur Azure. Si vous comptez exécuter le tutoriel suivant pour accéder à la machine virtuelle dans le lab, vous pouvez nettoyer les ressources à la fin de ce tutoriel. Dans le cas contraire, procédez ainsi : 

1. Revenez à la page d’accueil du lab que vous avez créé.

1. Dans le menu du haut, sélectionnez **Supprimer**.

   :::image type="content" source="./media/tutorial-create-custom-lab/portal-lab-delete.png" alt-text="Capture d’écran du bouton Supprimer du lab.":::

1. Dans la page **Voulez-vous vraiment le supprimer**, entrez le nom du lab dans la zone de texte et sélectionnez **Supprimer**.

1. Pendant la suppression, vous pouvez sélectionner **Notifications** en haut de votre écran pour voir la progression. La suppression du lab prend un certain temps. Passez à l’étape suivante une fois le lab supprimé.

1. Si vous avez créé le lab dans un groupe de ressources existant, toutes les ressources de lab ont été supprimées. Si vous avez créé un groupe de ressources pour ce tutoriel, il est maintenant vide et peut être supprimé. Vous ne pouvez pas supprimer le groupe de ressources plus tôt quand le lab y est toujours.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé un lab, ajouté une machine virtuelle, puis donné à un utilisateur l’accès au lab. Pour savoir comment accéder au laboratoire en tant qu’un utilisateur du laboratoire, passer au didacticiel suivant :

> [!div class="nextstepaction"]
> [Didacticiel : Accéder au laboratoire](tutorial-use-custom-lab.md)
