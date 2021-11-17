---
title: Vue d’ensemble d’Azure Automation
description: Cet article vous explique en quoi consiste Azure Automation et comment l’utiliser pour automatiser le cycle de vie de l’infrastructure et des applications.
services: automation
keywords: azure automation, DSC, powershell, configuration de l’état, gestion des mises à jour, suivi des modifications, DSC, inventaire, runbooks, python, graphique
ms.date: 10/25/2021
ms.topic: overview
ms.openlocfilehash: d6a703a04aeec9b764f74a7574cde4baea875604
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491485"
---
# <a name="what-is-azure-automation"></a>Qu'est-ce qu'Azure Automation ?

L’automatisation est nécessaire dans trois domaines essentiels des opérations cloud :

* Déployer et gérer : fournissez une infrastructure reproductible et cohérente sous forme de code.
* Réponse : créez l’automatisation basée sur les événements pour diagnostiquer et résoudre les problèmes.
* Orchestrer : orchestrez votre automatisation et l’intégrez à d’autres produits et services Azure ou tiers.

Azure Automation offre un service d’automatisation basé sur le cloud, des mises à jour de système d’exploitation et un service de configuration qui prend en charge une gestion cohérente de vos environnements Azure et non Azure. Il inclut l’automatisation des processus, la gestion de la configuration, la gestion des mises à jour, des fonctionnalités partagées et des fonctionnalités hétérogènes.

:::image type="content" source="./media/overview/automation-overview.png" alt-text="Diagramme des fonctionnalités Automation" border="true":::

Il existe plusieurs services Azure qui peuvent satisfaire les exigences ci-dessus, chacun incluant un ensemble de fonctionnalités et servant de plateforme programmable pour générer des solutions cloud. Par exemple, Azure Bicep et Resource Manager fournissent un langage permettant de développer des modèles de déploiement reproductibles et cohérents pour les ressources Azure. Azure Automation peut traiter ce modèle pour déployer une ressource Azure, puis traiter un ensemble de tâches de configuration post-déploiement.

Automation vous laisse un contrôle complet lors du déploiement, des opérations et de la désaffectation de charges de travail d’entreprise et de ressources.

## <a name="process-automation"></a>Automatisation de processus

L’automatisation des processus dans Azure Automation vous permet d’automatiser les tâches de gestion fréquentes, longues et susceptibles d’engendrer des erreurs. Ce service permet de vous concentrer sur des activités porteuses de valeur ajoutée. En diminuant le nombre d’erreurs et en augmentant l’efficacité, ce service vous permet également de réduire vos coûts d’exploitation. L’environnement d’exploitation d’automatisation des processus est détaillé dans [Exécution d’un runbook dans Azure Automation](automation-runbook-execution.md).

L’automatisation des processus prend en charge l’intégration de services Azure et d’autres systèmes tiers nécessaires pour le déploiement, la configuration et la gestion de vos processus de bout en bout. Le service vous permet de créer des [runbooks](automation-runbook-types.md) graphiques PowerShell et Python. Pour exécuter des runbooks directement sur la machine Windows ou Linux ou sur des ressources se trouvant dans l’environnement local ou dans un autre environnement cloud pour gérer ces ressources locales, vous pouvez déployer un [Runbook Worker hybride](automation-hybrid-runbook-worker.md) sur la machine.

Les [webhooks](automation-webhooks.md) vous permettent de répondre aux demandes et de garantir la continuité de la livraison et des opérations en déclenchant l’automatisation à partir d’Azure Logic Apps, d’Azure Function, d’un service ou produit ITSM, de DevOps et de systèmes de monitoring.

## <a name="configuration-management"></a>Gestion de la configuration

Dans Azure Automation, la Gestion de la configuration est prise en charge par deux fonctionnalités :

* Suivi des modifications et inventaire
* Azure Automation – State Configuration

### <a name="change-tracking-and-inventory"></a>Suivi des modifications et inventaire

La fonctionnalité Suivi des modifications et inventaire combine plusieurs fonctions pour vous permettre d’effectuer le suivi des changements apportés à l’infrastructure des machines virtuelles et des serveurs Linux et Windows. Le service prend en charge le suivi des modifications sur les services, les démons, les logiciels, le Registre et les fichiers de votre environnement pour vous aider à diagnostiquer les changements indésirables et à déclencher des alertes. La prise en charge de l’inventaire vous permet d’interroger les ressources intégrées pour voir les applications installées et d’autres éléments de configuration. Pour plus d’informations sur cette fonctionnalité, consultez [Suivi des modifications et inventaire](change-tracking/overview.md).

### <a name="azure-automation-state-configuration"></a>Azure Automation – State Configuration

[Azure Automation – State Configuration](automation-dsc-overview.md) est une fonctionnalité cloud pour la configuration DSC (Desired State Configuration) PowerShell qui fournit les services requis aux environnements d’entreprise. Avec cette fonctionnalité, vous pouvez gérer vos ressources DSC dans Azure Automation et appliquer des configurations aux machines virtuelles ou physiques à partir d’un serveur Pull DSC dans le cloud Azure.

## <a name="update-management"></a>Update Management

Azure Automation comprend la fonctionnalité [Gestion des mises à jour](./update-management/overview.md) pour les systèmes Windows et Linux dans des environnements hybrides. La fonctionnalité Gestion des mises à jour vous permet de voir la conformité des mises à jour sur Azure et d’autres clouds, et en local. La fonctionnalité vous permet de créer des déploiements planifiés qui coordonnent l’installation de mises à jour dans une fenêtre de maintenance définie. Si une mise à jour ne doit pas être installée sur un ordinateur, vous pouvez utiliser la fonctionnalité Gestion des mises à jour pour l’exclure d’un déploiement.

## <a name="shared-capabilities"></a>Fonctionnalités partagées

Azure Automation fournit un certain nombre de fonctionnalités partagées, notamment des ressources partagées, le contrôle d’accès en fonction du rôle, une planification flexible, l’intégration du contrôle de code source, l’audit et le balisage.

### <a name="shared-resources"></a><a name="shared-resources"></a>Ressources partagées

Azure Automation se compose d’un ensemble de ressources partagées qui facilitent l’automatisation et la configuration de vos environnements à grande échelle.

* **[Planifications](./shared-resources/schedules.md)** - Déclenchez des opérations Automation à des heures prédéfinies.
* **[Modules](./shared-resources/modules.md)** - Gérez Azure et d’autres systèmes. Vous pouvez importer les modules dans le compte Automation pour Microsoft, un tiers ou une communauté, et des ressources DSC et des applets de commande définies personnalisées.
* **[Galerie de modules](automation-runbook-gallery.md)** - Prend en charge l’intégration native à PowerShell Gallery pour que vous puissiez afficher des runbooks et les importer dans le compte Automation. La galerie vous permet de commencer rapidement à intégrer et à créer vos processus à partir de PowerShell Gallery et du Centre de scripts Microsoft.
* **[Packages Python 2 et 3](python-packages.md)**  : prennent en charge les runbooks Python 2 et 3 pour votre compte Automation.
* **[Informations d’identification](./shared-resources/credentials.md)** - Permettent de stocker en toute sécurité des informations sensibles que les runbooks et configurations peuvent utiliser lors de l’exécution.
* **[Connexions](automation-connections.md)** - Stockent des paires nom-valeur d’informations communes pour les connexions aux systèmes. L’auteur du module définit les connexions dans des runbooks et configurations pour les utiliser lors de l’exécution.
* **[Certificats](./shared-resources/certificates.md)** - Définissent des informations à utiliser pour l’authentification et la sécurisation des ressources déployées quand les runbooks ou les configurations DSC y accèdent au moment de l’exécution.
* **[Variables](./shared-resources/variables.md)** - Stockent du contenu pouvant être utilisé dans des runbooks et des configurations. Vous pouvez changer les valeurs des variables sans avoir à modifier les runbooks ou configurations qui y font référence.

### <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Azure Automation prend en charge le contrôle d’accès en fonction du rôle Azure (Azure RBAC) pour réguler l’accès au compte Automation et à ses ressources. Pour en savoir plus sur la configuration du contrôle d’accès en fonction du rôle Azure sur votre compte Automation, les runbooks et les travaux, consultez [Contrôle d’accès en fonction du rôle dans Azure Automation](automation-role-based-access-control.md).

### <a name="source-control-integration"></a>Intégration du contrôle de code source

Azure Automation prend en charge [l’intégration du contrôle de code source](source-control-integration.md). Cette fonctionnalité promeut la configuration en tant que code où les runbooks ou les configurations peuvent être archivés dans un système de contrôle de code source.

## <a name="heterogeneous-support-windows-and-linux"></a>Prise en charge hétérogène (Windows et Linux)

Automation est conçu pour fonctionner sur des serveurs physiques Windows et Linux et sur des machines virtuelles extérieures à Azure, sur votre réseau d’entreprise ou sur un autre fournisseur de cloud. Il offre un moyen cohérent d’automatiser et de configurer les charges de travail déployées et les systèmes d’exploitation qui les exécutent. La fonctionnalité Runbook Worker hybride d’Azure Automation permet d’exécuter des runbooks directement sur la machine virtuelle ou le serveur physique non Azure qui héberge le rôle et sur les ressources de l’environnement afin de gérer ces ressources locales.

Grâce aux [serveurs avec Arc](../azure-arc/servers/overview.md), il offre une expérience de déploiement et de gestion cohérente pour vos machines non Azure. Il permet l’intégration au service Automation à l’aide du framework d’extension de machine virtuelle pour déployer le rôle Runbook Worker hybride et simplifier l’intégration aux fonctionnalités Gestion des mises à jour et Suivi des modifications et inventaire.

## <a name="common-scenarios"></a>Scénarios courants

Azure Automation prend en charge la gestion tout au long du cycle de vie de votre infrastructure et des applications. Scénarios courants :

* **Planifier des tâches** : arrêtez les machines virtuelles ou services la nuit, et activez des workflows de maintenance périodiques quotidiens, hebdomadaires ou mensuels pendant la journée.
* **Écrire des runbooks** : créez des runbooks graphiques, PowerShell, PowerShell Workflow, DSC Python 2 et Python 3 dans des langages courants.
* **Générer et déployer des ressources** - Déployez des machines virtuelles dans un environnement hybride à l’aide de runbooks et de modèles Azure Resource Manager. Intégrez des outils de développement, comme Jenkins et Azure DevOps.
* **Configurer des machines virtuelles** - Évaluez et configurez des machines Windows et Linux avec des configurations pour l’infrastructure et l’application.
* **Partager les connaissances** - Transférez au système les connaissances relatives à la manière dont l’organisation fournit et gère les charges de travail.
* **Récupérer l’inventaire** - Obtenez un inventaire complet des ressources déployées à des fins de ciblage, de création de rapports et de conformité.
* **Localiser les changements** : identifiez et isolez les changements de la machine pouvant entraîner une configuration incorrecte et améliorez la conformité opérationnelle. Corrigez-les ou faites-les remonter aux systèmes de gestion.
* **Maintenance périodique** : pour exécuter des tâches qui doivent être effectuées à des intervalles définis, tels que la purge de données obsolètes ou anciennes, ou réindexer une base de données SQL.
* **Répondre aux alertes** : orchestrez une réponse lors de la génération d’alertes basées sur le coût, sur le système, sur le service et/ou sur l’utilisation des ressources.
* **Automatisation hybride** : gérez ou automatisez des serveurs et services locaux tels que SQL Server, Active Directory, SharePoint Server, etc.
* **Gestion du cycle de vie des ressources Azure** : pour les services IaaS et PaaS.
* **Scénarios d’automatisation du développement/test** : démarrez et démarrez des ressources, mettez les ressources à l’échelle, etc.
* **Automatisation liée à la gouvernance** : appliquez ou mettez à jour automatiquement les étiquettes, les verrous, etc.
* **Azure Site Recovery** : orchestrez des préscripts et des postscripts définis dans un workflow de reprise d’activité Site Recovery.
* **Windows Virtual Desktop** : orchestrer la mise à l’échelle des machines virtuelles ou démarrer/arrêter les machines virtuelles en fonction de l’utilisation.

Selon vos besoins, un ou plusieurs des services Azure suivants s’intègrent à Azure Automation ou le complètent pour vous permettre de les satisfaire :

* Les [serveurs avec Azure Arc](../azure-arc/servers/overview.md) permettent l’intégration simplifiée de machines hybrides aux fonctionnalités Gestion des mises à jour, Suivi des modifications et inventaire, ainsi qu’au rôle Runbook Worker hybride.
* Les [groupes d’actions des Alertes Azure](../azure-monitor/alerts/action-groups.md) peuvent lancer un runbook Automation lorsqu’une alerte est déclenchée.
* [Azure Monitor](../azure-monitor/overview.md) pour collecter des métriques et des données de journal à partir de votre compte Automation pour une analyse approfondie et agir sur la télémétrie. Les fonctionnalités Automation telles que Gestion des mises à jour et Change Suivi des modifications et inventaire s’appuient sur l’espace de travail Log Analytics pour fournir des éléments de leurs fonctionnalités.
* [Azure Policy](../governance/policy/samples/built-in-policies.md) comprend des définitions d’initiative pour faciliter l’établissement et la maintenance de la conformité avec différentes normes de sécurité pour votre compte Automation.
* [Azure Site Recovery](../site-recovery/site-recovery-runbook-automation.md) peut utiliser des runbooks Azure Automation pour automatiser des plans de récupération.

Ces services Azure peuvent fonctionner avec les ressources de travail et de runbook Automation à l’aide d’un webhook HTTP ou d’une méthode API :

* [Azure Logic Apps](/azure/connectors/built-in)
* [Azure Power Apps](/connectors/azureautomation)
* [Azure Event Grid](../event-grid/handler-webhooks.md)
* [Azure Power Automate](/connectors/azureautomation)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-azure-automation"></a>Tarification d’Azure Automation

Vous pouvez consulter les prix associés à Azure Automation dans la page relative aux [tarifs](https://azure.microsoft.com/pricing/details/automation/).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer un compte Automation](./quickstarts/create-account-portal.md)
