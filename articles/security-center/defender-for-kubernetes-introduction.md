---
title: 'Microsoft Defender pour Kubernetes : avantages et fonctionnalités'
description: Découvrez les avantages et les fonctionnalités de Microsoft Defender pour Kubernetes.
author: memildin
ms.author: memildin
ms.date: 07/20/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8de5687d645dce485514957d7be490bbd502b85a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131055879"
---
# <a name="introduction-to-microsoft-defender-for-kubernetes"></a>Présentation de Microsoft Defender pour Kubernetes

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender pour Kubernetes est le plan de sécurité renforcée qui fournit des protections pour vos clusters Kubernetes partout où ils sont exécutés. 

Defender pour le cloud peut défendre les clusters dans :

- **Azure Kubernetes Service (AKS)** , service managé de Microsoft conçu pour le développement, le déploiement et la gestion d’applications conteneurisées

- **Environnements locaux et multicloud** - Utilisation d’une [extension pour Kubernetes avec Azure Arc](defender-for-kubernetes-azure-arc.md)

Microsoft Defender pour le cloud et AKS constituent une offre de sécurité Kubernetes native cloud, avec un durcissement de l’environnement, une protection de charge de travail et une protection au moment de l’exécution, comme indiqué dans [Sécurité des conteneurs dans Defender pour le cloud](container-security.md).

La détection des menaces au niveau de l’hôte sur vos nœuds AKS Linux est disponible si vous activez [Microsoft Defender pour les serveurs](defender-for-servers-introduction.md) et son agent Log Analytics. En revanche, si votre cluster est déployé sur un groupe de machines virtuelles identiques Azure Kubernetes Service, l’agent Log Analytics n’est pas pris en charge pour le moment.



## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :|Disponibilité générale|
|Prix :|**Microsoft Defender pour Kubernetes** est facturé comme indiqué dans la [page des tarifs](https://azure.microsoft.com/pricing/details/security-center/)|
|Rôles et autorisations obligatoires :|L’**administrateur de sécurité** peut ignorer les alertes.<br>Le **Lecteur de sécurité** peut afficher les résultats.|
|Clouds :|:::image type="icon" source="./media/icons/yes-icon.png"::: Clouds commerciaux<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Nationaux/Souverains (Azure Government, Azure China 21Vianet)|
|||

## <a name="what-are-the-benefits-of-microsoft-defender-for-kubernetes"></a>Quels sont les avantages de Microsoft Defender pour Kubernetes ?

Microsoft Defender pour Kubernetes fournit une **protection contre les menaces au niveau du cluster** en supervisant les journaux de vos clusters.

Les tableaux de bord Kubernetes exposés, la création de rôles avec des privilèges élevés et la création de montages sensibles sont des exemples d’événements de sécurité que Microsoft Defender pour Kubernetes supervise. Pour obtenir la liste complète des alertes au niveau du cluster, consultez la [Table de référence des alertes](alerts-reference.md#alerts-k8scluster).

> [!TIP]
> Vous pouvez simuler des alertes de conteneur en suivant les instructions données dans [ce billet de blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).

De plus, notre équipe mondiale d’experts en sécurité surveille en permanence l’évolution des menaces. Ils ajoutent des alertes et des vulnérabilités propres aux conteneurs au fur et à mesure de leur découverte.

>[!NOTE]
> Defender pour le cloud génère des alertes de sécurité pour les actions et les déploiements qui se produisent après l’activation du plan Defender pour Kubernetes sur votre abonnement.




## <a name="faq---microsoft-defender-for-kubernetes"></a>FAQ - Microsoft Defender pour Kubernetes

- [Est-ce que je peux quand même bénéficier des protections de cluster sans l’agent Log Analytics ?](#can-i-still-get-cluster-protections-without-the-log-analytics-agent)
- [Est-ce qu’AKS m’autorise à installer des extensions de machine virtuelle personnalisées sur mes nœuds AKS ?](#does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes)
- [Si mon cluster exécute déjà un agent Azure Monitor pour conteneurs, ai-je besoin de l’agent Log Analytics ?](#if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too)
- [Microsoft Defender pour Kubernetes prend-il en charge AKS avec des nœuds de groupe de machines virtuelles identiques ?](#does-microsoft-defender-for-kubernetes-support-aks-with-virtual-machine-scale-set-nodes)

### <a name="can-i-still-get-cluster-protections-without-the-log-analytics-agent"></a>Est-ce que je peux quand même bénéficier des protections de cluster sans l’agent Log Analytics ?

Le plan **Microsoft Defender pour Kubernetes** fournit des protections au niveau du cluster. Si vous déployez également l’agent Log Analytics de **Microsoft Defender pour les serveurs**, vous bénéficiez de la protection contre les menaces sur vos nœuds compris dans ce plan. Pour en savoir plus, consultez [Présentation de Microsoft Defender pour les serveurs](defender-for-servers-introduction.md).

Nous vous recommandons de déployer les deux pour bénéficier de la protection la plus complète possible.

Si vous choisissez de ne pas installer l’agent sur vos hôtes, vous ne bénéficiez que d’une partie de la protection contre les menaces et des alertes de sécurité. Vous recevrez toujours les alertes liées à l’analyse réseau et aux communications avec des serveurs malveillants.

### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>Est-ce qu’AKS m’autorise à installer des extensions de machine virtuelle personnalisées sur mes nœuds AKS ?
Pour que Defender pour le cloud supervise vos nœuds AKS, ils doivent exécuter l’agent Log Analytics.

AKS est un service managé et, l’agent Log Analytics étant une extension managée par Microsoft, il est également pris en charge sur les clusters AKS.

### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>Si mon cluster exécute déjà un agent Azure Monitor pour conteneurs, ai-je besoin de l’agent Log Analytics ?
Pour que Defender pour le cloud supervise vos nœuds, ils doivent exécuter l’agent Log Analytics.

Si vos clusters exécutent déjà l’agent Azure Monitor pour conteneurs, vous pouvez également installer l’agent Log Analytics, et les deux agents peuvent fonctionner conjointement sans aucun problème.

[En savoir plus sur l’agent Azure Monitor pour conteneurs](../azure-monitor/containers/container-insights-manage-agent.md).


### <a name="does-microsoft-defender-for-kubernetes-support-aks-with-virtual-machine-scale-set-nodes"></a>Microsoft Defender pour Kubernetes prend-il en charge AKS avec des nœuds de groupe de machines virtuelles identiques ?
Si votre cluster est déployé sur un groupe de machines virtuelles identiques Azure Kubernetes Service, l’agent Log Analytics n’est pas pris en charge pour le moment.



## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert la protection Kubernetes de Defender pour le cloud, notamment Microsoft Defender pour Kubernetes. 

> [!div class="nextstepaction"]
> [Activer les protections renforcées](enable-enhanced-security.md)

Pour des informations connexes, consultez les articles suivants : 

- [Diffuser des alertes vers un système SIEM, SOAR ou une solution de gestion des services informatiques](export-to-siem.md)
- [Table de référence des alertes](alerts-reference.md)
