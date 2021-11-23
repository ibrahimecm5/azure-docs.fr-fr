---
title: Limites et problèmes connus d’Azure Chaos Studio
description: Découvrez les limites actuelles et les problèmes connus lors de l’utilisation d’Azure Chaos Studio.
services: chaos-studio
author: johnkemnetz
ms.topic: overview
ms.date: 11/02/2021
ms.author: johnkem
ms.service: chaos-studio
ms.openlocfilehash: b4df4b7f06c999bfc29154fbbed4133c2664dad8
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372451"
---
# <a name="azure-chaos-studio-preview-limitations-and-known-issues"></a>Limites et problèmes connus d’Azure Chaos Studio (préversion)

Pendant la préversion publique d’Azure Chaos Studio, il existe quelques limites et problèmes connus dont l’équipe est consciente et qu’elle s’efforce de résoudre.

## <a name="limitations"></a>Limites 

* Pour les erreurs dues à l’agent, la machine doit avoir accès aux **points de terminaison HTTPS** suivants :
    * http://agentcommunicationservice-frontdoor-canary.trafficmanager.net 
    * Si vous envoyez de la télémétrie à Application Insights, les adresses IP [de ce document](../azure-monitor/app/ip-addresses.md) sont également requises.
* Si vous exécutez une expérience qui utilise l’agent Chaos, la machine virtuelle doit exécuter l’un des **systèmes d’exploitation** suivants :
    * Windows Server 2019, Windows Server 2016, Windows Server 2012 et Windows Server 2012 R2
    * Redhat Enterprise Linux 8.2, SUSE Enterprise Linux 15 SP2, CentOS 8.2, Debian 10 Buster (avec installation décompressée requise), Oracle Linux 7.8, Ubuntu Server 16.04 LTS et Ubuntu Server 18.04 LTS
* L’agent Chaos n’est pas testé sur les distributions Linux personnalisées, les distributions Linux renforcées (par exemple, FIPS ou SELinux)
* L’expérience du portail Chaos Studio a été testée uniquement sur les navigateurs suivants :
    * **Windows :** Microsoft Edge, Google Chrome, Firefox
    * **MacOS :** Safari, Google Chrome, Firefox

## <a name="known-issues"></a>Problèmes connus
* En outre, l’expérience **Activer la cible basée sur l’agent** dans le portail Azure n’affecte pas l’identité managée affectée par l’utilisateur à la machine virtuelle ou au groupe de machines virtuelles identiques. Cette opération doit être effectuée manuellement, sinon une erreur basée sur un agent dans une expérience se produit avec le message : « Vérifiez que la cible est correctement intégrée et que des autorisations de lecture appropriées sont fournies à l’expérience msi. » Cette opération peut être faite après l’activation de la cible basée sur l’agent, mais peut nécessiter un redémarrage.
* L’intégration d’une cible dans le portail Azure peut échouer si vous quittez la vue Cibles avant la fin de l’intégration.
* Lors de la création d’une expérience, après que vous ayez cliqué sur **Vérifier + créer**, un délai s’écoule avant que l’expérience créée n’apparaisse dans la liste des expériences, et les utilisateurs doivent actualiser la page pour voir l’expérience dans la liste.
* Lors de la sélection des ressources cibles pour une erreur due à l’agent dans le concepteur d’expériences, il est possible de sélectionner des machines virtuelles ou des groupes de machines virtuelles identiques avec un système d’exploitation non pris en charge par l’erreur sélectionnée.


## <a name="next-steps"></a>Étapes suivantes
Commencez à créer et à exécuter des expériences de chaos pour améliorer la résilience des applications avec Chaos Studio à l’aide des liens ci-dessous.
- [Créer et exécuter votre première expérience](chaos-studio-tutorial-service-direct-portal.md)
- [En savoir plus sur l’ingénierie du chaos](chaos-studio-chaos-engineering-overview.md)
