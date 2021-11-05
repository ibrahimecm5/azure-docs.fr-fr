---
title: Comprendre la fonctionnalité de configuration invité de la Stratégie Azure
description: Découvrez comment la Stratégie Azure utilise la fonctionnalité de configuration invité pour auditer ou configurer les paramètres à l’intérieur des machines virtuelles.
ms.date: 07/15/2021
ms.topic: conceptual
ms.openlocfilehash: 6a40469b6cd391672ba953bac37402285ac4a097
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131040413"
---
# <a name="understand-the-guest-configuration-feature-of-azure-policy"></a>Comprendre la fonctionnalité de configuration invité de la Stratégie Azure

La fonctionnalité de configuration d’invité d’Azure Policy fournit des fonctionnalités natives pour auditer ou configurer des paramètres de système d’exploitation en tant que code, à la fois pour les machines s’exécutant dans Azure et les machines hybrides [avec Arc](../../../azure-arc/servers/overview.md).
La fonctionnalité peut être utilisée directement par machine ou être orchestrée à grande échelle par Azure Policy.

Les ressources de configuration dans Azure sont conçues comme une [ressource d’extension](../../../azure-resource-manager/management/extension-resource-types.md).
Vous pouvez imaginer chaque configuration comme un ensemble de propriétés supplémentaires pour la machine. Les configurations peuvent inclure des paramètres comme :

- Paramètres du système d’exploitation
- La configuration ou la présence de l’application
- Paramètres d'environnement

Les configurations sont différentes des définitions de stratégie. La configuration d’invité utilise Azure Policy pour attribuer dynamiquement des configurations aux machines. Vous pouvez également attribuer des configurations à des machines [manuellement](guest-configuration-assignments.md#manually-creating-guest-configuration-assignments) ou à l’aide d’autres services Azure comme [AutoManage](../../../automanage/automanage-virtual-machines.md).

Vous trouverez des exemples de chaque scénario dans le tableau suivant.

| Type | Description | Exemple de scénario |
| - | - | - |
| [Gestion des configurations](guest-configuration-assignments.md) | Vous souhaitez la représentation complète d’un serveur, en tant que code dans le contrôle de code source. Le déploiement doit inclure les propriétés du serveur (taille, réseau, stockage) et la configuration des paramètres du système d’exploitation et de l’application. | « Cet ordinateur doit être un serveur web configuré pour héberger mon site Web. » |
| [Conformité](../assign-policy-portal.md) | Vous souhaitez auditer ou déployer des paramètres sur tous les ordinateurs de l’étendue, soit de manière réactive vers des machines existantes, soit de manière proactive vers les nouveaux ordinateurs à mesure qu’ils sont déployés. | « Tous les ordinateurs doivent utiliser TLS 1.2. Auditez les machines existantes afin de pouvoir les modifier où cela est nécessaire, de manière contrôlée, à l’échelle. Pour les nouveaux ordinateurs, appliquez le paramètre lors de leur déploiement. » |

Les résultats par paramètre des configurations peuvent être affichés sur la [Page des affectations d’invités](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration) ou si la configuration est orchestrée par une affectation Azure Policy, en cliquant sur le lien « Dernière ressource évaluée » sur la [page « Détails de conformité »](../how-to/determine-non-compliance.md#view-configuration-assignment-details-at-scale).

[Un guide vidéo de ce document est disponible](https://youtu.be/t9L8COY-BkM). (mise à jour prochaine)

## <a name="enable-guest-configuration"></a>Activer la configuration invité

Pour vérifier l’état des machines dans votre environnement, y compris les machines dans les serveurs Azure et Arc, examinez les informations suivantes.

## <a name="resource-provider"></a>Fournisseur de ressources

Avant de pouvoir utiliser la fonctionnalité de configuration invité de la Stratégie Azure, vous devez inscrire le fournisseur de ressources `Microsoft.GuestConfiguration`. Si l’affectation d’une stratégie de configuration invité est effectuée via le portail, ou si l’abonnement est inscrit dans le Centre de sécurité Azure, le fournisseur de ressources est inscrit automatiquement. Vous pouvez l’inscrire manuellement via le [portail](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [Azure PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) ou [Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli).

## <a name="deploy-requirements-for-azure-virtual-machines"></a>Configuration requise pour le déploiement de machines virtuelles Azure

Pour gérer les paramètres à l’intérieur d’une machine, une [extension de machine virtuelle](../../../virtual-machines/extensions/overview.md) est activée et la machine doit disposer d’une identité managée par le système. L’extension télécharge l’affectation de configuration invité applicable et les dépendances correspondantes. L’identité est utilisée pour authentifier la machine lorsqu’elle lit et écrit dans le service de configuration invité. L'extension n'est pas nécessaire pour les serveurs avec Arc car elle est incluse dans l’Agent de la machine connectée Arc.

> [!IMPORTANT]
> L’extension de la configuration invité ainsi qu’une identité managée sont requises pour l’a gestion des machines virtuelles Azure.

Pour déployer l’extension à l’échelle sur toutes les machines, attribuez l’initiative de stratégie `Deploy prerequisites to enable guest configuration policies on virtual machines`
à un groupe de gestion, un abonnement ou un groupe de ressources contenant les machines que vous envisagez de gérer.

Si vous préférez déployer l’extension et l’identité managée sur une seule machine, suivez les instructions pour chacune d’elles :

- [Vue d’ensemble de l’extension Azure Policy Guest Configuration](../../../virtual-machines/extensions/guest-configuration.md)
- [Configurer des identités managées pour ressources Azure sur une machine virtuelle en utilisant le portail Azure](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

Pour utiliser les packages de configuration invité qui appliquent des configurations, l’extension de configuration invité de machine virtuelle Azure version **1.29.24** ou ultérieure, est requise.

### <a name="limits-set-on-the-extension"></a>Limites définies sur l’extension

Pour limiter l’impact de l’extension sur les applications qui s’exécutent à l’intérieur de la machine, la configuration invité ne peut pas dépasser plus de 5 % de la capacité du processeur. Cette limitation existe à la fois pour les définitions intégrées et personnalisées. Il en va de même pour le service de configuration invité de l’agent de la Machine connectée Arc.

### <a name="validation-tools"></a>Outils de validation

À l’intérieur de la machine, l’agent de la configuration invité utilise des outils locaux pour exécuter les tâches.

Le tableau suivant affiche une liste des outils locaux utilisés sur chaque système d’exploitation pris en charge. Pour le contenu intégré, la configuration d’invité gère automatiquement le chargement de ces outils.

|Système d’exploitation|Outil de validation|Notes|
|-|-|-|
|Windows|[Configuration de l’état désiré PowerShell](/powershell/scripting/dsc/overview/overview) v3| Chargé dans un dossier utilisé uniquement par Azure Policy. Aucun conflit avec Windows PowerShell DSC. PowerShell Core n’est pas ajouté au chemin d’accès système.|
|Linux|[Configuration de l’état désiré PowerShell](/powershell/scripting/dsc/overview/overview) v3| Chargé dans un dossier utilisé uniquement par Azure Policy. PowerShell Core n’est pas ajouté au chemin d’accès système.|
|Linux|[Chef InSpec](https://www.chef.io/inspec/) | Installe Chef InSpec version 2.2.61 dans l’emplacement par défaut et est ajouté au chemin d’accès système. Des dépendances pour le package InSpec, y compris Ruby et Python, sont également installées. |

### <a name="validation-frequency"></a>Fréquence de validation

L’agent de la configuration invité vérifie les affectations d’invités nouvelles ou modifiées toutes les 5 minutes. Une fois l’affectation d’invité reçue, les paramètres de cette configuration sont revérifiés à intervalle de 15 minutes. Si plusieurs configurations sont affectées, chacune d’elles est évaluée de manière séquentielle. Les configurations de longue durée ont un impact sur l’intervalle pour toutes les configurations, car la suivante ne s’exécute pas tant que la configuration précédente n’est pas terminée.

Les résultats sont envoyés au service de la configuration invité dès la fin de l’audit.
Lorsqu'un [déclencheur d’évaluation](../how-to/get-compliance-data.md#evaluation-triggers) de stratégie intervient, l'état de la machine est consigné dans le fournisseur de ressources de la configuration invité. Avec cette mise à jour, Azure Policy évalue alors les propriétés Azure Resource Manager. Une évaluation à la demande de la Stratégie Azure récupère la valeur la plus récente du fournisseur de ressources de la configuration invité. Toutefois, il ne déclenche pas une nouvelle activité au sein de la machine. L’état est ensuite écrit dans le Graphe de ressources Azure.

## <a name="supported-client-types"></a>Types de clients pris en charge

Les définitions de stratégie de la Configuration invité sont incluses dans les nouvelles versions. Les versions antérieures des systèmes d’exploitation disponibles dans Place de marché Azure sont exclues si le client Guest Configuration n’est pas compatible. Le tableau suivant affiche une liste des systèmes d’exploitation pris en charge sur des images Azure.
« .x » est utilisé pour représenter les nouvelles versions mineures des distributions Linux.

|Serveur de publication|Nom|Versions|
|-|-|-|
|Amazon|Linux|2|
|Canonical|Serveur Ubuntu|14.04 - 20.x|
|Credativ|Debian|8 - 10.x|
|Microsoft|Windows Server|2012 - 2019|
|Microsoft|Client Windows|Windows 10|
|Oracle|Oracle-Linux|7.x, 8.x|
|OpenLogic|CentOS|7.3 - 8.x|
|Red Hat|Red Hat Enterprise Linux\*|7.4 - 8.x|
|SUSE|SLES|12 SP3-SP5, 15.x|

\* Red Hat CoreOS n’est pas pris en charge.

Les images de machine virtuelle personnalisées sont prises en charge par les définitions de stratégie de la Configuration invité, dans la mesure où il s’agit d’un des systèmes d’exploitation répertoriés dans le tableau ci-dessus.

## <a name="network-requirements"></a>Conditions requises en matière de réseau

Dans Azure, les machines virtuelles peuvent utiliser leur carte réseau locale ou une liaison privée pour communiquer avec le service de la configuration invité.

Les machines Azure Arc se connectent à l’aide de l’infrastructure réseau locale pour atteindre les services Azure et signaler l’état de conformité.

### <a name="communicate-over-virtual-networks-in-azure"></a>Communiquer via des réseaux virtuels dans Azure

Pour communiquer avec le fournisseur de ressources de la configuration invité dans Azure, les machines nécessitent un accès sortant vers des centres de données Azure sur le port **443**. Si un réseau dans Azure n’autorise pas le trafic sortant, configurez des exceptions à l’aide de règles du [Groupe de sécurité réseau](../../../virtual-network/manage-network-security-group.md#create-a-security-rule). Les [balises de service](../../../virtual-network/service-tags-overview.md) « AzureArcInfrastructure » et « Stockage » peuvent être utilisées pour référencer les services de la configuration invité et de Stockage plutôt que de gérer manuellement la [liste des plages d’adresses IP](https://www.microsoft.com/download/details.aspx?id=56519) pour les centres de données Azure. Les deux étiquettes sont nécessaires, car les packages de contenu de la configuration invité sont hébergés par le Stockage Azure.

### <a name="communicate-over-private-link-in-azure"></a>Communiquer via une liaison privée dans Azure

Les machines virtuelles peuvent utiliser une [liaison privée](../../../private-link/private-link-overview.md) pour la communication avec le service de la configuration invité. Appliquez la balise avec le nom `EnablePrivateNetworkGC` et la valeur `TRUE` pour activer cette fonctionnalité. La balise peut être appliquée avant ou après l’application des définitions de stratégie de la configuration invité à la machine.

Le trafic est acheminé à l’aide de l’[IP publique virtuelle](../../../virtual-network/what-is-ip-address-168-63-129-16.md) d’Azure pour établir un canal sécurisé et authentifié avec les ressources de la plateforme Azure.

### <a name="azure-arc-enabled-servers"></a>Serveurs avec Azure Arc

Les nœuds situés en dehors d’Azure et qui sont connectés par le biais d’Azure Arc requièrent une connexion au service de la configuration invité. Des détails sur la configuration requise du réseau et du proxy sont fournis dans la [documentation Azure Arc](../../../azure-arc/servers/overview.md).

Pour les serveurs avec Arc dans des centres de données privés, autorisez le trafic à l’aide des modèles suivants :

- Port : seul le port TCP 443 est nécessaire pour l’accès Internet sortant
- URL globale : `*.guestconfiguration.azure.com`

## <a name="assigning-policies-to-machines-outside-of-azure"></a>Attribution de stratégies à des machines en dehors d’Azure

Les définitions de stratégie d’Audit disponibles pour la configuration invité incluent le type de ressource **Microsoft.HybridCompute/machines**. Toutes les machines intégrées à [Azure Arc pour serveurs](../../../azure-arc/servers/overview.md) qui relèvent du champ d’application de l’attribution de stratégies sont automatiquement incluses.

## <a name="managed-identity-requirements"></a>Exigences relatives à l’identité managée

Les définitions de stratégie dans l’initiative _Déployer les prérequis pour activer les stratégies de la configuration invité sur les machines virtuelles_ activent une identité managée affectée par le système, s’il n’en n’existe pas. L’initiative comporte deux définitions de stratégie qui gèrent la création d’identités. Les conditions IF dans les définitions de stratégie garantissent un comportement correct en fonction de l’état actuel de la ressource de la machine dans Azure.

Si la machine n’a pas d’identités managées, la stratégie effective est : [Ajouter une identité managée affectée par le système pour activer les attributions de la configuration invité sur les machines virtuelles sans identité](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e)

Si la machine possède actuellement une identité système affectée par l’utilisateur, la stratégie effective est : [Ajouter une identité managée affectée par le système pour activer les attributions de la configuration invité sur les machines virtuelles avec une identité affectée par l’utilisateur](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6)

## <a name="availability"></a>Disponibilité

Les clients qui conçoivent une solution hautement disponible doivent tenir compte des exigences de planification de la redondance pour les [machines virtuelles](../../../virtual-machines/availability.md), car les attributions d’invités sont des extensions des ressources de machine dans Azure. Lorsque les ressources d’attributions d’invités sont approvisionnées dans une région Azure [jumelée](../../../best-practices-availability-paired-regions.md), tant qu’au moins une région jumelée est disponible, les rapports d’attributions d’invités sont disponibles. Si la région Azure n’est pas jumelée et devient indisponible, il n’est pas possible d’accéder aux rapports d’une attribution d’invité tant que la région n’est pas restaurée.

Lorsque vous envisagez une architecture pour les applications hautement disponibles, en particulier lorsque les machines virtuelles sont approvisionnées dans des [groupes à haute disponibilité](../../../virtual-machines/availability.md#availability-sets) derrière une solution d’équilibrage de charge pour fournir une haute disponibilité, il est recommandé d’affecter les mêmes définitions de stratégie avec les mêmes paramètres à toutes les machines de la solution. Si possible, une attribution de stratégie unique couvrant toutes les machines offrirait la surcharge administrative la moins importante.

Pour les machines protégées par [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md), assurez-vous que les machines d’un site secondaire se trouvent dans l’étendue des attributions d’Azure Policy pour les mêmes définitions en utilisant les mêmes valeurs de paramètres que les machines du site principal.

## <a name="data-residency"></a>Résidence des données

Guest Configuration stocke/traite les données client. Par défaut, les données client sont répliquées dans la [région jumelée.](../../../best-practices-availability-paired-regions.md)
Pour une région résidente unique, toutes les données client sont stockées et traitées dans la région.

## <a name="troubleshooting-guest-configuration"></a>Résolution des problèmes de configuration invité

Pour plus d’informations sur la résolution des problèmes de la configuration invité, consultez [Résolution des problèmes de la Stratégie Azure](../troubleshoot/general.md).

### <a name="multiple-assignments"></a>Affectations multiples

Actuellement, les définitions de stratégie de la configuration invité prennent uniquement en charge une fois l’attribution de la même affectation invité par machine, même si l’attribution de stratégie utilise des paramètres différents.

### <a name="assignments-to-azure-management-groups"></a>Affectations aux Groupes de gestion Azure

Les définitions de la Stratégie Azure dans la catégorie « Configuration invité » peuvent être assignées aux Groupes de gestion uniquement lorsque l’effet est « AuditIfNotExists ». Les définitions de stratégie avec l’effet « DeployIfNotExists » ne sont pas prises en charge en tant qu’affectations aux Groupes de gestion.

### <a name="client-log-files"></a>Fichiers journaux du client

L’extension de la configuration invité écrit les fichiers journaux aux emplacements suivants :

Windows : `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux

- Machine virtuelle Azure : `/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`
- Serveur avec Arc :`/var/lib/GuestConfig/arc_policy_logs/gc_agent.log`

### <a name="collecting-logs-remotely"></a>Collecte des journaux à distance

La première étape de la résolution des problèmes liés à la configuration ou aux modules invité consiste à utiliser l'applet de commande en suivant les étapes décrites dans [Comment tester un package de la configuration invité](../how-to/guest-configuration-create-test.md).
Si cela ne réussit pas, la collecte des journaux des clients peut vous aider à diagnostiquer les problèmes.

#### <a name="windows"></a>Windows

Capturez des informations de fichiers journaux à l’aide de la [commande Run de la machine virtuelle Azure](../../../virtual-machines/windows/run-command.md). L’exemple de script PowerShell suivant peut être utile.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Capturez des informations de fichiers journaux à l’aide de la [commande Run de la machine virtuelle Azure](../../../virtual-machines/linux/run-command.md). L’exemple de script Bash suivant peut être utile.

```bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

### <a name="agent-files"></a>Fichier agent

L’agent de la configuration invité télécharge des packages de contenu sur une machine et en extrait le contenu. Pour vérifier quel contenu a été téléchargé et stocké, consultez les emplacements de dossier indiqués ci-dessous.

Windows : `c:\programdata\guestconfig\configuration`

Linux : `/var/lib/GuestConfig/Configuration`

## <a name="guest-configuration-samples"></a>Exemples de la configuration invité

Des exemples de stratégie intégrée de la configuration invité sont disponibles dans les emplacements suivants :

- [Définitions de stratégie intégrées - Configuration d’invité](../samples/built-in-policies.md#guest-configuration)
- [Initiatives intégrées - Configuration d’invité](../samples/built-in-initiatives.md#guest-configuration)
- [Exemples Azure Policy - Dépôt GitHub](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>Étapes suivantes

- Configurez un [environnement de développement](../how-to/guest-configuration-create-setup.md) de package de la configuration invité personnalisé.
- [Créez un artefact de package](../how-to/guest-configuration-create.md) pour la configuration d’invité.
- [Testez l’artefact de package](../how-to/guest-configuration-create-test.md) à partir de votre environnement de développement.
- Utilisez le module `GuestConfiguration` afin de [créer une définition Azure Policy](../how-to/guest-configuration-create-definition.md) pour une gestion à grande échelle de votre environnement.
- [Attribuez votre définition de stratégie personnalisée](../assign-policy-portal.md) à l’aide du portail Azure.
- Découvrez comment visualiser les [informations relatives à la conformité pour les attributions de stratégie de configuration d’invité](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration).
