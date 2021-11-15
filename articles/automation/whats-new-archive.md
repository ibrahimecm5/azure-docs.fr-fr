---
title: Archives pour les nouveautés d’Azure Automation
description: Les notes de publication des nouveautés de la section Vue d’ensemble de ce jeu de contenus contiennent six mois d’activité. Ensuite, les éléments sont supprimés de l’article principal et placés dans cet article.
services: automation
ms.topic: overview
ms.date: 10/27/2021
ms.custom: references_regions
ms.openlocfilehash: bd481c65dadbf127682087e4c0cf6ca018b17098
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473943"
---
# <a name="archive-for-whats-new-in-azure-automation"></a>Archives pour les nouveautés d’Azure Automation

L’article principal [Nouveautés d’Azure Automation](whats-new.md) contient les mises à jour des six derniers mois, tandis que cet article contient toutes les informations plus anciennes.

« Nouveautés d’Azure Automation » vous donne des informations sur les sujets suivants :

- Versions les plus récentes
- Problèmes connus
- Résolution des bogues

---

## <a name="march-2021"></a>Mars 2021

### <a name="new-azure-automation-built-in-policy-definitions"></a>Nouvelles définitions de stratégie intégrées Azure Automation

**Type :** Nouvelle fonctionnalité

Azure Automation a ajouté 5 nouvelles définitions de stratégie intégrées :

- Les comptes Automation doivent désactiver l’accès au réseau public
- Les comptes Azure Automation doivent utiliser des clés gérées par le client pour chiffrer les données au repos
- Configurer des comptes Azure Automation pour désactiver l'accès réseau public
- Configurer des connexions de point de terminaison privé sur des comptes Azure Automation
- Les connexions de point de terminaison privé sur les comptes Automation doivent être activées

Pour plus d’informations, consultez [Informations de référence sur Azure Policy](./policy-reference.md).

### <a name="support-for-automation-and-state-configuration-declared-ga-in-south-india"></a>Disponibilité générale déclarée de la prise en charge d’Automation et de State Configuration dans la région Inde Sud

**Type :** Nouvelle fonctionnalité

Utilisez la fonctionnalité Process Automation et State Configuration dans la région Inde Sud. Lisez cette [annonce](https://azure.microsoft.com/updates/azure-automation-in-south-india-region/) pour plus d’informations.

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uk-west"></a>Disponibilité générale déclarée de la prise en charge d’Automation et de State Configuration dans la région Royaume-Uni Ouest

**Type :** Nouvelle fonctionnalité

Utilisez la fonctionnalité Process Automation et State Configuration dans la région Royaume-Uni Ouest. Pour plus d'informations, lisez cette [annonce](https://azure.microsoft.com/updates/azure-automation-in-uk-west-region/).

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uae-central"></a>Disponibilité générale déclarée de la prise en charge d’Automation et de State Configuration dans la région Émirats arabes unis Centre

**Type :** Nouvelle fonctionnalité

Utilisez la fonctionnalités Process Automation et State Configuration dans la région Émirats arabes unis Centre. Lisez cette [annonce](https://azure.microsoft.com/updates/azure-automation-in-uae-central-region/) pour plus d’informations.

### <a name="support-for-automation-and-state-configuration-available-in-australia-central-2-norway-west-and-france-south"></a>Prise en charge d’Automation et de State Configuration disponible dans les régions Australie Centre 2, Norvège Ouest et France Sud

**Type :** Nouvelle fonctionnalité

Pour plus d’informations, consultez la page consacrée à la [résidence des données](https://azure.microsoft.com/global-infrastructure/data-residency/) en sélectionnant la zone géographique pour chaque région.

### <a name="new-scripts-added-for-installing-hybrid-worker-on-windows-and-linux"></a>Nouveaux scripts ajoutés pour l’installation du Worker hybride sur Windows et Linux

**Type :** Nouvelle fonctionnalité

Deux nouveaux scripts ont été ajoutés au [dépôt GitHub](https://github.com/azureautomation) Azure Automation, qui résolvent l’un des principaux scénarios d’Azure Automation de configuration d’un Runbook Worker hybride sur une machine Windows ou Linux. Le script crée une machine virtuelle ou en utilise une existante, le cas échéant il crée un espace de travail Log Analytics, installe l’agent Log Analytics pour Windows ou pour Linux et inscrit la machine sur l’espace de travail Log Analytics. Le script Windows est nommé **Create Automation Windows HybridWorker**, et le script Linux **Create Automation Linux HybridWorker**.

### <a name="invoke-runbook-through-an-azure-resource-manager-template-webhook"></a>Appeler un runbook par le biais d’un webhook de modèle Azure Resource Manager

**Type :** Nouvelle fonctionnalité

Pour plus d’informations, consultez [Utiliser un webhook à partir d’un modèle ARM](./automation-webhooks.md#create-runbook-and-webhook-with-arm-template).

### <a name="azure-update-management-now-supports-centos-8x-red-hat-enterprise-linux-server-8x-and-suse-linux-enterprise-server-15"></a>Azure Update Management prend désormais en charge CentOS 8.x, Red Hat Enterprise Linux Server 8.x et SUSE Linux Enterprise Server 15

**Type :** Nouvelle fonctionnalité

Pour plus d’informations, consultez la [liste complète](./update-management/operating-system-requirements.md) des systèmes d’exploitation Linux pris en charge.

### <a name="in-region-data-residency-support-for-brazil-south-and-south-east-asia"></a>Prise en charge de la résidence des données dans la région, pour Brésil Sud et Asie Sud-Est

**Type :** Nouvelle fonctionnalité

Dans toutes les régions, à l’exception de Brésil Sud et d’Asie Sud-Est, les données Azure Automation sont stockées dans une région différente (région jumelée Azure) pour assurer la continuité d’activité et reprise d’activité (BCDR). Pour les régions Brésil Sud et Asie Sud-Est seulement, nous stockons maintenant les données Azure Automation dans la même région, afin de répondre aux exigences de résidence des données pour ces régions. Pour plus d’informations, consultez [Géoréplication dans Azure Automation](./automation-managing-data.md#geo-replication-in-azure-automation).

## <a name="february-2021"></a>Février 2021

### <a name="support-for-automation-and-state-configuration-declared-ga-in-japan-west"></a>Disponibilité générale déclarée de la prise en charge d’Automation et de State Configuration dans la région Japon Ouest

**Type :** Nouvelle fonctionnalité

Disponibilité du compte Automation et de State Configuration dans la région Japon Ouest. Pour plus d'informations, lisez cette [annonce](https://azure.microsoft.com/updates/azure-automation-in-japan-west-region/).

### <a name="introduced-custom-azure-policy-compliance-to-enforce-runbook-execution-on-hybrid-worker"></a>Introduction de la conformité du service Azure Policy personnalisé pour appliquer l’exécution de runbook sur un Worker hybride

**Type :** nouvelle fonctionnalité

Vous pouvez utiliser la nouvelle règle de conformité Azure Policy pour autoriser la création de travaux, de webhooks et de planifications de travaux à exécuter seulement sur des groupes de Workers hybrides.

### <a name="update-management-availability-in-east-us-france-central-and-north-europe-regions"></a>Disponibilité d’Update Management dans les régions USA Est, France Centre et Europe Nord

**Type :** Nouvelle fonctionnalité

La fonctionnalité Update Management d’Automation est disponible dans les régions USA Est, France Centre et Europe Nord. Pour voir les mises à jour de la documentation en lien avec ce changement, consultez [Mappage de régions prises en charge](how-to/region-mappings.md).

## <a name="january-2021"></a>Janvier 2021

### <a name="support-for-automation-and-state-configuration-declared-ga-in-switzerland-west"></a>Disponibilité générale déclarée de la prise en charge d’Automation et de State Configuration dans la région Suisse Ouest

**Type :** Nouvelle fonctionnalité

Disponibilité du compte Automation et de State Configuration dans la région Suisse Ouest. Pour plus d'informations, lisez cette [annonce](https://azure.microsoft.com/updates/azure-automation-in-switzerland-west-region/).

### <a name="added-python-3-script-to-import-module-with-multiple-dependencies"></a>Ajout d’un script Python 3 pour importer un module avec plusieurs dépendances

**Type :** Nouvelle fonctionnalité

Le script est disponible en téléchargement à partir de notre [dépôt GitHub](https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py).
 
### <a name="hybrid-runbook-worker-role-support-for-centos-8xrhel-8xsles-15"></a>Prise en charge du rôle Runbook Worker hybride pour CentOS 8.x/RHEL 8.x/SLES 15

**Type.** Nouvelle fonctionnalité

La fonctionnalité Runbook Worker hybride prend en charge les distributions CentOS 8.x, REHL 8.x et SLES 15 uniquement pour l’automatisation des processus sur les Runbook Workers hybrides. Consultez [Systèmes d’exploitation pris en charge](automation-linux-hrw-install.md#supported-linux-operating-systems) pour voir les mises à jour de la documentation en lien avec ces changements.

### <a name="update-management-and-change-tracking-availability-in-australia-east-east-asia-west-us-and-central-us-regions"></a>Disponibilité d’Update Management et de Change Tracking dans les régions Australie Est, Asie Est, USA Ouest et USA Centre

**Type :** Nouvelle fonctionnalité

Le compte Automation, Suivi des modifications et inventaire, et Update Management sont disponibles dans les régions Australie Est, Asie Est, USA Ouest et USA Centre.

### <a name="introduced-public-preview-of-python-3-runbooks-in-us-government-cloud"></a>Introduction de la préversion publique des runbooks Python 3 dans le cloud US Government

**Type :** la nouvelle fonctionnalité Azure Automation introduit la prise en charge de l’exécution des runbooks cloud et hybrides Python 3 en préversion publique dans les régions cloud US Government. Pour plus d'informations, consultez [Annonce](https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/).

### <a name="azure-automation-runbooks-moved-from-technet-script-center-to-github"></a>Les runbooks Azure Automation ont été déplacés du Centre de scripts TechNet vers GitHub

**Type :** Modification planifiée

En raison de la mise hors service du Centre de scripts TechNet, tous les runbooks hébergés dans la galerie de runbooks ont été déplacés vers notre [organisation GitHub Automation](https://github.com/azureautomation). Pour plus d’informations, consultez l’article sur le [déplacement des runbooks Azure Automation vers GitHub](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337).

## <a name="december-2020"></a>Décembre 2020

### <a name="azure-automation-and-update-management-private-link-ga"></a>Disponibilité générale de Private Link pour Azure Automation et Update Management

**Type :** Nouvelle fonctionnalité

Annonce de la disponibilité générale de la prise en charge d’Azure Automation et Update Management pour les clouds Azure global et Government. La prise en charge de Private Link pour Azure Automation permet de sécuriser l’exécution d’un runbook sur un rôle de worker hybride, en utilisant Update Management pour appliquer des correctifs sur les machines, en appelant un runbook via un webhook et en utilisant le service State Configuration pour garantir la conformité de vos machines. Pour plus d’informations, consultez l’article sur la [prise en charge de Private Link pour Azure Automation](https://azure.microsoft.com/updates/azure-automation-private-link).

### <a name="azure-automation-classified-as-grade-c-certified-on-accessibility"></a>Azure Automation certifié Grade-C sur l’accessibilité

**Type :** Nouvelle fonctionnalité

Les fonctionnalités d’accessibilité des produits Microsoft aident les agences à être en conformité avec les normes d’accessibilité mondiales. Dans la page d’[annonces du blog](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/), recherchez **Azure Automation** pour lire le rapport de conformité de l’accessibilité relatif au service Automation.

### <a name="support-for-automation-and-state-configuration-ga-in-uae-north"></a>Disponibilité générale de la prise en charge d’Automation et de State Configuration dans la région Émirats arabes unis Nord

**Type :** Nouvelle fonctionnalité

Disponibilité du compte Automation et de State Configuration dans la région Émirats arabes unis Nord. Pour plus d'informations, lisez cette [annonce](https://azure.microsoft.com/updates/azure-automation-in-uae-north-region/).

### <a name="support-for-automation-and-state-configuration-ga-in-germany-west-central"></a>Disponibilité générale de la prise en charge d’Automation et de State Configuration dans la région Allemagne Centre-Ouest

**Type :** Nouvelle fonctionnalité

Disponibilité du compte Automation et de State Configuration dans la région Allemagne Ouest. Pour plus d'informations, lisez cette [annonce](https://azure.microsoft.com/updates/azure-automation-in-germany-west-central-region/).

### <a name="dsc-support-for-oracle-6-and-7"></a>Prise en charge de DSC pour Oracle 6 et 7

**Type :** Nouvelle fonctionnalité

Gérez les machines Oracle Linux 6 et 7 avec Automation State Configuration. Consultez [Distributions Linux prises en charge](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) pour voir les mises à jour de la documentation en lien avec ces changements.

### <a name="public-preview-for-python3-runbooks-in-automation"></a>Préversion publique des runbooks Python 3 dans Automation

**Type :** Nouvelle fonctionnalité

Azure Automation prend désormais en charge l’exécution des runbooks cloud et hybrides Python 3 en préversion publique dans toutes les régions du cloud mondial Azure. Pour plus d’informations, consultez l’[annonce]((https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/).

## <a name="november-2020"></a>Novembre 2020

### <a name="dsc-support-for-ubuntu-1804"></a>Prise en charge de DSC pour Ubuntu 18.04

**Type :** Nouvelle fonctionnalité

Consultez [Distributions Linux prises en charge](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) pour voir les mises à jour de la documentation en lien avec ces changements.

## <a name="october-2020"></a>Octobre 2020

### <a name="support-for-automation-and-state-configuration-ga-in-switzerland-north"></a>Disponibilité générale de la prise en charge d’Automation et de State Configuration dans la région Suisse Nord

**Type :** Nouvelle fonctionnalité

Disponibilité du compte Automation et de State Configuration dans la région Suisse Nord. Pour plus d'informations, lisez cette [annonce](https://azure.microsoft.com/updates/azure-automation-in-switzerland-north-region/).

### <a name="support-for-automation-and-state-configuration-ga-in-brazil-south-east"></a>Disponibilité générale de la prise en charge d’Automation et de State Configuration dans la région Brésil Sud

**Type :** Nouvelle fonctionnalité

Disponibilité du compte Automation et de State Configuration dans la région Brésil Sud. Pour plus d'informations, lisez cette [annonce](https://azure.microsoft.com/updates/azure-automation-in-brazil-southeast-region/).

### <a name="update-management-availability-in-south-central-us"></a>Disponibilité de la fonctionnalité Update Management dans la région USA Centre Sud

**Type :** Nouvelle fonctionnalité

Le mappage des régions Azure Automation a été mis à jour pour prendre en charge la fonctionnalité Update Management dans la région USA Centre Sud. Consultez [Mappage de régions prises en charge](how-to/region-mappings.md#supported-mappings) pour voir les mises à jour de la documentation en lien avec ce changement.

## <a name="september-2020"></a>Septembre 2020

### <a name="startstop-vms-during-off-hours-runbooks-updated-to-use-azure-az-modules"></a>Mise à jour des runbooks Start/Stop VMs during off-hours pour utiliser les modules Az Azure

**Type :** Nouvelle fonctionnalité

Les runbooks Start/Stop VMs during off-hours ont été mis à jour pour utiliser les modules Az à la place des modules Azure Resource Manager. Consultez [Vue d’ensemble de Start/Stop VMs during off-hours](automation-solution-vm-management.md) pour voir les mises à jour de la documentation en lien avec ces changements.

## <a name="august-2020"></a>Août 2020

### <a name="published-the-dsc-extension-to-support-azure-arc"></a>Publication de l’extension DSC pour prendre en charge Azure Arc

**Type :** Nouvelle fonctionnalité

Utilisez Azure Automation State Configuration pour stocker de manière centralisée les configurations et conserver l’état souhaité des machines connectées hybrides activées via l’extension de machine virtuelle DSC pour les serveurs avec Azure Arc. Pour plus d’informations, consultez la [vue d’ensemble des extensions de machines virtuelles pour les serveurs avec Arc](../azure-arc/servers/manage-vm-extensions.md).

### <a name="july-2020"></a>Juillet 2020

### <a name="introduced-public-preview-of-private-link-support-in-automation"></a>Introduction de la prise en charge de Private Link en préversion publique dans Automation

**Type :** Nouvelle fonctionnalité

Utilisez Azure Private Link pour connecter en toute sécurité des réseaux virtuels à Azure Automation en utilisant des points de terminaison privés. Pour plus d'informations, lisez cette [annonce](https://azure.microsoft.com/updates/public-preview-private-link-azure-automation-is-now-available/).

### <a name="hybrid-runbook-worker-support-for-windows-server-2008-r2"></a>Prise en charge de la fonctionnalité Runbook Worker hybride pour Windows Server 2008 R2

**Type :** Nouvelle fonctionnalité

La fonctionnalité Automation Runbook Worker hybride prend en charge le système d’exploitation Windows Server 2008 R2. Consultez [Systèmes d’exploitation pris en charge](automation-windows-hrw-install.md#supported-windows-operating-system) pour voir les mises à jour de la documentation en lien avec ces changements.

### <a name="update-management-support-for-windows-server-2008-r2"></a>Prise en charge de la fonctionnalité Update Management pour Windows Server 2008 R2

**Type :** Nouvelle fonctionnalité

Update Management prend en charge l’évaluation et la mise à jour corrective du système d’exploitation Windows Server 2008 R2. Consultez [Systèmes d’exploitation pris en charge](update-management/operating-system-requirements.md) pour voir les mises à jour de la documentation en lien avec ces changements.

### <a name="automation-diagnostic-logs-schema-update"></a>Mise à jour du schéma des journaux de diagnostic Automation

**Type :** Nouvelle fonctionnalité

Le schéma des données des journaux Azure Automation a été modifié dans le service Log Analytics. Pour en savoir plus, consultez [Transférer des données de travaux Azure Automation aux journaux Azure Monitor](automation-manage-send-joblogs-log-analytics.md#filter-job-status-output-converted-into-a-json-object).

### <a name="azure-lighthouse-supports-automation-update-management"></a>Azure Lighthouse prend en charge Automation Update Management

**Type :** Nouvelle fonctionnalité

Azure Lighthouse permet la gestion déléguée des ressources avec Update Management pour les fournisseurs de services et les clients. En savoir plus [ici](https://azure.microsoft.com/blog/how-azure-lighthouse-enables-management-at-scale-for-service-providers/).

## <a name="june-2020"></a>Juin 2020

### <a name="automation-and-update-management-availability-in-the-us-gov-arizona-region"></a>Automation et Update Management disponibles dans la région US Gov Arizona

**Type :** Nouvelle fonctionnalité

Le compte Automation et Update Management sont disponibles dans la région US Gov Arizona. Pour plus d'informations, lisez cette [annonce](https://azure.microsoft.com/updates/azure-automation-generally-available-in-usgov-arizona-region/).

### <a name="hybrid-runbook-worker-onboarding-script-updated-to-use-az-modules"></a>Mise à jour du script d’intégration de Runbook Worker hybride pour utiliser les modules Az

**Type :** Nouvelle fonctionnalité

Le runbook New-OnPremiseHybridWorker a été mis à jour pour prendre en charge les modules Az. Pour plus d’informations, consultez le package dans [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.7).

### <a name="update-management-availability-in-china-east-2"></a>Disponibilité de la fonctionnalité Update Management dans la région Chine Est 2

**Type :** Nouvelle fonctionnalité

Le mappage des régions Azure Automation a été mis à jour pour prendre en charge la fonctionnalité Update Management dans la région Chine Est 2. Consultez [Mappage de régions prises en charge](how-to/region-mappings.md#supported-mappings) pour voir les mises à jour de la documentation en lien avec ce changement.

## <a name="may-2020"></a>Mai 2020

### <a name="updated-automation-service-dns-records-from-region-specific-to-automation-account-specific-urls"></a>Mise à jour des enregistrements DNS du service Automation pour utiliser des URL propres au compte Automation à la place d’URL propres à la région

**Type :** Nouvelle fonctionnalité

Les enregistrements DNS dans Azure Automation ont été mis à jour pour prendre en charge Private Link. Pour plus d'informations, lisez cette [annonce](https://azure.microsoft.com/updates/azure-automation-updateddns-records/).

### <a name="added-capability-to-keep-automation-runbooks-and-dsc-scripts-encrypted-by-default"></a>Ajout de la fonctionnalité de conservation des scripts DSC et des runbooks Automation chiffrés par défaut

**Type :** Nouvelle fonctionnalité

En plus d’améliorer la sécurité des ressources, les scripts DSC et les runbooks sont également chiffrés pour renforcer la sécurité d’Azure Automation.

## <a name="april-2020"></a>Avril 2020

### <a name="retirement-of-the-automation-watcher-task"></a>Mise hors service de la tâche Automation Watcher

**Type :** Modification planifiée

Azure Logic Apps est maintenant la méthode recommandée et prise en charge pour superviser des événements, planifier des tâches récurrentes et déclencher des actions. Il n’y aura pas d’autres investissements dans la fonctionnalité de tâche Watcher. Pour en savoir plus, consultez [Planifier et exécuter des tâches automatisées récurrentes avec Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

## <a name="march-2020"></a>Mars 2020

### <a name="support-for-impact-level-5-il5-compute-isolation-in-azure-commercial-and-government-cloud"></a>Prise en charge de l’isolation de calcul Impact Level 5 (IL5) dans le cloud Azure Commercial et Government

**Tapez :**

Le Runbook Worker hybride d’Azure Automation peut être utilisé dans Azure Government pour prendre en charge les charges de travail Impact Level 5. Pour en savoir plus, consultez notre [documentation](automation-hybrid-runbook-worker.md#support-for-impact-level-5-il5).

## <a name="february-2020"></a>Février 2020

### <a name="introduced-support-for-azure-virtual-network-service-tags"></a>Introduction de la prise en charge des étiquettes de service de réseau virtuel Azure

**Type :** Nouvelle fonctionnalité

Avec la prise en charge dans Automation des étiquettes de service, il est possible d’autoriser ou de refuser le trafic pour le service Automation, dans certains scénarios. Pour plus d’informations, consultez la [documentation](automation-hybrid-runbook-worker.md#service-tags).

### <a name="enable-tls-12-support-for-azure-automation-service"></a>Activer la prise en charge de TLS 1.2 pour le service Azure Automation

**Type :** Modification planifiée

Azure Automation prend entièrement en charge TLS 1.2 et tous les appels clients (via les webhooks, les nœuds DSC et le Worker hybride). TLS 1.1 et TLS 1.0 continuent d’être pris en charge pour assurer la compatibilité descendante avec les clients plus anciens, le temps que les clients normalisent leur installation et migrent entièrement vers TLS 1.2.

## <a name="january-2020"></a>Janvier 2020

### <a name="introduced-public-preview-of-customer-managed-keys-for-azure-automation"></a>Introduction de la préversion publique des clés gérées par les clients pour Azure Automation

**Type :** Nouvelle fonctionnalité

Les clients peuvent gérer et sécuriser le chiffrement des ressources Azure Automation à l’aide de clés qu’ils gèrent eux-mêmes. Pour plus d’informations, consultez [Utilisation de clés gérées par le client](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account).

### <a name="retirement-of-azure-service-management-asm-rest-apis-for-azure-automation"></a>Mise hors service des API REST Gestion des services Azure (ASM) pour Azure Automation

**Type :** Mise hors service

Les API REST d’Azure Service Management (ASM) pour Azure Automation seront mises hors service et ne seront plus prises en charge à partir du 30 janvier 2020. Pour en savoir plus, lisez cette [annonce](https://azure.microsoft.com/updates/azure-automation-service-management-rest-apis-are-being-retired-april-30-2019/).

## <a name="next-steps"></a>Étapes suivantes

Pour contribuer à la documentation Azure Automation, consultez le [guide du contributeur Docs](/contribute/).
