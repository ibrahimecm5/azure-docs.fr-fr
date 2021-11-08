---
title: Résoudre les problèmes courants liés à Azure Chaos Studio
description: Apprenez à résoudre les problèmes courants liés à l’utilisation d’Azure Chaos Studio
author: c-ashton
ms.service: chaos-studio
ms.author: cashton
ms.topic: troubleshooting
ms.date: 11/01/2021
ms.custom: template-troubleshooting, ignite-fall-2021
ms.openlocfilehash: afe37f4e7600f248f6ea3be50b0a1972946e381d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096355"
---
# <a name="azure-chaos-studio-troubleshooting"></a>Résolution des problèmes d’Azure Chaos Studio

Lorsque vous utilisez Chaos Studio, vous pouvez parfois rencontrer des problèmes. Cet article décrit en détail les problèmes courants, et indique la façon de les résoudre.

## <a name="issues-due-to-prerequisites"></a>Problèmes dus aux conditions préalables

Certains problèmes sont dus à des conditions préalables manquantes. 

### <a name="why-do-agent-based-faults-fail-on-my-linux-virtual-machines"></a>Pourquoi les erreurs basées sur les agents échouent-elles sur mes machines virtuelles Linux ?

Les erreurs [Sollicitation du processeur](chaos-studio-fault-library.md#cpu-pressure), [Sollicitation de la mémoire physique](chaos-studio-fault-library.md#physical-memory-pressure), [Sollicitation des disques E/S](chaos-studio-fault-library.md#disk-io-pressure-linux) et [Contrainte Stress-ng arbitraire](chaos-studio-fault-library.md#arbitrary-stress-ng-stress) nécessitent l’installation de l’[utilitaire stress-ng](https://wiki.ubuntu.com/Kernel/Reference/stress-ng) sur votre machine virtuelle. Pour plus d’informations, consultez les sections Conditions préalables des erreurs.

### <a name="my-aks-chaos-mesh-faults-are-failing"></a>Mes erreurs AKS Chaos Mesh échouent

Avant d’utiliser les erreurs Chaos Mesh sur AKS, vous devez d’abord installer Chaos Mesh. Vous trouverez des instructions dans le [didacticiel sur les erreurs Chaos Mesh sur AKS](chaos-studio-tutorial-aks.md#set-up-chaos-mesh-on-your-aks-cluster).

## <a name="experiment-design-and-creation"></a>Concevoir et créer des expériences

### <a name="why-do-i-get-the-error-the-microsoftagent-provider-requires-a-managed-identity-when-i-try-to-create-an-experiment"></a>Pourquoi est-ce que j’obtiens l’erreur `The microsoft:agent provider requires a managed identity` lorsque j’essaie de créer une expérience ? 

Cette erreur se produit lorsque l’agent n’a pas été déployé sur votre machine virtuelle. Pour obtenir des instructions d’installation, consultez [Créer et exécuter une expérience qui utilise des erreurs basées sur des agents](chaos-studio-tutorial-agent-based.md).

### <a name="when-creating-an-experiment-i-get-the-error-the-content-media-type-null-is-not-supported-only-applicationjson-is-supported-what-does-this-mean"></a>Lors de la création d’une expérience, j’obtiens l’erreur `The content media type '<null>' is not supported. Only 'application/json' is supported.`. Qu’est-ce que cela signifie ?

Si vous fabriquez votre code JSON d’expérience, cette erreur est provoquée par un JSON mal formé dans votre définition d’expérience. Vérifiez si vous avez des erreurs de syntaxe, telles que des accolades ou des crochets incompatibles ({} et \[\]).

## <a name="experiment-execution"></a>Exécution de la commande experiment

### <a name="the-execution-status-of-my-experiment-is-failed-how-do-i-determine-what-went-wrong"></a>L’état d’exécution de mon expérience est « échec ». Comment faire pour déterminer la cause du problème ?

Sur la page Expériences, cliquez sur le nom de l’expérience pour accéder à la vue Détails de l’expérience. Dans la section Historique, cliquez sur le lien Détails de l’instance d’exécution pour obtenir plus d’informations.

![Historique des expériences](images/run-experiment-history.png)

### <a name="how-do-i-collect-agent-logs-on-a-linux-virtual-machine"></a>Comment faire pour collecter les journaux de l’agent sur une machine virtuelle Linux ?

Exécutez cette commande sur la machine virtuelle : `journalctl -u azure-chaos-agent`
