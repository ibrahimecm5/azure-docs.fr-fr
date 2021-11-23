---
title: Intégrer un serveur activé pour Azure Arc à Microsoft Sentinel
description: Découvrez comment ajouter vos serveurs activés pour Azure Arc à Microsoft Sentinel et superviser leur état de sécurité de manière proactive.
ms.date: 07/16/2021
ms.topic: conceptual
ms.openlocfilehash: 9a7f7fb8edb6edf18d9f8d5fed0608520cb3b80f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306488"
---
# <a name="onboard-azure-arc-enabled-servers-to-microsoft-sentinel"></a>Intégrer des serveurs avec Azure Arc à Microsoft Sentinel

Cet article est destiné à vous aider à intégrer votre serveur activé pour Azure Arc à [Microsoft Sentinel](../../sentinel/overview.md) et à commencer à collecter des événements liés à la sécurité. Microsoft Sentinel fournit une solution unique pour la détection des alertes, la visibilité des menaces, le repérage proactif et la réponse aux menaces dans l’entreprise.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous respectez les exigences suivantes :

- Un [espace de travail Log Analytics](../../azure-monitor/logs/data-platform-logs.md). Pour plus d’informations sur les espaces de travail Log Analytics, consultez [Conception de votre déploiement de journaux Azure Monitor](../../azure-monitor/logs/design-logs-deployment.md).

- Microsoft Sentinel [activé dans votre abonnement](../../sentinel/quickstart-onboard.md).

- Votre machine ou votre serveur est connecté à des serveurs activés pour Azure Arc.

## <a name="onboard-azure-arc-enabled-servers-to-microsoft-sentinel"></a>Intégrer des serveurs avec Azure Arc à Microsoft Sentinel

Microsoft Sentinel est fourni avec plusieurs connecteurs pour les solutions Microsoft, prêts à l’emploi et offrant une intégration en temps réel. Pour les machines physiques et virtuelles, vous pouvez installer l’agent Log Analytics qui collecte les journaux et les transfère à Microsoft Sentinel. Les serveurs activés pour Azure Arc prennent en charge le déploiement de l’agent Log Analytics à l’aide des méthodes suivantes :

- Utilisation de l’infrastructure des extensions de machine virtuelle.

    Cette fonctionnalité des serveurs activés pour Azure Arc vous permet de déployer l’extension de machine virtuelle de l’agent Log Analytics sur un serveur Windows et/ou Linux non-Azure. Les extensions de machine virtuelle peuvent être gérées à l’aide des méthodes suivantes sur vos machines ou serveurs hybrides gérés par des serveurs Azure Arc :

    - [Portail Azure](manage-vm-extensions-portal.md)
    - [Azure CLI](manage-vm-extensions-cli.md)
    - [Azure PowerShell](manage-vm-extensions-powershell.md)
    - [Modèles Azure Resource Manager](manage-vm-extensions-template.md)

- Utilisation d’Azure Policy.

    Avec cette approche, vous utilisez la stratégie intégrée [Déployer l’agent Log Analytics sur des machines Linux ou Windows Azure Arc](../../governance/policy/samples/built-in-policies.md#monitoring) d’Azure Policy si l’agent Log Analytics est installé sur le serveur Azure activé pour Arc. Si l’agent n’est pas installé, elle le déploie automatiquement en utilisant une tâche de remédiation. Autrement, si vous envisagez de superviser les machines avec Azure Monitor pour machines virtuelles, utilisez plutôt l’initiative [Activer Azure Monitor pour machines virtuelles](../../governance/policy/samples/built-in-initiatives.md#monitoring) pour installer et configurer l’agent Log Analytics.

Nous vous recommandons d’installer l’agent Log Analytics pour Windows ou Linux en utilisant Azure Policy.

Une fois vos serveurs activés pour Arc connectés, le streaming de vos données commence dans Microsoft Sentinel et vous pouvez commencer à les utiliser. Vous pouvez afficher les journaux dans les [classeurs intégrés](../../sentinel/get-visibility.md) et commencer à créer des requêtes dans Log Analytics pour [examiner les données](../../sentinel/investigate-cases.md).

## <a name="next-steps"></a>Étapes suivantes

Prise en main de la [détection des menaces avec Microsoft Sentinel](../../sentinel/detect-threats-built-in.md).
