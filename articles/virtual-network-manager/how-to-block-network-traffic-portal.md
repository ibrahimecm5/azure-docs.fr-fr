---
title: Blocage du trafic réseau avec Azure Virtual Network Manager (préversion) - Portail Azure
description: Découvrez comment bloquer le trafic réseau à l’aide de règles de sécurité dans Azure Virtual Network Manager avec le portail Azure.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 20adb2a748f839d63122a1fc5904746a29af33ff
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131098064"
---
# <a name="how-to-block-network-traffic-with-azure-virtual-network-manager-preview---azure-portal"></a>Blocage du trafic réseau avec Azure Virtual Network Manager (préversion) - Portail Azure

Cet article explique comment créer une règle d’administration de la sécurité pour bloquer le trafic réseau entrant sur le port RDP 3389 que vous pouvez ajouter à une collection de règles. Pour plus d’informations, consultez [Règles d’administration de la sécurité](concept-security-admins.md).

> [!IMPORTANT]
> Azure Virtual Network Manager est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

Avant de commencer à configurer les règles d’administration de la sécurité, vérifiez que vous avez effectué les étapes suivantes :

* Vous comprenez chaque élément dans une [règle d’administration de la sécurité](concept-security-admins.md).
* Vous avez créé une [instance Azure Virtual Network Manager](create-virtual-network-manager-portal.md).

## <a name="create-a-securityadmin-configuration"></a>Créer une configuration SecurityAdmin

1. Sous *Paramètres*, sélectionnez **Configurations**, puis choisissez **+ Ajouter une configuration**.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/create-security-admin.png" alt-text="Capture d’écran de l’ajout d’une configuration de l’administration de la sécurité.":::

1. Sélectionnez **SecurityAdmin** dans le menu déroulant.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/security-admin-drop-down.png" alt-text="Capture d’écran du menu déroulant d’ajout d’une configuration.":::

1. Entrez un *nom* pour identifier cette configuration de sécurité.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/security-configuration-name.png" alt-text="Capture d’écran du champ de nom de la configuration de sécurité.":::

## <a name="add-a-rule-collection"></a>Ajouter une collection de règles

1. Sélectionnez **+Ajouter une collection de règles** pour créer votre jeu de règles.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/add-rule-collection-button.png" alt-text="Capture d’écran du bouton Ajouter une collection de règles.":::

1. Entrez un *nom* pour identifier cette collection de règles, puis sélectionnez les *groupes réseaux* auxquels vous souhaitez appliquer ce jeu de règles.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/rule-collection-target.png" alt-text="Capture d’écran du nom de la collection de règles et des groupes de réseaux cibles.":::

## <a name="add-a-security-rule"></a>Ajouter une règle de sécurité

1. Sélectionnez **+Ajouter une règle** dans la *page Ajouter une collection de règles*.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/add-rule-button.png" alt-text="Capture d’écran du bouton ajouter une règle.":::

1. Entrez ou sélectionnez les informations suivantes, puis sélectionnez **Ajouter** pour ajouter la règle à la collection de règles.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/add-rule.png" alt-text="Capture d’écran de la page Ajouter une règle.":::

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez le nom **Deny_RDP** pour le nom de la règle. |
    | Description | Entrez la description de la règle. |
    | Priorité* | Entrez une valeur comprise entre 0 et 99 pour déterminer la priorité de la règle. Plus la valeur est faible, plus la priorité est élevée. Entrez **1** pour cet exemple.|
    | Action* | Sélectionnez **Refuser** pour bloquer le trafic. Pour plus d’informations, consultez [Action](concept-security-admins.md#action).
    | Direction* | Sélectionnez **Entrant**, comme vous souhaitez refuser le trafic entrant avec cette règle. |
    | Protocole* | Sélectionnez le protocole **TCP**. HTTP et HTTPs sont des ports TCP. |
    | Type de source | Sélectionnez le type de source **adresse IP** ou **balises de service**. |
    | Adresses IP sources | Ce champ s’affiche lorsque vous sélectionnez le type de source *Adresse IP*. Entrez une adresse IPv4 ou IPv6 ou une plage à l’aide de la notation CIDR. Lors de la définition de plusieurs adresses ou blocs d’adresses séparés par une virgule. Laissez le champ vide pour cet exemple.|
    | Balise du service source | Ce champ s’affiche lorsque vous sélectionnez le type de source *Balise de service*. Sélectionnez la ou les balises de service pour les services que vous souhaitez spécifier comme source. Pour obtenir la liste des balises prises en charge, consultez [balises de service disponibles](../virtual-network/service-tags-overview.md#available-service-tags). |
    | Port source | Entrez un numéro de port unique ou une plage de ports, par exemple (1024-65535). Lorsque vous définissez plusieurs ports ou plages de ports, séparez-les par une virgule. Pour spécifier n’importe quel port, entrez *. Laissez le champ vide pour cet exemple.|
    | Type de destination | Sélectionnez le type de destination **Adresse IP** ou **Balises de service**. |
    | Adresses IP de destination | Ce champ s’affiche lorsque vous sélectionnez le type de destination *Adresse IP*. Entrez une adresse IPv4 ou IPv6 ou une plage à l’aide de la notation CIDR. Lors de la définition de plusieurs adresses ou blocs d’adresses séparés par une virgule. |
    | Identification de destination | Ce champ s’affiche lorsque vous sélectionnez le type de destination *Balise de service*. Sélectionnez la ou les balises de service pour les services que vous souhaitez spécifier comme destination. Pour obtenir la liste des balises prises en charge, consultez [balises de service disponibles](../virtual-network/service-tags-overview.md#available-service-tags). |
    | Port de destination | Entrez un numéro de port unique ou une plage de ports, par exemple (1024-65535). Lorsque vous définissez plusieurs ports ou plages de ports, séparez-les par une virgule. Pour spécifier n’importe quel port, entrez *. Entrez **3389** pour cet exemple. |

1. Répétez les étapes 1-3 pour ajouter d’autres règles à la collection de règles.

1. Une fois que vous êtes satisfait de toutes les règles que vous souhaitez créer, sélectionnez **Enregistrer** pour ajouter la collection de règles à la configuration de l’administrateur de sécurité.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/save-rule-collection.png" alt-text="Capture d’écran d’une règle dans une collection de règles.":::

1. Sélectionnez ensuite **Ajouter** pour créer la configuration.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/create-security-configuration.png" alt-text="Capture d’écran du bouton d’ajout pour la création d’une configuration de sécurité.":::

## <a name="deploy-the-security-admin-configuration"></a>Déployer la configuration de l’administration de la sécurité

Si vous venez de créer une nouvelle configuration d’administrateur de sécurité, veillez à déployer cette configuration pour l’appliquer aux réseaux virtuels du groupe réseau.

1. Sélectionnez **Déploiement** sous *Paramètres*, puis cliquez sur **Déployer une configuration**.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/deploy-configuration.png" alt-text="Capture d’écran du bouton Déployer une configuration.":::

1. Sélectionnez le type de configuration **SecurityAdmin** et la configuration que vous avez créée dans la dernière section. Choisissez ensuite la ou les régions dans lesquelles vous souhaitez déployer cette configuration, puis sélectionnez **Déployer**.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/deploy-security-configuration.png" alt-text="Capture d’écran montrant le déploiement d’une configuration de sécurité.":::

1. Sélectionnez **OK** pour confirmer que vous souhaitez remplacer la configuration existante et déployer la configuration de l’administration de la sécurité.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/confirm-security.png" alt-text="Capture d’écran du message de confirmation pour le déploiement d’une configuration de la sécurité.":::

## <a name="update-existing-security-admin-configuration"></a>Mettre à jour la configuration de l’administration de la sécurité existante

* Si la configuration de l’administration de la sécurité que vous mettez à jour est appliquée à un groupe réseau contenant des membres statiques, vous devez redéployer la configuration pour qu’elle prenne effet.
* Les configurations d’administration de la sécurité sont appliquées automatiquement aux membres dynamiques dans un groupe réseau.

## <a name="verify-security-admin-rules"></a>Vérifier les règles d’administration de la sécurité

Accédez aux paramètres de **réseau** pour une des machines virtuelles dans l’un des réseaux virtuels auxquels vous avez appliqué les règles d’administration de la sécurité. Si vous n’en avez pas, déployez une machine virtuelle de test dans l’un des réseaux virtuels. Vous voyez maintenant une nouvelle section sous les règles de groupe de sécurité réseau sur les règles de sécurité appliquées par Azure Virtual Network Manager.

:::image type="content" source="./media/how-to-block-network-traffic-portal/vm-security-rules.png" alt-text="Capture d’écran des règles d’administration de sécurité dans les paramètres réseau de la machine virtuelle." lightbox="./media/how-to-block-network-traffic-portal/vm-security-rules-expanded.png":::

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [Règles d’administration de la sécurité](concept-security-admins.md)
