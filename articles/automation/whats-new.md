---
title: Nouveautés d’Azure Automation
description: Chaque mois, Azure Automation fait l’objet de mises à jour importantes.
services: automation
ms.subservice: ''
ms.topic: overview
ms.date: 11/02/2021
ms.custom: references_regions
ms.openlocfilehash: ea18171bdd957781e22743c3e59690fc8d4703f6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432431"
---
# <a name="whats-new-in-azure-automation"></a>Nouveautés d’Azure Automation

Azure Automation bénéficie d’améliorations en continu. Pour vous informer des développements les plus récents, cet article détaille les thèmes suivants :

- Versions les plus récentes
- Problèmes connus
- Résolution des bogues

Cette page est mise à jour tous les mois. Donc, consultez-la régulièrement. Si vous recherchez des éléments antérieurs aux six derniers mois, vous les trouverez dans l’[Archive des Nouveautés d’Azure Automation](whats-new-archive.md).

## <a name="october-2021"></a>Octobre 2021

### <a name="preview-support-for-hybrid-runbook-worker-extension-for-azure-vms-and-arc-enabled-servers"></a>Prise en charge en préversion de l’extension Runbook Worker hybride pour les machines virtuelles Azure et les serveurs Azure Arc

**Type :** Nouvelle fonctionnalité

Azure Automation a publié l’intégration native de Runbook Worker hybride utilisateur pour les machines virtuelles Azure et pour les machines non Azure par le biais de serveurs Azure Arc. Lisez cette [annonce](https://azure.microsoft.com/updates/automation-user-hybrid-extension-support) pour plus d’informations.

### <a name="preview-support-for-azure-active-directory-authentication"></a>Prise en charge en préversion de l’authentification Azure Active Directory

**Type :** Nouvelle fonctionnalité

Azure Automation ajouté une fonctionnalité de sécurité critique avec prise en charge de l’authentification Azure AD pour tous les points de terminaison publics du service Automation. La fonctionnalité a été implémentée par le biais de l’extension Runbook Worker hybride, qui prend en charge les machines virtuelles Azure et les serveurs Azure Arc.

Cela supprime la dépendance vis-à-vis des certificats et vous permet de répondre à vos exigences strictes en matière d’audit et de conformité en n’utilisant pas de méthodes d’authentification locales. Lisez cette [annonce](https://azure.microsoft.com/updates/automation-user-hybrid-extension-support) pour plus d’informations.

### <a name="source-control-enabled-to-use-managed-identities"></a>Contrôle de code source activé pour utiliser des identités managées

**Type :** Nouvelle fonctionnalité

L’intégration du contrôle de code source dans Azure Automation peut désormais utiliser des [identités managées](automation-security-overview.md#managed-identities) à la place d’un compte d’identification. Pour plus d’informations, consultez les [prérequis de l’intégration du contrôle de la source](source-control-integration.md#prerequisites).

## <a name="september"></a>Septembre

### <a name="support-for-az-modules-by-default"></a>Prise en charge des modules Az par défaut

**Type :** Nouvelle fonctionnalité

Azure Automation prend désormais en charge les modules Az par défaut. Les comptes Automation nouvellement créés incluent la version la plus récente des modules Az (6.4.0) par défaut. Automation comprend également une option dans le portail Azure, **Charger des modules Az**, vous permettant de mettre à jour les modules Az dans vos comptes Automation existants. Lisez cette [annonce](https://azure.microsoft.com/updates/azure-automation-az-module-support) pour plus d’informations.

## <a name="august-2021"></a>Août 2021

### <a name="azure-policy-guest-configuration"></a>Azure Policy Guest Configuration

**Type :** Modification planifiée

Les clients doivent évaluer et planifier la migration depuis Azure Automation State Configuration vers la configuration d’invité d’Azure Policy. Pour plus d’informations, consultez [Configuration d’invité d’Azure Policy](../governance/policy/concepts/guest-configuration.md).

## <a name="july-2021"></a>Juillet 2021

### <a name="preview-support-for-user-assigned-managed-identity"></a>Prise en charge en préversion d’une identité managée affectée par l’utilisateur

**Type :** Nouvelle fonctionnalité

Azure Automation prend désormais en charge les [identités managées affectées par l’utilisateur](automation-secure-asset-encryption.md) pour les travaux cloud dans les régions Azure globales, Azure Government et Azure Chine. Lisez cette [annonce](https://azure.microsoft.com/updates/azure-automation-user-assigned-identities/) pour plus d’informations.

### <a name="general-availability-of-customer-managed-keys-for-azure-automation"></a>Disponibilité générale des clés gérées par le client pour Azure Automation

**Type :** Nouvelle fonctionnalité

Les clients peuvent gérer et sécuriser le chiffrement des ressources Azure Automation à l’aide de clés qu’ils gèrent eux-mêmes. Grâce à l’introduction des clés gérées par le client, vous pouvez compléter le chiffrement par défaut avec une couche de chiffrement supplémentaire à l’aide de clés que vous créez et gérez dans Azure Key Vault. Ce niveau supplémentaire de chiffrement doit vous aider à répondre aux besoins de votre organisation en matière de réglementation ou de conformité.

Pour plus d’informations, consultez [Utilisation de clés gérées par le client](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account).

## <a name="june-2021"></a>Juin 2021

### <a name="security-update-for-log-analytics-contributor-role"></a>Mise à jour de sécurité pour le rôle Contributeur Log Analytics

**Type :** Modification planifiée

Microsoft envisage de supprimer les droits de compte Automation du rôle Contributeur Log Analytics. Actuellement, le rôle [Contributeur Log Analytics](./automation-role-based-access-control.md#log-analytics-contributor) intégré peut élever les privilèges du rôle [Contributeur](./../role-based-access-control/built-in-roles.md#contributor) d’abonnement. Les comptes d’identification de compte Automation étant initialement configurés avec des droits Contributeur sur l’abonnement, ils peuvent être utilisés par un attaquant pour créer de nouveaux runbooks et exécuter du code en tant que Contributeur sur l’abonnement.

Compte tenu de ce risque de sécurité, nous vous recommandons de ne pas utiliser le rôle Contributeur Log Analytics pour exécuter des tâches Automation. Créez plutôt le rôle personnalisé Contributeur Azure Automation et utilisez-le pour les actions associées au compte Automation. Pour connaître les étapes d’implémentation, consultez [Rôle Contributeur Azure Automation personnalisé](./automation-role-based-access-control.md#custom-azure-automation-contributor-role).

### <a name="support-for-automation-and-state-configuration-available-in-west-us-3"></a>Prise en charge d’Automation et de State Configuration disponible dans la région USA Ouest 3

**Type :** Nouvelle fonctionnalité

Pour plus d’informations, consultez [Résidence des données dans Azure](https://azure.microsoft.com/global-infrastructure/data-residency/) et sélectionnez votre région dans la liste déroulante.

## <a name="may-2021"></a>Mai 2021

### <a name="startstop-vms-during-off-hours-v1"></a>Start/Stop VMs during off-hours (v1)

**Type :** Modification planifiée

Start/Stop VMs during off-hours (v1) sera déprécié le 21 mai 2022. Les clients doivent évaluer et planifier la migration vers Start/Stop VMs v2 (préversion). Pour plus d’informations, consultez [Vue d’ensemble de Start/Stop VMs v2](../azure-functions/start-stop-vms/overview.md) (préversion).

## <a name="april-2021"></a>Avril 2021

### <a name="support-for-update-management-and-change-tracking"></a>Pris en charge d’Update Management et de Change Tracking

**Type :** Nouvelle fonctionnalité

Le mappage des régions a été mis à jour pour prendre en charge Update Management et Change Tracking dans les régions Norvège Est, Émirats arabes unis Nord, USA Centre Nord, Brésil Sud et Corée Centre. Pour plus d’informations, consultez [Mappages pris en charge](./how-to/region-mappings.md#supported-mappings).

### <a name="support-for-system-assigned-managed-identities"></a>Prise en charge des identités managées affectées par le système

**Type :** Nouvelle fonctionnalité

Azure Automation prend désormais en charge les [identités managées affectées par le système](./automation-security-overview.md#managed-identities) pour les travaux cloud et hybrides dans les régions Azure globales et Azure Government. Lisez cette [annonce](https://azure.microsoft.com/updates/azure-automation-system-assigned-managed-identities/) pour plus d’informations.

## <a name="next-steps"></a>Étapes suivantes

Pour contribuer à la documentation Azure Automation, consultez le [guide du contributeur Docs](/contribute/).
