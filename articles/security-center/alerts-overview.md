---
title: Alertes et incidents de sécurité dans Microsoft Defender pour le cloud
description: Découvrez comment Microsoft Defender pour le cloud génère des alertes de sécurité et les met en relation avec les incidents.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: memildin
ms.openlocfilehash: 6a22cc027e8abd631cb0c5b1a40e722d37225900
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131475385"
---
# <a name="security-alerts-and-incidents-in-microsoft-defender-for-cloud"></a>Alertes et incidents de sécurité dans Microsoft Defender pour le cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender pour le cloud génère des alertes pour les ressources déployées sur vos environnements Azure, locaux et cloud hybrides.

Les alertes de sécurité sont déclenchées par des détections avancées et sont disponibles uniquement avec les fonctionnalités de sécurité renforcée activées. Vous pouvez procéder à une mise à niveau à partir de la page **Paramètres d’environnement**, comme décrit dans [Démarrage rapide :Activer les fonctionnalités de sécurité avancée](enable-enhanced-security.md). Une version d’évaluation gratuite de 30 jours est disponible. Pour plus d’informations sur les prix dans la devise de votre choix et en fonction de votre région, consultez la [page sur la tarification](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="what-are-security-alerts-and-security-incidents"></a>Définition des alertes de sécurité et des incidents de sécurité 

Les **alertes** sont les notifications générées par Defender pour le cloud lorsqu’il détecte des menaces sur vos ressources. Defender pour le cloud hiérarchise et répertorie les alertes ainsi que les informations vous permettant d’investiguer rapidement le problème. Defender pour le cloud fournit également des étapes détaillées pour vous aider à atténuer les attaques. Les données d’alerte sont conservées pendant 90 jours.

Un **incident de sécurité** est une collection d’alertes apparentées, par opposition à une présentation individuelle des alertes. Defender pour le cloud utilise la [corrélation intelligente d’alertes cloud](#cloud-smart-alert-correlation-incidents) pour corréler différentes alertes et différents signaux faibles à des incidents de sécurité.

En utilisant les incidents, Defender pour le cloud vous offre une vue unique d’une campagne d’attaque et de toutes les alertes associées. Cette vue vous permet de comprendre rapidement quelles actions l’attaquant a effectuées et quelles ressources ont été impactées.

## <a name="respond-to-todays-threats"></a>Répondre aux menaces actuelles 

Les menaces ont fortement évolué au cours des 20 dernières années. Auparavant, les entreprises avaient uniquement à se soucier de la dégradation des sites web par des attaquants isolés qui cherchaient principalement à découvrir « jusqu’où ils pouvaient aller ». Les pirates d’aujourd’hui sont beaucoup plus évolués et organisés. Ils ont souvent des objectifs financiers et stratégiques spécifiques. Ils ont également davantage de ressources disponibles, car ils peuvent être financés par des États-nations ou encore par le crime organisé.

Ces évolutions ont conduit à un niveau inédit de professionnalisme dans les rangs des attaquants. Ces derniers ne s’intéressent plus à la dégradation de site web, mais plutôt au vol d’informations, aux comptes financiers et aux données privées qu’ils peuvent utiliser pour générer des liquidités sur le marché ouvert ou pour exploiter une activité commerciale particulière, ou encore une position politique ou militaire. Les pirates qui violent les réseaux afin de nuire à l’infrastructure et aux personnes constituent une menace bien plus importante que ceux avec des objectifs financiers.

Face au problème, les organisations déploient souvent plusieurs solutions, axées sur la défense du périmètre de l’entreprise ou de points de terminaison, en recherchant des signatures d’attaques connues. Ces solutions ont tendance à générer un volume élevé d’alertes basse fidélité, qui nécessitent qu’un analyste les trie et les examine. La plupart des organisations ne disposent pas du temps et de l’expertise nécessaires pour répondre à ces alertes, qui restent alors bien souvent sans réponse.  

En outre, les attaquants ont fait évoluer leurs méthodes pour compromettre les nombreuses défenses basées sur la signature et pour [s’adapter aux environnements de cloud](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). De nouvelles approches sont nécessaires pour identifier plus rapidement les nouvelles menaces, et accélérer la détection et la réaction.


## <a name="continuous-monitoring-and-assessments"></a>Surveillance et évaluations continues

Microsoft Defender pour le cloud tire parti des équipes de recherche de sécurité et de sciences des données au sein de Microsoft qui surveillent en continu les évolutions en matière de menaces. Cela inclut les initiatives suivantes :

* **Analyse des informations sur les menaces** : les informations sur les menaces incluent des mécanismes, des indicateurs, des implications et des conseils pratiques sur les menaces, nouvelles ou existantes. Ces informations sont partagées avec la communauté dédiée à la sécurité, et Microsoft surveille en permanence le flux des informations sur les menaces provenant de sources internes et externes.
* **Partage de signal** : les informations fournies par les équipes de sécurité sur le portefeuille complet de services cloud et locaux, de serveurs et d’appareils de point de terminaison client de Microsoft sont partagées et analysées.
* **Spécialistes de la sécurité Microsoft** : engagement continu avec les équipes Microsoft travaillant dans des domaines de la sécurité spécialisés, tels que la forensique et la détection d’attaques web.
* **Réglage de la détection** : des algorithmes sont exécutés sur les jeux de données client réels, et les chercheurs en sécurité collaborent avec les clients pour valider les résultats. Les vrais et les faux positifs sont utilisés pour affiner les algorithmes d’apprentissage automatique.

Ces efforts combinés aboutissent à de nouvelles détections et à des améliorations, dont vous pouvez bénéficier instantanément sans aucune action de votre part.

## <a name="how-does-defender-for-cloud-detect-threats"></a>Comment Defender pour le cloud détecte les menaces ? <a name="detect-threats"> </a>

Les chercheurs en sécurité de Microsoft sont constamment à l’affût des nouvelles menaces. Grâce à notre présence mondiale sur le cloud et localement, nous avons accès à un vaste jeu de télémétrie. Cette collection diverse et étendue de jeux de données nous permet de détecter de nouveaux modèles et de nouvelles tendances d’attaques dans nos produits locaux destinés au consommateur et aux entreprises, ainsi que dans nos services en ligne. Par conséquent, Defender pour le cloud peut rapidement mettre à jour ses algorithmes de détection, puisque les attaquants sont à l’origine d’attaques innovantes de plus en plus sophistiquées. Cette approche permet de faire face à des menaces en pleine mutation.

Pour détecter les menaces réelles et réduire le nombre de faux positifs, Defender pour le cloud collecte, analyse et intègre automatiquement les données des journaux de vos ressources Azure et du réseau. Cela fonctionne également avec les solutions de partenaires connectées, comme les solutions de pare-feu et de protection de point de terminaison. Defender pour le cloud analyse ces informations, souvent des informations corrélées issues de plusieurs sources, pour identifier les menaces.

![Collecte et présentation de données Defender pour le cloud](./media/alerts-overview/security-center-detection-capabilities.png)

Defender pour le cloud emploie une analytique de sécurité avancée qui va bien au-delà des approches simplement basées sur la signature. Les innovations en matière de Big Data et de technologies [Machine Learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) sont mises à profit pour évaluer des événements dans toute la structure du cloud, et permettent ainsi de détecter des menaces qui seraient impossibles à identifier à l’aide de méthodes manuelles et de prédire l’évolution des attaques. Ces analyses de sécurité comprennent les éléments suivants :

- **Threat Intelligence intégrée** : Microsoft dispose d’une multitude d’informations en matière de menaces à l’échelle mondiale. La télémétrie provient de plusieurs sources, telles qu’Azure, Microsoft 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Crimes Unit (DCU) et Microsoft Security Response Center (MSRC). Les chercheurs reçoivent aussi les informations sur les menaces partagées par les principaux fournisseurs de services cloud et les flux en provenance d’autres tiers. Microsoft Defender pour le cloud peut utiliser ces informations pour vous alerter en cas de menaces provenant d’éléments malveillants connus.

- **Analytique comportementale** : L’analyse comportementale est une technique qui analyse et compare les données à une collection de modèles connus. Toutefois, ces modèles ne sont pas de simples signatures. Ils sont déterminés par le biais d’algorithmes d’apprentissage automatique appliqués aux ensembles de données massifs. Ils sont également déterminés à travers une analyse minutieuse des comportements malveillants par des experts. Microsoft Defender pour le cloud peut utiliser l’analytique comportementale pour identifier les ressources compromises en se basant sur l’analyse des journaux de machine virtuelle, des journaux d’appareil réseau virtuel, des journaux d’infrastructure fabric et autres sources.

- **Détection d’anomalie** : Microsoft Defender pour le cloud utilise également la détection d’anomalie pour identifier les menaces. Contrairement à l’analyse comportementale (qui dépend de modèles connus dérivés de grands jeux de données), la détection d’anomalie est « personnalisée » et se concentre sur les lignes de base propres à vos déploiements. L’apprentissage automatique est appliqué pour déterminer l’activité normale de vos déploiements et les règles sont générées pour définir les conditions de valeurs aberrantes pouvant signaler un événement de sécurité.

## <a name="how-are-alerts-classified"></a>Comment les alertes sont-elles classifiées ?

Defender pour le cloud affecte un degré de gravité aux alertes pour vous aider à hiérarchiser l’ordre dans lequel vous remédiez à chaque alerte. Ainsi, quand une ressource est compromise, vous pouvez vous en occuper immédiatement. La gravité dépend du niveau de confiance que Defender pour le cloud accorde au résultat ou à l’analytique utilisés pour émettre l’alerte, ainsi que du niveau de confiance concernant le caractère malveillant de l’intention derrière l’activité à l’origine de l’alerte.

> [!NOTE]
> La gravité des alertes s’affiche différemment dans le portail et les versions de l’API REST antérieures au 01-01-2019. Si vous utilisez une ancienne version de l’API, effectuez une mise à niveau pour bénéficier de l’expérience cohérente décrite ci-dessous.

| severity          | Réponse recommandée                                                                                                                                                                                                                                                                                                                                                                                                                                             |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Importante**          | il est fort probable que votre ressource ait été compromise. Vous devez étudier le problème immédiatement. Defender pour le cloud a un niveau de confiance élevé quant à l’intention malveillante et aux résultats de recherche à l’origine de l’alerte. Par exemple, une alerte qui détecte l’exécution d’un outil malveillant connu, tel que Mimikatz, un outil couramment utilisé pour dérober des informations d’identification.                                                                                                           |
| **Moyenne**        | il s’agit probablement d’une activité suspecte susceptible d’indiquer qu’une ressource est compromise. Le niveau de confiance de Defender pour le cloud dans l’analytique ou les résultats de recherche est moyen, tandis le niveau de confiance quant à l’intention malveillante est moyen à élevé. Il s’agit généralement de détections basées sur des anomalies ou l’apprentissage automatique. Par exemple, une tentative de connexion depuis un emplacement anormal.                                                                                                            |
| **Faible**           | cela peut être un positif sans gravité ou une attaque bloquée. Defender pour le cloud n’est pas suffisamment confiant sur le fait que l’intention soit malveillante. L’activité est probablement innocente. Par exemple, l’effacement des journaux est une action qui peut se produire lorsqu’un pirate tente de masquer ses traces, mais, dans de nombreux cas, il s’agit d’une opération de routine effectuée par les administrateurs. Defender pour le cloud n’indique généralement pas les attaques bloquées, sauf s’il s’agit d’un cas qu’il peut être intéressant d’examiner. |
| **Informational** | Un incident est généralement constitué de plusieurs alertes, dont certaines peuvent apparaître individuellement à titre d’information seulement, mais mériter un examen plus approfondi dans le contexte des autres alertes.                                                                                                                                                                                                                                                               |
|                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |

## <a name="export-alerts"></a>Exporter les alertes

Vous disposez d’une gamme d’options pour l’affichage de vos alertes en dehors de Defender pour le cloud, notamment :

- **Télécharger le rapport CSV** dans le tableau de bord des alertes fournit une exportation ponctuelle au format CSV.
- L’**exportation continue** dan les paramètres Environnement vous permet de configurer des flux d’alertes et de recommandations de sécurité vers les espaces de travail Log Analytics et Event Hubs. [En savoir plus sur l’exportation continue](continuous-export.md).
- Le **connecteur Microsoft Sentinel** streame les alertes de sécurité de Microsoft Defender pour le cloud vers Microsoft Sentinel. [En savoir plus sur la connexion de Microsoft Defender pour le cloud à Microsoft Sentinel](../sentinel/connect-azure-security-center.md).

En savoir plus sur toutes les options d’exportation dans [Diffuser des alertes vers un système SIEM, SOAR ou une solution de gestion des services informatiques](export-to-siem.md) et [Exporter en continu des données Defender pour le cloud](continuous-export.md).

## <a name="cloud-smart-alert-correlation-incidents"></a>Corrélation intelligente d’alertes cloud (incidents)

Microsoft Defender pour le cloud analyse en continu les charges de travail cloud hybrides en utilisant une analytique avancée et le renseignement sur les menaces pour vous alerter sur les activités malveillantes.

La couverture contre les menaces s’étend progressivement. Face à la nécessité de détecter jusqu’à la moindre compromission, il peut s’avérer compliqué pour les analystes de sécurité de trier les différentes alertes et d’identifier une attaque réelle. Defender pour le cloud aide les analystes à venir à bout des alertes. Il facilite le diagnostic des attaques en temps réel, en mettant en corrélation les différentes alertes et les signaux de faible fidélité en incidents de sécurité.

L’analytique de fusion constitue le back-end technologique et analytique sur lequel reposent les incidents Defender pour le cloud, permettant la mise en corrélation des différentes alertes et signaux contextuels. La fusion examine les différents signaux relevés sur un abonnement parmi les ressources. La fusion recherche des modèles qui révèlent la progression des attaques ou les signaux avec des informations contextuelles partagées, indiquant que vous devez utiliser une procédure de réponse unifiée pour eux.

L’analytique de fusion combine les connaissances dans le domaine de la sécurité avec l’intelligence artificielle pour analyser les alertes, ce qui permet de découvrir les nouveaux modèles d’attaque à mesure qu’ils apparaissent. 

Defender pour le cloud tire parti de MITRE Attack Matrix pour associer les alertes à leur intention perçue, facilitant la formalisation des connaissances dans le domaine de la sécurité. De plus, en utilisant les informations collectées à chaque étape d’une attaque, Defender pour le cloud peut écarter les activités qui semblent être les étapes d’une attaque, mais qui ne le sont pas en réalité.

Étant donné que les attaques se produisent souvent sur différents locataires, Defender pour le cloud peut combiner des algorithmes IA pour analyser les séquences d’attaque signalées dans chaque abonnement. Cette technique identifie les séquences d’alerte comme des modèles d’alerte prédominants au lieu de simplement les associer les unes aux autres incidemment.

Dans le cadre d’une investigation d’un incident, les analystes ont souvent besoin de plus de contexte pour aboutir à un verdict sur la nature de la menace et sur les moyens de l’atténuer. Par exemple, même si une anomalie de réseau a été détectée, sans connaître les autres événements qui se produisent sur le réseau ou en lien avec la ressource ciblée, il est difficile de déterminer les actions à prendre. Pour vous aider, un incident de sécurité peut inclure des artefacts, des événements connexes et des informations. Les informations supplémentaires disponibles pour les incidents de sécurité varient en fonction du type de menace détecté et de la configuration de votre environnement. 

> [!TIP]
> Pour obtenir la liste des alertes d’incident de sécurité qui peuvent être générées par l’analytique de fusion, consultez la [table de référence des alertes](alerts-reference.md#alerts-fusion).

:::image type="content" source="./media/alerts-overview/security-incident.png" alt-text="Capture d’écran du rapport d’incident de sécurité détecté.":::

Pour gérer vos incidents de sécurité, consultez [Gérer les incidents de sécurité dans Microsoft Defender pour le cloud](incidents.md).

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a présenté les différents types d’alertes disponibles dans Defender pour le cloud. Pour plus d'informations, consultez les pages suivantes :

- [Alertes de sécurité dans le journal d’activité Azure](https://go.microsoft.com/fwlink/?linkid=2114113) : en plus d’être disponibles dans le portail Azure ou programmatiquement, les alertes et incidents de sécurité sont audités en tant qu’événements dans le Journal d’activité Azure.
- [Table de référence des alertes Defender pour le cloud](alerts-reference.md)
- [Répondre à des alertes de sécurité](managing-and-responding-alerts.md#respond-to-security-alerts)