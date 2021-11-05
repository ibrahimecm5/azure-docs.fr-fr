---
title: Intégrer des solutions de sécurité dans Microsoft Defender pour le cloud | Microsoft Docs
description: Découvrez comment Microsoft Defender pour le cloud s’intègre avec les partenaires pour améliorer la sécurité globale de vos ressources Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: b5b6869cf1d61f89ce9ac072411b0786da552a46
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097094"
---
# <a name="integrate-security-solutions-in-microsoft-defender-for-cloud"></a>Intégrer des solutions de sécurité dans Microsoft Defender pour le cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Ce document vous aide à gérer les solutions de sécurité déjà connectées à Microsoft Defender pour le cloud et à en ajouter de nouvelles.

## <a name="integrated-azure-security-solutions"></a>Solutions de sécurité Azure intégrées
Defender pour le cloud simplifie l’activation des solutions de sécurité intégrées dans Azure. Voici les avantages :

- **Déploiement simplifié** : Defender pour le cloud permet un approvisionnement rationalisé des solutions de partenaire intégrées. Pour les solutions telles que les logiciels anti-programme malveillant et l’évaluation des vulnérabilités, Defender pour le cloud peut approvisionner l’agent sur vos machines virtuelles. Pour les appliances de pare-feu, Defender pour le cloud peut prendre en charge une grande partie de la configuration réseau requise.
- **Détections intégrées** : les événements de sécurité des solutions de partenaire sont automatiquement collectés, agrégés et affichés dans le cadre des alertes et des incidents de Defender pour le cloud. Ces événements sont également fusionnés avec les détections d’autres sources pour fournir des fonctions de détection de menaces avancées.
- **Gestion et surveillance unifiées de l’intégrité** : Les clients peuvent utiliser des événements d’intégrité intégrés pour surveiller facilement les solutions des partenaires. La gestion de base offre un accès facile à la configuration avancée avec la solution de partenaire.

Les solutions de sécurité intégrées incluent actuellement l’évaluation des vulnérabilités par [Qualys](https://www.qualys.com/public-cloud/#azure), [Rapid7](https://www.rapid7.com/products/insightvm/) et [Microsoft Azure Web Application Firewall sur Azure Application Gateway](../web-application-firewall/ag/ag-overview.md).

> [!NOTE]
> Defender pour le cloud n’installe pas l’agent Log Analytics sur les appliances virtuelles de partenaire, car la plupart des fournisseurs de sécurité n’autorisent pas l’exécution d’agents externes sur leurs appliances.

Pour en savoir plus sur l’intégration des outils d’analyse des vulnérabilités de Qualys, y compris un analyseur intégré disponible pour les clients qui ont activé Azure Defender pour les serveurs, consultez [Analyseur de vulnérabilités Qualys intégré à Azure Defender pour le cloud pour les machines hybrides et Azure](deploy-vulnerability-assessment-vm.md).

Defender pour le cloud offre également une analyse de vulnérabilité pour les éléments suivants :

* Bases de données SQL : consultez [Explorer les rapports d’évaluation des vulnérabilités dans le tableau de bord de l’évaluation des vulnérabilités](defender-for-sql-on-machines-vulnerability-assessment.md#explore-vulnerability-assessment-reports).
* Images Azure Container Registry – Consultez [Analyse des vulnérabilités dans les images avec Microsoft Defender pour les registres de conteneurs](defender-for-container-registries-usage.md)

## <a name="how-security-solutions-are-integrated"></a>Comment sont intégrées les solutions de sécurité
Les solutions de sécurité Azure déployées à partir de Defender pour le cloud sont automatiquement connectées. Vous pouvez également connecter d’autres sources de données de sécurité, notamment des ordinateurs s’exécutant en local ou dans d’autres clouds.

:::image type="content" source="./media/partner-integration/security-solutions-page.png" alt-text="Intégration de solutions de partenaires." lightbox="./media/partner-integration/security-solutions-page.png":::

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Gérer des solutions de sécurité Azure intégrées et d’autres sources de données

1. Dans le [portail Azure](https://azure.microsoft.com/features/azure-portal/), ouvrez **Defender pour le cloud**.

1. Dans le menu de Defender pour le cloud, sélectionnez **Sécurités de sécurité**.

Sur la page **Solutions de sécurité**, vous pouvez voir l’intégrité des solutions de sécurité Azure intégrées et exécuter des tâches de gestion de base.

### <a name="connected-solutions"></a>Solutions connectées

La section **Solutions connectées** inclut les solutions de sécurité qui sont connectées à Defender pour le cloud. Elle indique également l’état d’intégrité de chaque solution.  

![Solutions connectées.](./media/partner-integration/connected-solutions.png)

L’état d’une solution de partenaire peut être :

* **Sain** (vert) : aucun problème d’intégrité.
* **Non sain** (rouge) : problème d’intégrité nécessitant une action immédiate.
* **Arrêté** (orange) : état d’intégrité non signalé par la solution.
* **Non signalé** (gris) : la solution n’a encore rien signalé et aucune donnée d’intégrité n’est disponible. L’état d’une solution peut être non signalé si celle-ci a été récemment connectée et qu’elle est toujours en cours de déploiement.

> [!NOTE]
> Si les données sur l’état d’intégrité ne sont pas disponibles, Defender pour le cloud affiche la date et l’heure du dernier événement reçu pour indiquer si la solution signale ou non des données. Si aucune donnée d’intégrité n’est disponible et qu’aucune alerte n’a été reçue au cours des 14 derniers jours, Defender pour le cloud indique que la solution est non saine ou qu’elle ne signale pas de données.
>
>

Sélectionnez **AFFICHAGE** pour obtenir des informations et des options supplémentaires, par exemple :

   - **Console de la solution** : ouvre l’expérience de gestion pour cette solution.
   - **Associer la machine virtuelle**. : ouvre la page Associer les applications. Il vous permet de connecter des ressources à la solution de partenaire.
   - **Supprimer la solution**
   - **Configurer**

   ![Détail de la solution de partenaire.](./media/partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>Solutions découvertes

Defender pour le cloud découvre automatiquement les solutions exécutées dans Azure mais qui ne sont pas connectées à Defender pour le cloud. Elles sont ensuite affichées dans la section **Solutions découvertes**. Ces solutions incluent les solutions Azure, telles qu’[Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md), et les solutions partenaires.

> [!NOTE]
> Activez les **protections avancées** au niveau de l’abonnement pour la fonctionnalité des Solutions découvertes. Pour en savoir plus, consultez [Démarrage rapide : Activer les fonctionnalités de sécurité améliorées](enable-enhanced-security.md).

Sélectionnez **CONNECTER** sous une solution pour l’intégrer avec Defender pour le cloud et être notifié des alertes de sécurité.

### <a name="add-data-sources"></a>Ajouter des sources de données

La section **Ajouter des sources de données** comprend d’autres sources de données disponibles qui peuvent être connectées. Pour obtenir des instructions sur l’ajout de données à partir d’une de ces sources, cliquez sur **AJOUTER**.

![Sources de données.](./media/partner-integration/add-data-sources.png)



## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à intégrer des solutions de partenaires dans Defender pour le cloud. Pour savoir comment configurer une intégration avec Microsoft Sentinel ou toute autre solution SIEM, consultez [Exporter en continu des données Defender pour le cloud](continuous-export.md).
