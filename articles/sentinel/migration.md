---
title: Migration vers Microsoft Sentinel à partir d’une solution SIEM existante
description: Découvrez comment optimiser la migration d’une solution SIEM existante vers Microsoft Sentinel pour bénéficier d’une analytique de sécurité évolutive et intelligente dans toute votre organisation.
services: sentinel
documentationcenter: na
author: batamig
ms.service: microsoft-sentinel
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5df30dd53422da751271ac644adf7072efc6dc46
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520785"
---
# <a name="migrate-to-microsoft-sentinel-from-an-existing-siem"></a>Migration vers Microsoft Sentinel à partir d’une solution SIEM existante

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

L’équipe de votre centre des opérations de sécurité (SOC) utilisera des solutions centralisées d’Informations de sécurité et gestion d’événements (SIEM) et d’orchestration, d’automatisation et de réponse en matière de sécurité (SOAR) pour protéger votre patrimoine numérique de plus en plus décentralisé.

Les SIEM héritées sont souvent locales et peuvent assurer une bonne couverture de vos ressources locales. Toutefois, les architectures locales peuvent proposer une couverture insuffisante pour vos ressources cloud, par exemple dans Azure, Microsoft 365, AWS ou Google Cloud Platform (GCP). Par comparaison, Microsoft Sentinel peut ingérer des données provenant à la fois de ressources locales et de ressources cloud, ce qui garantit une couverture de l’ensemble de votre patrimoine.

Cet article explique comment migrer une solution SIEM existante héritée vers Microsoft Sentinel, soit dans une configuration côte à côte, soit en passant au déploiement complet de Microsoft Sentinel.

## <a name="plan-your-migration"></a>Planifier votre migration

Vous avez peut-être décidé d’entamer une transition directe ou progressive vers Microsoft Sentinel, en fonction des besoins de votre entreprise et des ressources disponibles.

Vous voudrez planifier correctement votre migration pour vous assurer que la transition n’introduit pas de lacunes dans la couverture, ce qui pourrait mettre en péril la sécurité de votre organisation.

Pour commencer, identifiez vos capacités essentielles et vos besoins prioritaires. Évaluez les principaux cas d’usage couverts par votre solution SIEM actuelle et déterminez les détections et les capacités dont Microsoft Sentinel a besoin pour continuer à assurer la couverture.

Vous ajouterez un complément de planification en cours de processus à chaque étape de votre processus de migration, à mesure que vous examinerez les sources de données et les règles de détection exactes que vous souhaitez migrer. Pour plus d’informations, consultez [Migrer vos données](#migrate-your-data) et [Migrer des règles d’analyse](#migrate-analytics-rules).

> [!TIP]
> Votre SIEM actuelle peut comporter un nombre excessif de détections et de cas d’usage. Décidez lesquels sont les plus utiles à votre entreprise et déterminez ceux qui n’ont pas besoin d’être migrés. Par exemple, vérifiez quelles détections ont produit des résultats au cours de l’année écoulée.
>

### <a name="compare-your-legacy-siem-to-microsoft-sentinel"></a>Comparaison entre la solution SIEM héritée et Microsoft Sentinel

Comparez votre solution SIEM héritée à Microsoft Sentinel pour affiner vos critères d’achèvement de la migration et identifier où vous pouvez tirer plus de valeur de Microsoft Sentinel.

Par exemple, évaluez les domaines clés suivants :

|Zone d’évaluation |Description  |
|---------|---------|
|**Couverture de détection des attaques.**     | Comparez la capacité de chaque SIEM à détecter toute la gamme des attaques, en utilisant [MITRE ATT&CK](https://attack.mitre.org/) ou une infrastructure similaire.        |
|**Réactivité.**     |   Mesurez le délai moyen d’accusé de réception (MTTA), c’est-à-dire le temps qui s’écoule entre l’apparition d’une alerte dans la SIEM et le moment où un analyste commence à travailler dessus. Ce temps sera probablement similaire entre les SIEM.      |
|**Temps moyen de correction (MTTR).**     |    Comparez le MTTR pour les incidents examinés par chaque SIEM, en supposant que les analystes ont des niveaux de compétences équivalents.     |
|**Rapidité et souplesse de la chasse.**     | Mesurez la rapidité avec laquelle les équipes peuvent chasser, en partant d’une hypothèse entièrement formée, en interrogeant les données et en obtenant les résultats sur chaque plateforme SIEM.        |
|**Friction de la croissance de la capacité.**     |  Comparez le niveau de difficulté à ajouter de la capacité à mesure que l’utilisation augmente. Gardez à l’esprit que les applications et les services cloud ont tendance à générer plus de données de journal que les charges de travail locales traditionnelles.       |
|     |         |

Si vous disposez d’un investissement limité ou nul dans une solution SIEM locale, la migration vers Microsoft Sentinel peut représenter un déploiement direct et simple. Toutefois, les entreprises qui ont fortement investi dans une SIEM héritée ont généralement besoin d’un processus en plusieurs étapes pour réaliser les tâches de transition.

Bien que Microsoft Sentinel fournisse des données et des réponses étendues à la fois localement et dans le cloud, vous pouvez commencer votre migration lentement, en exécutant Microsoft Sentinel et votre solution SIEM héritée [côte à côte](#select-a-side-by-side-approach-and-method). Dans une architecture côte à côte, les ressources locales peuvent utiliser la SIEM locale et les ressources cloud, et les nouvelles charges de travail utilisent les analyses informatiques.

À moins que vous ne choisissiez une configuration côte à côte à long terme, effectuez votre migration vers un déploiement complet de Microsoft Sentinel pour bénéficier de coûts d’infrastructure réduits, d’une analyse de menaces en temps réel et de la scalabilité cloud.

## <a name="select-a-side-by-side-approach-and-method"></a>Sélectionner une approche et une méthode côte à côte

Utilisez une architecture côte à côte soit comme une phase de transition à court terme qui mène à une SIEM entièrement hébergée dans le cloud, soit comme un modèle opérationnel à moyen et long terme, en fonction des besoins de votre organisation en matière de SIEM.

Par exemple, bien que l’architecture recommandée consiste à utiliser une architecture côte à côte juste le temps d’effectuer la migration, votre organisation peut vouloir conserver sa configuration côte à côte plus longtemps, par exemple si vous n’êtes pas prêt à abandonner votre SIEM héritée. En règle générale, les organisations qui utilisent une configuration côte à côte à long terme ne se servent de Microsoft Sentinel que pour analyser leurs données cloud.

Prenez en compte les avantages et les inconvénients de chaque approche lorsque vous décidez laquelle utiliser pour votre migration.

> [!NOTE]
> De nombreuses organisations évitent d’exécuter plusieurs solutions d’analyse locales en raison de leur coût et de leur complexité.
>
> Microsoft Sentinel propose une [tarification à l’utilisation](azure-sentinel-billing.md) et une infrastructure flexible, ce qui donne aux équipes SOC le temps de s’adapter au changement. Migrez et testez votre contenu au rythme qui convient le mieux à votre organisation.
>
### <a name="short-term-approach"></a>Approche à court terme

:::row:::
   :::column span="":::
      **Avantages**

        - Donne au personnel SOC le temps de s’adapter aux nouveaux processus à mesure que les charges de travail et les analyses migrent.

        - Obtient une corrélation profonde sur toutes les sources de données pour les scénarios de chasse.

        - Élimine la nécessité d’effectuer des analyses entre les SIEM, de créer des règles de transfert et de fermer des investigations à deux endroits différents.

        - Permet à votre équipe SOC de déclasser rapidement les solutions SIEM héritées, en éliminant les coûts liés à l’infrastructure et aux licences.
   :::column-end:::
   :::column span="":::
      **Inconvénients**

        - Peut nécessiter une courbe d’apprentissage abrupte pour le personnel SOC.
   :::column-end:::
:::row-end:::

### <a name="medium--to-long-term-approach"></a>Approche à moyen et long terme

:::row:::
   :::column span="":::
      **Avantages**

        - Vous permet d’utiliser les avantages clés de Microsoft Sentinel, comme les capacités d’intelligence artificielle, de Machine Learning et d’investigation, sans vous éloigner complètement de votre solution SIEM héritée.

        - Vous permet d’économiser de l’argent par rapport à votre solution SIEM héritée, en analysant les données cloud ou Microsoft dans Microsoft Sentinel.
   :::column-end:::
   :::column span="":::
      **Inconvénients**

        - Augmente la complexité en séparant les analyses sur différentes bases de données.

        - Fractionne la gestion des cas et les investigations pour les incidents multienvironnements.

        - Engendre des coûts de personnel et d’infrastructure plus élevés.

        - Exige que le personnel SOC connaisse deux solutions SIEM différentes.
   :::column-end:::
:::row-end:::



### <a name="send-alerts-from-a-legacy-siem-to-microsoft-sentinel-recommended"></a>Envoi d’alertes d’une solution SIEM héritée à Microsoft Sentinel (recommandé)

Envoyez des alertes ou des indicateurs d’activité anormale de votre solution SIEM héritée à Microsoft Sentinel.

- Ingérez et analysez les données cloud dans Microsoft Sentinel.
- Utilisez votre SIEM héritée pour analyser les données locales et générer des alertes.
- Transférez les alertes de votre solution SIEM locale vers Microsoft Sentinel pour établir une interface unique.

Par exemple, transférez les alertes avec [Logstash](connect-logstash.md), des [API](/rest/api/securityinsights/) ou [Syslog](connect-syslog.md) et stockez-les au format [JSON](https://techcommunity.microsoft.com/t5/azure-sentinel/tip-easily-use-json-fields-in-sentinel/ba-p/768747) dans votre [espace de travail Log Analytics](../azure-monitor/logs/quick-create-workspace.md) Microsoft Sentinel.

En envoyant les alertes de votre solution SIEM héritée à Microsoft Sentinel, votre équipe peut les croiser et les examiner dans Microsoft Sentinel. L’équipe peut toujours accéder à la SIEM héritée pour une investigation plus approfondie, le cas échéant. Pendant ce temps, vous pouvez continuer à migrer des sources de données sur une période de transition prolongée.

Cette méthode de migration recommandée (côte à côte) vous permet de profiter pleinement de Microsoft Sentinel et de migrer les sources de données au rythme qui convient à votre organisation. Cette approche évite de dupliquer les coûts de stockage et d’ingestion des données pendant que vous transférez vos sources de données.

Pour plus d'informations, consultez les pages suivantes :

- [Migration des délits QRadar vers Microsoft Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/migrating-qradar-offenses-to-azure-sentinel/ba-p/2102043)
- [Exportation de données de Splunk vers Microsoft Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/how-to-export-data-from-splunk-to-azure-sentinel/ba-p/1891237)


### <a name="send-alerts-and-enriched-incidents-from-microsoft-sentinel-to-a-legacy-siem"></a>Envoi d’alertes et d’incidents enrichis de Microsoft Sentinel à une solution SIEM héritée

Analysez certaines données dans Microsoft Sentinel, comme les données cloud, puis envoyez les alertes générées à une solution SIEM héritée. Utilisez la solution SIEM *héritée* comme interface unique pour croiser les alertes générées par Microsoft Sentinel. Vous pouvez toujours vous servir de Microsoft Sentinel pour une investigation plus approfondie de ces alertes.

Cette configuration est économique, car elle permet de transférer l’analyse de données cloud vers Microsoft Sentinel sans dupliquer les coûts ni payer deux fois les données. Vous avez toujours la liberté de migrer à votre propre rythme. Plus vous déplacez de sources de données et de détections vers Microsoft Sentinel, plus il devient facile de migrer vers Microsoft Sentinel pour en faire votre interface principale. Cependant, en transférant simplement les incidents enrichis vers une solution SIEM héritée, vous limitez la valeur que vous tirez des capacités d’investigation, de chasse et d’automatisation de Microsoft Sentinel.

Pour plus d'informations, consultez les pages suivantes :

- [Envoi d’alertes Microsoft Sentinel enrichies à une solution SIEM héritée](https://techcommunity.microsoft.com/t5/azure-sentinel/sending-enriched-azure-sentinel-alerts-to-3rd-party-siem-and/ba-p/1456976)
- [Envoi d’alertes Microsoft Sentinel enrichies à IBM QRadar](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-side-by-side-with-qradar/ba-p/1488333)
- [Ingestion d’alertes Microsoft Sentinel dans Splunk](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-side-by-side-with-splunk/ba-p/1211266)

### <a name="other-methods"></a>Autres méthodes

Le tableau suivant décrit les configurations côte à côte qui ne sont *pas* recommandées, en expliquant pourquoi :

|Méthode  |Description  |
|---------|---------|
|**Envoyer les journaux Microsoft Sentinel à la solution SIEM héritée**     |  Avec cette méthode, vous continuerez à subir les problèmes de coût et d’échelle de votre SIEM locale. <br><br>Vous devez payer l’ingestion des données dans Microsoft Sentinel, ainsi que les coûts de stockage dans votre solution SIEM héritée. Par ailleurs, vous ne pouvez pas tirer parti des détections SIEM et SOAR, de l’analytique, de l’analyse du comportement des utilisateurs et des entités (UEBA, User and Entity Behavior Analytics), de l’intelligence artificielle ni des outils d’investigation et d’automatisation de Microsoft Sentinel.       |
|**Envoyer les journaux d’une solution SIEM héritée à Microsoft Sentinel**     |   Bien que cette méthode vous offre toutes les fonctionnalités de Microsoft Sentinel, votre entreprise paie toujours deux sources d’ingestion des données différentes. Outre l’ajout de complexité architecturale, ce modèle peut entraîner des coûts plus élevés.     |
|**Utiliser Microsoft Sentinel et la solution SIEM héritée comme deux solutions entièrement distinctes**     |  Vous pouvez utiliser Microsoft Sentinel pour analyser certaines sources de données, comme vos données cloud, et continuer de vous servir de votre solution SIEM locale pour les autres. Cette configuration permet d’établir des limites claires quant à l’utilisation de chaque solution et d’éviter la duplication des coûts. <br><br>Toutefois, la corrélation croisée devient difficile, et vous ne pouvez pas diagnostiquer entièrement les attaques qui traversent les deux ensembles de sources de données. Dans le paysage actuel, où les menaces se déplacent souvent latéralement dans une organisation, de telles lacunes de visibilité peuvent poser des risques de sécurité importants.       |
|     |         |



## <a name="migrate-your-data"></a>Migration de vos données

Veillez à migrer uniquement les données qui représentent vos principaux cas d’usage actuels.

1. Déterminez les données nécessaires à la prise en charge de chacun de vos cas d’usage.

1. Déterminez si vos sources de données actuelles fournissent des données utiles.

1. Identifiez les éventuelles lacunes de visibilité de votre SIEM actuelle et la manière dont vous pouvez les combler.

1. Pour chaque source de données, demandez-vous si vous devez ingérer des journaux bruts, ce qui peut être coûteux, ou si des alertes enrichies fournissent suffisamment de contexte pour vos principaux cas d’usage.

      Par exemple, vous pouvez ingérer les données enrichies issues des différents produits de sécurité de l’entreprise et utiliser Microsoft Sentinel pour les mettre en corrélation, sans avoir à ingérer les journaux bruts des sources de données elles-mêmes.

1. Utilisez l’une des ressources suivantes pour ingérer des données :

    - Utilisez les **[connecteurs de données intégrés](connect-data-sources.md) de Microsoft Sentinel** pour commencer à ingérer des données. Par exemple, vous pouvez commencer un [essai gratuit](azure-sentinel-billing.md#free-trial) avec vos données cloud ou utiliser des [connecteurs de données gratuits](azure-sentinel-billing.md#free-data-sources) pour ingérer des données provenant d’autres produits Microsoft.

    - Utilisez **[Syslog](connect-data-sources.md#syslog), [Common Event Format (CEF)](connect-data-sources.md#common-event-format-cef) ou les [API REST](connect-data-sources.md#rest-api-integration)** pour connecter d’autres sources de données.

        Pour plus d’informations, consultez [Informations de référence sur les connecteurs de données Microsoft Sentinel](data-connectors-reference.md) et le [catalogue de solutions Microsoft Sentinel](sentinel-solutions-catalog.md).

> [!TIP]
> - Si vous vous limitez aux sources de données gratuites, vous risquez de limiter votre capacité à tester les données qui sont importantes pour vous. Lorsque vous effectuez des tests, envisagez de limiter l’ingestion des données à partir de connecteurs de données gratuits et payants afin de tirer le meilleur parti des résultats de vos tests.
>
> - Lorsque vous migrez les détections et créez des cas d’usage dans Microsoft Sentinel, restez attentif aux données que vous ingérez et vérifiez leur valeur par rapport à vos principales priorités. Revenez sur les conversations de collecte de données pour vous assurer de la profondeur et de l’étendue des données dans vos cas d’usage.
>

## <a name="migrate-analytics-rules"></a>Migrer des règles d’analyse

Microsoft Sentinel utilise l’analytique Machine Learning pour créer des incidents de haute fidélité actionnables. Certaines de vos détections existantes peuvent être redondantes dans Microsoft Sentinel. Par conséquent, ne migrez pas aveuglément toutes vos règles de détection et d’analyse :

- Veillez à sélectionner les cas d’usage qui justifient la migration des règles, en tenant compte des priorités et de l’efficacité de l’entreprise.

- Passez en revue les [règles d’analyse intégrées](detect-threats-built-in.md) qui peuvent déjà répondre à vos cas d’usage. Dans Microsoft Sentinel, accédez à l’onglet **Configuration > Analytique > Modèles de règles** pour créer des règles basées sur des modèles intégrés.

- Examinez toutes les règles qui n’ont pas déclenché d’alertes au cours des 6 à 12 derniers mois et déterminez si elles sont toujours pertinentes.

- Éliminez les menaces ou les alertes de bas niveau que vous ignorez régulièrement.

**Pour migrer vos règles d’analytique vers Microsoft Sentinel, procédez comme suit** :

1. Vérifiez que vous avez mis en place un système de test pour chaque règle que vous souhaitez migrer.

    1. **Préparez un processus de validation** pour vos règles migrées, y compris des scénarios et des scripts de test complets.

    1. **Assurez-vous que votre équipe dispose des ressources utiles** pour tester vos règles migrées.

    1. **Confirmez que vous avez connecté toutes les sources de données requises**, et révisez vos méthodes de connexion de données.

1. Vérifiez si vos détections sont disponibles comme modèles intégrés dans Microsoft Sentinel :

    - **Si les règles intégrées sont suffisantes**, utilisez les modèles de règles intégrées pour créer des règles pour votre propre espace de travail.

        Dans Microsoft Sentinel, accédez à l’onglet **Configuration > Analytique > Modèles de règles**, puis créez et mettez à jour chacune des règles d’analytique pertinentes.

        Pour plus d’informations, consultez [Détection des menaces prête à l’emploi](detect-threats-built-in.md).

    - **Si certaines de vos détections ne sont pas couvertes par les règles intégrées de Microsoft Sentinel**, essayez un convertisseur de requêtes en ligne, par exemple [Uncoder.io](https://uncoder.io/), pour convertir vos requêtes en KQL.

        Identifiez la condition du déclencheur et l’action de la règle, puis créez et passez en revue votre requête KQL.

    - **Si ni les règles intégrées ni un convertisseur de règles en ligne ne suffisent**, vous devrez créer la règle manuellement. Dans ce cas, procédez comme suit pour commencer à créer votre règle :

        1. **Identifiez les sources de données que vous souhaitez utiliser dans votre règle**. Vous devez créer une table de correspondance entre les sources de données et les tables de données de Microsoft Sentinel pour identifier les tables que vous souhaitez interroger.

        1. **Identifiez tous les attributs, champs ou entités** de vos données que vous souhaitez utiliser dans vos règles.

        1. **Identifiez les critères et la logique de vos règles**. À ce stade, vous pouvez utiliser des modèles de règle comme exemples pour construire vos requêtes KQL.

            Pensez aux filtres, aux règles de corrélation, aux listes actives, aux ensembles de référence, aux listes de surveillance, aux anomalies de détection, aux agrégations, etc. Vous pouvez utiliser les références fournies par votre SIEM héritée pour comprendre comment mapper au mieux la syntaxe de vos requêtes.

            par exemple :

            - [Exemple de mappage de règles entre ArcSight/QRadar et Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel/blob/master/Tools/RuleMigration/Rule%20Logic%20Mappings.md)
            - [Exemples de mappage SPL vers KQL](https://github.com/Azure/Azure-Sentinel/blob/master/Tools/RuleMigration/Rule%20Logic%20Mappings.md) 

        1. **Identifiez la condition du déclencheur et l’action de la règle, puis créez et passez en revue votre requête KQL**. Lorsque vous examinez votre requête, tenez compte des aides relatives à l’optimisation de KQL.

1. Testez la règle avec chacun de vos cas d’usage pertinents. Si elle ne fournit pas les résultats attendus, vous pouvez revoir la requête KQL et la tester à nouveau.

1. Lorsque vous êtes satisfait, vous pouvez considérer que la règle a été migrée. Créez un playbook pour votre action de règle le cas échéant. Pour plus d’informations, consultez [Automatisation de la réponse aux menaces avec des playbooks dans Microsoft Sentinel](automate-responses-with-playbooks.md).

**Pour plus d’informations, consultez** :

- [**Créez des règles d’analyse personnalisées pour détecter des menaces**](detect-threats-custom.md). Utilisez le [regroupement d’alertes](detect-threats-custom.md#alert-grouping) pour réduire la fatigue des alertes en regroupant les alertes qui se produisent dans un laps de temps donné.
- [**Mappez les champs de données avec les entités de Microsoft Sentinel**](map-data-fields-to-entities.md) pour permettre aux ingénieurs SOC de définir les entités comme faisant partie des preuves à suivre pendant une enquête. Le mappage des entités permet également aux analystes SOC de tirer parti d’un [graphique d’investigation (investigate-cases.md#use-the-investigation-graph-to-deep-dive) intuitif qui peut contribuer à réduire le temps et les efforts.
- [**Examinez les incidents avec les données UEBA**](investigate-with-ueba.md), en guise d’exemple d’utilisation de preuves pour faire apparaître les événements, les alertes et les signets associés à un incident particulier dans le volet d’aperçu de l’incident.
- [**Kusto Query Language (KQL)**](/azure/data-explorer/kusto/query/), que vous pouvez utiliser pour envoyer des requêtes en lecture seule à votre base de données [Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) pour traiter les données et renvoyer les résultats. KQL est également utilisé dans d’autres services Microsoft, tels que [Microsoft Defender pour point de terminaison](https://www.microsoft.com/microsoft-365/security/endpoint-defender) et [Application Insights](../azure-monitor/app/app-insights-overview.md).

## <a name="use-automation-to-streamline-processes"></a>Utiliser l’automatisation pour simplifier les processus

Utilisez des flux de travail automatisés pour regrouper et classer par ordre de priorité les alertes en un incident commun et modifier sa priorité.

Pour plus d'informations, consultez les pages suivantes :

- [Orchestration, automatisation et réponse dans le domaine de la sécurité (SOAR, Security Orchestration, Automation, and Response) dans Microsoft Sentinel](automation-in-azure-sentinel.md)
- [Automatisation de la réponse aux menaces avec des règles dans Microsoft Sentinel](automate-responses-with-playbooks.md)
- [Automatisation de la gestion des incidents dans Microsoft Sentinel avec des règles d’automatisation](automate-incident-handling-with-automation-rules.md)

## <a name="retire-your-legacy-siem"></a>Mettre hors service votre SIEM héritée

Utilisez la liste de vérification suivante pour vérifier que vous avez effectué une migration complète vers Microsoft Sentinel et que vous pouvez mettre hors service votre solution SIEM héritée :


|Zone de préparation  |Détails  |
|---------|---------|
|**Préparation technologique**     | **Vérifiez les données critiques** : assurez-vous que toutes les sources et alertes sont disponibles dans Microsoft Sentinel. <br><br>**Archivez tous les enregistrements** : Sauvegardez les enregistrements critiques d’incidents et de cas passés, données brutes facultatives, pour conserver l’historique institutionnel.   |
|**Préparation des processus**     |  **Playbooks** : mettez à jour les [processus d’enquête et de chasse](investigate-cases.md) en les basculant dans Microsoft Sentinel.<br><br>**Métriques** : vérifiez que vous pouvez obtenir toutes les métriques clés dans Microsoft Sentinel.<br><br>**Classeurs** : Créez des [classeurs personnalisés](monitor-your-data.md) ou utilisez des modèles de classeur intégré pour obtenir rapidement des informations dès que vous [vous connectez aux sources de données](connect-data-sources.md).<br><br>**Incidents** : Veillez à transférer tous les incidents actuels vers le nouveau système, y compris les données sources requises.        |
|**Préparation du personnel**     |  **Analystes SOC** : vérifiez que tous les membres de votre équipe sont formés à Microsoft Sentinel et qu’ils sont à l’aise pour quitter la solution SIEM héritée.   |
|     |         |
## <a name="next-steps"></a>Étapes suivantes

Après la migration, explorez les ressources Microsoft Sentinel de Microsoft pour développer vos compétences et tirer le meilleur parti de Microsoft Sentinel.

Envisagez également de renforcer votre protection contre les menaces en utilisant Microsoft Sentinel aux côtés de [Microsoft 365 Defender](./microsoft-365-defender-sentinel-integration.md) et de [Microsoft Defender pour le cloud](../security-center/azure-defender.md) afin de bénéficier d’une [protection intégrée contre les menaces](https://www.microsoft.com/security/business/threat-protection). Tirez parti de toute la visibilité offerte par Microsoft Sentinel tout en plongeant plus en détail dans l’analyse de menaces.

Pour plus d'informations, consultez les pages suivantes :

- [Meilleures pratiques de migration des règles](https://techcommunity.microsoft.com/t5/azure-sentinel/best-practices-for-migrating-detection-rules-from-arcsight/ba-p/2216417)
- [Webinaire : Meilleures pratiques pour la conversion des règles de détection](https://www.youtube.com/watch?v=njXK1h9lfR4)
- [Orchestration, automatisation et réponse dans le domaine de la sécurité (SOAR, Security Orchestration, Automation, and Response) dans Microsoft Sentinel](automation-in-azure-sentinel.md)
- [Mieux gérer votre SOC avec des métriques d’incident](manage-soc-with-incident-metrics.md)
- [Parcours d’apprentissage Microsoft Sentinel](/learn/paths/security-ops-sentinel/)
- [Certification SC-200 d’analyste des opérations de sécurité de Microsoft](/learn/certifications/exams/sc-200)
- [Formation Microsoft Sentinel, niveau Ninja](https://techcommunity.microsoft.com/t5/azure-sentinel/become-an-azure-sentinel-ninja-the-complete-level-400-training/ba-p/1246310)
- [Examen d’une attaque dans un environnement hybride avec Microsoft Sentinel](https://mslearn.cloudguides.com/guides/Investigate%20an%20attack%20on%20a%20hybrid%20environment%20with%20Azure%20Sentinel)
