---
title: Vue d’ensemble de la fonctionnalité Start/Stop VMs v2 (préversion)
description: Cet article décrit la version 2 de la fonctionnalité Start/Stop VMs (préversion), qui démarre ou arrête les machines virtuelles Azure Resource Manager et les machines virtuelles classiques selon une planification.
ms.topic: conceptual
ms.service: azure-functions
ms.subservice: start-stop-vms
ms.date: 06/25/2021
ms.openlocfilehash: 7edd651e5c6a52f707cfebd87310e0a104fef0d4
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401204"
---
# <a name="startstop-vms-v2-preview-overview"></a>Vue d’ensemble de la fonctionnalité Start/Stop VMs v2 (préversion)

La fonctionnalité Start/Stop VMs v2 (préversion) démarre ou arrête les machines virtuelles Azure sur plusieurs abonnements. Elle démarre ou arrête les machines selon une planification définie par l’utilisateur, fournit des insights via [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) et peut envoyer des notifications à l’aide de [groupes d’actions](../../azure-monitor/alerts/action-groups.md). La fonctionnalité peut gérer les machines virtuelles Azure Resource Manager et les machines virtuelles classiques dans la plupart des scénarios.

Cette nouvelle version de Start/Stop VMs v2 (préversion) fournit une option d’automatisation low-cost décentralisée pour les clients qui souhaitent optimiser les coûts de leurs machines virtuelles. Elle offre les mêmes fonctionnalités que la [version d’origine](../../automation/automation-solution-vm-management.md) disponible avec Azure Automation, mais est conçue pour tirer parti des technologies les plus récentes dans Azure.

## <a name="overview"></a>Vue d’ensemble

La fonctionnalité Start/Stop VMs v2 (préversion) a été repensée et ne dépend pas des journaux Azure Automation ou Azure Monitor, comme requis par la [version précédente](../../automation/automation-solution-vm-management.md). Cette version s’appuie sur [Azure Functions](../../azure-functions/functions-overview.md) pour gérer l’exécution du démarrage et de l’arrêt des machines virtuelles.

Une identité managée est créée dans Azure Active Directory (Azure AD) pour cette application Azure Functions et permet à la fonctionnalité Start/Stop VMs v2 (préversion) d’accéder facilement à d’autres ressources protégées par Azure AD, telles que des applications logiques et des machines virtuelles Azure. Pour plus d’informations sur les identités managées dans Azure AD, consultez [Identités managées pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Une fonction de point de terminaison de déclencheur HTTP est créée pour prendre en charge les scénarios de planification et de séquence inclus avec la fonctionnalité, comme indiqué dans le tableau suivant.

|Nom |Déclencheur |Description |
|-----|--------|------------|
|Planifié |HTTP |Cette fonction concerne à la fois les scénarios planifiés et les scénarios séquencés (différenciés par le schéma de charge utile). Il s’agit de la fonction de point d’entrée appelée à partir de l’application logique et qui prend la charge utile pour traiter l’opération de démarrage ou d’arrêt de la machine virtuelle. |
|AutoStop |HTTP |Cette fonction prend en charge le scénario **AutoStop**, qui est la fonction de point d’entrée appelée à partir d’une application logique.|
|AutoStopVM |HTTP |Cette fonction est déclenchée automatiquement par l’alerte de machine virtuelle lorsque la condition d’alerte est définie sur true.|
|VirtualMachineRequestOrchestrator |File d'attente |Cette fonction obtient les informations de charge utile de la fonction **Scheduled** et orchestre les demandes de démarrage et d’arrêt de la machine virtuelle.|
|VirtualMachineRequestExecutor |File d'attente |Cette fonction effectue l’opération de démarrage et d’arrêt réelle sur la machine virtuelle.|
|CreateAutoStopAlertExecutor |File d'attente |Cette fonction obtient les informations de charge utile de la fonction **AutoStop** pour créer l’alerte sur la machine virtuelle.|
|HeartBeatAvailabilityTest |Minuteur |Cette fonction analyse la disponibilité des fonctions HTTP principales.|
|CostAnalyticsFunction |Minuteur |Cette fonction calcule chaque mois le coût d’exécution de la solution de démarrage/arrêt v2.|
|SavingsAnalyticsFunction |Minuteur |Cette fonction calcule chaque mois les économies totales réalisées par la solution de démarrage/arrêt v2.|
|VirtualMachineSavingsFunction |File d'attente |Cette fonction calcule les économies réelles que la solution de démarrage/arrêt v2 a permis de réaliser sur une machine virtuelle.|

Par exemple, la fonction de déclencheur HTTP **Scheduled** est utilisée pour gérer les scénarios de planification et de séquence. De même, la fonction de déclencheur HTTP **AutoStop** gère le scénario d’arrêt automatique.

Les fonctions de déclencheur basées sur la file d’attente sont requises pour la prise en charge de cette fonctionnalité. Tous les déclencheurs basés sur une minuterie sont utilisés pour effectuer le test de disponibilité et surveiller l’intégrité du système.

 [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) permet de configurer et de gérer les planifications de démarrage et d’arrêt de la machine virtuelle en appelant la fonction à l’aide d’une charge utile JSON. Par défaut, pendant le déploiement initial, il crée un total de cinq applications logiques pour les scénarios suivants :

- **Planification** : Les actions de démarrage et d’arrêt sont basées sur une planification que vous spécifiez sur les machines virtuelles Azure Resource Manager et classiques. **ststv2_vms_Scheduled_start** et **ststv2_vms_Scheduled_stop** configurent le démarrage et l’arrêt planifiés.

- **Séquençage** : Les actions de démarrage et d’arrêt sont basées sur une planification ciblant les machines virtuelles avec des balises de séquencement prédéfinies. Seules deux balises nommées sont prises en charge : **sequencestart** et **sequencestop**. **ststv2_vms_Sequenced_start** et **ststv2_vms_Sequenced_stop** configurent le démarrage et l’arrêt séquencés. 

    La façon correcte d’utiliser la fonctionnalité de séquence est de créer une balise nommée **sequencestart** sur chaque machine virtuelle que vous souhaitez démarrer dans une séquence. La valeur de balise doit être un entier compris entre 1 et N pour chaque machine virtuelle dans l’étendue respective. La balise est facultative et, si elle n’est pas présente, la machine virtuelle ne participera tout simplement pas au séquencement. Le même critère s’applique à l’arrêt des machines virtuelles, seul le nom de la balise est différent : utilisez **sequencestop** dans ce cas. Vous devez configurer les deux balises dans chaque machine virtuelle pour obtenir une action de démarrage et d’arrêt. Si deux machines virtuelles ou plus partagent la même valeur d’étiquette, ces machines virtuelles seront démarrées ou arrêtées en même temps.

    Par exemple, le tableau suivant montre que les actions de démarrage et d’arrêt sont traitées dans l’ordre croissant de la valeur de l’étiquette.

    :::image type="content" source="media/overview/sequence-settings-table.png" alt-text="Tableau illustrant des exemples de balises de paramètres de séquence":::

    > [!NOTE]
    > Ce scénario ne prend en charge que les machines virtuelles Azure Resource Manager.

- **Autostop** : Cette fonctionnalité est utilisée uniquement pour effectuer une action d’arrêt sur les machines virtuelles Azure Resource Manager et classiques en fonction de leur utilisation de l’UC. Il peut également s’agir d’une *action* basée sur une planification, qui crée des alertes sur les machines virtuelles et, sur la base de la condition, l’alerte est déclenchée pour exécuter l’action d’arrêt. **ststv2_vms_AutoStop** configure la fonctionnalité d’arrêt automatique.

Chaque action de démarrage/d’arrêt prend en charge l’affectation d’un ou de plusieurs abonnements, groupes de ressources ou d’une liste de machines virtuelles.

Un compte de stockage Azure, qui est requis par Functions, est également utilisé par Start/Stop VMs v2 (préversion) pour deux raisons :

   - Utilise le Stockage Table Azure pour stocker les métadonnées de l’opération d’exécution (autrement dit, l’action Démarrer/arrêter une machine virtuelle).

   - Utilise le Stockage File d’attente Azure pour prendre en charge les déclencheurs Azure Functions basés sur une file d’attente.

Toutes les données de télémétrie, c’est-à-dire les journaux de suivi provenant de l’exécution de l’application de fonction, sont envoyées à votre instance Application Insights connectée. Vous pouvez afficher les données de télémétrie stockées dans Application Insights à partir d’un ensemble de visualisations prédéfinies présentées dans un [tableau de bord Azure](../../azure-portal/azure-portal-dashboards.md) partagé.

:::image type="content" source="media/overview/shared-dashboard-sml.png" alt-text="Tableau de bord de l’état partagé du démarrage et de l’arrêt des machines virtuelles":::

Les notifications par e-mail sont également envoyées à la suite des actions effectuées sur les machines virtuelles.

## <a name="new-releases"></a>Nouvelles versions

Quand une nouvelle version de Start/Stop VMs v2 (préversion) est publiée, votre instance est mise à jour automatiquement sans que vous deviez effectuer un redéploiement manuel.

## <a name="supported-scoping-options"></a>Options d’étendue prises en charge

### <a name="subscription"></a>Abonnement

L’étendue au niveau d’un abonnement peut être utilisée lorsque vous devez exécuter l’action de démarrage et d’arrêt sur toutes les machines virtuelles d’un abonnement entier, et vous pouvez sélectionner plusieurs abonnements si nécessaire.  

Vous pouvez également spécifier une liste de machines virtuelles à exclure et elles seront ignorées de l’action. Vous pouvez également utiliser des caractères génériques pour spécifier tous les noms qui peuvent être ignorés simultanément.

### <a name="resource-group"></a>Groupe de ressources

L’étendue au niveau d’un groupe de ressources peut être utilisée lorsque vous devez exécuter l’action de démarrage et d’arrêt sur toutes les machines virtuelles en spécifiant un ou plusieurs noms de groupes de ressources, et dans un ou plusieurs abonnements.

Vous pouvez également spécifier une liste de machines virtuelles à exclure et elles seront ignorées de l’action. Vous pouvez également utiliser des caractères génériques pour spécifier tous les noms qui peuvent être ignorés simultanément.

### <a name="vmlist"></a>VMList

La spécification d’une liste de machines virtuelles peut être utilisée lorsque vous devez exécuter l’action de démarrage et d’arrêt sur un ensemble spécifique de machines virtuelles et sur plusieurs abonnements. Cette option ne prend pas en charge la spécification d’une liste de machines virtuelles à exclure.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’un compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/).

- L’autorisation [Contributeur](../../role-based-access-control/built-in-roles.md#contributor) a été accordée à votre compte dans l’abonnement.

- La fonctionnalité Start/Stop VMs v2 (préversion) est disponible dans toutes les régions cloud Azure internationales et US Government répertoriées dans la page [Produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=functions) pour Azure Functions.

## <a name="next-steps"></a>Étapes suivantes

Pour déployer cette fonctionnalité, consultez [Déployer Start/Stop v2](deploy.md) (préversion).
