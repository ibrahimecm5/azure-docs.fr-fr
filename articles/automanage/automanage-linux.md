---
title: Azure Automanage pour Linux
description: Découvrez les meilleures pratiques d’Azure Automanage relatives aux machines virtuelles pour les services automatiquement intégrés et configurés sur les machines Linux.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.collection: linux
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: 5e0dea425d309e6a92036214dfe43a5c779c586c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132327573"
---
# <a name="azure-automanage-for-machines-best-practices---linux"></a>Meilleures pratiques Azure Automanage pour les machines – Linux

Ces services Azure sont automatiquement intégrés pour vous lorsque vous utilisez des profils des meilleures pratiques de machine Automanage sur une machine virtuelle Linux. Ils sont essentiels pour notre livre blanc sur les meilleures pratiques, que vous pouvez trouver dans notre [Cloud Adoption Framework](/azure/cloud-adoption-framework/manage/azure-server-management).

Nous allons intégrer et configurer automatiquement tous ces services, superviser leur fonctionnement et apporter les corrections nécessaires en cas de dérive. Pour en savoir plus, consultez [Azure Automanage pour machines virtuelles](automanage-virtual-machines.md).

## <a name="supported-linux-distributions-and-versions"></a>Distributions et versions Linux prises en charge

Automanage prend en charge les distributions et versions Linux suivantes :

- CentOS 7.3+, 8
- RHEL 7.4+, 8
- Ubuntu 16.04 et 18.04
- SLES 12 (SP3-SP5 uniquement)

## <a name="participating-services"></a>Services participant

>[!NOTE]
> Microsoft Antimalware n’est pas pris en charge sur les machines Linux pour le moment.

|Service    |Description    |Profil de configuration pris en charge<sup>1</sup>|
|-----------|---------------|----------------------|
|[Supervision des insights de machines](../azure-monitor/vm/vminsights-overview.md)    |Azure Monitor pour machines surveille les performances et l’intégrité de vos machines virtuelles, y compris de leurs processus en cours d’exécution et de leurs dépendances vis-à-vis d’autres ressources. En savoir [plus](../azure-monitor/vm/vminsights-overview.md).    |Production    |
|[Sauvegarde](../backup/backup-overview.md)   |Le service Sauvegarde Azure propose des sauvegardes indépendantes et isolées pour éviter une destruction involontaire des données de vos machines virtuelles. En savoir [plus](../backup/backup-azure-vms-introduction.md). Les frais sont calculés en fonction du nombre et de la taille des machines virtuelles protégées. En savoir [plus](https://azure.microsoft.com/pricing/details/backup/).    |Production    |
|[Microsoft Defender pour le cloud](../security-center/security-center-introduction.md)    |Microsoft Defender pour le cloud est un système de gestion de la sécurité de l’infrastructure unifié qui renforce la posture de sécurité de vos centres de données et fournit une protection avancée contre les menaces pour vos charges de travail hybrides dans le cloud. En savoir [plus](../security-center/security-center-introduction.md).  Automanage configure l’abonnement dans lequel votre machine virtuelle réside en offre de niveau gratuit de Microsoft Defender pour le cloud. Si votre abonnement est déjà intégré à Microsoft Defender pour le cloud, Automanage ne le reconfigure pas.    |Production, Dev/Test    |
|[Gestion des mises à jour](../automation/update-management/overview.md)    |Vous pouvez utiliser la solution Update Management d’Azure Automation pour gérer les mises à jour du système d’exploitation de vos machines. Vous pouvez rapidement évaluer l’état des mises à jour disponibles sur toutes les machines d’agent et gérer le processus d’installation des mises à jour nécessaires pour les serveurs. En savoir [plus](../automation/update-management/overview.md).    |Production, Dev/Test    |
|[Suivi des modifications et inventaire](../automation/change-tracking/overview.md) |La fonctionnalité Suivi des modifications et inventaire combine les fonctions de suivi des modifications et d’inventaire pour vous permettre d’effectuer le suivi des modifications apportées à l’infrastructure des machines virtuelles et des serveurs. Le service prend en charge le suivi des modifications sur les services, les démons, les logiciels, le registre et les fichiers de votre environnement pour vous aider à diagnostiquer les changements indésirables et à déclencher des alertes. La prise en charge de l’inventaire vous permet d’interroger les ressources intégrées pour voir les applications installées et d’autres éléments de configuration.  En savoir [plus](../automation/change-tracking/overview.md).    |Production, Dev/Test    |
|[Configuration invité](../governance/policy/concepts/guest-configuration.md)  | La stratégie de Configuration invité est utilisée pour superviser la configuration et signaler la conformité de la machine. Le service Automanage installe la base de référence Azure pour Linux à l’aide de l’extension de configuration d’invité. Pour les machines Linux, le service Guest Configuration installe la base de référence en mode d’audit uniquement. Vous pouvez voir à quel niveau votre machine virtuelle n’est pas conforme à la base de référence. Toutefois, la non-conformité n’est pas automatiquement corrigée. En savoir [plus](../governance/policy/concepts/guest-configuration.md).    |Production, Dev/Test    |
|[Diagnostics de démarrage](../virtual-machines/boot-diagnostics.md)  | Les diagnostics de démarrage sont une fonctionnalité de débogage pour les machines virtuelles Azure qui permet de diagnostiquer les échecs de démarrage de machine virtuelle. Les diagnostics de démarrage permettent à un utilisateur d’observer l’état de la machine virtuelle lors de son démarrage en collectant des informations de journal série et des captures d’écran. Cette fonctionnalité est activée uniquement pour les machines qui utilisent des disques managés. |Production, Dev/Test    |
|[Compte Azure Automation](../automation/automation-create-standalone-account.md)    |Azure Automation prend en charge la gestion tout au long du cycle de vie de votre infrastructure et des applications. En savoir [plus](../automation/automation-intro.md).    |Production, Dev/Test    |
|[Espace de travail Log Analytics](../azure-monitor/logs/log-analytics-overview.md) |Azure Monitor stocke des données de journal dans un espace de travail Log Analytics, qui est une ressource Azure et un conteneur où les données sont collectées, agrégées, et qui sert de limite d’administration. En savoir [plus](../azure-monitor/logs/design-logs-deployment.md).    |Production, Dev/Test    |


<sup>1</sup> La sélection de profil de configuration est disponible lorsque vous activez Automanage. En savoir [plus](automanage-virtual-machines.md#configuration-profile). Vous pouvez également créer votre propre profil personnalisé avec l’ensemble des services et paramètres Azure dont vous avez besoin.


## <a name="next-steps"></a>Étapes suivantes

Essayez d’activer le service Automanage pour machines dans le portail Azure.

> [!div class="nextstepaction"]
> [Activer le service Automanage pour machines dans le portail Azure](quick-create-virtual-machines-portal.md)
