---
title: 'Démarrage rapide : Créer un labo dans le portail Azure'
description: Dans ce guide de démarrage rapide, vous créez un labo avec le portail Azure et Azure DevTest Labs.
ms.topic: quickstart
ms.date: 11/04/2021
ms.openlocfilehash: 5f86c09c4f2d6fab4ad590d7e0bf62194c666e10
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286390"
---
# <a name="quickstart-create-a-lab-in-azure-devtest-labs-in-azure-portal"></a>Démarrage rapide : Créer un labo dans Azure DevTest Labs dans le portail Azure

Démarrez avec Azure DevTest Labs en utilisant le portail Azure pour créer un labo. Azure DevTest Labs inclut un groupe de ressources, comme des réseaux et des machines virtuelles Azure. Cette infrastructure vous permet de mieux gérer ces ressources en spécifiant des limites et des quotas. Ce guide de démarrage rapide vous guide dans le processus de création d’un labo avec le portail Azure.

## <a name="prerequisites"></a>Prérequis

Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). Pour créer le laboratoire, vous devez être le propriétaire de l’abonnement.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

En sélectionnant le lien suivant, vous êtes transféré vers la page du portail Azure qui vous permet de commencer à créer un lab dans Azure DevTest Labs.

[Prenez en main Azure DevTest Labs en quelques minutes.](https://go.microsoft.com/fwlink/?LinkID=627034&clcid=0x409)

## <a name="create-a-devtest-labs-resource"></a>Créer une ressource DevTest Labs

La page **Créer un lab DevTest Labs** contient cinq onglets. Le premier onglet est **Paramètres de base**.

> [!TIP]
> En bas de chaque page, vous trouverez un lien qui vous permet de **télécharger un modèle d’automatisation**.

### <a name="basic-settings-tab"></a>Onglet Paramètres de base

Fournissez les informations suivantes :

|Propriété | Description |
|---|---|
|Abonnement| Dans la liste déroulante, sélectionnez l’abonnement Azure à utiliser pour le lab.|
|Groupe de ressources| Dans la liste déroulante, sélectionnez votre groupe de ressources existant ou **Créer**.|
|Nom du lab| Entrez un nom unique dans votre abonnement pour le lab.|
|Location| Dans la liste déroulante, sélectionnez un emplacement utilisé pour le lab.|
|Environnements publics| Le dépôt d’environnements publics contient la liste des modèles Azure Resource Manager organisés qui permettent aux utilisateurs lab de créer des ressources PaaS dans Labs. Pour plus d’informations, consultez [Configurer et utiliser des environnements publics](devtest-lab-configure-use-public-environments.md).|

:::image type="content" source="./media/devtest-lab-create-lab/portal-create-basic-settings.png" alt-text="Capture d’écran de l’onglet Paramètres de base pour Créer un lab DevTest Labs.":::


### <a name="auto-shutdown-tab"></a>Onglet Arrêt automatique

L’arrêt automatique vous permet d’arrêter automatiquement toutes les machines d’un laboratoire à une heure planifiée chaque jour. La fonctionnalité d’arrêt automatique a principalement vocation à réduire les coûts. Pour modifier les paramètres d’arrêt automatique après avoir créé le lab, consultez [Gérer toutes les stratégies d’un lab dans Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown).

Fournissez les informations suivantes :

|Propriété | Description |
|---|---|
|activé| Sélectionnez **Activer** ou **Désactiver** pour activer ou désactiver cette stratégie.|
|Arrêt&nbsp;planifié| Entrez une heure à laquelle arrêter toutes les machines virtuelles du lab actuel.|
|Fuseau horaire| Sélectionnez un fuseau horaire dans la liste déroulante.|
|Envoyer une notification avant l’arrêt automatique ? | Sélectionnez **Oui** ou **Non** pour envoyer une notification 30 minutes avant l’heure d’arrêt automatique spécifiée. Si vous choisissez **Oui**, saisissez un point de terminaison de l’URL de Webhook ou une adresse e-mail spécifiant où vous désirez publier ou envoyer la notification. L’utilisateur reçoit une notification et peut retarder l’arrêt.|
|URL du webhook| Une notification est publiée sur le point de terminaison de webhook spécifié quand l’arrêt automatique est proche.|
|Adresse de messagerie| Entrez un ensemble d’adresses e-mail délimitées par des points-virgules pour recevoir les e-mails de notification d’alerte.|

:::image type="content" source="./media/devtest-lab-create-lab/portal-create-auto-shutdown.png" alt-text="Capture d’écran des détails de la planification de l’arrêt automatique.":::

### <a name="networking-tab"></a>Onglet Réseau

Vous disposez d’un réseau par défaut créé automatiquement (vous pouvez le changer/configurer plus tard), ou vous pouvez sélectionner un réseau virtuel existant.

Fournissez les informations suivantes :

|Propriété | Description |
|---|---|
|Réseau&nbsp;virtuel| Conservez la valeur par défaut ou sélectionnez-en une existante dans la liste déroulante. Les réseaux virtuels sont isolés logiquement les uns des autres dans Azure. Par défaut, les machines virtuelles situées dans le même réseau virtuel peuvent accéder les unes aux autres.|
|Subnet| Conservez la valeur par défaut ou sélectionnez-en une existante dans la liste déroulante. Un sous-réseau est une plage d’adresses IP au sein de votre réseau virtuel, qui permet d’isoler les machines virtuelles les unes des autres ou d’Internet.|

:::image type="content" source="./media/devtest-lab-create-lab/portal-create-networking.png" alt-text="Capture d’écran des détails réseau.":::

### <a name="tags-tab"></a>Onglet balises

Les balises sont utiles pour vous aider à gérer et à organiser les ressources du laboratoire par catégorie. Pour plus d’informations, consultez [Ajouter des balises à un lab](devtest-lab-add-tag.md).

Fournissez les informations suivantes :

|Propriété | Description |
|---|---|
|Nom| Les noms de balise ne respectent pas la casse et sont limités à 512 caractères.|
|Valeur| Les valeurs de balise respectent la casse et sont limitées à 256 caractères.|

:::image type="content" source="./media/devtest-lab-create-lab/portal-create-tags.png" alt-text="Capture d’écran des détails de balise.":::

### <a name="review--create-tab"></a>Onglet Vérifier + créer

L’onglet **Vérifier + créer** valide toutes vos configurations. Si tous les paramètres sont valides, **Opération réussie** s’affiche tout en haut. Vérifiez vos paramètres, puis sélectionnez **Créer**. Vous pouvez surveiller l’état du processus de création du laboratoire en regardant la zone **Notifications** en haut à droite de la page du portail. 

:::image type="content" source="./media/devtest-lab-create-lab/portal-review-and-create.png" alt-text="Capture d’écran des détails de l’onglet Vérifier + créer.":::

## <a name="post-creation"></a>Après la création

1. Quand vous avez terminé le processus de création, sélectionnez **Accéder à la ressource** dans la notification de déploiement.

    :::image type="content" source="./media/devtest-lab-create-lab/creation-notification.png" alt-text="Capture d’écran de la notification de déploiement de DevTest Labs.":::

1. La page **Vue d’ensemble** du lab ressemble à l’image suivante :

    :::image type="content" source="./media/devtest-lab-create-lab/lab-home-page.png" alt-text="Capture d’écran de la page de présentation de DevTest Labs.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Supprimez des ressources pour éviter les frais liés à l’exécution du labo sur Azure. Si vous envisagez de passer à l’article suivant pour ajouter une machine virtuelle au labo, vous pouvez nettoyer les ressources une fois l’article terminé. Dans le cas contraire, procédez ainsi :

1. Revenez à la page d’accueil du lab que vous avez créé.

1. Dans le menu du haut, sélectionnez **Supprimer**.

   :::image type="content" source="./media/devtest-lab-create-lab/portal-lab-delete.png" alt-text="Capture d’écran du bouton Supprimer du lab.":::

1. Dans la page **Voulez-vous vraiment le supprimer**, entrez le nom du lab dans la zone de texte et sélectionnez **Supprimer**.

1. Pendant la suppression, vous pouvez sélectionner **Notifications** en haut de votre écran pour voir la progression. La suppression du lab prend un certain temps. Passez à l’étape suivante une fois le lab supprimé.

1. Si vous avez créé le lab dans un groupe de ressources existant, toutes les ressources de lab ont été supprimées. Si vous avez créé un groupe de ressources pour ce tutoriel, il est maintenant vide et peut être supprimé. Vous ne pouvez pas supprimer le groupe de ressources plus tôt quand le lab y est toujours.

## <a name="next-steps"></a>Étapes suivantes
Dans le cadre de ce guide de démarrage rapide, vous avez créé un labo. Pour savoir comment ajouter une machine virtuelle, passez à l’article suivant :

> [!div class="nextstepaction"]
> [Créer et ajouter des machines virtuelles à un lab dans Azure DevTest Labs](devtest-lab-add-vm.md)
