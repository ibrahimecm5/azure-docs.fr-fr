---
title: 'Démarrage rapide : Créer une topologie de réseau maillé avec Azure Virtual Network Manager en utilisant le portail Azure'
description: Utilisez ce guide de démarrage rapide pour apprendre à créer une topologie de réseau maillé avec Virtual Network Manager à l’aide du portail Azure.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: template-quickstart, ignite-fall-2021
ms.openlocfilehash: 4794e327ceeacaa91075317d1009ae45834580d8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028833"
---
# <a name="quickstart-create-a-mesh-network-topology-with-azure-virtual-network-manager-using-the-azure-portal"></a>Démarrage rapide : Créer une topologie de réseau maillé avec Azure Virtual Network Manager en utilisant le portail Azure

Démarrez avec Azure Virtual Network Manager en utilisant le portail Azure pour gérer la connectivité de tous vos réseaux virtuels.

Dans ce guide de démarrage rapide, vous allez déployer trois réseaux virtuels et utiliser le gestionnaire de réseau virtuel Azure pour créer une topologie de réseau maillé. Ensuite, vous vérifierez si la configuration de la connectivité a été appliquée.

> [!IMPORTANT]
> Azure Virtual Network Manager est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-virtual-network-manager"></a>Créer une liaison de réseau virtuel

1. Sélectionnez **+ Créer une ressource**, puis recherchez **Network Manager**. Sélectionnez ensuite **Créer** pour commencer la configuration d’Azure Virtual Network Manager.

1. Sous l’onglet *Informations de base*, entrez ou sélectionnez les informations suivantes :

    :::image type="content" source="./media/create-virtual-network-manager-portal/network-manager-basics.png" alt-text="Capture d’écran de l’option Créer sous l’onglet Informations de base.":::

    | Paramètre | Valeur |
    | ------- | ----- |
    | Abonnement | Sélectionnez l’abonnement dans lequel vous souhaitez déployer Azure Virtual Network Manager. |
    | Resource group | Sélectionnez ou créez un groupe de ressources où stocker Azure Virtual Network Manager. Cet exemple utilise le groupe **myAVNMResourceGroup** créé précédemment.
    | Nom | Entrez un nom pour cette instance d’Azure Virtual Network Manager. Cet exemple utilise le nom **myAVNM**. |
    | Région | Sélectionnez la région de ce déploiement. Azure Virtual Network Manager peut gérer des réseaux virtuels dans n’importe quelle région. La région sélectionnée correspond à l’emplacement où l’instance Virtual Network Manager sera déployée. |
    | Description | *(Facultatif)* Fournissez une description de cette instance Virtual Network Manager, ainsi que de la tâche qu’elle doit gérer. |
    | [Étendue](concept-network-manager-scope.md#scope) | Définissez l’étendue de gestion d’Azure Virtual Network Manager.
    | [Caractéristiques](concept-network-manager-scope.md#features) | Sélectionnez les fonctionnalités que vous souhaitez activer pour Azure Virtual Network Manager. Les fonctionnalités disponibles sont *Connectivité*, *Administrateur de sécurité* et *Tout sélectionner*. </br> Connectivité : permet de créer une topologie réseau de type maille pleine ou hub-and-spoke entre les différents réseaux virtuels de l’étendue. </br> Administrateur de sécurité : permet de créer des règles de sécurité réseau globales. |

1. Sélectionnez **Vérifier + créer**, puis choisissez **Créer** une fois la validation réussie.

    :::image type="content" source="./media/create-virtual-network-manager-portal/network-manager-validation.png" alt-text="Capture d’écran de la page de validation pour la création d’une ressource Network Manager.":::

## <a name="create-three-virtual-networks"></a>Créer trois réseaux virtuels

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Sélectionnez **+ Créer une ressource**, puis recherchez **Réseau virtuel**. Sélectionnez ensuite **Créer** pour commencer à configurer le réseau virtuel.

    :::image type="content" source="./media/create-virtual-network-manager-portal/create-vnet.png" alt-text="Capture d’écran de la page Créer un réseau virtuel.":::

1. Sous l’onglet *Informations de base*, entrez ou sélectionnez les informations suivantes.

    :::image type="content" source="./media/create-virtual-network-manager-portal/create-mesh-vnet-basic.png" alt-text="Capture d’écran de l’onglet Informations de base pour créer un réseau virtuel.":::

    | Paramètre | Valeur |
    | ------- | ----- |
    | Abonnement | Sélectionnez l’abonnement dans lequel vous souhaitez déployer ce réseau virtuel. |
    | Resource group | Sélectionnez ou créez un groupe de ressources pour stocker le réseau virtuel. Ce guide de démarrage rapide utilise un nouveau groupe de ressources nommé **myAVNMResourceGroup**.
    | Nom | Entrez **VNetA** comme nom de réseau virtuel. |
    | Région | Sélectionnez **USA Ouest**. |

1. Sélectionnez **Suivant : Adresses IP>** puis configurez les espaces d’adressage réseau suivants :

     :::image type="content" source="./media/create-virtual-network-manager-portal/create-mesh-vnet-ip.png" alt-text="Capture d’écran de la page Adresses IP pour Créer un réseau virtuel.":::

    | Paramètre | Valeur |
    | -------- | ----- |
    | Espace d’adressage IPv4 | 10.0.0.0/16 |
    | Nom du sous-réseau | default |
    | Espace d’adressage du sous-réseau | 10.0.0.0/24 |

1. Sélectionnez **Vérifier + créer**, puis **Créer** une fois que le déploiement du réseau virtuel a été validé.

    :::image type="content" source="./media/create-virtual-network-manager-portal/vnet-validation.png" alt-text="Capture d’écran de la page de validation pour la création d’un réseau virtuel.":::

1. Répétez les étapes 2 à 5 pour créer deux réseaux virtuels supplémentaires avec les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Abonnement | Sélectionnez l’abonnement que vous avez sélectionné à l’étape 3. |
    | Resource group | Sélectionnez **myAVNMResourceGroup**. |
    | Nom | Entrez **VNetB** pour le deuxième réseau virtuel et **VNetC** pour le troisième réseau virtuel. |
    | Région | La région est sélectionnée automatiquement lorsque vous sélectionnez le groupe de ressources. |
    | Adresses IP VNetB | Espace d’adressage IPv4 : 10.1.0.0/16 </br> Nom du sous-réseau : par défaut </br> Espace d’adressage du sous-réseau : 10.1.0.0/24|
    | Adresses IP VNetC | Espace d’adressage IPv4 : 10.2.0.0/16 </br> Nom du sous-réseau : par défaut </br> Espace d’adressage du sous-réseau : 10.2.0.0/24|

## <a name="create-a-network-group"></a>Créer un groupe réseau

1. Accédez à l’instance Azure Virtual Network Manager que vous avez créée.

1. Sélectionnez **Groupes réseau** sous *Paramètres*, puis sélectionnez **+ Ajouter**.

    :::image type="content" source="./media/create-virtual-network-manager-portal/add-network-group.png" alt-text="Capture d’écran du bouton Ajouter un groupe réseau.":::

1. Dans la page *Ajouter un groupe réseau*, entrez un **Nom** pour le groupe réseau. Cet exemple utilise le nom **myNetworkGroup**. Sélectionnez **Suivant : Regrouper les membres statiques >** pour commencer à ajouter des réseaux virtuels au groupe réseau.

    :::image type="content" source="./media/create-virtual-network-manager-portal/network-group-basics.png" alt-text="Capture d’écran de l’option Créer un groupe réseau sous l’onglet Informations de base.":::

1. Sous l’onglet *Regrouper les membres statiques*, sélectionnez **+ Ajouter des réseaux virtuels**.

    :::image type="content" source="./media/create-virtual-network-manager-portal/add-virtual-networks-button.png" alt-text="Capture d’écran du bouton Ajouter un réseau virtuel.":::

1. Dans la page *Ajouter des réseaux virtuels*, sélectionnez les trois réseaux virtuels créés précédemment (VNetA, VNetB et VNetC). Ensuite, sélectionnez **Ajouter** pour valider la sélection.

    :::image type="content" source="./media/create-virtual-network-manager-portal/add-virtual-networks.png" alt-text="Capture d’écran de la page Ajouter des réseaux virtuels à un groupe réseau.":::

1. Sélectionnez **Vérifier + créer**, puis **Créer** une fois la validation réussie.

    :::image type="content" source="./media/create-virtual-network-manager-portal/review-create.png" alt-text="Capture d’écran du bouton Vérifier et créer pour un nouveau groupe réseau.":::

1. Vous verrez que le nouveau groupe réseau a été ajouté à la page *Groupes réseau*.

    :::image type="content" source="./media/create-virtual-network-manager-portal/network-groups-list.png" alt-text="Capture d’écran de la page Groupes réseau avec le nouveau groupe réseau.":::

## <a name="create--a-connectivity-configuration"></a>Créer une configuration de connectivité

1. Sous *Paramètres*, sélectionnez **Configurations**, puis choisissez **+ Ajouter une configuration**.

    :::image type="content" source="./media/create-virtual-network-manager-portal/add-configuration.png" alt-text="Capture d’écran du bouton Ajouter une configuration pour le gestionnaire de réseau.":::

1. Sélectionnez **Connectivité** dans le menu déroulant pour commencer à créer une configuration de connectivité.

    :::image type="content" source="./media/create-virtual-network-manager-portal/configuration-menu.png" alt-text="Capture d’écran du menu déroulant de configuration.":::

1. Sur la page *Ajouter une configuration de connectivité*, entrez ou sélectionnez les informations suivantes :

    :::image type="content" source="./media/create-virtual-network-manager-portal/connectivity-configuration.png" alt-text="Capture d’écran de la page Ajouter une configuration de connectivité.":::

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez un nom pour cette configuration de connectivité. |
    | Description | *(Facultatif)* Fournissez une description de la configuration de connectivité. |
    | Topologie | Sélectionnez le type de topologie que vous souhaitez créer avec cette configuration. Cet exemple utilise la topologie **Maillage**. |

1. Une fois que vous avez sélectionné la topologie *Maillage*, les options **Maillage global** et **Groupes réseau** s’affichent. Le *maillage global* n’est pas obligatoire pour cette configuration, car tous les réseaux virtuels se trouvent dans la même région. Sélectionnez **+ Ajouter des groupes réseau**, puis sélectionnez le groupe réseau que vous avez créé dans la dernière section. Cliquez sur **Sélectionner** pour ajouter le groupe réseau à la configuration.

    :::image type="content" source="./media/create-virtual-network-manager-portal/add-network-group-configuration.png" alt-text="Capture d’écran de l’ajout d’un groupe réseau à une configuration de connectivité.":::

1. Sélectionnez **Ajouter** pour créer la configuration.

    :::image type="content" source="./media/create-virtual-network-manager-portal/create-connectivity-configuration.png" alt-text="Capture d’écran de la création d’une configuration de connectivité.":::

1. Vous verrez la nouvelle configuration de connectivité ajoutée à la page *Configuration*.

    :::image type="content" source="./media/create-virtual-network-manager-portal/connectivity-configuration-list.png" alt-text="Capture d’écran de la liste des configurations de connectivité.":::

## <a name="deploy-the-connectivity-configuration"></a>Déployer la configuration de la connectivité

Pour appliquer vos configurations à votre environnement, vous devez valider la configuration de chaque déploiement. Vous devez déployer la configuration dans la région **USA Ouest** où les réseaux virtuels sont déployés.

1. Sous *Paramètres*, sélectionnez **Déploiements**, puis **Déployer une configuration**.

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployments.png" alt-text="Capture d’écran de la page Déploiements dans le gestionnaire de réseau.":::

1. Sélectionnez les paramètres suivants :

    :::image type="content" source="./media/create-virtual-network-manager-portal/deploy-configuration.png" alt-text="Capture d’écran de la page Déployer une configuration.":::

    | Paramètre | Value |
    | ------- | ----- |
    | Type de configuration | Sélectionnez le type de configuration que vous souhaitez déployer. Cet exemple va déployer une configuration de **connectivité**. |
    | Configurations | Sélectionnez la configuration **myConnectivityConfig** créée dans la section précédente. |
    | Régions cibles | Sélectionnez la région vers laquelle déployer cette configuration. La région **USA Ouest** est sélectionnée, car tous les réseaux virtuels ont été créés dans cette région. |

1. Sélectionnez **Déployer** puis **OK** pour confirmer que vous souhaitez remplacer la configuration existante.

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployment-confirmation.png" alt-text="Capture d’écran du message de confirmation du déploiement.":::

1. Vous devez maintenant voir le déploiement s’afficher dans la liste pour la région sélectionnée. Le déploiement de la configuration peut prendre environ 15 à 20 minutes.

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployment-in-progress.png" alt-text="Capture d’écran de la configuration en cours de déploiement.":::

## <a name="confirm-configuration-deployment"></a>Valider le déploiement de la configuration

1. Sélectionnez **Actualiser** dans la page *Déploiement* pour afficher l’état mis à jour de la configuration que vous avez validée.

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployment-status.png" alt-text="Capture d’écran du bouton permettant d’actualiser l’état du déploiement.":::

1. Accédez au réseau virtuel **VNetA**, puis, sous *Paramètres*, sélectionnez **Network Manager**. Vous verrez que la configuration que vous avez déployée avec Azure Virtual Network Manager est associée au réseau virtuel.

    :::image type="content" source="./media/create-virtual-network-manager-portal/vnet-configuration-association.png" alt-text="Capture d’écran de la configuration de connectivité associée au réseau virtuel VNetA.":::

1. Vous pouvez également le vérifier pour **VNetB** et **VNetC**.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin d’Azure Virtual Network Manager, vous devez vérifier que toutes les conditions suivantes sont remplies avant de supprimer la ressource :

* Aucune configuration n’est déployée dans les régions.
* Toutes les configurations ont été supprimées.
* Tous les groupes de réseaux ont été supprimés.

1. Pour supprimer toutes les configurations d’une région, déployez une configuration de type **Aucune** dans la région cible. Sélectionnez **Déployer** puis **OK** pour confirmer.

    :::image type="content" source="./media/create-virtual-network-manager-portal/none-configuration.png" alt-text="Capture d’écran du déploiement d’une configuration de connectivité de type Aucune.":::

1. Pour supprimer une configuration, sélectionnez **Configurations** sous *Paramètres* dans le volet gauche d’Azure Virtual Network Manager. Cochez la case en regard de la configuration que vous souhaitez supprimer, puis sélectionnez **Supprimer** en haut de la page des ressources.

    :::image type="content" source="./media/create-virtual-network-manager-portal/delete-configuration.png" alt-text="Capture d’écran du bouton Supprimer pour une configuration de connectivité.":::

1. Pour supprimer un groupe réseau, sélectionnez **Groupes réseau** sous *Paramètres* dans le volet gauche d’Azure Virtual Network Manager. Cochez la case en regard du groupe réseau que vous souhaitez supprimer, puis sélectionnez **Supprimer** en haut de la page des ressources.

    :::image type="content" source="./media/create-virtual-network-manager-portal/delete-network-group.png" alt-text="Capture d’écran du bouton Supprimer pour le groupe réseau.":::

1. Une fois que tous les groupes réseau ont été supprimés, vous pouvez supprimer la ressource en cliquant avec le bouton droit sur Azure Virtual Network Manager dans la liste, puis en sélectionnant **Supprimer**.

    :::image type="content" source="./media/create-virtual-network-manager-portal/delete-network-manager.png" alt-text="Capture d’écran du bouton Supprimer pour une instance Azure Virtual Network Manager.":::

1. Pour supprimer le groupe de ressources, recherchez-le puis sélectionnez **Supprimer le groupe de ressources**. Confirmez que vous souhaitez le supprimer en entrant le nom du groupe de ressources, puis sélectionnez **Supprimer**.

    :::image type="content" source="./media/create-virtual-network-manager-portal/delete-resource-group.png" alt-text="Capture d’écran du bouton Supprimer pour un groupe de ressources.":::

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez créé l’instance Azure Virtual Network Manager, passez à la rubrique suivante pour savoir comment bloquer le trafic réseau à l’aide d’une configuration d’administrateur de sécurité :

> [!div class="nextstepaction"]
> [Bloquer le trafic réseau avec les règles d’administration de sécurité](how-to-block-network-traffic-portal.md)
