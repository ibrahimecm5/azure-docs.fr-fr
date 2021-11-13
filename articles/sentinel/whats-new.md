---
title: Nouveautés d’Azure Sentinel
description: Cet article décrit les nouvelles fonctionnalités d’Azure Sentinel introduites au cours des derniers mois.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 95753a40c60e6b191b768fa3ac9cae3c5e9cb466
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433267"
---
# <a name="whats-new-in-azure-sentinel"></a>Nouveautés d’Azure Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cet article liste les fonctionnalités récentes ajoutées à Azure Sentinel et les nouvelles fonctionnalités des services associés qui offrent une expérience utilisateur améliorée dans Azure Sentinel.

Si vous recherchez des éléments datant de plus de six mois, vous les trouverez dans l’[Archive des nouveautés d’Azure Sentinel](whats-new-archive.md). Pour plus d’informations sur les fonctionnalités précédentes fournies, consultez nos [blogs Tech Community](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New).

> [!IMPORTANT]
> Les fonctionnalités indiquées sont disponibles en préversion. Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

> [!TIP]
> Nos équipes de chasse des menaces Microsoft mettent à la disposition de la [communauté Azure Sentinel](https://github.com/Azure/Azure-Sentinel) des requêtes, playbooks, workbooks et notebooks, notamment des [requêtes de chasse](https://github.com/Azure/Azure-Sentinel) spécifiques que vos équipes peuvent adapter et utiliser.
>
> Vous pouvez également contribuer ! Rejoignez-nous dans la [communauté GitHub des chasseurs de menaces Azure Sentinel](https://github.com/Azure/Azure-Sentinel/wiki).
>

## <a name="november-2021"></a>Novembre 2021

### <a name="windows-forwarded-events-connector-now-available-public-preview"></a>Connecteur Windows Forwarded Events à présent disponible (en préversion publique)

Vous pouvez maintenant diffuser en continu des journaux d’événements à partir de serveurs Windows Server connectés à votre espace de travail Azure Sentinel à l’aide de Windows Event Collection/Windows Event Forwarding (WEC/WEF), grâce à ce nouveau connecteur de données. Le connecteur utilise le nouvel agent Azure Monitor Agent (AMA), qui offre un certain nombre d’avantages par rapport à l’ancien agent Log Analytics (également appelé MMA) :
- **Scalabilité** : si vous avez activé Windows Event Collection (WEC), vous pouvez installer l’Agent Azure Monitor Agent (AMA) sur l’ordinateur WEC pour collecter les journaux de plusieurs serveurs avec un point de connexion unique.

- **Vitesse** : l’agent AMA peut envoyer des données à un taux de transfert EPS 5K, ce qui permet d’accélérer l’actualisation des données.

- **Efficacité** : AMA vous permet de concevoir des règles de collecte de données complexes pour filtrer les journaux à leur source, en choisissant les événements exacts à diffuser dans votre espace de travail. Les règles de collecte de données permettent de réduire le trafic réseau et les coûts d’ingestion en excluant les événements indésirables.

- **Couverture** : WEC/WEF permet la collecte de journaux d’événements Windows à partir de serveurs hérités (locaux et physiques) et également de machines à utilisation élevée ou sensibles, telles que des contrôleurs de domaine, où l’installation d’un agent n’est pas souhaitée. 

Nous vous recommandons d’utiliser ce connecteur avec les analyseurs [ASIM (Azure Sentinel Information Model)](normalization.md) installés pour garantir une prise en charge complète de la normalisation des données.

Apprenez-en plus sur le [connecteur Windows Forwarded Events](data-connectors-reference.md#windows-forwarded-events-preview).

### <a name="near-real-time-nrt-threat-detection-rules-now-available-public-preview"></a>Des règles de détection des menaces en temps quasi-réel (NRT) sont désormais disponibles (version préliminaire publique)

Quand vous êtes confronté à des menaces de sécurité, le temps et la vitesse sont vitaux. Vous devez être conscient des menaces au fur et à mesure de leur matérialisation afin de pouvoir les analyser et y répondre rapidement pour les contenir. Les règles d’analytique NRT d’Azure Sentinel offrent une détection plus rapide des menaces, plus proche de celle d’un dispositif SIEM local, et la possibilité de raccourcir les temps de réponse dans des scénarios spécifiques.

Les [règles d’analytique en quasi temps réel](detect-threats-built-in.md#nrt) d’Azure Sentinel fournissent une détection des menaces prête à l’emploi à la minute près. Ce type de règle a été conçu pour être très réactif en exécutant sa requête à des intervalles d’une minute seulement.

En savoir plus sur les [Règles NRT](near-real-time-rules.md) et leur [utilisation](create-nrt-rules.md).

### <a name="fusion-engine-now-detects-emerging-and-unknown-threats-public-preview"></a>Le moteur Fusion détecte maintenant les menaces émergentes et inconnues (version préliminaire publique)

En plus de détecter les attaques basées sur des [scénarios prédéfinis](fusion-scenario-reference.md), le moteur Fusion basé sur le ML d’Azure Sentinel peut vous aider à identifier les menaces émergentes et inconnues dans votre environnement en appliquant une analyse de ML étendue et en mettant en corrélation une étendue plus large de signaux anormaux, tout en réduisant le niveau de fatigue des alertes.

Les algorithmes de ML du moteur Fusion se forment constamment contre les attaques existantes et appliquent une analyse en fonction de la façon de penser des analystes de sécurité. Il peut donc découvrir des menaces non détectées auparavant à partir de millions de comportements anormaux sur l’ensemble de la chaîne de destruction de votre environnement, ce qui vous permet d’avoir une longueur d’avance sur les attaquants.

En savoir plus sur [Fusion pour les menaces émergentes](fusion.md#fusion-for-emerging-threats).

En outre, la [règle d’analyse de Fusion est désormais plus configurable](configure-fusion-rules.md)et reflète ses fonctionnalités améliorées.

### <a name="get-fine-tuning-recommendations-for-your-analytics-rules-public-preview"></a>Obtenir des recommandations de réglage précis pour vos règles d’analyse (version préliminaire publique)

Le réglage des règles de détection des menaces dans votre SIEM peut être un processus difficile, délicat et continu afin d’arriver à un équilibre entre l’optimisation de la couverture de détection des menaces et la réduction du taux de faux positifs. Azure Sentinel simplifie et optimise ce processus avec l’apprentissage automatique pour analyser les milliards de signaux de vos sources de données, ainsi que vos réponses aux incidents au fil du temps, afin d’en déduire des modèles et de vous fournir des recommandations et des informations exploitables qui réduisent considérablement le temps de paramétrage et vous permettent de vous concentrer sur la détection et la réaction face aux menaces réelles.

Les [recommandations et les informations de paramétrage](detection-tuning.md) sont désormais intégrées à vos règles d’analyse. 

### <a name="free-trial-updates"></a>Mises à jour de la version d’évaluation gratuite

La version d’évaluation gratuite d’Azure Sentinel continue de prendre en charge les espaces de travail, nouveaux ou existants de Log Analytics, sans coût supplémentaire pour les 31 premiers jours.
Nous développons notre expérience d’évaluation gratuite actuelle pour inclure les mises à jour suivantes :

- Les **nouveaux espaces de travail log Analytics** peuvent ingérer gratuitement jusqu’à 10 Go/jour de données de journal pour les 31 derniers jours. Les nouveaux espaces de travail incluent des espaces de travail datant de moins de trois jours.

   L’ingestion des données Log Analytics et les frais Azure Sentinel sont exclus au cours de la période d’évaluation de 31 jours. Cette version d’essai gratuite est soumise à une limite de 20 espaces de travail par locataire Azure.


- Les **espaces de travail log Analytics existants** peuvent activer Azure Sentinel sans coût supplémentaire. Les espaces de travail existants incluent tous les espaces de travail créés il y a plus de trois jours.

   Seuls les frais Azure Sentinel sont exclus pendant la période d’essai de 31 jours.

L’utilisation au-delà de ces limites sera facturée en fonction de la tarification indiquée sur la page de [Tarification Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel) . Les frais liés aux fonctionnalités supplémentaires [d’automatisation](automation-in-azure-sentinel.md) et de[ BYOML (apportez votre propre machine learning)](bring-your-own-ml.md) sont toujours applicables durant l’essai gratuit.

> [!TIP]
> Pendant la période d’essai gratuit, recherchez des ressources pour la gestion des coûts, la formation et bien plus encore sur l’onglet **Nouveautés & guides > Essai gratuit** dans Azure Sentinel. Cet onglet affiche également des détails sur les dates de votre version d’évaluation gratuite et le nombre de jours restants jusqu’à leur expiration.
>

Pour plus d’informations, consultez [Planifier et gérer les coûts d’Azure Sentinel](azure-sentinel-billing.md).

### <a name="content-hub-and-new-solutions-public-preview"></a>Concentrateur de contenu et nouvelles solutions (version préliminaire publique)

Azure Sentinel fournit désormais un **hub de contenu**, un emplacement centralisé pour rechercher et déployer des solutions et du contenu intégrés Azure Sentinel dans votre espace de travail Azure Sentinel. Trouvez le contenu dont vous avez besoin en filtrant le type de contenu, les modèles de support, les catégories, etc. ou utilisez la puissante recherche de texte.

Sous **Gestion de contenu**, sélectionnez **Hub de contenu**. Sélectionnez une solution pour afficher plus de détails sur la droite, puis cliquez sur **Installer** pour l’installer dans votre espace de travail.

:::image type="content" source="media/whats-new/solutions-list.png" alt-text="Capture d’écran du nouveau hub de contenu Azure Sentinel." lightbox="media/whats-new/solutions-list.png":::

La liste suivante présente les principales nouveautés des solutions prêtes à l’emploi ajoutées au hub de contenu :

:::row:::
   :::column span="":::
      - Laboratoire de formation Azure Sentinel
      - Cisco ASA
      - Cisco Duo Security
      - Cisco Meraki
      - Cisco StealthWatch
      - Digital Guardian
      - 365 Dynamics
      - GCP Cloud DNS
   :::column-end:::
   :::column span="":::
      - GCP CloudMonitor
      - GCP Identity and Access Management
      - FalconForce
      - FireEye NX
      - Flare Systems Firework
      - Forescout
      - Fortinet Fortigate
      - Imperva Cloud FAW
   :::column-end:::
   :::column span="":::
      - Insiders Risk Management
      - IronNet CyberSecurity Iron Defense
      - Lookout
      - McAfee Network Security Platform
      - Microsoft MITRE ATT&CK Solution for Cloud
      - Palo Alto PAN-OS
   :::column-end:::
   :::column span="":::
      - Rapid7 Nexpose / Insight VM
      - ReversingLabs
      - RSA SecurID
      - Semperis
      - Tenable Nessus Scanner
      - Vectra Stream
      - Confiance Zéro
   :::column-end:::
:::row-end:::

Pour plus d'informations, consultez les pages suivantes :

- [À propos des solutions Azure Sentinel](sentinel-solutions.md)
- [Découvrir et déployer des solutions Azure Sentinel](sentinel-solutions-deploy.md)
- [Catalogue de solutions Azure Sentinel](sentinel-solutions-catalog.md)

### <a name="enable-continuous-deployment-from-your-content-repositories-public-preview"></a>Activer le déploiement continu à partir de vos référentiels de contenu (version préliminaire publique)

La nouvelle page **Référentiels** Azure Sentinel offre la possibilité de gérer et de déployer votre contenu personnalisé à partir de GitHub ou de référentiels Azure DevOps, en guise d’alternative à leur gestion dans le Portail Azure. Cette fonctionnalité introduit une approche plus rationalisée et automatisée pour la gestion et le déploiement de contenu dans les espaces de travail Azure Sentinel.

Si vous stockez votre contenu personnalisé dans un référentiel externe afin de le conserver en dehors d’Azure Sentinel, vous pouvez maintenant connecter ce référentiel à votre espace de travail Azure Sentinel. Le contenu que vous ajoutez, créez ou modifiez dans votre référentiel est automatiquement déployé dans vos espaces de travail Azure Sentinel et sera visible à partir des différentes galeries Azure Sentinel, telles que les pages **Analyse**, **Chasse** ou **Classeurs**.

Pour plus d’informations, consultez [Déployer du contenu personnalisé à partir de votre référentiel](ci-cd.md).

### <a name="enriched-threat-intelligence-with-geolocation-and-whois-data-public-preview"></a>Intelligence des menaces enrichie avec la géolocalisation et les données WhoIs (version préliminaire publique)

À présent, toutes les données d’informations sur les menaces que vous apportez à Azure Sentinel via des connecteurs de données et des règles d’application logique, ou que vous créez dans Azure Sentinel, sont automatiquement enrichies avec les informations de géolocalisation et WhoIs.

La géolocalisation et les données WhoIs peuvent fournir davantage de contexte pour les investigations pour lesquelles l’indicateur de compromission (IOC) sélectionné est trouvé.

Par exemple, utilisez les données de géolocalisation pour rechercher des détails tels que l’*organisation* ou le *pays* associé à l’indicateur, et les données Whois pour rechercher des données telles que celles sur le *bureau d’enregistrement* et la *création d’enregistrements*.

Vous pouvez voir les données de géolocalisation et WhoIs dans le volet **Renseignement sur les menaces** pour chaque indicateur de compromission que vous avez importé dans Azure Sentinel. Les détails de l’indicateur s’affichent à droite, y compris les données de géolocalisation et WhoIs disponibles.

Par exemple :

:::image type="content" source="media/whats-new/geolocation-whois-ti.png" alt-text="Capture d’écran des détails des indicateurs, notamment la géolocalisation et les données WhoIs." lightbox="media/whats-new/geolocation-whois-ti.png":::

> [!TIP]
> Les informations relatives à la géolocalisation et à WhoIs proviennent du service Microsoft Threat Intelligence, auquel vous pouvez également accéder via l’API. Pour plus d’informations, consultez [Enrichir les entités avec des données de géolocalisation via l’API](geolocation-data-api.md).
>

Pour plus d'informations, consultez les pages suivantes :

- [Comprendre le renseignement sur les menaces dans Azure Sentinel](understand-threat-intelligence.md)
- [Intégrations du renseignement sur les menaces](threat-intelligence-integration.md)
- [Utiliser des indicateurs de menace dans Azure Sentinel](work-with-threat-indicators.md)
- [Se connecter aux plateformes de renseignement sur les menaces](connect-threat-intelligence-tip.md)

### <a name="use-notebooks-with-azure-synapse-analytics-in-azure-sentinel-public-preview"></a>Utiliser des notebooks avec Azure Synapse Analytics dans Azure Sentinel (version préliminaire publique)

Azure Sentinel intègre désormais des notebooks Jupyter avec Azure Synapse pour des scénarios d’analyse de sécurité à grande échelle.

Jusqu’à présent, les notebooks Jupyter dans Azure Sentinel ont été intégrés à Azure Machine Learning. Cette fonctionnalité prend en charge les utilisateurs qui souhaitent incorporer des notebooks, des boîtes à outils open source populaires Machine Learning et des bibliothèques telles que TensorFlow, ainsi que leurs propres modèles personnalisés, dans des flux de travail de sécurité.

La nouvelle intégration d’Azure Synapse offre une puissance analytique supplémentaire, par exemple :

- **Analyses de sécurité du Big Data**, à l’aide d’un pool de calcul Azure Synapse Apache Spark entièrement géré.

- **Accès au lac de données rentable** pour générer des analyses sur les données historiques via Azure Data Lake Storage Gen2, qui est un ensemble de fonctionnalités dédiées aux analyses de Big Data, basées sur le Stockage Blob Azure.

- **Flexibilité pour intégrer des sources de données** dans des flux de travail d’opération de sécurité à partir de plusieurs sources et formats.

- **PySpark, une API basée sur Python** pour l’utilisation de l’infrastructure Spark en association avec Python, ce qui réduit la nécessité d’apprendre un nouveau langage de programmation si vous connaissez déjà Python.

Pour prendre en charge cette intégration, nous avons ajouté la possibilité de créer et de lancer un espace de travail Azure Synapse directement à partir d’Azure Sentinel. Nous avons également ajouté de nouveaux exemples de notebooks pour vous guider dans la configuration de l’environnement de Synapse Azure, la configuration d’un pipeline d’exportation de données continu à partir de Log Analytics dans Azure Data Lake Storage, puis la chasse à l’échelle de ces données.

Pour plus d’informations, consultez [Intégrer des notebooks avec Azure Synapse](notebooks-with-synapse.md).

### <a name="enhanced-notebooks-area-in-azure-sentinel"></a>Zone notebooks améliorée dans Azure Sentinel

La zone **Notebooks** dans Azure Sentinel possède également un onglet **Vue d’ensemble**, où vous pouvez trouver des informations de base sur les notebooks et une nouvelle colonne **Types de notebook** sous l’onglet **Modèles** pour indiquer le type de chaque notebook affiché. Par exemple, les notebooks peuvent avoir des types de **prise en main**, de **configuration**, de **chasse** et désormais de **synapse**.

Par exemple :

:::image type="content" source="media/whats-new/notebooks-synapse.png" alt-text="Capture d’écran de la nouvelle fonctionnalité Azure Synapse sur la page Notebooks." lightbox="media/whats-new/notebooks-synapse.png":::

Pour plus d’informations, consultez [Utiliser des notebooks Jupyter pour faire la chasse aux menaces de sécurité](notebooks.md).


### <a name="deploy-and-monitor-azure-key-vault-honeytokens-with-azure-sentinel"></a>Déployer et surveiller des honeytokens Azure Key Vault avec Azure Sentinel

La nouvelle solution **Azure Sentinel Deception** vous aide à surveiller les activités malveillantes dans vos coffres de clés en vous aidant à déployer des clés et des secrets de leurre, appelés « *honeytokens* », sur des coffres de clés Azure sélectionnés.

Une fois le déploiement effectué, tout accès ou opération avec les clés et les secrets honeytoken génère des incidents que vous pouvez examiner dans Azure Sentinel.

Étant donné qu’il n’y a aucune raison d’utiliser réellement les clés et les secrets honeytoken, toute activité similaire dans votre espace de travail peut être malveillante et doit être examinée.

La solution **Azure Sentinel Deception** inclut un classeur pour vous aider à déployer les honeytokens, à grande échelle ou un à la fois, des watchlists pour suivre les règles de création de honeytokens et des règles d’analyse pour générer des incidents en fonction des besoins.

Pour plus d’informations, consultez [Déployer et surveiller des honeytokens Azure Key Vault avec Azure Sentinel (préversion publique)](monitor-key-vault-honeytokens.md).


## <a name="october-2021"></a>Octobre 2021

- [Connecteur pour les événements de sécurité Windows utilisant Azure Monitor Agent maintenant en GA](#windows-security-events-connector-using-azure-monitor-agent-now-in-ga)
- [Defender pour les événements Office 365 est désormais disponible dans le connecteur Microsoft 365 Defender (préversion publique)](#defender-for-office-365-events-now-available-in-the-microsoft-365-defender-connector-public-preview)
- [Modèles de playbook et galerie désormais disponibles (préversion publique)](#playbook-templates-and-gallery-now-available-public-preview)
- [Gestion des versions des modèles pour vos règles d’analyse planifiée (préversion publique)](#manage-template-versions-for-your-scheduled-analytics-rules-public-preview)
- [Schéma de normalisation DHCP (préversion publique)](#dhcp-normalization-schema-public-preview)

### <a name="windows-security-events-connector-using-azure-monitor-agent-now-in-ga"></a>Connecteur pour les événements de sécurité Windows utilisant Azure Monitor Agent maintenant en GA

La nouvelle version du connecteur d’événements de sécurité Windows, basée sur l’agent Azure Monitor, est désormais disponible ! Pour plus d’informations, consultez [Se connecter aux serveurs Windows pour collecter des événements de sécurité](connect-windows-security-events.md?tabs=AMA).

### <a name="defender-for-office-365-events-now-available-in-the-microsoft-365-defender-connector-public-preview"></a>Defender pour les événements Office 365 est désormais disponible dans le connecteur Microsoft 365 Defender (préversion publique)

En plus des événements de Microsoft Defender pour point de terminaison, vous pouvez maintenant ingérer des [événements bruts de chasse avancée](/microsoft-365/security/defender/advanced-hunting-overview) de [Microsoft Defender pour Office 365](/microsoft-365/security/office-365-security/overview) via le [connecteur Microsoft 365 Defender](connect-microsoft-365-defender.md). [Plus d’informations](microsoft-365-defender-sentinel-integration.md#advanced-hunting-event-collection)
### <a name="playbook-templates-and-gallery-now-available-public-preview"></a>Modèles de playbook et galerie désormais disponibles (préversion publique)

Un modèle de playbook est un flux de travail prédéfini, testé et prêt à l’emploi qui peut être personnalisé pour répondre à vos besoins. Les modèles peuvent également servir de référence pour les meilleures pratiques lors du développement de playbooks à partir de zéro, ou servir d’inspiration pour de nouveaux scénarios d’automatisation.

Les modèles de playbook ont été développés par la communauté Azure Sentinel, des éditeurs de logiciels indépendants (ISV) et les experts de Microsoft. Vous pouvez les trouver dans l’onglet **Modèles de playbook** (sous **Automation**), dans le cadre d’une [**solution Azure Sentinel**](sentinel-solutions.md) ou dans le [**référentiel GitHub d’Azure Sentinel**](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks). 

Pour plus d’informations, consultez [Créer et personnaliser des règles à partir de modèles intégrés](use-playbook-templates.md).

### <a name="manage-template-versions-for-your-scheduled-analytics-rules-public-preview"></a>Gestion des versions des modèles pour vos règles d’analyse planifiée (préversion publique)

Lorsque vous créez des règles d’analyse à partir de [modèles de règles Azure Sentinel intégrés](detect-threats-built-in.md), vous créez en réalité une copie du modèle. Au-delà de ce point, la règle active n’est ***pas*** mise à jour dynamiquement pour correspondre aux modifications apportées au modèle d’origine.

Toutefois, les règles créées à partir de modèles ***se souviennent*** des modèles dont elles sont issues, ce qui présente deux avantages :

- Si vous avez apporté des modifications à une règle lors de sa création à partir d’un modèle (ou à tout moment par la suite), vous pouvez toujours rétablir la règle dans sa version originale (en tant que copie du modèle).

- Vous pouvez être averti de la mise à jour d’un modèle, et vous aurez le choix de mettre à jour vos règles avec la nouvelle version de leurs modèles ou de les laisser telles quelles.

[Découvrez comment gérer ces tâches](manage-analytics-rule-templates.md) et ce qu’il faut garder à l’esprit. Ces procédures s’appliquent à toutes les règles d’analyse [planifiées](detect-threats-built-in.md#scheduled) créées à partir de modèles.

### <a name="dhcp-normalization-schema-public-preview"></a>Schéma de normalisation DHCP (préversion publique)

Le modèle d’information Azure Sentinel (ASIM) prend désormais en charge un schéma de normalisation DHCP, qui sert à décrire les événements signalés par un serveur DHCP et qui est utilisé par Azure Sentinel pour permettre des analyses indépendantes de la source. 

Les événements décrits dans le schéma de normalisation DHCP incluent le traitement des demandes d’adresses IP DHCP louées à partir des systèmes clients et la mise à jour d’un serveur DNS avec les baux accordés.

Pour plus d'informations, consultez les pages suivantes :

- [Informations de référence du schéma de normalisation DHCP Azure Sentinel (préversion publique)](dhcp-normalization-schema.md)
- [Normalisation et modèle ASIM (Azure Sentinel Information Model)](normalization.md)

## <a name="september-2021"></a>Septembre 2021

- [Nouveautés dans docs : mise à l’échelle de la documentation du connecteur de données](#new-in-docs-scaling-data-connector-documentation)
- [Modifications du connecteur du compte de stockage Azure](#azure-storage-account-connector-changes)

### <a name="new-in-docs-scaling-data-connector-documentation"></a>Nouveautés dans docs : mise à l’échelle de la documentation du connecteur de données

Comme nous continuons à ajouter de plus en plus de connecteurs de données intégrés pour Azure Sentinel, nous avons réorganisé notre documentation sur le connecteur de données pour refléter cette mise à l’échelle.

Pour la plupart des connecteurs de données, nous avons remplacé des articles complets qui décrivent un connecteur individuel avec une série de procédures génériques et une référence complète de tous les connecteurs actuellement pris en charge.

Consultez les informations de [Référence sur les connecteurs de données Azure Sentinel](data-connectors-reference.md) pour plus d’informations sur votre connecteur, y compris les références à la procédure générique appropriée, ainsi que les informations et configurations supplémentaires requises.

Pour plus d'informations, consultez les pages suivantes :

- **Informations conceptuelles** : [Connecter des sources de données](connect-data-sources.md)

- **Articles de savoir-faire génériques** :

   - [Connecter aux services Azure, Windows, Microsoft et Amazon](connect-azure-windows-microsoft-services.md)
   - [Connecter votre source de données à l’API du Collecteur de données Azure Sentinel pour l’ingestion des données](connect-rest-api-template.md)
   - [Recevoir des journaux au format CEF à partir de votre appareil ou de votre appliance dans Azure Sentinel](connect-common-event-format.md)
   - [Collecter des données de sources Linux à l’aide de Syslog](connect-syslog.md)
   - [Collecter des données dans des formats de journal personnalisés vers Azure Sentinel avec l’agent Log Analytics](connect-custom-logs.md)
   - [Utiliser Azure Functions pour connecter votre source de données à Azure Sentinel](connect-azure-functions-template.md)
   - [Ressources pour la création de connecteurs Azure Sentinel personnalisés](create-custom-connector.md)

### <a name="azure-storage-account-connector-changes"></a>Modifications du connecteur du compte de stockage Azure

En raison de certaines modifications apportées dans la configuration de ressource de compte stockage Azure elle-même, le connecteur doit également être reconfiguré.
La ressource de compte de stockage (parent) comprend d’autres ressources (enfants) pour chaque type de stockage : fichiers, tables, files d’attente et objets BLOB.

Lors de la configuration des diagnostics pour un compte de stockage, vous devez sélectionner et configurer à son tour les éléments suivants :
- La ressource de compte parent, en exportant la métrique de **Transaction**.
- Chacune des ressources de type de stockage enfant, en exportant tous les journaux et métriques (voir le tableau ci-dessus).

Vous ne verrez que les types de stockage pour lesquels vous avez réellement défini des ressources.

:::image type="content" source="media/whats-new/storage-diagnostics.png" alt-text="Capture d’écran de la configuration des diagnostics de stockage Azure.":::

## <a name="august-2021"></a>Août 2021

- [Recherche d’incidents avancée (préversion publique)](#advanced-incident-search-public-preview)
- [Détection de fusion des rançongiciels (préversion publique)](#fusion-detection-for-ransomware-public-preview)
- [Modèles Watchlist pour les données UEBA](#watchlist-templates-for-ueba-data-public-preview)
- [Schéma de normalisation des événements de fichier (préversion publique)](#file-event-normalization-schema-public-preview)
- [Nouveau dans la documentation : Guide des meilleures pratiques](#new-in-docs-best-practice-guidance)

### <a name="advanced-incident-search-public-preview"></a>Recherche d’incidents avancée (préversion publique)

Par défaut, les recherches d’incidents se font uniquement avec les valeurs **ID d’incident**, **Titre**, **Étiquettes**, **Propriétaires** et **Nom du produit**. Azure Sentinel propose désormais des [options de recherche avancée](investigate-cases.md#search-for-incidents) pour rechercher des données supplémentaires, notamment des détails sur les alertes, des descriptions, des entités, des tactiques et bien plus.

Par exemple :

:::image type="content" source="media/tutorial-investigate-cases/advanced-search.png" alt-text="Capture d’écran des options de recherche avancée de la page Incidents.":::

Pour plus d’informations, consultez [Rechercher des incidents](investigate-cases.md#search-for-incidents).

### <a name="fusion-detection-for-ransomware-public-preview"></a>Détection de fusion des rançongiciels (préversion publique)

Azure Sentinel fournit désormais de nouvelles détections de fusion pour les éventuelles activités de rançongiciel, générant des incidents de type **Plusieurs alertes pouvant être liées à une activité de rançongiciel détectées**.

Les incidents sont générés pour les alertes éventuellement associées à des activités de rançongiciel, lorsqu’ils interviennent pendant un laps de temps spécifique, et sont associés aux étapes Exécution et Évasion défense d’une attaque. Vous pouvez utiliser les alertes répertoriées dans l’incident pour analyser les techniques éventuellement utilisées par les attaquants pour compromettre un hôte/appareil et échapper à la détection.

Parmi les connecteurs de données pris en charge figurent les suivants :

- [Azure Defender (Azure Security Center)](connect-azure-security-center.md)
- [Microsoft Defender for Endpoint](./data-connectors-reference.md#microsoft-defender-for-endpoint)
- [Microsoft Defender pour Identity](./data-connectors-reference.md#microsoft-defender-for-identity)
- [Microsoft Cloud App Security](./data-connectors-reference.md#microsoft-cloud-app-security-mcas)
- [Règles d’analyse planifiée d’Azure Sentinel](detect-threats-built-in.md#scheduled)

Pour plus d’informations, consultez [Plusieurs alertes pouvant être liées à une activité de rançongiciel détectées](fusion.md#fusion-for-ransomware).

### <a name="watchlist-templates-for-ueba-data-public-preview"></a>Modèles Watchlist pour les données UEBA (préversion publique)

Azure Sentinel fournit désormais des modèles Watchlist intégrés pour les données UEBA. Vous pouvez les personnaliser pour votre environnement et les utiliser lors de vos investigations.

Une fois les Watchlists UEBA renseignées, vous pouvez corréler ces données avec des règles d’analyse, les afficher dans pages d’entités et les graphiques d’investigations sous forme d’insights, créer des utilisations personnalisées pour suivre les adresses IP virtuelles ou les utilisateurs sensibles, et bien plus.

Actuellement, les modèles Watchlist incluent :

- **Utilisateurs d’adresses IP virtuelles**. Liste des comptes utilisateurs des employés ayant une valeur à fort impact au sein de l’organisation.
- **Employés congédiés**. Liste des comptes utilisateurs des employés ayant été congédiés ou en passe de l’être.
- **Comptes de service**. Liste des comptes de service et de leurs propriétaires.
- **Corrélation d’identité**. Liste des comptes utilisateurs associés appartenant à la même personne.
- **Ressources de valeur élevée**. Liste des appareils, ressources ou autres actifs ayant une valeur critique au sein de l’organisation.
- **Mappage réseau**. Liste des sous-réseaux IP et de leurs contextes organisationnels respectifs.

Pour plus d’informations, consultez [Créer une nouvelle Watchlist à l’aide d’un modèle](watchlists.md#create-a-new-watchlist-using-a-template-public-preview) et [Schémas Watchlist intégrés](watchlist-schemas.md).



### <a name="file-event-normalization-schema-public-preview"></a>Schéma de normalisation des événements de fichier (préversion publique)

Le modèle ASIM (Azure Sentinel Information Model) prend désormais en charge un schéma de normalisation des événements de fichier, qui est utilisé pour décrire l’activité des fichiers, comme la création, la modification ou la suppression de fichiers ou de documents. Ces événements sont signalés par les systèmes d’exploitation, les systèmes de stockage de fichiers tels qu’Azure Files et les systèmes de gestion de documents tels que Microsoft SharePoint.

Pour plus d'informations, consultez les pages suivantes :

- [Référence de schéma de normalisation d’événement du fichier Azure Sentinel (préversion publique)](file-event-normalization-schema.md)
- [Normalisation et modèle ASIM (Azure Sentinel Information Model)](normalization.md)


### <a name="new-in-docs-best-practice-guidance"></a>Nouveau dans la documentation : Guide des meilleures pratiques

En réponse à plusieurs demandes de nos clients et équipes de support technique, nous avons ajouté une série de conseils en matière de meilleures pratiques à notre documentation.

Pour plus d'informations, consultez les pages suivantes :

- [Prérequis pour le déploiement d’Azure Sentinel](prerequisites.md)
- [Meilleures pratiques pour Azure Sentinel](best-practices.md)
- [Meilleures pratiques pour l’architecture de l’espace de travail Azure Sentinel](best-practices-workspace-architecture.md)
- [Concevoir votre architecture d’espace de travail Azure Sentinel](design-your-workspace-architecture.md)
- [Exemples de conceptions d’espaces de travail Azure Sentinel](sample-workspace-designs.md)
- [Meilleures pratiques de collecte de données](best-practices-data.md)

> [!TIP]
> Pour plus d’informations, consultez notre documentation sur les articles conceptuels et procédures pertinents. Pour plus d’informations, consultez les [références sur les meilleures pratiques](best-practices.md#best-practice-references).
>

## <a name="july-2021"></a>Juillet 2021

- [Analyse de correspondance Microsoft Threat Intelligence (préversion publique)](#microsoft-threat-intelligence-matching-analytics-public-preview)
- [Utiliser des données Azure AD avec la table IdentityInfo d’Azure Sentinel (préversion publique)](#use-azure-ad-data-with-azure-sentinels-identityinfo-table-public-preview)
- [Enrichir les entités avec des données de géolocalisation via l’API (préversion publique)](#enrich-entities-with-geolocation-data-via-api-public-preview)
- [Prise en charge des requêtes de ressources croisées ADX (préversion publique)](#support-for-adx-cross-resource-queries-public-preview)
- [Watchlists en disponibilité générale](#watchlists-are-in-general-availability)
- [Prise en charge de la résidence des données dans plus de zones géographiques](#support-for-data-residency-in-more-geos)
- [Synchronisation bidirectionnelle dans le connecteur Azure Defender (préversion publique)](#bidirectional-sync-in-azure-defender-connector-public-preview)

### <a name="microsoft-threat-intelligence-matching-analytics-public-preview"></a>Analyse de correspondance Microsoft Threat Intelligence (préversion publique)

Azure Sentinel fournit désormais la règle intégrée **Analyse de correspondance Microsoft Threat Intelligence** qui met en correspondance les données de renseignement sur les menaces générées par Microsoft avec vos journaux. Cette règle génère des alertes et des incidents haute fidélité, avec des gravités appropriées en fonction du contexte des journaux détectés. Lorsqu’une correspondance est détectée, l’indicateur est également publié dans votre référentiel de renseignement sur les menaces Azure Sentinel.

La règle **Analyse de correspondance Microsoft Threat Intelligence** met en correspondance des indicateurs de domaine avec les sources de journaux suivantes :

- [CEF](connect-common-event-format.md)
- [DNS](./data-connectors-reference.md#windows-dns-server-preview)
- [Syslog](connect-syslog.md)

Pour plus d’informations, consultez [Détecter des menaces à l’aide de l’analyse de correspondance (préversion publique)](work-with-threat-indicators.md#detect-threats-using-matching-analytics-public-preview).

### <a name="use-azure-ad-data-with-azure-sentinels-identityinfo-table-public-preview"></a>Utiliser des données Azure AD avec la table IdentityInfo d’Azure Sentinel (préversion publique)

Les attaquants utilisant souvent les propres comptes d’utilisateur et de service de l’organisation, les données relatives à ces comptes d’utilisateur, notamment les informations d’identification et les privilèges de l’utilisateur, sont essentielles pour les analystes dans le processus d’investigation.

Désormais, l'[activation d’UEBA](enable-entity-behavior-analytics.md)dans votre espace de travail Azure Sentinel synchronise également les données Azure AD dans la nouvelle table **IdentityInfo** de Log Analytics. Les synchronisations entre votre instance Azure AD et la **IdentityInfo** créent un instantané de vos données de profil utilisateur incluant les métadonnées utilisateur, les informations de groupe et les rôles Azure AD attribués à chaque utilisateur.

Utilisez la table **IdentityInfo** lorsque vous menez des investigations et affinez les règles d’analyse de votre organisation afin de réduire le nombre de faux positifs.

Pour plus d’informations, consultez [Table IdentityInfo](ueba-enrichments.md#identityinfo-table-public-preview) dans la documentation de référence sur les enrichissements UEBA et [Utiliser les données UEBA pour analyser les faux positifs](investigate-with-ueba.md#use-ueba-data-to-analyze-false-positives).

### <a name="enrich-entities-with-geolocation-data-via-api-public-preview"></a>Enrichir les entités avec des données de géolocalisation via l’API (préversion publique)

Azure Sentinel propose désormais une API pour enrichir vos données avec les informations de géolocalisation. Les données de géolocalisation peuvent ensuite être utilisées pour analyser et examiner les incidents de sécurité.

Pour plus d’informations, consultez [Enrichir des entités dans Azure Sentinel avec des données de géolocalisation via l’API REST (préversion publique)](geolocation-data-api.md) et [Classer et analyser les données à l’aide d’entités dans Azure Sentinel](entities-in-azure-sentinel.md).


### <a name="support-for-adx-cross-resource-queries-public-preview"></a>Prise en charge des requêtes de ressources croisées ADX (préversion publique)

Dans Azure Sentinel, l’expérience de chasse prend désormais en charge les [requêtes de ressources croisées ADX](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md#cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer).
 
Bien que Log Analytics reste l’emplacement de stockage de données principal pour effectuer des analyses avec Azure Sentinel, ADX est parfois requis pour stocker des données en raison de coûts, de périodes de rétention ou d’autres facteurs. Cette fonctionnalité permet aux clients de parcourir un plus vaste ensemble de données et d’afficher les résultats dans les [expériences de chasse Azure Sentinel](hunting.md), y compris les requêtes de chasse, [Livestream](livestream.md) et la page de recherche Log Analytics.

Pour interroger les données stockées dans les clusters ADX, utilisez la fonction adx() pour spécifier le cluster ADX, le nom de la base de données et la table souhaitée. Vous pouvez ensuite interroger la sortie comme n’importe quelle autre table. Pour plus d’informations, consultez les pages liées ci-dessus.




### <a name="watchlists-are-in-general-availability"></a>Watchlists en disponibilité générale

La fonctionnalité [Watchlists](watchlists.md) est désormais en disponibilité générale. Utilisez les Watchlists pour enrichir les alertes avec des données métier, créer des listes d’autorisation et de refus lesquelles vérifier les événements d’accès, faciliter l’examen des menaces et réduire la charge de travail liée aux alertes.

### <a name="support-for-data-residency-in-more-geos"></a>Prise en charge de la résidence des données dans plus de zones géographiques

Azure Sentinel prend désormais en charge la résidence des données dans les zones géographiques supplémentaires suivantes :

Brésil, Norvège, Afrique du Sud, Corée, Allemagne, Émirats Arabes Unis et Suisse.

Pour plus d’informations sur la résidence des données, consultez la [liste complète des zones géographiques prises en charge](quickstart-onboard.md#geographical-availability-and-data-residency).

### <a name="bidirectional-sync-in-azure-defender-connector-public-preview"></a>Synchronisation bidirectionnelle dans le connecteur Azure Defender (préversion publique)

Le connecteur Azure Defender prend désormais en charge la synchronisation bidirectionnelle de l’état des alertes entre Defender et Azure Sentinel. Lorsque vous fermez un incident Sentinel contenant une alerte Defender, l’alerte est automatiquement fermée dans le portail Defender.

Consultez cette [description complète du connecteur Azure Defender mis à jour](connect-azure-security-center.md).

## <a name="june-2021"></a>Juin 2021

- [Normalisation et modèle ASIM (Azure Sentinel Information Model)](#upgrades-for-normalization-and-the-azure-sentinel-information-model)
- [Connecteurs de service à service mis à jour](#updated-service-to-service-connectors)
- [Exporter et importer des règles d’analyse (préversion publique)](#export-and-import-analytics-rules-public-preview)
- [Enrichissement des alertes : détails des alertes (préversion publique)](#alert-enrichment-alert-details-public-preview)
- [Aide supplémentaire pour les playbooks](#more-help-for-playbooks)
- [Nouvelle réorganisation de la documentation](#new-documentation-reorganization)

### <a name="upgrades-for-normalization-and-the-azure-sentinel-information-model"></a>Normalisation et modèle ASIM (Azure Sentinel Information Model)

Le modèle ASIM (Azure Sentinel Information Model) vous permet d’utiliser et de créer du contenu indépendant de la source, ce qui simplifie l’analyse des données dans votre espace de travail Azure Sentinel.

Dans le cadre de la mise à jour de ce mois-ci, nous avons amélioré notre documentation de normalisation, en fournissant de nouveaux niveaux de données DNS, d’événements de processus et de schémas de normalisation de l’authentification.

Pour plus d'informations, consultez les pages suivantes :

- [Normalisation et modèle ASIM (Azure Sentinel Information Model)](normalization.md) (mise à jour)
- [Référence sur le schéma de normalisation de l’authentification Azure Sentinel (préversion publique)](authentication-normalization-schema.md) (nouveau)
- [Référence de schéma de normalisation des données Azure Sentinel](./network-normalization-schema.md)
- [Référence de schéma de normalisation DNS Azure Sentinel (préversion publique)](dns-normalization-schema.md) (nouveau)
- [Référence de schéma de normalisation des événements de processus Azure Sentinel (préversion publique)](process-events-normalization-schema.md) (nouveau)
- [Référence de schéma de normalisation d’événement du registre Azure Sentinel (préversion publique)](registry-event-normalization-schema.md) (nouveau)


### <a name="updated-service-to-service-connectors"></a>Connecteurs de service à service mis à jour

Deux de nos connecteurs les plus utilisés ont bénéficié de mises à niveau majeures.

- Le [connecteur d’événements de sécurité Windows (préversion publique)](connect-windows-security-events.md) est désormais basé sur le nouvel agent Azure Monitor (AMA), ce qui vous permet de choisir les données à ingérer et de bénéficier d’une visibilité maximale à moindre coût.

- Le [connecteur des journaux d’activité Azure](./data-connectors-reference.md#azure-activity) est désormais basé sur le pipeline des paramètres de diagnostic, ce qui vous permet d’obtenir des données plus complètes, de réduire la latence d’ingestion et de bénéficier de meilleures performances et d’une fiabilité accrue.

Les mises à niveau ne sont pas automatiques. Les utilisateurs de ces connecteurs sont encouragés à activer les nouvelles versions.

### <a name="export-and-import-analytics-rules-public-preview"></a>Exporter et importer des règles d’analyse (préversion publique)

Vous pouvez désormais exporter vos règles d’analyse vers des fichiers modèles ARM (Azure Resource Manager) au format JSON et importer des règles à partir de ces fichiers dans le cadre de la gestion et du contrôle de vos déploiements Azure Sentinel en tant que code. Tout type de [règle d’analyse](detect-threats-built-in.md), pas uniquement les règles **planifiées**, peut être exporté vers un modèle ARM. Le fichier de modèle comprend toutes les informations de la règle, de sa requête aux tactiques MITRE ATT&CK qui lui sont attribuées.

Pour plus d’informations, consultez [Exporter et importer des règles analytiques vers et depuis des modèles ARM](import-export-analytics-rules.md).

### <a name="alert-enrichment-alert-details-public-preview"></a>Enrichissement des alertes : détails des alertes (préversion publique)

Outre l’enrichissement de votre contenu d’alerte avec les détails personnalisés et le mappage d’entités, vous pouvez désormais personnaliser la façon dont les alertes (et par extension, les incidents) sont présentées et affichées en fonction de leur contenu particulier. À l’instar des autres fonctionnalités d’enrichissement des alertes, cela peut être configuré dans l’[Assistant règle d’analyse](detect-threats-custom.md).

Pour plus d’informations, consultez [Personnaliser les détails des alertes dans Azure Sentinel](customize-alert-details.md).


### <a name="more-help-for-playbooks"></a>Aide supplémentaire pour les playbooks

Deux nouveaux documents peuvent vous aider à vous familiariser avec la création et l’utilisation de playbooks.
- [Authentifier les playbooks auprès d’Azure Sentinel](authenticate-playbooks-to-sentinel.md) vous aide à comprendre les différentes méthodes d’authentification grâce auxquelles les règles basées sur Logic Apps peuvent se connecter aux informations et y accéder dans Azure Sentinel, ainsi que le moment opportun pour les utiliser.
- [Utiliser des déclencheurs et des actions dans les playbooks](playbook-triggers-actions.md) explique la différence entre le **déclencheur d’incident** et le **déclencheur d’alerte**, ainsi que les différentes actions que vous pouvez effectuer dans les playbooks pour répondre aux incidents, y compris comment accéder aux informations dans les [détails personnalisés](playbook-triggers-actions.md#work-with-custom-details).

La documentation relative aux playbooks aborde également explicitement le scénario MSSP mutualisé.

### <a name="new-documentation-reorganization"></a>Nouvelle réorganisation de la documentation

Ce mois-ci, nous avons réorganisé notre [documentation Azure Sentinel](index.yml), en regroupant les catégories intuitives qui suivent les parcours courants des clients. Utilisez la page d’accueil de la recherche de documents filtrés et mis à jour pour naviguer dans la documentation Azure Sentinel.

:::image type="content" source="media/whats-new/new-docs.png" alt-text="Nouvelle réorganisation de la documentation Azure Sentinel." lightbox="media/whats-new/new-docs.png":::


## <a name="may-2021"></a>Mai 2021

- [Module Azure Sentinel PowerShell](#azure-sentinel-powershell-module)
- [Améliorations du regroupement des alertes](#alert-grouping-enhancements)
- [Solutions Azure Sentinel (préversion publique)](#azure-sentinel-solutions-public-preview)
- [Solution Continuous Threat Monitoring pour SAP (préversion publique)](#continuous-threat-monitoring-for-sap-solution-public-preview)
- [Intégrations Threat Intelligence (préversion publique)](#threat-intelligence-integrations-public-preview)
- [Fusion sur les alertes planifiées (préversion publique)](#fusion-over-scheduled-alerts-public-preview)
- [Anomalies SOC-ML (préversion publique)](#soc-ml-anomalies-public-preview)
- [Page Entité IP (préversion publique)](#ip-entity-page-public-preview)
- [Personnalisation d’activité (préversion publique)](#activity-customization-public-preview)
- [Tableau de bord de repérage (préversion publique)](#hunting-dashboard-public-preview)
- [Équipe d’incident - Collaborer dans Microsoft Teams (préversion publique)](#azure-sentinel-incident-team---collaborate-in-microsoft-teams-public-preview)
- [Classeur Confiance Zéro (TIC 3.0)](#zero-trust-tic30-workbook)


### <a name="azure-sentinel-powershell-module"></a>Module Azure Sentinel PowerShell

Le module officiel Azure Sentinel PowerShell permettant d’automatiser les tâches opérationnelles quotidiennes est en disponibilité générale.

Vous pouvez le télécharger ici : [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.SecurityInsights/).

Pour plus d’informations, consultez la documentation PowerShell : [Az.SecurityInsights](/powershell/module/az.securityinsights/).

### <a name="alert-grouping-enhancements"></a>Améliorations du regroupement des alertes

Vous pouvez maintenant configurer votre règle d’analyse pour regrouper les alertes dans un seul incident, non seulement lorsqu’elles correspondent à un type d’entité spécifique, mais également lorsqu’elles correspondent à un nom d’alerte spécifique, à une gravité ou à d’autres détails personnalisés pour une entité configurée. 

Dans l’onglet **Paramètres des incidents** de l’Assistant règle d’analyse, sélectionnez cette option pour activer le regroupement des alertes, puis sélectionnez l’option **Regrouper les alertes dans un seul incident si les types d’entités et les détails correspondent**. 

Sélectionnez ensuite le type d’entité et les détails que vous souhaitez faire correspondre :

:::image type="content" source="media/whats-new/alert-grouping-details.png" alt-text="Regrouper les alertes en mettant en correspondant les détails d’entité.":::

Pour plus d'informations, consultez [Regroupement des alertes](detect-threats-custom.md#alert-grouping).

### <a name="azure-sentinel-solutions-public-preview"></a>Solutions Azure Sentinel (préversion publique)

Azure Sentinel offre désormais des [solutions](sentinel-solutions-catalog.md) à **contenu packagé** qui incluent des combinaisons d’un ou plusieurs connecteurs de données, classeurs, règles d’analyse, playbooks, requêtes de chasse, analyseurs, watchlists et d’autres composants pour Azure Sentinel.

Les solutions améliorent la détectabilité dans les produits, le déploiement en une seule étape et des scénarios de produit de bout en bout. Pour plus d’informations, consultez [Découverte et déploiement de manière centralisée de solutions et de contenus intégrés](sentinel-solutions-deploy.md).

### <a name="continuous-threat-monitoring-for-sap-solution-public-preview"></a>Solution Continuous Threat Monitoring pour SAP (préversion publique)

Les solutions Azure Sentinel incluent désormais **Continuous Threat Monitoring pour SAP**, ce qui vous permet de superviser les systèmes SAP à la recherche des menaces sophistiquées au sein des couches métier et d’application.

Le connecteur de données SAP diffuse 14 journaux d’application de l’ensemble du paysage du système SAP, et collecte des journaux tant à partir du langage de programmation ABAP (Advanced Business Application Programming) via des appels RFC NetWeaver, qu’à partir des données de stockage de fichiers via l’interface de contrôle OSSAP. Le connecteur de données SAP ajoute à Azure Sentinel la capacité de surveiller l’infrastructure SAP sous-jacente.

Pour ingérer des journaux SAP dans Azure Sentinel, le connecteur de données SAP Sentinel Azure doit être installé sur votre environnement SAP. Une fois le connecteur de données SAP déployé, déployez le contenu riche de sécurité de la solution SAP pour avoir un aperçu de l’environnement SAP de votre organisation et améliorer les fonctionnalités d’opération de sécurité associées.

Pour plus d’informations, consultez [Tutoriel : Déployer la solution Azure Sentinel pour SAP (préversion publique)](sap-deploy-solution.md).

### <a name="threat-intelligence-integrations-public-preview"></a>Intégrations Threat Intelligence (préversion publique)

Azure Sentinel vous offre différentes façons d’utiliser des flux de [renseignement sur les menaces](./understand-threat-intelligence.md) pour améliorer la capacité des analystes de la sécurité à détecter et hiérarchiser les menaces connues.

Vous pouvez maintenant utiliser l’un des nombreux nouveaux produits de la plateforme décisionnelle intégrée (TIP) disponibles, vous connecter aux serveurs TAXII pour tirer parti de toute source de renseignement sur les menaces compatible avec STIX, et également utiliser les solutions personnalisées qui peuvent communiquer directement avec [l’API Microsoft Graph Security tiIndicators](/graph/api/resources/tiindicator).

Vous pouvez également vous connecter à des sources de renseignement sur les menaces à partir de playbooks, afin d’enrichir les incidents avec des informations IT qui peuvent aider à effectuer des investigations et à exécuter des actions de réponse.

Pour plus d’informations, consultez [Intégration Threat Intelligence dans Azure Sentinel](threat-intelligence-integration.md).

### <a name="fusion-over-scheduled-alerts-public-preview"></a>Fusion sur les alertes planifiées (préversion publique)

Le moteur de corrélation de Machine Learning **Fusion** peut désormais détecter les attaques multiphases à l’aide d’alertes générées par un ensemble de [règles analytiques planifiées](detect-threats-custom.md) dans ses corrélations, en plus des alertes importées à partir d’autres sources de données.

Pour plus d’informations, consultez [Détection avancée des attaques multiphases dans Azure Sentinel](fusion.md).

### <a name="soc-ml-anomalies-public-preview"></a>Anomalies SOC-ML (préversion publique)

Les anomalies basées sur le Machine Learning SOC-ML d’Azure Sentinel peuvent identifier un comportement inhabituel susceptible d’échapper à la détection.

SOC-ML utilise des modèles de règle analytique qui peuvent être mis en place et fonctionner immédiatement. Même si les anomalies n’indiquent pas nécessairement un comportement malveillant ou suspect, elles peuvent être utilisées pour améliorer la fidélité des détections, des investigations et de la recherche de menaces.

Pour plus d’informations, consultez [Utiliser des anomalies SOC-ML pour détecter les menaces dans Azure Sentinel](soc-ml-anomalies.md).

### <a name="ip-entity-page-public-preview"></a>Page Entité IP (préversion publique)

Azure Sentinel prend désormais en charge l’entité d’adresse IP, et vous pouvez désormais afficher les informations d’entité IP dans la nouvelle page Entité IP.

À l’instar des pages d’entité utilisateur et hôte, la page IP contient des informations générales sur l’adresse IP, une liste des activités auxquelles l’adresse IP a participé, et bien plus encore, vous offrant une banque d’informations encore plus riche pour améliorer votre investigation des incidents de sécurité.

Pour plus d’informations, consultez [Pages d’entité](identify-threats-with-entity-behavior-analytics.md#entity-pages).

### <a name="activity-customization-public-preview"></a>Personnalisation d’activité (préversion publique)

Concernant les pages d’entité, vous pouvez désormais créer des activités personnalisées pour vos entités, qui seront suivies et affichées sur leurs pages d’entité respectives, en plus des activités prêtes à l’emploi dont vous disposez déjà.

Pour plus d’informations, consultez [Personnaliser les activités sur les chronologies des pages d’entité](customize-entity-activities.md).

### <a name="hunting-dashboard-public-preview"></a>Tableau de bord de repérage (préversion publique)

Le panneau **Hunting** (Repérage) a été actualisé. Le nouveau tableau de bord vous permet d’exécuter toutes vos requêtes, ou un sous-ensemble sélectionné, en un seul clic.

Identifiez où commencer le repérage en examinant le nombre de résultats, les pics ou la modification du nombre de résultats sur une période de 24 heures. Vous pouvez également trier et filtrer par favoris, source de données, tactique et technique MITRE ATT&CK, résultats ou delta de résultat. Affichez les requêtes qui n’ont pas encore les sources de données nécessaires connectées et recevez des recommandations sur la façon d’activer ces requêtes.

Pour plus d’informations, consultez [Repérer les menaces avec Azure Sentinel](hunting.md).

### <a name="azure-sentinel-incident-team---collaborate-in-microsoft-teams-public-preview"></a>Équipe d’incident - Azure Sentinel - Collaborer avec Microsoft Teams (préversion publique)

Azure Sentinel prend désormais en charge une intégration directe avec Microsoft Teams, ce qui vous permet de collaborer en toute transparence au sein de l’organisation et avec des parties prenantes externes.

Directement à partir de l’incident dans Azure Sentinel, créez une nouvelle *équipe d’incident* à utiliser pour la communication et la coordination centralisées.

Les équipes d’incident sont particulièrement utiles lorsqu’elles sont utilisées en tant que pont de conférence dédié pour les incidents à gravité élevée en cours. Les organisations qui utilisent déjà Microsoft Teams pour la communication et la collaboration peuvent utiliser l’intégration Azure Sentinel pour intégrer des données de sécurité directement dans leurs conversations et leurs tâches quotidiennes.

Dans Microsoft Teams, l’onglet **page Incident** de la nouvelle équipe contient toujours les données les plus récentes et à jour d’Azure Sentinel, garantissant ainsi que les données les plus pertinentes sont disponibles pour vos équipes.

[ ![Page Incident dans Microsoft Teams.](media/collaborate-in-microsoft-teams/incident-in-teams.jpg) ](media/collaborate-in-microsoft-teams/incident-in-teams.jpg#lightbox)

Pour plus d’informations, consultez [Collaborer dans Microsoft Teams (préversion publique)](collaborate-in-microsoft-teams.md).

### <a name="zero-trust-tic30-workbook"></a>Classeur Confiance Zéro (TIC 3.0)

Le nouveau classeur Confiance Zéro (TIC3.0) d’Azure Sentinel fournit une visualisation automatisée des principes de [Confiance Zéro](/security/zero-trust/) croisée avec l’infrastructure [Trusted Internet Connections](https://www.cisa.gov/trusted-internet-connections) (TIC).

Nous savons que la conformité n’est pas seulement une exigence annuelle et que les organisations doivent surveiller les configurations au fil du temps. Le classeur Confiance Zéro d’Azure Sentinel utilise toute la gamme des offres de sécurité Microsoft dans Azure, Office 365, Teams, Intune, Windows Virtual Desktop et bien d’autres.

[ ![Classeur Confiance Zéro. ](media/zero-trust-workbook.gif) ](media/zero-trust-workbook.gif#lightbox)

**Le classeur Confiance Zéro** :

- Permet aux implémenteurs, analystes des opérations de sécurité, évaluateurs, décideurs de sécurité et de conformité, MSSP et autres d’avoir connaissance de la posture de sécurité des charges de travail cloud.
- Propose plus de 75 cartes de contrôle, alignées sur les fonctionnalités de sécurité TIC 3.0, avec des boutons d’interface utilisateur sélectionnables pour la navigation.
- Est conçu pour habiliter le personnel grâce à l’automatisation, l’intelligence artificielle, le Machine Learning, la génération de requêtes/alertes, les visualisations, les recommandations personnalisées et les références de documentation respectives.

Pour plus d’informations, consultez [Visualiser et superviser vos données](monitor-your-data.md).

## <a name="april-2021"></a>Avril 2021

- [Connecteurs de données basés sur Azure Policy](#azure-policy-based-data-connectors)
- [Chronologie des incidents (préversion publique)](#incident-timeline-public-preview)

### <a name="azure-policy-based-data-connectors"></a>Connecteurs de données basés sur Azure Policy

Azure Policy vous permet d'appliquer un ensemble commun de paramètres de journaux de diagnostic à toutes les ressources (actuelles et futures) d'un type particulier dont vous souhaitez ingérer les journaux dans Azure Sentinel.

Dans le cadre des efforts que nous déployons pour mettre la puissance d'[Azure Policy](../governance/policy/overview.md) au service de la configuration de la collecte de données, nous proposons désormais en préversion publique un autre collecteur de données basé sur Azure Policy pour les [comptes de stockage Azure](./data-connectors-reference.md#azure-storage-account).

En outre, deux de nos connecteurs qui étaient jusque-là en préversion, pour [Azure Key Vault](./data-connectors-reference.md#azure-key-vault) et [Azure Kubernetes Service](./data-connectors-reference.md#azure-kubernetes-service-aks), sont désormais en disponibilité générale (GA), comme le connecteur [Azure SQL Database](./data-connectors-reference.md#azure-sql-databases) qui les a précédé.

### <a name="incident-timeline-public-preview"></a>Chronologie des incidents (préversion publique)

Le premier onglet d’une page de détails d’incident est maintenant **Chronologie**, qui affiche une chronologie des alertes et des signets dans l’incident. La chronologie d’un incident peut vous aider à mieux comprendre l’incident et à retracer la chronologie de l’activité de l’attaquant parmi les alertes et les signets associés.

- Sélectionnez un élément dans la chronologie pour afficher ses détails, sans quitter le contexte de l’incident
- Filtrez le contenu de la chronologie pour afficher uniquement les alertes ou les signets, ou les éléments d’une gravité ou d’une tactique MITRE spécifique.
- Vous pouvez sélectionner le lien **ID d’alerte système** pour afficher l’intégralité de l’enregistrement, ou le lien **Événements** pour afficher les événements associés dans la zone **Journaux**.

Par exemple :

:::image type="content" source="media/tutorial-investigate-cases/incident-timeline.png" alt-text="Onglet Chronologie de l’incident":::

Pour plus d’informations, consultez [Didacticiel : Examiner les incidents avec Azure Sentinel](investigate-cases.md).


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
>[Intégrer Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Obtenir une visibilité des alertes](get-visibility.md)
