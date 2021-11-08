---
title: Utiliser des indicateurs de menace dans Azure Sentinel | Microsoft Docs
description: Cet article explique comment afficher, créer, gérer, visualiser et détecter des menaces à l’aide d’indicateurs de renseignement sur les menaces dans Azure Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/17/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: cfa9d14d13270a038586d5c0949a510ccd5ad44b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131019517"
---
# <a name="work-with-threat-indicators-in-azure-sentinel"></a>Utiliser des indicateurs de menace dans Azure Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Vous pouvez intégrer le renseignement sur les menaces à Azure Sentinel par le biais des activités suivantes :

- **Importez le renseignement sur les menaces** dans Azure Sentinel en activant des **connecteurs de données** sur différents [flux](connect-threat-intelligence-taxii.md) et [plateformes](connect-threat-intelligence-tip.md) de renseignement sur les menaces.

- **Affichez et gérez** le renseignement sur les menaces importé dans des **journaux** et dans la page **Renseignement sur les menaces** d’Azure Sentinel.

- **Détectez les menaces** et générez des alertes et des incidents de sécurité à l’aide des modèles de règle d’**analytique** intégrés basés sur le renseignement sur les menaces importé.

- **Visualisez les informations essentielles** concernant le renseignement sur les menaces importé dans Azure Sentinel à l’aide du **workbook Threat Intelligence**.

## <a name="view-your-threat-indicators-in-azure-sentinel"></a>Consulter vos indicateurs de menace dans Azure Sentinel

### <a name="find-and-view-your-indicators-in-logs"></a>Rechercher et afficher vos indicateurs dans les journaux

Cette procédure décrit comment afficher les indicateurs de menace importés dans la zone **Journaux** d’Azure Sentinel, ainsi que d’autres données d’événement Azure Sentinel, quel que soit le flux source ou le connecteur utilisé.

Les indicateurs de menace importés sont répertoriés dans le tableau **Azure Sentinel > ThreatIntelligenceIndicator**, qui est la base des requêtes de renseignement sur les menaces exécutées ailleurs dans Azure Sentinel, par exemple dans les analyses ou les classeurs.

**Pour afficher les indicateurs de renseignements sur les menaces dans les journaux** :

1. Ouvrez le [portail Azure](https://portal.azure.com/), puis accédez au service **Azure Sentinel**.

1. Sélectionnez l’espace de travail dans lequel vous avez importé des indicateurs de menace à l’aide de l’un ou l’autre des connecteurs de données du renseignement sur les menaces.

1. Sélectionnez **Journaux** dans la section **Général** du menu Azure Sentinel.

1. La table **ThreatIntelligenceIndicator** se trouve sous le groupe **Azure Sentinel**.

1. Sélectionnez l’icône **Aperçu des données** (représentant un œil) en regard du nom de la table, puis sélectionnez le bouton **Afficher dans l’éditeur de requête** pour exécuter une requête qui affiche les enregistrements de cette table.

Vos résultats doivent ressembler à l’exemple d’indicateur de menace illustré ci-dessous :

:::image type="content" source="media/work-with-threat-indicators/threat-intel-sample-query.png" alt-text="Exemple de données de requête":::

### <a name="find-and-view-your-indicators-in-the-threat-intelligence-page"></a>Rechercher et afficher vos indicateurs dans la page Renseignement sur les menaces

Vous pouvez également afficher et gérer vos indicateurs dans la page **Renseignement sur les menaces**, accessible à partir du menu Azure Sentinel principal. Utilisez la page **Renseignement sur les menaces** pour trier et filtrer vos indicateurs de menace importés, et y effectuer des recherches, sans même écrire de requête Log Analytics.

**Pour afficher vos indicateurs d’informations sur les menaces dans la page Renseignement sur les menaces** :

1. Ouvrez le [portail Azure](https://portal.azure.com/), puis accédez au service **Azure Sentinel**.

1. Sélectionnez l’espace de travail dans lequel vous avez importé les indicateurs de menace à l’aide des connecteurs/playbooks ou avez créé des données de renseignement sur les menaces.

1. Sélectionnez la page **Renseignement sur les menaces** dans la section **Gestion des menaces** sur la gauche.

1. Dans la grille, sélectionnez l’indicateur pour lequel vous souhaitez afficher plus de détails. Les détails de l’indicateur s’affichent à droite, présentant des informations telles que les niveaux de confiance, les balises, les types de menaces, etc.

    Microsoft enrichit chaque indicateur avec des données de géolocalisation et WhoIs supplémentaires, fournissant davantage de contexte pour les investigations dans lesquelles l’indicateur sélectionné est trouvé.

    Par exemple :

    :::image type="content" source="media/whats-new/geolocation-whois-ti.png" alt-text="Capture d’écran de la page Renseignement sur les menaces avec un indicateur montrant les données de géolocalisation et WhoIs." lightbox="media/whats-new/geolocation-whois-ti.png":::

La page **Renseignement sur les menaces** vous permet également de créer des indicateurs de menace directement dans l’interface Azure Sentinel ainsi que d’effectuer deux des tâches d’administration les plus courantes concernant le renseignement sur les menaces : l’étiquetage des indicateurs et la création d’indicateurs liés aux investigations de sécurité.

> [!IMPORTANT]
> La géolocalisation et l’enrichissement WhoIs sont actuellement en VERSION PRÉLIMINAIRE. Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>

#### <a name="create-a-new-indicator"></a>Créer un indicateur

1. Depuis le [portail Azure](https://portal.azure.com/), accédez au service **Azure Sentinel**.

1. Choisissez l’**espace de travail** dans lequel vous avez importé des indicateurs de menace à l’aide de l’un ou l’autre des connecteurs de données du renseignement sur les menaces.

1. Sélectionnez **Threat Intelligence** dans la section Gestion des menaces du menu Azure Sentinel.

1. Sélectionnez le bouton **Ajouter nouveau** dans le menu en haut de la page.

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-add-new-indicator.png" alt-text="Ajouter un nouvel indicateur de menace" lightbox="media/work-with-threat-indicators/threat-intel-add-new-indicator.png":::

1. Choisissez le type d’indicateur, puis renseignez le formulaire dans le panneau **Nouvel indicateur**. Les champs obligatoires sont indiqués d’un astérisque rouge (*).

1. Sélectionnez **Appliquer**. L’indicateur est ajouté à la liste d’indicateurs, et envoyé à la table *ThreatIntelligenceIndicator* dans **Journaux**.

#### <a name="tag-threat-indicators"></a>Étiqueter les indicateurs de menace

L’étiquetage des indicateurs de menace est un moyen simple de les regrouper pour faciliter leur recherche. En général, vous pouvez appliquer une étiquette à des indicateurs liés à un incident spécifique ou à ceux représentant les menaces provenant d’un auteur connu particulier ou d’une campagne d’attaque bien connue. Vous pouvez étiqueter les indicateurs de menace individuellement, ou sélectionner plusieurs indicateurs et les étiqueter tous en même temps. L’exemple ci-dessous montre l’étiquetage de plusieurs indicateurs avec un ID d’incident. Étant donné que l’étiquetage est de forme libre, il est recommandé de créer des conventions de nommage standard pour les étiquettes d’indicateur de menace. Vous pouvez appliquer plusieurs étiquettes à chaque indicateur.

:::image type="content" source="media/work-with-threat-indicators/threat-intel-tagging-indicators.png" alt-text="Appliquer des étiquettes à des indicateurs de menace" lightbox="media/work-with-threat-indicators/threat-intel-tagging-indicators.png":::

## <a name="detect-threats-with-threat-indicator-based-analytics"></a>Détecter les menaces avec l’analytique basée sur des indicateurs de menace

Le cas d’usage le plus important pour les indicateurs de menace dans les solutions SIEM comme Azure Sentinel consiste à alimenter les règles d’analyse de détection des menaces. Ces règles basées sur les indicateurs comparent les événements bruts provenant de vos sources de données avec vos indicateurs de menace pour déterminer la présence de menaces de sécurité qui pèsent sur votre organisation. Dans l’option **Analytique** d’Azure Sentinel, vous créez des règles d’analytique qui s’exécutent selon une planification et qui génèrent des alertes de sécurité. Les règles sont pilotées par des requêtes ainsi que par des configurations qui déterminent la fréquence à laquelle la règle doit s’exécuter, le type de résultats de requête qui doivent générer des alertes et incidents de sécurité et, le cas échéant, les réponses automatisées à déclencher.

Alors que vous pouvez toujours créer des règles d’analytique à partir de zéro, Azure Sentinel fournit un ensemble de modèles de règle intégrés, créés par des ingénieurs Sécurité Microsoft, que vous pouvez utiliser tels quels ou modifier pour répondre à vos besoins. Vous pouvez facilement identifier les modèles de règle qui utilisent des indicateurs de menace, car ils ont tous un nom qui commence par « *TI map*... » (Carte de renseignement sur les menaces...). Tous ces modèles de règle fonctionnent de la même manière, la seule différence étant le type d’indicateurs de menace utilisés (domaine, e-mail, hachage de fichier, adresse IP ou URL) et le type d’événements à mettre en correspondance. Chaque modèle liste les sources de données nécessaires pour que la règle fonctionne, de sorte que vous pouvez voir d’un coup d’œil si les événements nécessaires sont déjà importés dans Azure Sentinel. Lorsque vous modifiez et enregistrez un modèle de règle existant ou que vous créez une règle, elle est activée par défaut.

### <a name="configure-a-rule-to-generate-security-alerts"></a>Configurer une règle pour générer des alertes de sécurité

Vous trouverez ci-dessous un exemple montrant comment activer et configurer une règle pour générer des alertes de sécurité à l’aide des indicateurs de menace que vous avez importés dans Azure Sentinel. Pour cet exemple, utilisez le modèle de règle nommé **TI map IP entity to AzureActivity**. Cette règle fait correspondre n’importe quel indicateur de menace de type adresse IP à tous vos événements d’activité Azure. Quand une correspondance est trouvée, une **alerte** est générée, ainsi qu’un **incident** correspondant en vue d’une enquête par votre équipe en charge des opérations de sécurité. Cette règle d’analytique ne fonctionnera correctement que si vous avez activé l’un des connecteurs de données **Threat Intelligence** ou les deux (pour importer des indicateurs de menace), et le connecteur de données **Activité Azure** (pour importer vos événements au niveau de l’abonnement Azure).

1. Depuis le [portail Azure](https://portal.azure.com/), accédez au service **Azure Sentinel**.

1. Choisissez l’**espace de travail** dans lequel vous avez importé des indicateurs de menace à l’aide des connecteurs de données **Threat Intelligence** et les données d’activité Azure à l’aide du connecteur de données **Azure Activity**.

1. Sélectionnez **Analytics** dans la section **Configuration** du menu Azure Sentinel.

1. Sélectionnez l’onglet **Modèles de règle** pour voir la liste des modèles de règle d’analytique disponibles.

1. Recherchez la règle intitulée **TI map IP entity to AzureActivity**, puis vérifiez que vous avez connecté toutes les sources de données nécessaires comme ci-dessous.

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-required-data-sources.png" alt-text="Sources de données nécessaires":::

1. Sélectionnez la règle **TI map IP entity to AzureActivity** (entité IP de carte de renseignement sur les menaces sur AzureActivity), puis sélectionnez **Créer une règle** pour ouvrir l’Assistant Configuration de la règle. Configurez les paramètres de l’Assistant, puis sélectionnez **Suivant : Définir la logique de la règle >** .

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-create-analytics-rule.png" alt-text="Créer une règle d’analytique":::

1. La partie logique de règle de l’Assistant contient les éléments suivants :
    - La requête qui sera utilisée dans la règle.

    - Les mappages d’entité, qui indiquent à Azure Sentinel comment reconnaître des entités comme des comptes, des adresses IP et des URL, afin que les **incidents** et les **enquêtes** comprennent comment utiliser les données se trouvant dans toute alerte de sécurité générée par cette règle.

    - La planification de l’exécution de cette règle.

    - Le nombre de résultats de requête nécessaires avant qu’une alerte de sécurité soit générée.

    Les paramètres par défaut du modèle sont les suivants :
    - Exécuter une fois par heure.

    - Faire correspondre tous les indicateurs de menace d’adresse IP de la table **ThreatIntelligenceIndicator** avec toute adresse IP trouvée au cours de la dernière heure d’événements de la table **AzureActivity**.

    - Générer une alerte de sécurité si les résultats de requête sont supérieurs à zéro, c’est-à-dire si des correspondances sont trouvées.

    Vous pouvez conserver les paramètres par défaut ou les modifier en fonction de vos besoins, et définir des paramètres de génération d’incident sous l’onglet **Paramètres d’incident**. Pour plus d’informations, consultez [Créer des règles d’analytique personnalisées pour détecter des menaces](detect-threats-custom.md). Lorsque vous avez terminé, sélectionnez l’onglet **Automated response** (Réponse automatique).

1. Configurez une automatisation que vous souhaitez déclencher quand une alerte de sécurité est générée à partir de cette règle d’analytique. Dans Azure Sentinel, l’automatisation est effectuée à l’aide de **règles d’automatisation** et de **playbooks** alimentés par Azure Logic Apps. Pour plus d’informations, consultez [Didacticiel : Utiliser des playbooks avec des règles d’automatisation dans Azure Sentinel](./tutorial-respond-threats-playbook.md). Lors que vous avez fini, sélectionnez le bouton **Suivant : Révision >** pour continuer.

1. Lorsque vous voyez le message indiquant que la validation de la règle a réussi, cliquez sur le bouton **Create** (Créer) pour terminer.

Vous pouvez trouver vos règles activées sous l’onglet **Règles actives** de la section **Analytique** d’Azure Sentinel. Vous pouvez modifier, activer, désactiver, dupliquer ou supprimer la règle active à partir de cet emplacement. La nouvelle règle s’exécute immédiatement lors de l’activation, et à compter de ce moment, s’exécute selon la planification définie pour elle.

Selon les paramètres par défaut, chaque fois que la règle s’exécute selon sa planification, tous les résultats trouvés génèrent une alerte de sécurité. Dans Azure Sentinel, les alertes de sécurité peuvent être consultées dans la section **Journaux** d’Azure Sentinel, dans la table **SecurityAlert** située sous le groupe **Azure Sentinel**.

Dans Azure Sentinel, les alertes générées à partir des règles d’analytique génèrent également des incidents de sécurité qui se trouvent dans **Incidents** sous **Gestion des menaces**  dans le menu Azure Sentinel. Les incidents sont ce que vos équipes chargées des opérations de sécurité trient et examinent pour déterminer les actions de réponse appropriées. Vous trouverez des informations détaillées dans ce [Tutoriel : Examiner les incidents avec Azure Sentinel](./investigate-cases.md).

## <a name="detect-threats-using-matching-analytics-public-preview"></a>Détecter les menaces à l’aide de l’analyse de correspondance (préversion)

> [!IMPORTANT]
> L’analyse de correspondance est actuellement en PRÉVERSION. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.
>

[Créez une règle](detect-threats-built-in.md#use-built-in-analytics-rules) à l’aide du modèle de règle analytique **Analyse de correspondance Microsoft Threat Intelligence** intégré pour demander à Azure Sentinel de mettre en correspondance les données du renseignement sur les menaces générées par Microsoft avec les journaux que vous avez ingérés dans Azure Sentinel.

La mise en correspondance des données du renseignement sur les menaces avec vos journaux permet de générer des alertes et des incidents de haute fidélité, avec des gravités appropriées appliquées. Quand une correspondance est trouvée, toutes les alertes générées sont regroupées dans des incidents.

Les alertes sont regroupées par aspect observable sur une période de 24 heures. Par exemple, toutes les alertes générées au cours d’une période de 24 heures qui correspondent au domaine `abc.com` sont regroupées dans un incident unique.

### <a name="triage-through-an-incident-generated-by-matching-analytics"></a>Triage d’un incident généré par analyse de correspondance

Si une correspondance est trouvée, toutes les alertes générées sont regroupées dans des incidents.

Suivez les étapes suivantes pour trier les incidents générés par la règle **Analyse de correspondance Microsoft Threat Intelligence** :

1. Dans l’espace de travail Azure Sentinel dans lequel vous avez activé la règle **Analyse de correspondance Microsoft Threat Intelligence**, sélectionnez **Incidents**, puis recherchez **Analyse Microsoft Threat Intelligence**.

    Tous les incidents détectés sont affichés dans la grille.

1. Sélectionnez **Afficher tous les détails** pour voir les entités et d’autres détails sur l’incident, tels que des alertes spécifiques.

    Par exemple :

    :::image type="content" source="media/work-with-threat-indicators/matching-analytics.png" alt-text="Exemples de détails d’analyse correspondants.":::

Quand une correspondance est trouvée, l’indicateur est également publié dans l’analytique des journaux **ThreatIntelligenceIndicators** et affiché dans la page **Threat Intelligence**. Pour tous les indicateurs publiés à partir de cette règle, la source est définie comme **Analyse Microsoft Threat Intelligence**.

Par exemple, dans le journal **ThreatIntelligenceIndicators** :

:::image type="content" source="media/work-with-threat-indicators/matching-analytics-logs.png" alt-text="Analyse de correspondance affichée dans le journal ThreatIntelligenceIndicators.":::

Dans la page **Threat Intelligence** :

:::image type="content" source="media/work-with-threat-indicators/matching-analytics-threat-intelligence.png" alt-text="Analyse de correspondance affichée dans la page Threat Intelligence.":::

### <a name="supported-log-sources-for-matching-analytics"></a>Sources de journal prises en charge pour l’analyse de correspondance

La règle **Analyse de correspondance Microsoft Threat Intelligence** est actuellement prise en charge pour les sources de journal suivantes :

|Source de journal  |Description  |
|---------|---------|
|[CEF](connect-common-event-format.md)     |  La correspondance est établie pour tous les journaux CEF ingérés dans la table **CommonSecurityLog** de Log Analytics, sauf pour ceux où la valeur `DeviceVendor` est `Cisco`. <br><br>Pour faire correspondre le renseignement sur les menaces généré par Microsoft avec les journaux CEF, veillez à mapper le domaine dans le champ `RequestURL` du journal CEF.      |
|[DNS](./data-connectors-reference.md#domain-name-server)     | La correspondance est établie pour tous les journaux DNS qui sont des requêtes DNS de recherche adressées par des clients aux services DNS (`SubType == "LookupQuery"`). Les requêtes DNS sont traitées uniquement pour les requêtes IPv4 (`QueryType=”A”`) et IPv6 (`QueryType=” AAAA”`).<br><br>Pour faire correspondre le renseignement sur les menaces généré par Microsoft avec les journaux DNS, aucun mappage manuel de colonnes n’est nécessaire, car toutes les colonnes du serveur DNS Windows sont standard, et les domaines se trouvent dans la colonne `Name` par défaut.   |
|[Syslog](connect-syslog.md)     |  La correspondance est actuellement établie uniquement pour les événements Syslog où `Facility` est `cron`. <br><br>Pour faire correspondre le renseignement sur les menaces généré par Microsoft avec Syslog, aucun mappage manuel de colonnes n’est nécessaire. Les détails sont fournis dans le champ `SyslogMessage` du Syslog par défaut, et la règle analyse le domaine directement à partir du SyslogMessage.     |
|     |         |


## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>Les classeurs fournissent des insights concernant le renseignement sur les menaces

Vous pouvez utiliser un classeur Azure Sentinel spécialement conçu pour visualiser des informations essentielles concernant votre renseignement sur les menaces dans Azure Sentinel, et personnaliser facilement le classeur en fonction de vos besoins.
Voici comment trouver le classeur de renseignement sur les menaces fourni dans Azure Sentinel, et comment y apporter des modifications pour le personnaliser.

1. Depuis le [portail Azure](https://portal.azure.com/), accédez au service **Azure Sentinel**.

1. Choisissez l’**espace de travail** dans lequel vous avez importé des indicateurs de menace à l’aide de l’un ou l’autre des connecteurs de données du renseignement sur les menaces.

1. Sélectionnez **Classeurs** dans la section **Gestion des menaces** du menu Azure Sentinel.

1. Recherchez le classeur intitulé **Threat Intelligence**, puis vérifiez que vous avez des données dans la table **ThreatIntelligenceIndicator**, comme ci-dessous.

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-verify-data.png" alt-text="Vérifier les données":::

1. Sélectionnez le bouton **Enregistrer**, puis choisissez un emplacement Azure pour stocker le classeur. Cette étape est nécessaire si vous envisagez de modifier le classeur de quelque façon que ce soit et d’enregistrer les modifications apportées.

1. Sélectionnez maintenant le bouton **Afficher le workbook enregistré** pour ouvrir le classeur afin de le consulter et de le modifier.

1. Vous devez maintenant voir les graphiques par défaut fournis par le modèle. Pour modifier un graphique, sélectionnez le bouton **Modifier** en haut de la page afin de passer en mode édition pour le classeur.

1. Ajoutons un nouveau graphique des indicateurs de menace par type de menace. Faites défiler la page vers le bas, puis sélectionnez **Ajouter une requête**.

1. Ajoutez le texte suivant dans la zone de texte **Journal des requêtes de l’espace de travail Log Analytics** :
    ```kusto
    ThreatIntelligenceIndicator
    | summarize count() by ThreatType
    ```

1. Dans la liste déroulante **Visualisation**, sélectionnez **Graphique à barres**.

1. Sélectionnez le bouton **Modification terminée**. Vous avez créé un graphique pour votre classeur.

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-bar-chart.png" alt-text="Graphique à barres":::

Les classeurs fournissent des tableaux de bord interactifs puissants qui vous donnent des insights de tous les aspects d’Azure Sentinel. Il y a beaucoup de choses que vous pouvez faire avec des classeurs, et même si les modèles fournis constituent un excellent point de départ, vous voudrez probablement aller plus loin en personnalisant ces modèles, ou en créant des tableaux de bord qui combinent de nombreuses sources de données différentes pour vous permettre de visualiser vos données de manière unique. Étant donné que les classeurs Azure Sentinel sont basés sur des classeurs Azure Monitor, une documentation complète et de nombreux autres modèles sont déjà disponibles. Cet article est idéal pour découvrir comment [Créer des rapports interactifs avec les classeurs Azure Monitor](../azure-monitor/visualize/workbooks-overview.md). 

Il existe également une vaste communauté de [classeurs Azure Monitor sur GitHub](https://github.com/microsoft/Application-Insights-Workbooks) où vous pouvez télécharger des modèles supplémentaires et partager vos propres modèles.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris toutes les façons dont vous pouvez utiliser des indicateurs de renseignement sur les menaces dans Azure Sentinel. Pour plus d’informations sur le renseignement sur les menaces dans Azure Sentinel, consultez les articles suivants :
- [Comprendre le renseignement sur les menaces dans Azure Sentinel](understand-threat-intelligence.md).
- Connecter Azure Sentinel aux [flux de renseignement sur les menaces STIX/TAXII](./connect-threat-intelligence-taxii.md).
- [Connecter des plateformes de renseignement sur les menaces](./connect-threat-intelligence-tip.md) à Azure Sentinel.
- Découvrez les [plateformes de renseignement sur les menaces, flux TAXII et enrichissements](threat-intelligence-integration.md) qui peuvent être facilement intégrés avec Azure Sentinel.
