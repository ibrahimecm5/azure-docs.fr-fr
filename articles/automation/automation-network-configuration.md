---
title: Détails de la configuration réseau d’Azure Automation
description: Cet article fournit des détails sur les informations réseau requises par State Configuration dans Azure Automation, Runbook Worker hybride dans Azure Automation, Update Management, ainsi que Suivi des modifications et inventaire.
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 7f441f49d7f94447ab16a6a3f98b0db55dabcc1f
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491447"
---
# <a name="azure-automation-network-configuration-details"></a>Détails de la configuration réseau d’Azure Automation

Cette page fournit des informations sur la mise en réseau qui sont requises pour [Runbook Worker hybride et State Configuration](#hybrid-runbook-worker-and-state-configuration) et [Update Management, ainsi que Suivi des modifications et inventaire](#update-management-and-change-tracking-and-inventory).

## <a name="hybrid-runbook-worker-and-state-configuration"></a>Runbook Worker hybride et State Configuration

Le port et les URL suivants sont requis pour Runbook Worker hybride et pour la communication entre [Automation State Configuration](automation-dsc-overview.md) et Azure Automation.

* Port : seul le port 443 est nécessaire pour l’accès Internet sortant
* URL globale : `*.azure-automation.net`
* URL globale de US Gov Virginie : `*.azure-automation.us`
* Service Agent : `https://<workspaceId>.agentsvc.azure-automation.net`

### <a name="network-planning-for-hybrid-runbook-worker"></a>planification réseau pour Runbook Worker hybride

Pour qu’un système ou un utilisateur Runbook Worker hybride puisse se connecter et s’inscrire dans Azure Automation, il doit avoir accès au numéro de port et aux URL décrits dans cette section. Le Worker doit également avoir accès aux [ports et URL requis pour l’agent Log Analytics](../azure-monitor/agents/log-analytics-agent.md) pour pouvoir se connecter à l’espace de travail Azure Monitor Log Analytics.

Si vous avez un compte Automation défini pour une région spécifique, vous pouvez restreindre la communication du Runbook Worker hybride à ce centre de données régional. Examinez les [enregistrements DNS utilisés par Azure Automation](how-to/automation-region-dns-records.md) pour identifier les enregistrements DNS obligatoires.

### <a name="configuration-of-private-networks-for-state-configuration"></a>Configuration de réseaux privés pour State Configuration

Si vos nœuds se trouvent dans un réseau privé, le port et les URL définis ci-dessus sont requis. Ces ressources fournissent une connectivité réseau pour le nœud géré et permettent à DSC de communiquer avec Azure Automation.

Si vous utilisez des ressources DSC qui communiquent entre des nœuds, telles que les [ressources WaitFor*](/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), vous devez également autoriser le trafic entre les nœuds. Pour connaître la configuration réseau requise, consultez la documentation de chaque ressource DSC.

Pour comprendre les exigences client pour TLS 1.2, consultez [TLS 1.2 pour Azure Automation](automation-managing-data.md#tls-12-for-azure-automation).

## <a name="update-management-and-change-tracking-and-inventory"></a>Update Management et Suivi des modifications et inventaire

Les adresses de ce tableau sont requises à la fois pour Update Management et pour Suivi des modifications et inventaire. Le paragraphe suivant le tableau s’applique également aux deux services.

La communication avec ces adresses utilise le **port 443**.

|Azure (public)  |Azure Government  |
|---------|---------|
|\*.ods.opinsights.azure.com    | \*.ods.opinsights.azure.us         |
|\*.oms.opinsights.azure.com     | \*.oms.opinsights.azure.us        |
|\*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net|
|\*.azure-automation.net | \*.azure-automation.us|

Lorsque vous créez des règles de sécurité de groupe réseau ou configurez le pare-feu Azure pour autoriser le trafic vers le service Automation et l’espace de travail Log Analytics, utilisez les [étiquettes de service](../virtual-network/service-tags-overview.md#available-service-tags) **GuestAndHybridManagement** et **AzureMonitor**. Cela simplifie la gestion continue de vos règles de sécurité réseau. Pour vous connecter au service Automation à partir de vos machines virtuelles Azure en toute sécurité et de façon privée, consultez [Utiliser Azure Private Link](./how-to/private-link-security.md). Pour obtenir la balise de service et les informations de plage actuelles à inclure dans le cadre de vos configurations du pare-feu local, consultez les [fichiers JSON téléchargeables](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [Présentation d’Update Management dans Azure Automation](update-management\overview.md).
* En savoir plus sur [Runbook Worker hybride](automation-hybrid-runbook-worker.md).
* En savoir plus sur [Suivi des modifications et inventaire](change-tracking\overview.md).
* En savoir plus sur [State Configuration dans Azure Automation](automation-dsc-overview.md).
