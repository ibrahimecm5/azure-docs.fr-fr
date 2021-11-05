---
title: Créer une topologie en étoile avec Azure Virtual Network Manager (préversion)
description: Découvrez comment créer une topologie de réseau en étoile (Hub and Spoke) avec Azure Virtual Network Manager.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: c07eccfda6492a5957ce9d2f0df6e0133e5b9d3d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097508"
---
# <a name="create-a-hub-and-spoke-topology-with-azure-virtual-network-manager-preview"></a>Créer une topologie en étoile avec Azure Virtual Network Manager (préversion)

Cet article explique comment créer une topologie de réseau en étoile avec Azure Virtual Network Manager. Avec cette configuration, vous sélectionnez un réseau virtuel qui agit en tant que hub. Par défaut, tous les réseaux virtuels constituant des branches de l’étoile auront un appairage (peering) bidirectionnel uniquement avec le hub. Vous pouvez également activer une connectivité directe entre les réseaux virtuels constituant les branches de l’étoile et leur permettre d’utiliser la passerelle de réseau virtuel dans le hub.

> [!IMPORTANT]
> *Azure Virtual Network Manager* est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [**Conditions supplémentaires relatives à l’utilisation des préversions de Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

* Lire la topologie de réseau [en étoile (Hub and Spoke)](concept-connectivity-configuration.md#hub-and-spoke-topology) .
* Créé une [instance Azure Virtual Network Manager](create-virtual-network-manager-portal.md#create-virtual-network-manager).
* Identifiez les réseaux virtuels que vous souhaitez utiliser dans la configuration en étoile, ou créez des [réseaux virtuels](../virtual-network/quick-create-portal.md). 

## <a name="create-a-network-group"></a><a name="group"></a> Créer un groupe de réseaux

Cette section vous aidera à créer un groupe réseau contenant les réseaux virtuels que vous utiliserez pour la topologie de réseau hub-and-spoke.

1. Accéder à votre instance Azure Virtual Network Manager. Ce guide pratique suppose que vous avez déjà créé une instance à l’aide du Guide de [démarrage rapide](create-virtual-network-manager-portal.md).

1. Sélectionnez **Connexions** sous *Paramètres*, puis sélectionnez **Ajouter** pour créer une nouvelle connexion.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-network-group.png" alt-text="Capture d’écran du bouton Ajouter un groupe réseau.":::

1. Sous l’onglet *De base*, entrez un **Nom** et une **Description** pour le groupe réseau.

    :::image type="content" source="./media/how-to-create-hub-and-spoke/basics.png" alt-text="Capture d’écran de l’onglet De base pour l’ajout d’un groupe réseau.":::

1. Pour ajouter un réseau virtuel manuellement, sélectionnez l’onglet **Membres du groupe statique**. Pour plus d’informations, consultez [Membres statiques](concept-network-groups.md#static-membership).

    :::image type="content" source="./media/how-to-create-hub-and-spoke/static-group.png" alt-text="Capture d’écran de l’onglet Membres du groupe statique.":::

1. Pour ajouter des réseaux virtuels de manière dynamique, sélectionnez l’onglet **Instructions conditionnelles**. Pour plus d’informations, consultez [Appartenance dynamique](concept-network-groups.md#dynamic-membership).

    :::image type="content" source="./media/how-to-create-hub-and-spoke/conditional-statements.png" alt-text="Capture d’écran de l’onglet Instructions conditionnelles.":::

1. Une fois que vous êtes satisfait des réseaux virtuels choisis pour le groupe réseau, sélectionnez **Examiner + créer**. Sélectionnez **Créer** après avoir passé la validation.
 
## <a name="create-a-hub-and-spoke-connectivity-configuration"></a>Créer une configuration de connectivité de réseau en étoile

Cette section vous guide tout au long de la création d’une configuration en étoile avec le groupe de réseaux que vous avez créé dans la section précédente.

1. Sous *Paramètres*, sélectionnez **Configuration**, puis choisissez **+ Ajouter une configuration**.

    :::image type="content" source="./media/how-to-create-hub-and-spoke/configuration-list.png" alt-text="Capture d’écran de la liste des configurations.":::

1. Dans le menu déroulant, sélectionnez **Connectivité**.

    :::image type="content" source="./media/create-virtual-network-manager-portal/configuration-menu.png" alt-text="Capture d’écran du menu déroulant de configuration.":::

1. Sur la page *Ajouter une configuration de connectivité*, entrez ou sélectionnez les informations suivantes :

    :::image type="content" source="./media/how-to-create-hub-and-spoke/connectivity-configuration.png" alt-text="Capture d’écran de la page Ajouter une configuration de connectivité.":::

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez un *nom* pour cette configuration. |
    | Description | *Facultatif* Entrez une description de ce que fait cette configuration de connectivité. |
    | Topologie | Sélectionnez la topologie **en étoile (Hub and Spoke)** . |
    | Hub | Sélectionnez un réseau virtuel qui fera office de réseau virtuel hub. |
    | Peerings existants | Activez cette case à cocher si vous souhaitez supprimer toutes les appairages de réseaux virtuels créés précédemment entre les réseaux virtuels dans le groupe de réseaux défini dans cette configuration. |

1. Ensuite, sélectionnez **+ Ajouter des groupes réseau**. 

1. Dans la page *Ajouter des groupes réseau*, sélectionnez les groupes réseau que vous souhaitez ajouter à cette configuration. Sélectionnez ensuite **Ajouter** pour sauvegarder.

1. Les trois options suivantes s’affichent en regard du nom du groupe de réseaux sous *Groupes de réseaux formant les rayons de l’étoile* :
    
    :::image type="content" source="./media/how-to-create-hub-and-spoke/spokes-settings.png" alt-text="Capture d’écran des paramètres des groupes de réseaux formant les rayons de l’étoile." lightbox="./media/how-to-create-hub-and-spoke/spokes-settings-expanded.png":::

    * *Connectivité directe* : sélectionnez **Activer le peering au sein du groupe de réseaux** si vous souhaitez établir un appairage (peering) entre réseaux virtuels au sein du groupe de réseaux de la même région.
    * *Maillage global* : sélectionnez **Activer la connectivité du maillage entre les régions** si vous souhaitez établir un appairage pour tous les réseaux virtuels au sein du groupe de réseaux dans plusieurs régions.
    * *Passerelle* : sélectionnez **Utiliser le hub comme passerelle** si vous avez dans le réseau virtuel hub une passerelle de réseau virtuel que vous souhaitez que ce groupe de réseaux utilise pour passer le trafic à l’emplacement local.

    Sélectionnez les paramètres que vous souhaitez activer pour chaque groupe de réseaux.

1. Enfin, sélectionnez **Ajouter** pour créer la configuration de la connectivité en étoile.

## <a name="deploy-the-hub-and-spoke-configuration"></a>Déployer la configuration en étoile

Pour que cette configuration soit effective dans votre environnement, vous devez la déployer dans les régions où vos réseaux virtuels sélectionnés sont créés.

1. Sélectionnez **Déploiement** sous *Paramètres*, puis cliquez sur **Déployer une configuration**.

1. Dans la section *Déployer une configuration*, sélectionnez les paramètres suivants :

    :::image type="content" source="./media/how-to-create-hub-and-spoke/deploy.png" alt-text="Capture d’écran de la page Déployer une configuration.":::

    | Paramètre | Value |
    | ------- | ----- |
    | Type de configuration | Sélectionnez **Connectivité**. |
    | Configurations | Sélectionnez le nom de la configuration que vous avez créée dans la section précédente. |
    | Régions cibles | Sélectionnez toutes les régions qui s’appliquent aux réseaux virtuels que vous sélectionnez pour la configuration. |

1. Sélectionnez **Déployer**, puis cliquez sur **OK** pour valider la configuration dans les régions sélectionnées.

1. Le déploiement de la configuration peut prendre jusqu’à 15-20 minutes, sélectionner le bouton **Actualiser** pour vérifier l’état du déploiement.

## <a name="confirm-deployment"></a>Confirmer le déploiement

1. Consultez [Afficher la configuration appliquée](how-to-view-applied-configurations.md).

1. Pour tester *la connectivité directe* entre les branches de l’étoile, déployez une machine virtuelle dans chacun de leurs réseaux virtuels. Lancez ensuite une requête ICMP d’une machine virtuelle à l’autre.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [Règles de sécurité administrateur](concept-security-admins.md)
- Découvrez comment bloquer le trafic réseau avec une [Configuration de l’administration de la sécurité](how-to-block-network-traffic-portal.md).
