---
title: Guide pratique pour planifier et déployer des serveurs avec Azure Arc
description: Découvrez comment activer un grand nombre de machines sur des serveurs avec Azure Arc, afin de simplifier la configuration des fonctionnalités essentielles de sécurité, de gestion et de surveillance dans Azure.
ms.date: 08/27/2021
ms.topic: conceptual
ms.openlocfilehash: b32090caf8167874e61bbca0c5f3782557abd620
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132340284"
---
# <a name="plan-and-deploy-azure-arc-enabled-servers"></a>Planifier et déployer des serveurs avec Azure Arc

Le déploiement d’un service d’infrastructure informatique ou d’une application métier est un défi pour toute entreprise. Pour opérer la migration correctement et éviter les mauvaises surprises et les coûts imprévus, vous devez la planifier minutieusement afin de vous assurer que vous êtes aussi prêt que possible. La planification du déploiement à toute échelle de serveurs avec Azure Arc doit couvrir les critères de conception et de déploiement à remplir pour pouvoir réaliser correctement les tâches.

Pour que le déploiement se déroule sans problème, votre plan doit clairement établir les points suivants :

* Rôles et responsabilités.
* Inventaire des serveurs physiques ou des machines virtuelles pour vérifier qu’ils présentent la configuration requise du réseau et du système.
* Ensemble de compétences et formation requises pour opérer un déploiement réussi et une gestion continue.
* Critère d’acceptation et manière de suivre sa satisfaction.
* Outils ou méthodes à utiliser pour automatiser les déploiements.
* Identification des risques et des plans d’atténuation pour éviter les retards, interruptions, etc.
* Manière de procéder pour éviter une interruption pendant le déploiement.
* Chemin de réaffectation d’un éventuel problème majeur.

L’objectif de cet article est de vous aider à vérifier que vous êtes prêt à déployer correctement des serveurs avec Azure Arc sur plusieurs serveurs physiques ou machines virtuelles de production au sein de votre environnement.

Pour en savoir plus sur les recommandations de déploiement à l’échelle, vous pouvez également vous référer à cette vidéo.

> [!VIDEO https://www.youtube.com/embed/Cf1jUPOB_vs]

## <a name="prerequisites"></a>Prérequis

* Vos machines exécutent un [système d’exploitation pris en charge](agent-overview.md#supported-operating-systems) pour l’agent Connected Machine.
* Vos machines disposent d’une connectivité à partir de votre réseau local ou d’un autre environnement cloud aux ressources Azure, directement ou via un serveur proxy.
* Pour installer et configurer l’agent Connected Machine des serveurs avec Azure Arc, vous devez disposer d’un compte doté de privilèges élevés (administrateur ou racine) sur les machines.
* Pour intégrer des ordinateurs, vous devez être membre du rôle **Intégration de machine connectée à Azure**.
* Pour lire, modifier et supprimer un ordinateur, vous devez être membre du rôle **Administrateur des ressources Azure Connected Machine**.

## <a name="pilot"></a>Pilote

Avant de déployer sur toutes les machines de production, commencez par évaluer ce processus de déploiement avant de l’adopter largement dans votre environnement. Pour un pilote, identifiez un échantillonnage représentatif de machines qui ne sont pas critiques en lien avec la capacité de votre entreprise à mener son activité. Veillez à prévoir suffisamment de temps pour exécuter le pilote et évaluer son impact : nous recommandons un minimum de 30 jours.

Établissez un plan formel décrivant l’étendue et les détails du pilote. Voici un exemple de ce qu’un plan doit inclure pour vous aider à démarrer.

* Objectifs : facteurs métier et techniques qui ont conduit à la décision quant à la nécessité d’un pilote.
* Critères de sélection : critères utilisés pour sélectionner les aspects de la solution qui seront démontrés via un pilote.
* Étendue : étendue du pilote, notamment composants de la solution, planification prévue, durée du pilote et nombre de machines à cibler.
* Critères et métriques de réussite : critères de réussite et mesures spécifiques du pilote servant à déterminer le niveau de réussite.
* Plan de formation : plan relatif à la formation des ingénieurs système, administrateurs et autres intervenants qui découvrent Azure et ses services dans le cadre du pilote.
* Plan de transition : stratégie et critères utilisés pour guider la transition du pilote à la production.
* Restauration : procédures de restauration d’un pilote dans l’état d’avant le déploiement.
* Risques : risques identifiés pour la conduite du pilote et associés au déploiement en production.

## <a name="phase-1-build-a-foundation"></a>Phase 1 : créer une fondation

Au cours de cette phase, des administrateurs ou ingénieurs système activent les principales fonctionnalités de l’abonnement Azure de leur organisation afin d’établir une fondation avant d’activer vos machines pour la gestion par des serveurs avec Azure Arc et d’autres services Azure.

|Tâche |Detail |Duration |
|-----|-------|---------|
| [Créer un groupe de ressources](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | Créez un groupe de ressources dédié pour inclure uniquement les serveurs avec Azure Arc et centraliser la gestion et la surveillance de ces ressources. | 1 heure |
| Appliquer des [étiquettes](../../azure-resource-manager/management/tag-resources.md) pour faciliter l’organisation des machines | Évaluez et développez une [stratégie d’étiquetage](/azure/cloud-adoption-framework/decision-guides/resource-tagging/) alignée sur les services informatiques, susceptible de vous aider à réduire la complexité de la gestion de vos serveurs avec Azure Arc, ainsi qu’à simplifier la prise de décisions de gestion. | Un jour |
| Concevoir et déployer des [journaux Azure Monitor](../../azure-monitor/logs/data-platform-logs.md) | Évaluez les [considérations relatives à la conception et au déploiement](../../azure-monitor/logs/design-logs-deployment.md) afin de déterminer si votre organisation doit utiliser un espace de travail Log Analytics existant ou en implémenter un autre pour stocker les données de journal collectées à partir de serveurs et de machines hybrides<sup>1</sup>. | Un jour |
| [Élaborer un plan de gouvernance Azure Policy](../../governance/policy/overview.md) | Déterminez comment vous allez implémenter la gouvernance de serveurs et de machines hybrides au niveau de l’abonnement ou de l’étendue du groupe de ressources avec Azure Policy. | Un jour |
| Configurer un [contrôle d’accès en fonction du rôle](../../role-based-access-control/overview.md) (RBAC) | Élaborez un plan d’accès pour contrôler qui a accès à la gestion des serveurs avec Azure Arc, et la capacité de ces personnes à afficher leurs données à partir d’autres services et solutions Azure. | Un jour |
| Identifier les machines sur lesquelles l’agent de Log Analytics est déjà installé | Exécutez la requête de journal suivante dans [Log Analytics](../../azure-monitor/logs/log-analytics-overview.md) pour opérer la conversion de déploiements d’agents Log Analytics existants en agents gérés par extension :<br> Heartbeat <br> &#124; where TimeGenerated > ago(30d) <br> &#124; where ResourceType == "machines" and (ComputerEnvironment == "Non-Azure") <br> &#124; summarize by Computer, ResourceProvider, ResourceType, ComputerEnvironment | 1 heure |

<sup>1</sup> Lors de l’évaluation de la conception de votre espace de travail Log Analytics, il est important de tenir compte de l’intégration à Azure Automation pour la prise en charge de ses fonctionnalités Update Management et Suivi des modifications et inventaire, ainsi qu’à Microsoft Defender pour le cloud et Microsoft Sentinel. Si votre organisation dispose déjà d’un compte Automation et a activé ses fonctionnalités de gestion liées à un espace de travail Log Analytics, déterminez si vous pouvez centraliser et rationaliser les opérations de gestion, ainsi que réduire les coûts, en utilisant ces ressources existantes plutôt que de créer un compte, un espace de travail ou une autre ressources en double.

## <a name="phase-2-deploy-azure-arc-enabled-servers"></a>Phase 2 : planifier et déployer des serveurs avec Azure Arc

Ensuite, nous développons la fondation posée en phase 1 en préparant et en déployant l’agent Connected Machine des serveurs avec Azure Arc.

|Tâche |Detail |Duration |
|-----|-------|---------|
| Télécharger le script d’installation prédéfini | Révisez et personnalisez le script d’installation prédéfini pour le déploiement à grande échelle de l’agent Connected Machine afin de prendre en charge vos exigences de déploiement automatisé.<br><br> Exemples de ressources d’intégration à grande échelle :<br><br> <ul><li> [Script de déploiement de base à grande échelle](onboard-service-principal.md)</ul></li> <ul><li>[Intégration à grande échelle de machines virtuelles Windows Server VMware vSphere](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_win/_index.md)</ul></li> <ul><li>[Intégration à grande échelle de machines virtuelles Linux VMware vSphere](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_linux/_index.md)</ul></li> <ul><li>[Intégration à grande échelle d’instances AWS EC2 à l’aide d’Ansible](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/aws_scaled_ansible/_index.md)</ul></li> <ul><li>[Déploiement à grande échelle à l’aide d’un accès distant PowerShell](./onboard-powershell.md) (Windows uniquement)</ul></li>| Un ou plusieurs jours selon les exigences, les processus organisationnels (par exemple, gestion des mises en production et des changements) et la méthode d’automatisation utilisée. |
| [Créer un principal du service](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) |Créez un principal de service pour connecter des machines de manière non interactive à l’aide d’Azure PowerShell ou à partir du portail.| 1 heure |
| Déployer l’agent Connected Machine sur vos serveurs et machines cibles |Servez-vous de votre outil Automation pour déployer les scripts sur vos serveurs et les connecter à Azure.| Un ou plusieurs jours selon votre plan de mise en production et que vous opérez un déploiement échelonné. |

## <a name="phase-3-manage-and-operate"></a>Phase 3 : gérer et exploiter

Au cours de la phase 3, des administrateurs ou ingénieurs système activent l’automatisation de tâches manuelles pour gérer et exploiter l’agent Connected Machine et la machine pendant leur cycle de vie.

|Tâche |Detail |Duration |
|-----|-------|---------|
|Créer une alerte Resource Health |Si un serveur cesse d’envoyer des pulsations à Azure pendant plus de 15 minutes, cela peut signifier qu’il est hors connexion, que la connexion réseau a été bloquée ou que l’agent n’est pas en cours d’exécution. Élaborez un plan décrivant la manière de répondre à ces incidents et d’investiguer à leur sujet, et utilisez des [alertes Resource Health](../..//service-health/resource-health-alert-monitor-guide.md) pour être informé de leur démarrage.<br><br> Spécifiez les aspects suivants lors de la configuration de l’alerte :<br> **Type de ressource** = **Serveurs avec Azure Arc**<br> **État actuel de la ressource** = **Indisponible**<br> **État précédent de la ressource** = **Disponible** | 1 heure |
|Créer une alerte Azure Advisor | Pour bénéficier d’une expérience optimale et des correctifs de sécurité et de bogues les plus récents, nous vous recommandons de tenir à jour l’agent des serveurs avec Azure Arc. Les agents obsolètes sont identifiés avec une [alerte Azure Advisor](../../advisor/advisor-alerts-portal.md).<br><br> Spécifiez les aspects suivants lors de la configuration de l’alerte :<br> **Type de recommandation** = **Opérer une mise à niveau vers la dernière version d’Azure Connected Machine Agent** | 1 heure |
|[Attribuer des stratégies Azure](../../governance/policy/assign-policy-portal.md) à l’étendue de votre abonnement ou groupe de ressources |Attribuez la **stratégie** [Activer Azure Monitor pour machines virtuelles](../../azure-monitor/vm/vminsights-enable-policy.md) (et d’autres qui répondent à vos besoins) à l’étendue de l’abonnement ou du groupe de ressources. Azure Policy vous permet d’attribuer des définitions de stratégie qui installent les agents nécessaires à VM Insights pour votre environnement.| Variable |
|[Activer Update Management pour vos serveurs avec Azure Arc](../../automation/update-management/enable-from-automation-account.md) |Configurez Update Management dans Azure Automation afin de gérer les mises à jour des systèmes d’exploitation de vos machines virtuelles Windows et Linux inscrites auprès de serveurs avec Azure Arc. | 15 minutes |

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la résolution des problèmes, consultez le [guide Résoudre les problèmes de l’agent Connected Machine](troubleshoot-agent-onboard.md).

* Découvrez comment simplifier le déploiement avec d’autres services Azure tels que [State Configuration](../../automation/automation-dsc-overview.md) d’Azure Automation et d’autres [extensions de machine virtuelle Azure](manage-vm-extensions.md) prises en charge.
