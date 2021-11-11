---
title: Azure Automanage pour les serveurs Azure Arc
description: En savoir plus sur le service Azure Automanage pour les serveurs Azure Arc
ms.service: virtual-machines
ms.subservice: automanage
ms.collection: linux
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 06/24/2021
ms.openlocfilehash: 42800401334da2f348cee6796e773c1ed0c0f8ff
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451157"
---
# <a name="azure-automanage-for-machines-best-practices---azure-arc-enabled-servers"></a>Meilleures pratiques concernant Azure Automanage pour les machines – Serveurs Azure Arc

Ces services Azure sont automatiquement intégrés pour vous lorsque vous utilisez les meilleures pratiques d’Automanage pour les machines sur une machine virtuelle de serveur Azure Arc. Ils sont essentiels pour notre livre blanc sur les meilleures pratiques, que vous pouvez trouver dans notre [Cloud Adoption Framework](/azure/cloud-adoption-framework/manage/azure-server-management).

Nous allons intégrer et configurer automatiquement tous ces services, superviser leur fonctionnement et apporter les corrections nécessaires en cas de dérive. Pour en savoir plus, consultez [Azure Automanage pour machines virtuelles](automanage-virtual-machines.md).

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

Automanage prend en charge les systèmes d’exploitation suivants pour les serveurs Azure Arc :

- Windows Server 2012/R2
- Windows Server 2016
- Windows Server 2019
- CentOS 7.3+, 8
- RHEL 7.4+, 8
- Ubuntu 16.04 et 18.04
- SLES 12 (SP3-SP5 uniquement)

## <a name="participating-services"></a>Services participant

|Service    |Description    |Profil de configuration<sup>1</sup>    |
|-----------|---------------|----------------------|
|[Supervision des insights de machines](../azure-monitor/vm/vminsights-overview.md)    |Azure Monitor pour machines surveille les performances et l’intégrité de vos machines virtuelles, y compris de leurs processus en cours d’exécution et de leurs dépendances vis-à-vis d’autres ressources.    |Production    |
|[Gestion des mises à jour](../automation/update-management/overview.md)    |Vous pouvez utiliser la solution Update Management d’Azure Automation pour gérer les mises à jour du système d’exploitation de vos machines. Vous pouvez rapidement évaluer l’état des mises à jour disponibles sur toutes les machines d’agent et gérer le processus d’installation des mises à jour nécessaires pour les serveurs.    |Production, Dev/Test    |
|[Suivi des modifications et inventaire](../automation/change-tracking/overview.md) |La fonctionnalité Suivi des modifications et inventaire combine les fonctions de suivi des modifications et d’inventaire pour vous permettre d’effectuer le suivi des modifications apportées à l’infrastructure des machines virtuelles et des serveurs. Le service prend en charge le suivi des modifications sur les services, les démons, les logiciels, le registre et les fichiers de votre environnement pour vous aider à diagnostiquer les changements indésirables et à déclencher des alertes. La prise en charge de l’inventaire vous permet d’interroger les ressources intégrées pour voir les applications installées et d’autres éléments de configuration.    |Production, Dev/Test    |
|[Configuration d’invité Azure](../governance/policy/concepts/guest-configuration.md)  | La stratégie Guest Configuration est utilisée pour superviser la configuration et signaler la conformité de la machine. Le service Automanage installe la base de référence Azure pour Linux à l’aide de l’extension Guest Configuration. Pour les machines Linux, le service Guest Configuration installe la base de référence en mode d’audit uniquement. Vous pouvez voir à quel niveau votre machine virtuelle n’est pas conforme à la base de référence. Toutefois, la non-conformité n’est pas automatiquement corrigée.    |Production, Dev/Test    |
|[Compte Azure Automation](../automation/automation-create-standalone-account.md)    |Azure Automation prend en charge la gestion tout au long du cycle de vie de votre infrastructure et des applications.    |Production, Dev/Test    |
|[Espace de travail Log Analytics](../azure-monitor/logs/log-analytics-overview.md) |Azure Monitor stocke des données de journal dans un espace de travail Log Analytics, qui est une ressource Azure et un conteneur où les données sont collectées, agrégées, et qui sert de limite d’administration.    |Production, Dev/Test    |


<sup>1</sup> La sélection de [profil de configuration](automanage-virtual-machines.md#configuration-profile) est disponible lorsque vous activez Automanage. Vous pouvez également créer votre propre profil personnalisé avec l’ensemble des services et paramètres Azure dont vous avez besoin.


## <a name="next-steps"></a>Étapes suivantes

Essayez d’activer le service Automanage pour machines dans le portail Azure.

> [!div class="nextstepaction"]
> [Activer le service Automanage pour machines dans le portail Azure](quick-create-virtual-machines-portal.md)
