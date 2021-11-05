---
title: 'Microsoft Defender pour Resource Manager : avantages et fonctionnalités'
description: Découvrir les avantages et les fonctionnalités de Microsoft Defender pour Resource Manager
author: memildin
ms.author: memildin
ms.date: 09/05/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.custom: ignite-fall-2021
ms.openlocfilehash: b08ffb4e19a74f04ef597c03881f2eb8246826bb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084271"
---
# <a name="introduction-to-microsoft-defender-for-resource-manager"></a>Présentation de Microsoft Defender pour Resource Manager

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[Azure Resource Manager](../azure-resource-manager/management/overview.md) est le service de déploiement et de gestion d’Azure. Il fournit une couche de gestion qui vous permet de créer, de mettre à jour et de supprimer des ressources dans votre compte Azure. Vous utilisez des fonctionnalités de gestion, telles que le contrôle d’accès, les verrous et les étiquettes, pour sécuriser et organiser vos ressources après le déploiement.

La couche de gestion cloud est un service essentiel, connecté à toutes vos ressources cloud. De ce fait, il est également une cible potentielle pour les attaquants. Par conséquent, nous recommandons aux équipes des opérations de sécurité de surveiller attentivement la couche de gestion des ressources. 

Microsoft Defender pour Resource Manager supervise automatiquement les opérations de gestion des ressources dans votre organisation, qu’elles soient effectuées via le portail Azure, les API REST Azure, Azure CLI ou d’autres clients programmatiques Azure. Defender pour le cloud exécute une analyse de sécurité avancée pour détecter les menaces et vous avertit en cas d’activité suspecte.

>[!NOTE]
> Certaines de ces analyses sont alimentées par [Microsoft Defender pour les applications cloud (anciennement Microsoft Cloud App Security)](/cloud-app-security/what-is-cloud-app-security). Pour tirer parti de ces analyses, vous devez activer une licence Cloud App Security. Si vous avez une licence Cloud App Security, ces alertes sont activées par défaut. Pour désactiver les alertes :
>
> 1. Dans le menu de Defender pour le cloud, sélectionnez **Paramètres de l’environnement**.
> 1. Sélectionnez l’abonnement que vous souhaitez modifier.
> 1. Sélectionnez **Intégrations**.
> 1. Désactiver **Autoriser Microsoft Defender pour le cloud à accéder à mes données**, puis sélectionnez **Enregistrer**.


## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :|Disponibilité générale|
|Prix :|**Microsoft Defender pour Resource Manager** est facturé comme indiqué sur [la page des tarifs](https://azure.microsoft.com/pricing/details/security-center/).|
|Clouds :|:::image type="icon" source="./media/icons/yes-icon.png"::: Clouds commerciaux<br>:::image type="icon" source="./media/icons/yes-icon.png":::Azure Government<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure China 21Vianet|
|||

## <a name="what-are-the-benefits-of-microsoft-defender-for-resource-manager"></a>Quels sont les avantages de Microsoft Defender pour Resource Manager ?

Microsoft Defender pour Resource Manager protège de certains problèmes, notamment :

- **Les opérations de gestion des ressources suspectes**, comme les opérations à partir d’adresses IP malveillantes, la désactivation du logiciel anti-programme malveillant et les scripts suspects s’exécutant dans des extensions de machine virtuelle
- **L’utilisation de kits de ressources d’exploitation**, comme Microburst ou PowerZure
- **Le mouvement latéral**, depuis la couche de gestion Azure vers le plan de données des ressources Azure

:::image type="content" source="media/defender-for-resource-manager-introduction/consistent-management-layer-with-defender.png" alt-text="Diagramme de présentation d’Azure Resource Manager.":::

La liste complète des alertes fournies par Microsoft Defender pour Resource Manager se trouve dans la [page de référence des alertes](alerts-reference.md#alerts-resourcemanager).


 ## <a name="how-to-investigate-alerts-from-microsoft-defender-for-resource-manager"></a>Comment examiner les alertes de Microsoft Defender pour Resource Manager

Les alertes de sécurité de Microsoft Defender pour Resource Manager sont basées sur les menaces détectées par la supervision des opérations Azure Resource Manager. Defender pour le cloud utilise les sources de journaux internes d’Azure Resource Manager ainsi que le journal d’activité Azure, un journal de plateforme dans Azure qui fournit des insights sur les événements de niveau abonnement.

Découvrez-en plus sur le [Journal d’activité Azure](../azure-monitor/essentials/activity-log.md).

Pour examiner les alertes de sécurité de Microsoft Defender pour Resource Manager :

1. Ouvrez le journal d’activité Azure.

    :::image type="content" source="media/defender-for-resource-manager-introduction/opening-azure-activity-log.png" alt-text="Comment ouvrir le journal d’activité Azure.":::

1. Filtrez les événements sur :
    - L’abonnement mentionné dans l’alerte
    - La plage de temps de l’activité détectée
    - Le compte d’utilisateur associé (le cas échéant)

1. Recherchez des activités suspectes.

> [!TIP]
> Pour une meilleure expérience d’investigation plus complète, envoyez en streaming vos journaux d’activité Azure à Microsoft Sentinel, comme décrit dans [Connecter des données à partir du journal d’activité Azure](../sentinel/data-connectors-reference.md#azure-activity).



## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert Microsoft Defender pour Resource Manager. 

> [!div class="nextstepaction"]
> [Activer les protections renforcées](enable-enhanced-security.md)

Pour des informations connexes, consultez l’article suivant : 

- Des alertes de sécurité peuvent être générées par Defender pour le cloud ou reçues par Defender pour le cloud de différents produits de sécurité. Pour exporter toutes ces alertes vers Microsoft Sentinel, un système SIEM tiers ou tout autre outil externe, suivez les instructions indiquées dans [Exportation d’alertes vers un système SIEM](continuous-export.md).
