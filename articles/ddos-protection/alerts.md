---
title: Afficher et configurer les alertes de protection DDoS pour Azure DDoS Protection Standard
description: Découvrez comment afficher et configurer les alertes de protection DDoS pour Azure DDoS Protection Standard.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 315f97e6c392578e10159c767c56111e6c34f544
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286924"
---
# <a name="view-and-configure-ddos-protection-alerts"></a>Afficher et configurer les alertes de protection DDoS

La norme Azure DDoS Protection fournit une visualisation et des insights détaillés sur les attaques avec l’analyse des attaques DDoS. Les clients qui protègent leurs réseaux virtuels contre les attaques DDoS ont une visibilité détaillée du trafic des attaques et des mesures prises pour leur prévention par le biais de rapports de prévention des attaques et de journaux de flux de prévention des attaques. La télémétrie enrichie est exposée via Azure Monitor et inclut des métriques détaillées pendant la durée d’une attaque DDoS. La génération d’alertes peut être configurée pour l’une des métriques Azure Monitor exposées par DDoS Protection. La journalisation peut également être intégrée à [Microsoft Sentinel](../sentinel/data-connectors-reference.md#azure-ddos-protection), Splunk (Azure Event Hubs), OMS Log Analytics et Stockage Azure pour l’analyse avancée par le biais de l’interface de diagnostic d’Azure Monitor.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer des alertes via Azure Monitor
> * Configurer des alertes via le portail
> * Afficher les alertes dans Microsoft Defender pour le cloud
> * Valider et tester vos alertes

## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- Avant de pouvoir effectuer les étapes de ce didacticiel, vous devez d’abord créer un [plan de protection Azure DDoS Standard](manage-ddos-protection.md), et la protection DDoS Standard doit être activée sur un réseau virtuel.
- Le service DDoS surveille les adresses IP publiques affectées aux ressources dans un réseau virtuel. Si aucune ressource dans le réseau virtuel ne possède une adresse IP publique, vous devez d’abord créer une ressource dotée d’une adresse IP publique. Vous pouvez surveiller l’IP publique de toutes les ressources déployées par le biais de Resource Manager (non Classic) qui figurent dans [Réseau virtuel pour services Azure](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (y compris les équilibreurs de charge Azure pour lesquels les machines virtuelles principales se trouvent dans le réseau virtuel), à l’exception des instances Azure App Service Environment. Pour poursuivre ce tutoriel, vous pouvez créer rapidement une machine virtuelle [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).     

## <a name="configure-alerts-through-azure-monitor"></a>Configurer des alertes via Azure Monitor

Avec ces modèles, vous pouvez configurer des alertes pour toutes les adresses IP publiques pour lesquelles vous avez activé la journalisation des diagnostics. Par conséquent, pour utiliser ces modèles d’alerte, vous devez d’abord disposer d’un espace de travail Log Analytics avec les paramètres de diagnostic activés. Consultez [Afficher et configurer la journalisation des diagnostics DDoS](diagnostic-logging.md).

### <a name="azure-monitor-alert-rule"></a>Règle d’alerte Azure Monitor

Cette [règle d’alerte Azure Monitor](https://aka.ms/DDOSmitigationstatus) exécute une requête simple pour détecter si une atténuation DDoS active se produit. Cela indique une attaque potentielle. Les groupes d’actions peuvent être utilisés pour appeler des actions à la suite de l’alerte.

[![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520Monitor%2520Alert%2520-%2520DDoS%2520Mitigation%2520Started%2FDDoSMitigationStarted.json)

### <a name="azure-monitor-alert-rule-with-logic-app"></a>Règle d’alerte Azure Monitor avec une application logique

Ce [modèle](https://aka.ms/ddosalert) déploie les composants nécessaires d’une alerte d’atténuation DDoS enrichie : règle d’alerte Azure Monitor, groupe d’actions et application logique. Le résultat du processus est une alerte par e-mail contenant des informations sur l’adresse IP attaquée, notamment des informations sur la ressource associée à l’adresse IP. Le propriétaire de la ressource est ajouté en tant que destinataire de l’e-mail, ainsi que l’équipe de sécurité. Un test de base pour la disponibilité des applications est également effectué et les résultats sont inclus dans l’alerte par e-mail.

[![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FDDoS%2520Mitigation%2520Alert%2520Enrichment%2FEnrich-DDoSAlert.json)

## <a name="configure-alerts-through-portal"></a>Configurer des alertes via le portail

À l’aide de la configuration des alertes Azure Monitor, vous pouvez sélectionner les métriques du service de protection DDoS disponibles de votre choix pour être averti quand il existe une atténuation active pendant une attaque. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com/) et accédez à votre plan de protection DDoS.
2. Sous **Supervision**, sélectionnez **Métriques**.
3. Dans la barre de navigation grise, sélectionnez **Nouvelle règle d’alerte**. 
4. Entrez ou sélectionnez vos propres valeurs, ou entrez les exemples de valeur suivants, acceptez les valeurs par défaut restantes, puis sélectionnez **Créer une règle d’alerte** :

    |Paramètre                  |Valeur                                                                                               |
    |---------                |---------                                                                                           |
    | Étendue                   | Sélectionnez **Sélectionner une ressource**. </br> Sélectionnez l’**Abonnement** qui contient l’adresse IP publique que vous souhaitez journaliser, sélectionnez **Adresse IP publique** comme **Type de ressource**, sélectionnez l’adresse IP publique spécifique pour laquelle vous souhaitez enregistrer les métriques. </br> Sélectionnez **Terminé**. | 
    | Condition | Sélectionnez **Sélectionner une condition**. </br> Comme nom du signal, sélectionnez **Sous attaque DDoS ou non**. </br> Comme **Opérateur**, sélectionnez **Supérieur ou égal à**. </br> Comme **Type d’agrégation**, sélectionnez **Maximum**. </br> Comme **Valeur de seuil**, entrez *1*. Pour la métrique **Sous attaque DDoS ou non**, **0** signifie que vous n’êtes pas attaqué et **1** que vous êtes attaqué. </br> Sélectionnez **Terminé**. | 
    | Actions | Sélectionnez **Ajouter des groupes d’actions**. </br> Sélectionnez **Créer un groupe d’actions**. </br> Sous **Notifications**, sous **Type de notification**, sélectionnez **E-mail/SMS/Push/Message vocal**. </br> Sous **Nom**, entrez _MyUnderAttackEmailAlert_. </br> Cliquez sur le bouton Modifier, puis sélectionnez **E-mail** et toutes les options dont vous avez besoin, puis sélectionnez **OK**. </br> Sélectionnez **Revoir + créer**. | 
    | Détails des règles d’alerte | Sous **Nom de la règle d’alerte**, entrez _MyDdosAlert_. |

Dans les minutes qui suivent la détection d’une attaque, vous devez recevoir un e-mail en provenance des métriques Azure Monitor qui ressemble à l’image suivante :

![Alerte en cas d’attaque](./media/manage-ddos-protection/ddos-alert.png)

Vous pouvez également découvrir plus en détail la [configuration de webhooks](../azure-monitor/alerts/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et les [applications logiques](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pour créer des alertes.

## <a name="view-alerts-in-microsoft-defender-for-cloud"></a>Afficher les alertes dans Microsoft Defender pour le cloud

Microsoft Defender pour le cloud fournit une liste des [alertes de sécurité](../security-center/security-center-managing-and-responding-alerts.md), accompagnée d’informations pour examiner et corriger les problèmes. Avec cette fonctionnalité, vous obtenez une vue unifiée des alertes, y compris les alertes en rapport avec les attaques DDoS et les actions mises en place pour atténuer l’attaque en temps quasi-réel.
Il existe deux alertes spécifiques qui seront affichées pour toutes les détections et atténuation d’attaque DDoS :

- **DDoS Attack detected for Public IP** (Attaque DDoS détectée pour l’adresse IP publique) : Cette alerte est générée lorsque le service de protection DDoS détecte que l’une de vos adresses IP publiques est la cible d’une attaque DDoS.
- **DDoS Attack mitigated for Public IP** (Attaque DDoS atténuée pour l’adresse IP publique) : Cette alerte est générée lorsqu’une attaque sur l’adresse IP publique a été atténuée.
Pour afficher les alertes, ouvrez **Defender pour le cloud** dans le portail Azure. Sous **Protection contre les menaces**, sélectionnez **Alertes de sécurité**. La capture d’écran suivante présente un exemple des alertes d’attaque DDoS.

![Alerte DDoS dans Microsoft Defender pour le cloud](./media/manage-ddos-protection/ddos-alert-asc.png)

Les alertes incluent des informations générales sur l’adresse IP publique faisant l’objet de l’attaque, des informations géographiques et sur l’intelligence des menaces, ainsi que des étapes de correction.

## <a name="validate-and-test"></a>Valider et tester

Pour simuler une attaque DDoS afin de valider vos alertes, consultez [Valider la détection d’une attaque DDoS](test-through-simulations.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

- Configurer des alertes via Azure Monitor
- Configurer des alertes via le portail
- Afficher les alertes dans Microsoft Defender pour le cloud
- Valider et tester vos alertes

Pour savoir comment tester et simuler une attaque DDoS, consultez le guide de test de simulation :

> [!div class="nextstepaction"]
> [Tester à l’aide de simulations](test-through-simulations.md)
