---
title: Planification de Defender pour le cloud et guide des opérations
description: Ce document vous aide à planifier l’utilisation de Defender pour le cloud avant son adoption et présente des considérations concernant les opérations quotidiennes.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 3f4f174594e7aa10369ed1d822b51df6c22e33b6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131445063"
---
# <a name="planning-and-operations-guide"></a>Guide Planification et opérations

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Ce guide s’adresse aux professionnels des technologies de l’information, aux architectes informatiques, aux analystes de la sécurité des informations et aux administrateurs de cloud qui prévoient d’utiliser Defender pour le cloud.


## <a name="planning-guide"></a>Guide de planification
Ce guide couvre les tâches que vous pouvez effectuer pour optimiser votre utilisation de Defender pour le cloud en fonction des critères de sécurité et du modèle de gestion de cloud de votre entreprise. Pour tirer pleinement parti de Defender pour le cloud, il est important de comprendre comment les différents utilisateurs ou équipes de votre entreprise utilisent ce service afin de répondre aux besoins de sécurisation du développement et des opérations, de supervision, de gouvernance et de réponse aux incidents. Voici les points clés à prendre en compte lors de la planification de l’utilisation de Defender pour le cloud :

* Rôles de sécurité et contrôles d’accès
* Stratégies et recommandations de sécurité
* Collecte et stockage des données
* Intégration de ressources non Azure
* Surveillance continue de la sécurité
* Réponse aux incidents

Dans la section suivante, vous allez apprendre à planifier chacun de ces aspects et à appliquer ces recommandations en fonction de vos besoins.


> [!NOTE]
> Consultez les [Questions fréquentes (FAQ) sur Defender pour le cloud](faq-general.yml) pour obtenir des réponses utiles lors des phases de conception et de planification.

## <a name="security-roles-and-access-controls"></a>Rôles de sécurité et contrôles d’accès
Selon la taille et la structure de votre entreprise, plusieurs personnes et équipes peuvent utiliser Defender pour le cloud pour différentes tâches liées à la sécurité. Le diagramme suivant vous présente un exemple de personnes fictives avec leurs responsabilités et rôles respectifs en termes de sécurité :

![Rôles.](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01-new.png)

Defender pour le cloud permet à ces personnes d’assumer ces différentes responsabilités. Par exemple :

**Jeff (propriétaire de la charge de travail)**

* Gérer une charge de travail de cloud et ses ressources connexes
* Chargé de l’implémentation et de la maintenance des protections conformément à la stratégie de sécurité de l’entreprise

**Ellen (directrice informatique/responsable de la sécurité des systèmes d’information)**

* Responsable de tous les aspects liés à la sécurité de l’entreprise
* Souhaite comprendre la position de l’entreprise en matière de sécurité à travers les charges de travail de cloud
* Doit être informée des attaques et des risques majeurs

**David (sécurité informatique)**

* Définit les stratégies de sécurité de l’entreprise afin de garantir que les protections appropriées sont en place
* Surveille la conformité aux stratégies
* Génère les rapports pour la direction ou les auditeurs

**Judy (opérations de sécurité)**

* Surveille et répond aux alertes de sécurité 24h/24, 7j/7.
* Rend des comptes au propriétaire de la charge de travail de cloud ou à l’analyste de la sécurité informatique

**Sam (analyste de sécurité)**

* Analyse les attaques
* Collabore avec le propriétaire de la charge de travail de cloud pour mettre en œuvre les solutions

Defender pour le cloud utilise le [contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md), qui fournit des [rôles intégrés](../role-based-access-control/built-in-roles.md) susceptibles d’être affectés à des utilisateurs, des groupes et des services dans Azure. Quand un utilisateur ouvre Defender pour le cloud, il voit uniquement les informations relatives aux ressources auxquelles il a accès. Autrement dit, l’utilisateur se voit attribuer le rôle de propriétaire, collaborateur ou lecteur, pour l’abonnement ou le groupe de ressources auquel appartiennent les ressources. En plus de ces rôles, il existe deux rôles spécifiques de Defender pour le cloud :

- **Lecteur de sécurité** : un utilisateur ayant ce rôle est en mesure d’afficher uniquement les configurations de Defender pour le cloud, notamment les recommandations, les alertes, la stratégie et l’intégrité, mais il ne peut pas apporter de modifications.
- **Administrateur de la sécurité** : il a les mêmes droits que le lecteur de sécurité, mais il peut également mettre à jour la stratégie de sécurité, et rejeter des alertes et des recommandations.

Les rôles de Defender pour le cloud décrits ci-dessus n’ont pas accès à d’autres zones de service d’Azure comme Stockage, Web et Mobile, ou Internet des objets.

En considérant les personnes décrites dans le diagramme précédent, voici le RBAC Azure qui serait nécessaire :

**Jeff (propriétaire de la charge de travail)**

* Propriétaire/contributeur du groupe de ressources

**Ellen (directrice informatique/responsable de la sécurité des systèmes d’information)**

* Propriétaire/contributeur de l’abonnement ou Administrateur de la sécurité

**David (sécurité informatique)**

* Propriétaire/contributeur de l’abonnement ou Administrateur de la sécurité

**Judy (opérations de sécurité)**

* Lecteur de l’abonnement ou lecteur de sécurité, autorisé à consulter les alertes
* Propriétaire/contributeur de l’abonnement ou Administrateur de la sécurité, requis pour ignorer les alertes

**Sam (analyste de sécurité)**

* Lecteur de l’abonnement, autorisé à consulter les alertes
* Propriétaire/contributeur de l’abonnement, requis pour ignorer les alertes
* Accès à l’espace de travail potentiellement requis

Autres informations importantes à prendre en compte :

* Seuls les collaborateurs/propriétaires d’abonnement et les administrateurs de sécurité peuvent modifier une stratégie de sécurité.
* Seuls les propriétaires et collaborateurs d’abonnement et de groupe de ressources peuvent appliquer les recommandations de sécurité pour une ressource.

Quand vous planifiez le contrôle d’accès à l’aide du contrôle RBAC d’Azure pour Defender pour le cloud, veillez à identifier les personnes de votre entreprise qui vont utiliser Defender pour le cloud. ainsi que les types de tâches qu’ils effectueront, puis configurez RBAC Azure en conséquence.

> [!NOTE]
> Nous vous recommandons d’attribuer le rôle le moins permissif permettant aux utilisateurs d’effectuer leurs tâches. Par exemple, les utilisateurs qui n’ont besoin que de consulter des informations sur l’état de sécurité des ressources sans effectuer aucune action, telles que l’application des recommandations ou la modification des stratégies, doivent recevoir le rôle de lecteur.
>
>

## <a name="security-policies-and-recommendations"></a>Stratégies et recommandations de sécurité
Une stratégie de sécurité définit la configuration souhaitée de vos charges de travail, tout en garantissant leur conformité aux exigences de sécurité réglementaires. Dans Defender pour le cloud, vous pouvez définir les stratégies de vos abonnements Azure, qui peuvent être adaptées au type de charge de travail ou à la sensibilité des données.

Les stratégies Defender pour le cloud contiennent les composants suivants :
- [Collecte des données](enable-data-collection.md) : paramètres de l’approvisionnement d’agent et de la collecte de données.
- [Stratégie de sécurité](tutorial-security-policy.md) : [Azure Policy](../governance/policy/overview.md) qui détermine les contrôles supervisés et recommandés par Defender pour le cloud. Vous pouvez aussi utiliser une stratégie Azure pour créer des définitions, définir des stratégies supplémentaires et assigner des stratégies à des groupes d’administration.
- [Notifications par e-mail](configure-email-notifications.md) : paramètres des contacts de sécurité et des notifications.
- [Niveau tarifaire](enhanced-security-features-overview.md) : avec ou sans les fonctionnalités de sécurité améliorées de Defender pour le cloud, ce qui détermine quelles fonctionnalités de Defender pour le cloud sont disponibles pour les ressources dans l’étendue (pouvant être spécifiées pour les abonnements et les espaces de travail ou pour les groupes de ressources à l’aide de l’API).

> [!NOTE]
> Spécifier un contact de sécurité permettra à Azure de joindre la personne adéquate de votre organisation si un incident survient. Consultez [Fournir les détails du contact de sécurité dans Defender pour le cloud](configure-email-notifications.md) pour plus d’informations sur l’activation de cette recommandation.

### <a name="security-policies-definitions-and-recommendations"></a>Définitions et recommandations de stratégies de sécurité
Defender pour le cloud crée automatiquement une stratégie de sécurité par défaut, pour chacun de vos abonnements Azure. Vous pouvez modifier la stratégie dans Defender pour le cloud ou utiliser Azure Policy pour créer des définitions, définir des stratégies supplémentaires et affecter des stratégies à des groupes d’administration (qui peuvent représenter toute l’organisation, une des unités commerciales, etc.) et superviser la conformité de ces stratégies à ces étendues.

Avant de configurer des stratégies de sécurité, examinez chacune des [recommandations de sécurité](review-security-recommendations.md)et déterminer si ces stratégies conviennent à vos différents abonnements et groupes de ressources. Il est également important de comprendre l’action à mener concernant les recommandations de sécurité et de connaître la personne de votre organisation chargée de surveiller les nouvelles recommandations et de prendre les mesures nécessaires.

## <a name="data-collection-and-storage"></a>Collecte et stockage des données
Defender pour le cloud utilise l’agent Log Analytics (le même agent que celui utilisé par le service Azure Monitor) pour collecter les données relatives à la sécurité sur vos machines virtuelles. [Les données collectées](enable-data-collection.md) à partir de cet agent seront stockées dans vos espaces de travail Log Analytics.

### <a name="agent"></a>Agent

Une fois l’approvisionnement automatique activé dans la stratégie de sécurité, l’agent Log Analytics (pour [Windows](../azure-monitor/agents/agent-windows.md) ou [Linux](../azure-monitor/vm/monitor-virtual-machine.md)) est installé sur toutes les machines virtuelles Azure prises en charge et sur toute nouvelle machine créée. Si l’agent Log Analytics est déjà installé sur la machine virtuelle ou sur l’ordinateur, Defender pour le cloud utilise l’agent actuellement installé. Les processus de l’agent sont conçus pour être non invasifs et ont un impact minimal sur les performances de la machine Virtuelle.

L’agent Log Analytics pour Windows requiert l’utilisation du port TCP 443. Consultez l’[article sur la résolution des problèmes](troubleshooting-guide.md) pour plus d’informations.

Si, à un moment donné, vous souhaitez désactiver la collecte de données, vous pouvez le faire dans la stratégie de sécurité. Toutefois, étant donné que l’agent Log Analytics peut être utilisé par d’autres services de supervision et de gestion Azure, cet agent n’est pas désinstallé automatiquement quand vous désactivez la collecte de données dans Defender pour le cloud. Vous pouvez le désinstaller manuellement si nécessaire.

> [!NOTE]
> Pour obtenir la liste des machines virtuelles prises en charge, consultez les [Questions fréquentes (FAQ) sur Defender pour le cloud](faq-vms.yml).

### <a name="workspace"></a>Espace de travail

Un espace de travail est une ressource Azure servant de conteneur à données. Vous ou d’autres membres de votre organisation pouvez utiliser plusieurs espaces de travail pour gérer différents ensembles de données provenant de tout ou partie de votre infrastructure informatique.

Les données collectées à partir de l’agent Log Analytics (pour le compte de Defender pour le cloud) sont stockées dans un ou plusieurs espaces de travail Log Analytics existants associés à votre abonnement Azure ou dans un nouvel espace de travail, en tenant compte de la zone géographique de la machine virtuelle.

Dans le portail Azure, vous pouvez parcourir le contenu pour afficher la liste de vos espaces de travail Log Analytics, notamment ceux créés par Defender pour le cloud. Un groupe de ressources associées sera créé pour les nouveaux espaces de travail. Les deux respecteront la convention d’affectation de noms suivante :

* Espace de travail : *DefaultWorkspace-[subscription-ID]-[geo]*
* Groupe de ressources : *DefaultResourceGroup-[geo]*

Pour les espaces de travail créés par Defender pour le cloud, les données sont conservées pendant 30 jours. Pour les espaces de travail existants, la rétention dépend du niveau tarifaire de l’espace de travail. Si vous le souhaitez, vous pouvez aussi utiliser un espace de travail existant.

> [!NOTE]
> Microsoft prend des engagements fermes pour protéger la confidentialité et la sécurité de ces données. Microsoft adhère aux recommandations strictes de conformité et de sécurité, du codage jusqu'à l'exploitation d'un service. Pour plus d’informations sur la gestion des données et la confidentialité, lisez [Sécurité des données de Defender pour le cloud](data-security.md).
>

## <a name="onboard-non-azure-resources"></a>Intégrer des ressources non-Azure

Defender pour le cloud peut superviser l’état de sécurité de vos ordinateurs autres qu’Azure, mais vous devez d’abord intégrer ces ressources. Pour plus d’informations sur la façon d’intégrer des ressources non-Azure, consultez [Intégrer des ordinateurs non-Azure](quickstart-onboard-machines.md).

## <a name="ongoing-security-monitoring"></a>Surveillance continue de la sécurité
Après la configuration initiale et la mise en œuvre des recommandations de Defender pour le cloud, l’étape suivante détermine les processus opérationnels de ce dernier.

La vue d’ensemble sur Defender pour le cloud offre une vision unifiée de la sécurité sur toutes les ressources Azure et non Azure que vous avez connectées. L’exemple ci-dessous montre un environnement dans lequel plusieurs problèmes sont à régler :

![tableau de bord.](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig11.png)

> [!NOTE]
> Defender pour le cloud n’interfère pas avec vos procédures de fonctionnement normales. Il supervise passivement vos déploiements et fournit des recommandations en fonction des stratégies de sécurité activées.

Quand vous choisissez pour la première fois d’utiliser Defender pour le cloud pour votre environnement Azure, veillez à passer en revue toutes les recommandations, ce que vous pouvez faire dans la page **Recommandations**.

Prévoyez de consulter l’option Informations sur les menaces dans le cadre de vos opérations de sécurité quotidiennes. Vous pouvez y identifier les menaces de sécurité qui pèsent sur votre environnement. Par exemple, pour déterminer si un ordinateur fait parti d’un botnet.

### <a name="monitoring-for-new-or-changed-resources"></a>Surveillance des ressources nouvelles ou modifiées

La plupart des environnements Azure sont dynamiques, avec des ressources régulièrement créées, activées, désactivées, reconfigurées et modifiées. Defender pour le cloud vous offre une visibilité sur l’état de la sécurité de ces nouvelles ressources.

Quand vous ajoutez de nouvelles ressources (machines virtuelles, bases de données SQL) dans votre environnement Azure, Defender pour le cloud les détecte automatiquement et commence à superviser leur sécurité. Cela inclut également les rôles Web et les rôles de travail PaaS. Si la collecte de données est activée dans la [stratégie de sécurité](tutorial-security-policy.md), des fonctionnalités de surveillance supplémentaires sont activées automatiquement pour vos machines virtuelles.

Vous devez également superviser régulièrement les ressources existantes pour rechercher les modifications de configuration qui peuvent être à l’origine de risques de sécurité, d’alertes de sécurité et de dérive par rapport aux bases de référence recommandées. 

### <a name="hardening-access-and-applications"></a>Renforcement des applications et de l’accès

Dans le cadre de vos opérations de sécurité, vous devez entreprendre des mesures préventives pour restreindre l’accès aux machines virtuelles, et contrôler les applications exécutées sur ces dernières. En verrouillant le trafic entrant de vos machines virtuelles, vous réduisez l’exposition aux attaques et fournissez en même temps un accès simple pour se connecter aux machines virtuelles si nécessaire. Utilisez la fonctionnalité d’accès [juste-à-temps à la machine virtuelle](just-in-time-access-usage.md) pour durcir l’accès à vos machines virtuelles.

Vous pouvez utiliser les [contrôles d’application adaptatifs](adaptive-application-controls.md) pour limiter le nombre d’applications qui peuvent s’exécuter sur vos machines virtuelles hébergées dans Azure. Parmi d’autres avantages, cela permet de renforcer la protection de vos machines virtuelles contre les logiciels malveillants. À l’aide de l’apprentissage automatique, Defender pour le cloud analyse les processus en cours d’exécution dans la machine virtuelle pour vous aider à créer des règles de liste d’autorisation.


## <a name="incident-response"></a>Réponse aux incidents
Defender pour le cloud détecte les menaces et vous les signale dès qu’elles se produisent. Les organisations doivent surveiller les nouvelles alertes de sécurité et réagir soit en examinant l’attaque plus en détail,soit en la contrant. Pour plus d’informations sur le fonctionnement de Defender pour la protection contre les menaces dans le Cloud, consultez [Comment Defender pour le cloud détecte les menaces et y répond](alerts-overview.md#detect-threats).

Même si cet article ne vise pas à vous aider à créer votre propre plan de réponse aux incidents, nous allons utiliser le cycle de vie Microsoft Azure Security Response in the Cloud comme base pour les étapes de réponse aux incidents. Les étapes sont présentées dans le diagramme suivant :

![Étapes de la réponse aux incidents dans le cycle de vie du cloud.](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> Vous pouvez utiliser le document [Computer Security Incident Handling Guide](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) (Guide de gestion des incidents de sécurité informatique) du NIST (National Institute of Standards and Technology) comme référence pour vous aider à créer votre plan.
>

Vous pouvez utiliser Defender pour le cloud au cours des étapes suivantes :

* **Détecter**: identifier une activité suspecte dans une ou plusieurs ressources.
* **Évaluer**: procéder à l’évaluation initiale pour obtenir plus d’informations sur l’activité suspecte.
* **Diagnostiquer**: suivre les étapes techniques de résolution pour remédier au problème.

Chaque alerte de sécurité fournit des informations permettant de mieux comprendre la nature de l’attaque et propose des solutions possibles. Certaines alertes fournissent également des liens vers des informations plus détaillées ou d’autres sources d’information dans Azure. Vous pouvez utiliser les informations fournies pour d’autres recherches et pour commencer l’atténuation. Vous pouvez également rechercher des données relatives à la sécurité qui sont stockées dans votre espace de travail.

L’exemple suivant montre une activité RDP suspecte :

![Activité suspecte.](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

Cette page affiche des informations sur l’heure de l’attaque, le nom d’hôte source, la machine virtuelle cible, et détaille une procédure recommandée. Parfois, aucune information ne sera disponible concernant la source de l’attaque. Pour plus d’informations sur ce type de comportement, consultez [Informations sur la source manquantes dans les alertes Defender pour le cloud](/archive/blogs/azuresecurity/missing-source-information-in-azure-security-center-alerts).

Une fois le système compromis identifié, vous pouvez exécuter une [automatisation de workflow](workflow-automation.md) déjà créée. Il s’agit d’une collection de procédures qui peuvent être exécutées à partir de Defender pour le cloud une fois qu’elles ont été déclenchées par une alerte.

La vidéo [Comment tirer profit de Defender pour le cloud et de Microsoft Operations Management Suite pour répondre aux incidents](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703) présente des démonstrations montrant comment Defender pour le cloud peut être utilisé dans chacune de ces étapes.

> [!NOTE]
> Consultez l’article [Gestion et résolution des alertes de sécurité dans Defender pour le cloud](managing-and-responding-alerts.md) pour plus d’informations sur l’utilisation des fonctionnalités de Defender pour le cloud afin de vous aider lors du processus de réponse aux incidents.
>
>

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à planifier l’adoption de Defender pour le cloud. Pour en savoir plus sur Defender pour le cloud, consultez les articles suivants :

* [Gérer les alertes de sécurité et y répondre dans Defender pour le cloud](managing-and-responding-alerts.md)
* [Supervision des solutions de partenaire avec Defender pour le cloud](./partner-integration.md) : découvrez comment superviser l’état d’intégrité de vos solutions de partenaire.
* [Questions fréquentes (FAQ) sur Defender pour le cloud](faq-general.yml) : découvrez les réponses aux questions fréquentes à propos de l’utilisation de ce service.
* [Blog sur la sécurité Azure](/archive/blogs/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure.