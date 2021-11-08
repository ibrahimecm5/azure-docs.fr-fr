---
title: Sécurité des conteneurs avec Microsoft Defender pour le cloud
description: En savoir plus sur les fonctionnalités de sécurité des conteneurs de Microsoft Defender pour le cloud
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 11/02/2021
ms.author: memildin
ms.openlocfilehash: 947aa4c5506b01ee3d22cd4372a4e5a5f0ca4992
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131579271"
---
# <a name="container-security-in-microsoft-defender-for-cloud"></a>Sécurité des conteneurs dans Microsoft Defender pour le cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender pour le cloud est une solution cloud native dédiée à la sécurisation de vos conteneurs.

Defender pour le cloud peut protéger les types de ressources de conteneur suivants :

| Type de ressource | Protections offertes par Defender pour le cloud |
|:--------------------:|-----------|
| ![Kubernetes Service.](./media/container-security/icon-kubernetes-service-rec.png)<br>**Clusters Kubernetes** | Évaluation continue de vos clusters pour apporter une visibilité sur les erreurs de configuration, ainsi que des instructions qui vous aident à atténuer les menaces identifiées. Explorez plus en détail le [durcissement des environnements par le biais des recommandations de sécurité](#environment-hardening).<br><br>Protection contre les menaces pour les clusters et les nœuds Linux. Les alertes des activités suspectes sont fournies par [Microsoft Defender pour Kubernetes](defender-for-kubernetes-introduction.md). Ce plan défend vos clusters Kubernetes, qu’ils soient hébergés dans Azure Kubernetes Service (AKS), en local ou sur d’autres fournisseurs de cloud. clusters. <br>Explorez plus en détail la [protection au moment de l’exécution pour les clusters et les nœuds Kubernetes](#run-time-protection-for-kubernetes-nodes-and-clusters).|
| ![Hôte de conteneur.](./media/container-security/icon-container-host-rec.png)<br>**Hôtes de conteneur**<br>(Machines virtuelles exécutant Docker) | Évaluation continue de vos environnements Docker pour fournir une visibilité sur les erreurs de configuration, ainsi que des instructions qui vous aident à atténuer les menaces identifiées par l’option facultatif [Microsoft Defender pour les serveurs](defender-for-servers-introduction.md).<br>Explorez plus en détail le [durcissement des environnements par le biais des recommandations de sécurité](#environment-hardening).|
| ![Registre de conteneurs.](./media/container-security/icon-container-registry-rec.png)<br>**Registres ACR (Azure Container Registry)** | Outils de gestion et d’évaluation des vulnérabilités pour les images de vos registres ACR basés sur Azure Resource Manager avec l’option facultative[Microsoft Defender pour les registres de conteneurs](defender-for-container-registries-introduction.md).<br>Explorez plus en détail la [recherche de vulnérabilités dans vos images conteneur](#vulnerability-management---scanning-container-images). |
|||

Cet article explique comment utiliser Defender pour le cloud, ainsi que les outils facultatifs de protection renforcée pour les registres de conteneurs, les serveurs et Kubernetes afin d’améliorer, surveiller et maintenir la sécurité de vos conteneurs et de leurs applications.

Il explique de quelle façon Defender pour le cloud améliore les principaux aspects de la sécurité des conteneurs :

- [Gestion des vulnérabilités - analyse des images conteneur](#vulnerability-management---scanning-container-images)
- [Sécurisation renforcée de l’environnement](#environment-hardening)
- [Protection à l’exécution pour les nœuds et les clusters Kubernetes](#run-time-protection-for-kubernetes-nodes-and-clusters)

La capture d’écran suivante montre la page d’inventaire des ressources et les différents types de ressources de conteneur protégés par Defender pour le cloud.

:::image type="content" source="./media/container-security/inventory-container-resources.png" alt-text="Ressources liées aux conteneurs dans la page d’inventaire des ressources Defender pour le cloud" lightbox="./media/container-security/inventory-container-resources.png":::

## <a name="vulnerability-management---scanning-container-images"></a>Gestion des vulnérabilités : analyse des images conteneur

Pour surveiller les images dans vos registres de conteneurs Azure basés sur Azure Resource Manager, activez [Microsoft Defender pour les registres de conteneurs](defender-for-container-registries-introduction.md). Defender pour le cloud analyse les images extraites au cours des 30 derniers jours, envoyées à votre registre ou importées. Le scanner intégré est fourni par le fournisseur d’analyse des vulnérabilités leader du secteur, Qualys.

Quand des problèmes sont détectés (par Qualys ou Defender pour le cloud), vous êtes averti dans le [tableau de bord Protections des charges de travail](workload-protections-dashboard.md). Pour chaque vulnérabilité, Defender pour le cloud fournit des recommandations actionnables, accompagnées d’une classification de la gravité et de conseils sur la façon de corriger le problème. Pour plus d’informations sur les recommandations de Defender pour le cloud pour les conteneurs, consultez la [liste de référence des recommandations](recommendations-reference.md#recs-compute).

Defender pour le cloud filtre et classe les résultats du scanneur. Quand une image est saine, Defender pour le cloud la marque comme telle. Defender pour le cloud génère des recommandations de sécurité uniquement pour les images qui ont des problèmes à résoudre. En vous avertissant seulement en cas de problème, Defender pour le cloud réduit la possibilité d’alertes informatives indésirables.

## <a name="environment-hardening"></a>Durcissement des environnements

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Supervision continue de votre configuration Docker

Microsoft Defender pour le cloud identifie les conteneurs non managés qui sont hébergés sur des machines virtuelles IaaS Linux, ou d’autres machines Linux exécutant des conteneurs Docker. Defender pour le cloud évalue en continu les configurations de ces conteneurs. Il les compare ensuite au document de référence [Center for Internet Security (CIS) Docker Benchmark](https://www.cisecurity.org/benchmark/docker/).

Defender pour le cloud inclut la totalité des règles définies dans le CIS Docker Benchmark et vous envoie une alerte si vos conteneurs ne satisfont pas à tous les contrôles. Quand il détecte des configurations incorrectes, Defender pour le cloud génère des recommandations de sécurité. Accédez à la **page des recommandations** de Defender pour le cloud pour lire les suggestions et corriger les problèmes. Ces vérifications des règles du CIS Benchmark ne sont pas effectuées sur les instances managées par AKS ni sur les machines virtuelles managées par Databricks.

Pour plus d’informations sur les recommandations Defender pour le cloud qui peuvent s’afficher au sujet de cette fonctionnalité, consultez la [section relative au calcul](recommendations-reference.md#recs-compute) de la table de référence des recommandations.

Quand vous examinez les problèmes de sécurité d’une machine virtuelle, Defender pour le cloud fournit des informations supplémentaires à propos des conteneurs sur la machine. Il indique notamment la version de Docker et le nombre d’images exécutées sur la machine hôte. 

Pour surveiller les conteneurs non gérés hébergés sur des machines virtuelles IaaS Linux, activez l’option facultative [Microsoft Defender pour les serveurs](defender-for-servers-introduction.md).


### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Supervision continue de vos clusters Kubernetes
Defender pour le cloud s’utilise conjointement avec Azure Kubernetes Service (AKS), le service d’orchestration de conteneurs managés de Microsoft conçu pour le développement, le déploiement et la gestion d’applications conteneurisées.

AKS fournit des contrôles de sécurité et apporte une visibilité sur la posture de sécurité de vos clusters. Defender pour le cloud utilise ces fonctionnalités pour surveiller en permanence la configuration de vos clusters AKS et générer des recommandations de sécurité alignées sur les normes du secteur.

Voici un diagramme de haut niveau présentant l’interaction existante entre Microsoft Defender pour le cloud, Azure Kubernetes Service et Azure Policy :

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Architecture de haut niveau de l’interaction existante entre Microsoft Defender pour le cloud, Azure Kubernetes Service et Azure Policy" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::

Comme vous pouvez le constater, les éléments reçus et analysés par Defender pour le cloud sont les suivants :

- Journaux d’audit provenant du serveur d’API
- Événements de sécurité bruts provenant de l’agent Log Analytics

    > [!NOTE]
    > Actuellement, nous ne prenons pas en charge l’installation de l’agent Log Analytics sur les clusters Azure Kubernetes Service qui s’exécutent sur des groupes de machines virtuelles identiques.

- Informations de configuration du cluster provenant du cluster AKS
- Configuration des charges de travail provenant d’Azure Policy (par le biais du **module complémentaire Azure Policy pour Kubernetes**)

Pour plus d’informations sur les recommandations Defender pour le cloud qui peuvent s’afficher au sujet de cette fonctionnalité, consultez la [section relative au calcul](recommendations-reference.md#recs-compute) de la table de référence des recommandations.


###  <a name="workload-protection-best-practices-using-kubernetes-admission-control"></a>Meilleures pratiques pour la protection de charge de travail à l’aide du contrôle d’admission Kubernetes

Pour obtenir un ensemble de recommandations pour la protection des charges de travail de vos conteneurs Kubernetes, installez le **module complémentaire Azure Policy pour Kubernetes**. Vous pouvez également déployer automatiquement ce module complémentaire comme cela est expliqué dans [Activer l’approvisionnement automatique de l’agent et des extensions Log Analytics](enable-data-collection.md#auto-provision-mma). Lorsque le provisionnement automatique du module complémentaire est défini sur « activé », l’extension est activée par défaut dans tous les clusters existants et futurs (qui répondent à la configuration requise de l’installation du module complémentaire).

Comme expliqué dans [cette page Azure Policy pour page Kubernetes](../governance/policy/concepts/policy-for-kubernetes.md), le module complémentaire étend le webhook de contrôleur d’admission open source [Gatekeeper v3](https://github.com/open-policy-agent/gatekeeper)  pour  [Open Policy Agent](https://www.openpolicyagent.org/). Les contrôleurs d’admission Kubernetes sont des modules complémentaires qui appliquent la manière dont vos clusters sont utilisés. Le module complémentaire s’inscrit en tant que webhook pour le contrôle d’admission Kubernetes et permet d’appliquer une mise en œuvre et une protection à grande échelle sur vos clusters, de manière centralisée et cohérente. 

Avec le module complémentaire sur votre cluster AKS, chaque demande adressée au serveur d’API Kubernetes est analysée par rapport à l’ensemble prédéfini de bonnes pratiques, avant d’être conservée sur le cluster. Vous pouvez ensuite configurer l’**application** des meilleures pratiques et les imposer aux charges de travail futures. 

Par exemple, vous pouvez faire en sorte que des conteneurs privilégiés ne soient pas créés, et que toutes les demandes ultérieures soient bloquées.

Pour plus d’informations, consultez [Protéger vos charges de travail Kubernetes](kubernetes-workload-protections.md).


## <a name="run-time-protection-for-kubernetes-nodes-and-clusters"></a>Protection à l’exécution pour les nœuds et les clusters Kubernetes

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]



## <a name="next-steps"></a>Étapes suivantes

Dans cette vue d’ensemble, vous avez découvert des informations sur les principaux éléments de la sécurité des conteneurs dans Microsoft Defender pour le cloud. Pour des documents connexes, consultez :

- [Présentation de Microsoft Defender pour Kubernetes](defender-for-kubernetes-introduction.md)
- [Présentation de Microsoft Defender pour les registres de conteneurs](defender-for-container-registries-introduction.md)
