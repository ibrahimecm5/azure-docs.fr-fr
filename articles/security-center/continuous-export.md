---
title: L’exportation continue peut envoyer des alertes et des recommandations Microsoft Defender pour le cloud à des espaces de travail Log Analytics ou à Azure Event Hubs
description: Découvrez comment configurer l’exportation continue d’alertes et de recommandations de sécurité vers des espaces de travail Log Analytics ou vers Azure Event Hubs.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 11/02/2021
ms.author: memildin
ms.openlocfilehash: 628958b2b378b6b6d36b98c08f49c2f453f19af7
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056280"
---
# <a name="continuously-export-microsoft-defender-for-cloud-data"></a>Exportation continue des données de Microsoft Defender pour le cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender pour le cloud génère des alertes de sécurité et des recommandations détaillées. Vous pouvez les afficher dans le portail ou au moyen d’outils de programmation. Si nécessaire, vous pouvez également exporter en partie ou en totalité ces informations à des fins de suivi avec d’autres outils de supervision dans votre environnement. 

Vous pouvez personnaliser entièrement *ce qui* sera exporté et *où* avec l’**exportation continue**. Par exemple, vous pouvez la configurer de sorte que :

- Toutes les alertes de gravité élevée sont envoyées à un Event Hub Azure
- Tous les résultats de gravité moyenne ou plus élevée issus des analyses d’évaluation des vulnérabilités de vos serveurs SQL sont envoyés à un espace de travail Log Analytics spécifique
- Les recommandations spécifiques sont transmises à un Event Hub ou à un espace de travail Log Analytics lorsqu’elles sont générées 
- Le score sécurisé pour un abonnement est envoyé à un espace de travail Log Analytics chaque fois que le score d’un contrôle change de 0,01 ou plus 

Même s’il s’agit d’une fonctionnalité *en continu*, il existe également une option permettant d’exporter des captures instantanées hebdomadaires.

Cet article explique comment configurer l’exportation continue vers des espaces de travail Log Analytics ou vers Azure Event Hubs.

> [!NOTE]
> Si vous avez besoin d’intégrer Defender pour le cloud à un SIEM, consultez [Diffuser des alertes vers un système SIEM, SOAR ou une solution de gestion des services informatiques](export-to-siem.md).

> [!TIP]
> Defender pour le cloud offre également la possibilité d’effectuer une exportation manuelle ponctuelle au format CSV. Pour plus d’informations, consultez [Exportation ponctuelle et manuelle des alertes de sécurité](#manual-one-time-export-of-alerts-and-recommendations).


## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :|Disponibilité générale|
|Prix :|Gratuit|
|Rôles et autorisations obligatoires :|<ul><li>**Administrateur de sécurité** ou **Propriétaire** sur le groupe de ressources</li><li>Autorisations en écriture pour la ressource cible.</li><li>Si vous utilisez les stratégies Azure Policy « DeployIfNotExist » décrites ci-dessous, vous aurez également besoin d’autorisations pour attribuer des stratégies</li><li>Pour exporter des données vers Event Hub, vous devez disposer de l’Autorisation en écriture sur la Stratégie Hub d’événements.</li><li>Pour exporter vers un espace de travail Log Analytics :<ul><li>s’il **possède la solution SecurityCenterFree**, vous avez besoin d’autorisations de lecture au minimum pour la solution d’espace de travail : `Microsoft.OperationsManagement/solutions/read`</li><li>s’il **ne possède pas la solution SecurityCenterFree**, vous devez disposer d’autorisations d’écriture pour la solution d’espace de travail : `Microsoft.OperationsManagement/solutions/action`</li><li>En savoir plus sur [les solutions d’espace de travail Azure Monitor et Log Analytics](../azure-monitor/insights/solutions.md)</li></ul></li></ul>|
|Clouds :|:::image type="icon" source="./media/icons/yes-icon.png"::: Clouds commerciaux<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Nationaux/Souverains (Azure Government, Azure China 21Vianet)| 
|||


## <a name="what-data-types-can-be-exported"></a>Quels types de données peuvent être exportés ?

L’exportation continue peut exporter les types de données suivants à chaque modification :

- Alertes de sécurité.
- Recommandations relatives à la sécurité.
- Résultats de sécurité. Ils peuvent être considérés comme des « sous » recommandations et appartiennent à une recommandation « parent ». Par exemple :
    - La recommandation « Des mises à jour système doivent être installées sur vos machines » aura une « sous »recommandation pour chaque mise à jour système en suspens.
    - La recommandation « Les vulnérabilités de vos machines virtuelles doivent être corrigées » aura une « sous »recommandation pour chaque vulnérabilité identifiée par l’analyseur des vulnérabilités.
    > [!NOTE]
    > Si vous configurez une exportation continue avec l’API REST, incluez toujours le parent avec les résultats. 
- Score sécurisé par abonnement ou par contrôle
- Données de conformité réglementaire


## <a name="set-up-a-continuous-export"></a>Configurer une exportation continue 

Vous pouvez configurer l’exportation continue à partir des pages Defender pour le cloud dans le Portail Azure, via l’API REST, ou à grande échelle à l’aide des modèles Azure Policy fournis. Sélectionnez l’onglet approprié ci-dessous pour obtenir des informations détaillées sur chacune des options.

### <a name="use-the-azure-portal"></a>[**Utilisation du portail Azure**](#tab/azure-portal)

### <a name="configure-continuous-export-from-the-defender-for-cloud-pages-in-azure-portal"></a>Configurer l’exportation continue à partir des pages Defender pour le cloud dans le Portail Azure

Les étapes ci-dessous sont nécessaires si vous configurez une exportation continue vers un espace de travail Log Analytics ou vers Azure Event Hubs.

1. Dans le menu de Defender pour le cloud, ouvrez **Paramètres de l’environnement**.

1. Sélectionnez l’abonnement pour lequel vous souhaitez configurer l’exportation de données.

1. Dans la barre latérale de la page de paramètres de cet abonnement, sélectionnez **Exportation continue**.

    :::image type="content" source="./media/continuous-export/continuous-export-options-page.png" alt-text="Options d’exportation dans Microsoft Defender pour le cloud.":::

    Les options d’exportation sont affichées ici. Il y a un onglet distinct pour chaque cible d’exportation disponible. 

1. Sélectionnez le type de données que vous souhaitez exporter, puis choisissez les filtres à appliquer sur chaque type (par exemple, exporter uniquement les alertes d’un niveau de gravité élevé).

1. Sélectionnez la fréquence d’exportation appropriée :
    - **Streaming** : les évaluations sont envoyées quand l’état d’intégrité d’une ressource est mis à jour (si aucune mise à jour n’est effectuée, aucune donnée n’est envoyée).
    - **Captures instantanées** - Une capture instantanée de l’état actuel des types de données sélectionnés est envoyée une fois par semaine et par abonnement. Pour identifier les données de capture instantanée, recherchez le champ ``IsSnapshot``.

1. Éventuellement, si votre sélection inclut l’une de ces recommandations, vous pouvez inclure les résultats de l’évaluation des vulnérabilités :
    - Les résultats des vulnérabilités des bases de données SQL doivent être résolus
    - Les résultats des vulnérabilités des serveurs SQL Server sur les machines doivent être résolus
    - Les vulnérabilités dans les images Azure Container Registry doivent être corrigées (avec Qualys)
    - Les vulnérabilités de vos machines virtuelles doivent être corrigées
    - Des mises à jour système doivent être installées sur vos machines

    Pour inclure les résultats avec ces recommandations, activez l’option **Intégrer les découvertes de sécurité**.

    :::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Intégrer les résultats de sécurité dans la configuration de l’exportation continue" :::

1. À partir de la zone « Cible d’exportation », choisissez l’emplacement où enregistrer les données. Les données peuvent être enregistrées à un emplacement cible dans un abonnement différent (par exemple, sur une instance Event Hub centrale ou un espace de travail Log Analytics central).
1. Sélectionnez **Enregistrer**.

### <a name="use-the-rest-api"></a>[**Utiliser l’API REST**](#tab/rest-api)

### <a name="configure-continuous-export-using-the-rest-api"></a>Configurer l’exportation continue à l’aide de l’API REST

L’exportation continue peut être configurée et gérée via l’[API Automations](/rest/api/securitycenter/automations) de Microsoft Defender pour le cloud. Utilisez cette API pour créer ou mettre à jour des règles d’exportation vers l’une des destinations possibles suivantes :

- Azure Event Hub
- Espace de travail Log Analytics
- Azure Logic Apps 

L’API fournit des fonctionnalités supplémentaires qui ne sont pas disponibles dans le portail Azure, par exemple :

* **Volume supérieur** : vous pouvez créer plusieurs configurations d’exportation sur un seul abonnement avec l’API. La page **Exportation continue** dans l’interface utilisateur du portail Defender pour le cloud ne prend en charge qu’une seule configuration d’exportation par abonnement.

* **Fonctionnalités supplémentaires** : l’API offre des paramètres supplémentaires qui n’apparaissent pas dans l’interface utilisateur. Par exemple, vous pouvez ajouter des balises à votre ressource d’automatisation, ainsi que définir votre exportation sur la base d’un ensemble plus vaste de propriétés d’alerte et de recommandation que celles proposées dans la page **Exportation continue** de l’interface utilisateur du portail Defender pour le cloud.

* **Étendue plus ciblée** : l’API fournit un niveau plus granulaire pour l’étendue de vos configurations d’exportation. Lorsque vous définissez une exportation avec l’API, vous pouvez le faire au niveau du groupe de ressources. Si vous utilisez la page **Exportation continue** dans l’interface utilisateur du portail Defender pour le cloud, vous devez la définir au niveau de l’abonnement.

    > [!TIP]
    > Si vous avez défini plusieurs configurations d’exportation à l’aide de l’API, ou si vous avez utilisé des paramètres uniquement d’API, ces fonctionnalités supplémentaires n’apparaissent pas dans l’interface utilisateur du service Defender pour le cloud. Au lieu de cela, une bannière s’affiche, qui vous informe que d’autres configurations existent.

Pour plus d’informations sur l’API Automations, consultez la [documentation de l’API REST](/rest/api/securitycenter/automations).

### <a name="deploy-at-scale-with-azure-policy"></a>[**Déployez à grande échelle avec Azure Policy**](#tab/azure-policy)

### <a name="configure-continuous-export-at-scale-using-the-supplied-policies"></a>Configurer l’exportation continue à grande échelle à l’aide des stratégies fournies

L’automatisation des processus d’analyse et de réponse aux incidents de votre organisation peut réduire de manière significative le temps requis pour examiner et atténuer les incidents de sécurité.

Pour déployer vos configurations d’exportation continue à l’échelle de votre organisation, utilisez les stratégies Azure Policy « DeployIfdNotExist » fournies qui sont décrites ci-dessous pour créer et configurer les procédures d’exportation continue.

**Pour implémenter ces stratégies**

1. Dans le tableau ci-dessous, sélectionnez la stratégie que vous souhaitez appliquer :

    |Objectif  |Policy  |ID de stratégie  |
    |---------|---------|---------|
    |Exportation continue vers Event Hub|[Déployer l’exportation vers Event Hub pour les alertes et recommandations Microsoft Defender pour le cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
    |Exportation continue vers l’espace de travail Log Analytics|[Déployer l’exportation vers l’espace de travail Log Analytics pour les alertes et recommandations Microsoft Defender pour le cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
    ||||

    > [!TIP]
    > Vous pouvez également les trouver faisant une recherche dans Azure Policy :
    > 1. Ouvrez Azure Policy.
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="Accès à Azure Policy.":::
    > 2. Dans le menu Azure Policy, sélectionnez **Définitions** et recherchez-les par nom. 

1. Dans la page Azure Policy appropriée, sélectionnez **Attribuer**.
    :::image type="content" source="./media/continuous-export/export-policy-assign.png" alt-text="Attribution d’Azure Policy.":::

1. Ouvrez chaque onglet et définissez les paramètres comme vous le souhaitez :
    1. Sous l’onglet **Général**, définissez l’étendue de la stratégie. Pour utiliser la gestion centralisée, attribuez la stratégie au groupe d’administration contenant les abonnements qui utiliseront la configuration de l’exportation continue. 
    1. Dans l’onglet **Paramètres**, définissez les détails du groupe de ressources et du type de données. 
        > [!TIP]
        > Chaque paramètre est accompagné d’une info-bulle qui explique les options disponibles.
        >
        > L’onglet Paramètres d’Azure Policy (1) donne accès à des options de configuration similaires à celles de la page d’exportation continue de Defender pour le cloud (2).
        > :::image type="content" source="./media/continuous-export/azure-policy-next-to-continuous-export.png" alt-text="Comparaison des paramètres de l’exportation continue avec la Stratégie Azure." lightbox="./media/continuous-export/azure-policy-next-to-continuous-export.png":::
    1. Si vous le souhaitez, pour appliquer cette attribution à des abonnements existants, ouvrez l’onglet **Correction** et sélectionnez l’option permettant de créer une tâche de correction.
1. Consultez la page de résumé et sélectionnez **Créer**.

--- 

## <a name="information-about-exporting-to-a-log-analytics-workspace"></a>Informations relatives à l’exportation vers un espace de travail Log Analytics

Si vous voulez analyser des données Microsoft Defender pour le cloud dans un espace de travail Log Analytics ou utiliser des alertes Azure avec des alertes Defender pour le cloud, configurez l’exportation continue vers votre espace de travail Log Analytics.

### <a name="log-analytics-tables-and-schemas"></a>Tables et schémas Log Analytics

Les alertes et les recommandations de sécurité sont stockées dans les tables *SecurityAlert* et *SecurityRecommendation* respectivement. 

Le nom de la solution Log Analytics contenant ces tables varie selon que vous avez activé les fonctionnalités de sécurité renforcée : Security (« Security and Audit ») ou SecurityCenterFree. 

> [!TIP]
> Pour afficher les données dans l’espace de travail de destination, vous devez activer l’une de ces solutions : **Security and Audit** ou **SecurityCenterFree**.

![Table *SecurityAlert* dans Log Analytics.](./media/continuous-export/log-analytics-securityalert-solution.png)

Pour afficher les schémas d’événements des types de données exportés, visitez les [schémas de table Log Analytics](https://aka.ms/ASCAutomationSchemas).


##  <a name="view-exported-alerts-and-recommendations-in-azure-monitor"></a>Voir les alertes et les recommandations exportées dans Azure Monitor

Vous pouvez choisir de voir les alertes de sécurité exportées et/ou les recommandations dans [Azure Monitor](../azure-monitor/alerts/alerts-overview.md). 

Azure Monitor fournit une expérience d’alerte unifiée pour diverses alertes Azure, dont le Journal de diagnostic, les Alertes de métriques et les alertes personnalisées basées sur des requêtes d’espace de travail Log Analytics.

Pour voir les alertes et les recommandations à partir de Defender pour le cloud dans Azure Monitor, configurez une règle d’alerte en fonction des requêtes Log Analytics (Alerte de journal) :

1. Dans la page **Alertes** d’Azure Monitor, sélectionnez **Nouvelle règle d’alerte**.

    ![Page d’alertes Azure Monitor](./media/continuous-export/azure-monitor-alerts.png)

1. Dans la page Créer une règle, configurez votre nouvelle règle (de la même façon que vous configurez une [règle d’alerte de journal dans Azure Monitor](../azure-monitor/alerts/alerts-unified-log.md)) :

    * Pour **Ressource**, sélectionnez l’espace de travail Log Analytics vers lequel vous avez exporté des alertes de sécurité et des recommandations.

    * Pour **Condition**, sélectionnez **Recherche personnalisée dans les journaux**. Dans la page qui s’affiche, configurez la requête, la période de recherche arrière et la période de fréquence. Dans la requête de recherche, vous pouvez taper *SecurityAlert* ou *SecurityRecommendation* pour interroger les types de données vers lequel Defender pour le cloud exporte en continu quand vous activez la fonctionnalité d’exportation continue vers Log Analytics. 
    
    * Vous pouvez éventuellement configurer le [Groupe d’actions](../azure-monitor/alerts/action-groups.md) que vous souhaitez déclencher. Les groupes d’actions peuvent déclencher l’envoi d’e-mails, des tickets ITSM, des webhooks, et plus encore.
    ![Règle d’alerte Azure Monitor.](./media/continuous-export/azure-monitor-alert-rule.png)

Vous voyez maintenant de nouvelles alertes ou recommandations Microsoft Defender pour le cloud (en fonction des règles d’exportation continue configurées et de la condition que vous avez définie dans votre règle d’alerte Azure Monitor) dans les alertes Azure Monitor, avec le déclenchement automatique d’un groupe d’actions (le cas échéant).

## <a name="manual-one-time-export-of-alerts-and-recommendations"></a>Exportation ponctuelle et manuelle des alertes de sécurité

Pour télécharger un rapport CSV pour les alertes ou les recommandations, ouvrez la page **Alertes de sécurité** ou **Recommandations**, puis sélectionnez le bouton **Télécharger le rapport CSV**.

> [!TIP]
> En raison des limitations d’Azure Resource Graph, les rapports sont limités à une taille de fichier de 13 000 lignes. Si vous constatez des erreurs liées à l’exportation d’un trop grand nombre de données, essayez de limiter la sortie en sélectionnant un plus petit ensemble d’abonnements à exporter.

:::image type="content" source="./media/continuous-export/download-alerts-csv.png" alt-text="Télécharger les données d’alertes dans un fichier CSV." lightbox="./media/continuous-export/download-alerts-csv.png":::

> [!NOTE]
> Ces rapports contiennent les alertes et les recommandations générées pour les ressources incluses dans les abonnements actuellement sélectionnés.


## <a name="faq---continuous-export"></a>FAQ – Exportation continue

### <a name="what-are-the-costs-involved-in-exporting-data"></a>Quels sont les coûts liés à l’exportation des données ?

L’activation d’une exportation continue n’entraîne aucun coût. Des coûts peuvent résulter de l’ingestion et de la rétention de données dans votre espace de travail Log Analytics, en fonction de la configuration que vous définissez ici. 

Pour en savoir plus, consultez la [tarification de l’espace de travail Log Analytics](https://azure.microsoft.com/pricing/details/monitor/).

Pour en savoir plus, consultez la [tarification d’Azure Event Hub](https://azure.microsoft.com/pricing/details/event-hubs/).


### <a name="does-the-export-include-data-about-the-current-state-of-all-resources"></a>L’exportation inclut-elle des données sur l’état actuel de toutes les ressources ?

Non. L’exportation continue est générée pour le streaming d’**événements** :

- Les **alertes** reçues avant l’activation de l’exportation ne sont pas exportées.
- Des **recommandations** sont envoyées dès que l’état de conformité d’une ressource change. Par exemple, lorsqu’une ressource passe de l’état sain à l’état non sain. Par conséquent, comme pour les alertes, les recommandations liées aux ressources dont l’état n’a pas changé depuis l’activation de l’exportation ne sont pas exportées.
- **Le score sécurisé** par contrôle de sécurité ou abonnement est envoyé lorsque le score d’un contrôle de sécurité change de 0,01 ou plus. 
- **L’état de conformité réglementaire** est envoyé lorsque l’état de conformité de la ressource change.



### <a name="why-are-recommendations-sent-at-different-intervals"></a>Pourquoi les recommandations sont-elles envoyées à des intervalles différents ?

Différentes recommandations ont des intervalles d’évaluation de la conformité différents, qui peuvent varier de quelques minutes à plusieurs jours. Par conséquent, le temps nécessaire à l’affichage des recommandations dans les exportations n’est pas toujours le même.

### <a name="does-continuous-export-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>L’exportation continue prend-elle en charge les scénarios de continuité d’activité et de reprise d’activité (BCDR) ?

Lors de la préparation de votre environnement pour les scénarios BCDR, où la ressource cible subit une panne ou un autre incident, il incombe à l’organisation d’éviter la perte de données en créant des sauvegardes conformes aux instructions fournies par Azure Event Hubs, l’espace de travail Log Analytics et l’application logique.

Pour en savoir plus, consultez [Azure Event Hubs - Géorécupération d’urgence](../event-hubs/event-hubs-geo-dr.md).


### <a name="is-continuous-export-available-for-free"></a>L’exportation continue est-elle disponible gratuitement ?

Oui. Notez que de nombreuses alertes sont fournies uniquement lorsque vous avez activé les protections avancées. Pour afficher les alertes obtenues dans vos données exportées, vous pouvez afficher les alertes présentées dans les pages Defender pour le cloud du Portail Azure.



## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à configurer des exportations continues de vos alertes et recommandations. Vous avez également appris à télécharger vos données d’alertes dans un fichier CSV. 

Pour des informations connexes, consultez la documentation suivante : 

- En savoir plus sur les [modèles d’automatisation de workflow](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).
- [Documentation Azure Event Hubs](../event-hubs/index.yml)
- [Documentation Microsoft Sentinel](../sentinel/index.yml)
- [Documentation Azure Monitor](../azure-monitor/index.yml)
- [Exporter des schémas de types de données](https://aka.ms/ASCAutomationSchemas)