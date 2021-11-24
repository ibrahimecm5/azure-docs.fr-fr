---
title: Comprendre le renseignement sur les menaces dans Microsoft Sentinel | Microsoft Docs
description: Découvrez la façon dont les flux de renseignement sur les menaces sont connectés à Microsoft Sentinel et y sont gérés et utilisés pour analyser les données, détecter les menaces et enrichir les alertes.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 09835e201417040d3f8db6fd2387f325b2728e9d
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132516947"
---
# <a name="understand-threat-intelligence-in-microsoft-sentinel"></a>Comprendre le renseignement sur les menaces dans Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="introduction-to-threat-intelligence"></a>Introduction au renseignement sur les menaces

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Le renseignement sur les cybermenaces (CTI, Cyber Threat Intelligence) est constitué d’informations qui décrivent des menaces connues existantes ou potentielles pour les systèmes et les utilisateurs. Ce type d’informations prend de nombreuses formes, depuis des rapports écrits détaillant les motivations, l’infrastructure et les techniques d’un auteur de menace particulier jusqu’à des observations spécifiques d’adresses IP, de domaines, de hachages de fichier et d’autres artefacts associés à des cybermenaces connues. Les organisations utilisent le renseignement sur les cybermenaces pour fournir un contexte essentiel à une activité inhabituelle, afin que le personnel de sécurité puisse prendre rapidement des mesures pour protéger leurs collaborateurs, informations et autres ressources. Le renseignement sur les cybermenaces peut provenir de plusieurs emplacements, comme des communautés de partage de renseignements sur les menaces, des flux de renseignements commerciaux et des renseignements recueillis au niveau local, le tout collecté au cours d’enquêtes de sécurité au sein d’une organisation.

Dans une solution SIEM (Informations de sécurité et gestion d’événements) comme Microsoft Sentinel, les indicateurs de menace, souvent appelés « indicateurs de compromission » ou « IoC », constituent la forme de renseignement sur les cybermenaces la plus utilisée. Les indicateurs de menace sont des données qui associent des artefacts observés comme des URL, des hachages de fichier ou des adresses IP, à une activité de menace connue comme le hameçonnage, les botnets ou les programmes malveillants. Cette forme de renseignement sur les menaces est souvent appelée *renseignement tactique sur les menaces*, car elle peut être appliquée à des produits de sécurité et à l’automatisation à grande échelle. Elle permet également de détecter les menaces potentielles auxquelles une organisation est exposée et de la protéger contre celles-ci. Dans Microsoft Sentinel, vous pouvez utiliser des indicateurs de menace pour détecter les activités malveillantes observées dans votre environnement et fournir un contexte aux enquêteurs sur la sécurité afin d’éclairer leurs décisions.

Intégrez le renseignement sur les menaces à Microsoft Sentinel par le biais des activités suivantes :

- **Importez le renseignement sur les menaces** dans Microsoft Sentinel en activant des **connecteurs de données** sur différentes [plateformes](connect-threat-intelligence-tip.md) et différents [flux](connect-threat-intelligence-taxii.md) de renseignement sur les menaces.

- **Affichez et gérez** le renseignement sur les menaces importé dans **Journaux** et dans le panneau **Renseignement sur les menaces** de Microsoft Sentinel.

- **Détectez les menaces** et générez des alertes et des incidents de sécurité à l’aide des modèles de règle d’**analytique** intégrés basés sur le renseignement sur les menaces importé.

- **Visualisez les informations essentielles** concernant le renseignement sur les menaces importé dans Microsoft Sentinel à l’aide du **classeur Renseignement sur les menaces**.

Microsoft enrichit tous les indicateurs du renseignement sur les menaces importés avec des [données de géolocalisation et WhoIs](#view-your-geolocation-and-whois-data-enrichments-public-preview), qui sont affichées avec d’autres détails d’indicateur.

> [!TIP]
> Le renseignement sur les menaces fournit également un contexte utile dans d’autres expériences Microsoft Sentinel, telles que la **chasse** et les **notebooks**. Pour plus d’informations, consultez [Notebooks Jupyter dans Microsoft Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/using-threat-intelligence-in-your-jupyter-notebooks/ba-p/860239) et [Tutoriel : Prise en main des notebooks Jupyter et de MSTICPy dans Microsoft Sentinel](notebook-get-started.md).
>

## <a name="import-threat-intelligence-with-data-connectors"></a>Importer le renseignement sur les menaces avec des connecteurs de données

Comme toutes les autres données d’événement dans Microsoft Sentinel, les indicateurs de menace sont importés à l’aide de connecteurs de données. Il existe deux connecteurs de données dans Microsoft Sentinel fournis spécifiquement pour les indicateurs de menace : **Threat Intelligence – TAXII** pour les flux STIX/TAXII standard et les **plateformes de renseignement sur les menaces** pour les flux intégrés et organisés de renseignement sur les menaces. Vous pouvez utiliser soit un connecteur de données seul, soit les deux connecteurs ensemble, en fonction de l’endroit d’où proviennent les indicateurs de menace utilisés par votre organisation.

Consultez le catalogue des [intégrations du renseignement sur les menaces](threat-intelligence-integration.md) disponibles avec Microsoft Sentinel.

### <a name="add-threat-indicators-to-microsoft-sentinel-with-the-threat-intelligence-platforms-data-connector"></a>Ajouter des indicateurs de menace à Microsoft Sentinel avec le connecteur de données Plateformes de renseignement sur les menaces

De nombreuses organisations utilisent des solutions de plateforme de renseignement sur les menaces (TIP) pour agréger les flux d’indicateurs de menace provenant de diverses sources, organiser les données au sein de la plateforme, puis choisir les indicateurs de menace à appliquer à différentes solutions de sécurité, comme les appareils réseau, les solutions EDR/XDR ou les solutions SIEM comme Microsoft Sentinel. Si votre organisation utilise une [solution intégrée de plateforme de renseignement sur les menaces](connect-threat-intelligence-tip.md), le **connecteur de données Plateformes de renseignement sur les menaces** vous permet d’utiliser cette plateforme pour importer des indicateurs de menace dans Microsoft Sentinel.

Étant donné que le connecteur de données Plateformes de renseignement sur les menaces fonctionne avec l’[API Microsoft Graph Security tiIndicators](/graph/api/resources/tiindicator) pour effectuer cette opération, il peut également être utilisé par n’importe quelle plateforme personnalisée de renseignement sur les menaces qui communique avec l’API tiIndicators pour envoyer des indicateurs à Microsoft Sentinel (et à d’autres solutions de sécurité Microsoft comme Microsoft 365 Defender).

:::image type="content" source="media/understand-threat-intelligence/threat-intel-import-path.png" alt-text="Chemin d’importation du renseignement sur les menaces":::

Pour plus d’informations sur les solutions de plateformes de renseignement sur les menaces intégrées à Microsoft Sentinel, consultez [Plateformes intégrées de renseignement sur les menaces](threat-intelligence-integration.md#integrated-threat-intelligence-platform-products).

**Pour importer des indicateurs de menace dans Microsoft Sentinel à partir de votre plateforme intégrée ou personnalisée de renseignement sur les menaces** :

1. Obtenir un **ID d’application** et un **secret client** à partir de votre instance Azure Active Directory

1. Entrer ces informations dans votre solution de plateforme Threat Intelligence (TIP) ou votre application personnalisée

1. Activer le connecteur de données Plateformes de renseignement sur les menaces dans Microsoft Sentinel

Pour plus d’informations, consultez [Connecter votre plateforme de renseignement sur les menaces à Microsoft Sentinel](connect-threat-intelligence-tip.md).

### <a name="add-threat-indicators-to-microsoft-sentinel-with-the-threat-intelligence---taxii-data-connector"></a>Ajouter des indicateurs de menace à Microsoft Sentinel avec le connecteur de données Threat Intelligence – TAXII

La norme du secteur la plus largement adoptée pour la transmission du renseignement sur les menaces est une [combinaison du format de données STIX et du protocole TAXII](https://oasis-open.github.io/cti-documentation/). Si votre organisation obtient des indicateurs de menace de solutions prenant en charge la version actuelle de STIX/TAXII (2.0 ou 2.1), vous pouvez utiliser le connecteur de données **Threat Intelligence – TAXII** pour introduire vos indicateurs de menace dans Microsoft Sentinel. Le connecteur de données Threat Intelligence – TAXII permet à un client TAXII intégré dans Microsoft Sentinel d’importer le renseignement sur les menaces à partir de serveurs TAXII 2.x.

:::image type="content" source="media/understand-threat-intelligence/threat-intel-taxii-import-path.png" alt-text="Chemin d’importation de TAXII":::

**Pour importer des indicateurs de menace au format STIX dans Microsoft Sentinel à partir d’un serveur TAXII** :

1. Obtenir la racine d’API et l’ID de collection du serveur TAXII

1. Pour activer le connecteur de données Threat Intelligence – TAXII dans Microsoft Sentinel

Pour plus d’informations, consultez [Connecter Microsoft Sentinel aux flux de renseignement sur les menaces STIX/TAXII](connect-threat-intelligence-taxii.md).

## <a name="view-and-manage-your-threat-indicators"></a>Afficher et gérer vos indicateurs de menace

Vous pouvez afficher les indicateurs de menace correctement importés, quel que soit le flux source ou le connecteur utilisé, dans la table **ThreatIntelligenceIndicator** (sous le groupe **Microsoft Sentinel**) dans **Journaux**, où sont stockées toutes vos données d’événement Microsoft Sentinel. Cette table est la base des requêtes de renseignement sur les menaces effectuées par d’autres fonctionnalités de Microsoft Sentinel, telles que les analyses et les classeurs.

Vos résultats doivent ressembler à l’exemple d’indicateur de menace illustré ci-dessous :

:::image type="content" source="media/understand-threat-intelligence/threat-intel-sample-query.png" alt-text="Exemple de données de requête":::

Vous pouvez également afficher et gérer vos indicateurs dans le nouveau panneau **Renseignement sur les menaces**, accessible à partir du menu principal de Microsoft Sentinel. Vous pouvez trier et filtrer vos indicateurs de menace importés, et y effectuer des recherches, sans même écrire de requête Log Analytics. Cette fonctionnalité vous permet également de créer des indicateurs de menace directement dans l’interface de Microsoft Sentinel, ainsi que d’effectuer deux des tâches d’administration les plus courantes concernant le renseignement sur les menaces : l’étiquetage des indicateurs et la création d’indicateurs liés aux investigations de sécurité.

L’étiquetage des indicateurs de menace est un moyen simple de les regrouper pour faciliter leur recherche. En général, vous pouvez appliquer une étiquette à des indicateurs liés à un incident spécifique ou à ceux représentant les menaces provenant d’un auteur connu particulier ou d’une campagne d’attaque bien connue. Vous pouvez étiqueter les indicateurs de menace individuellement, ou sélectionner plusieurs indicateurs et les étiqueter tous en même temps. L’exemple ci-dessous montre l’étiquetage de plusieurs indicateurs avec un ID d’incident. Étant donné que l’étiquetage est de forme libre, il est recommandé de créer des conventions de nommage standard pour les étiquettes d’indicateur de menace. Vous pouvez appliquer plusieurs étiquettes à chaque indicateur.

:::image type="content" source="media/understand-threat-intelligence/threat-intel-tagging-indicators.png" alt-text="Appliquer des étiquettes à des indicateurs de menace" lightbox="media/understand-threat-intelligence/threat-intel-tagging-indicators.png":::

Pour plus d’informations sur l’affichage et la gestion de vos indicateurs de menace, consultez [Utiliser des indicateurs de menace dans Microsoft Sentinel](work-with-threat-indicators.md#view-your-threat-indicators-in-microsoft-sentinel).

### <a name="view-your-geolocation-and-whois-data-enrichments-public-preview"></a>Voir vos enrichissements de données de géolocalisation et WhoIs (préversion publique)

Microsoft enrichit chaque indicateur avec des données de géolocalisation et WhoIs supplémentaires, fournissant davantage de contexte pour les investigations dans lesquelles l’indicateur de compromission (IOC) sélectionné est trouvé.

Vous pouvez voir les données GeoLocation et WhoIs dans le volet **Renseignement sur les menaces** pour chaque indicateur de compromission que vous avez importé dans Microsoft Sentinel.

Par exemple, utilisez les données de géolocalisation pour rechercher des détails tels que l’*organisation* ou le *pays* associé à l’indicateur, et les données Whois pour rechercher des données telles que celles sur le *bureau d’enregistrement* et la *création d’enregistrements*.

## <a name="detect-threats-with-threat-indicator-based-analytics"></a>Détecter les menaces avec l’analytique basée sur des indicateurs de menace

Le cas d’usage le plus important pour les indicateurs de menace dans les solutions SIEM comme Microsoft Sentinel consiste à fournir des règles d’analyse dans le cadre de la détection des menaces. Ces règles basées sur les indicateurs comparent les événements bruts provenant de vos sources de données avec vos indicateurs de menace pour détecter les menaces de sécurité qui pèsent sur votre organisation. Dans **Analyse** de Microsoft Sentinel, vous créez des règles d’analyse qui s’exécutent selon une planification et qui génèrent des alertes de sécurité. Les règles sont pilotées par des requêtes ainsi que par des configurations qui déterminent la fréquence à laquelle la règle doit s’exécuter, le type de résultats de requête qui doivent générer des alertes et incidents de sécurité et, le cas échéant, les réponses automatisées à déclencher.

Même si vous pouvez toujours créer des règles d’analyse à partir de zéro, Microsoft Sentinel fournit un ensemble de modèles de règle intégrés, créé par des ingénieurs en sécurité de Microsoft, que vous pouvez utiliser tels quels ou modifier pour répondre à vos besoins. Vous pouvez facilement identifier les modèles de règle qui utilisent des indicateurs de menace, car ils ont tous un nom qui commence par « **TI map**... » (Carte de renseignement sur les menaces...). Tous ces modèles de règle fonctionnent de la même manière, la seule différence étant le type d’indicateurs de menace utilisés (domaine, e-mail, hachage de fichier, adresse IP ou URL) et le type d’événements à mettre en correspondance. Chaque modèle liste les sources de données nécessaires pour que la règle fonctionne, de sorte que vous pouvez voir d’un coup d’œil si les événements nécessaires sont déjà importés dans Microsoft Sentinel. Lorsque vous modifiez et enregistrez un modèle de règle existant ou que vous créez une règle, elle est activée par défaut.

Vous pouvez retrouver la règle que vous avez activée sous l’onglet **Règles actives** de la section **Analyse** de Microsoft Sentinel. Vous pouvez modifier, activer, désactiver, dupliquer ou supprimer la règle active à partir de cet emplacement. La nouvelle règle s’exécute immédiatement lors de l’activation, et à compter de ce moment, s’exécute selon la planification définie pour elle.

Selon les paramètres par défaut, chaque fois que la règle s’exécute selon sa planification, tous les résultats trouvés génèrent une alerte de sécurité. Dans Microsoft Sentinel, les alertes de sécurité peuvent être consultées dans la section **Journaux**, dans la table **SecurityAlert** située sous le groupe **Microsoft Sentinel**.

Dans Microsoft Sentinel, les alertes générées à partir des règles d’analyse génèrent également des incidents de sécurité qui se trouvent dans **Incidents** sous **Gestion des menaces**  dans le menu de Microsoft Sentinel. Les incidents sont ce que vos équipes chargées des opérations de sécurité trient et examinent pour déterminer les actions de réponse appropriées. Vous trouverez des informations détaillées dans le [tutoriel : Examiner les incidents avec Microsoft Sentinel](./investigate-cases.md).

Pour plus d’informations sur l’utilisation des indicateurs de menace dans vos règles d’analyse, consultez [Utiliser des indicateurs de menace dans Microsoft Sentinel](work-with-threat-indicators.md#detect-threats-with-threat-indicator-based-analytics).

## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>Les classeurs fournissent des insights concernant le renseignement sur les menaces

Les classeurs fournissent des tableaux de bord interactifs puissants qui vous donnent des insights sur tous les aspects de Microsoft Sentinel, et le renseignement sur les menaces ne fait pas exception. Vous pouvez utiliser le **workbook Threat Intelligence** intégré pour visualiser des informations essentielles concernant le renseignement sur les menaces et vous pouvez facilement personnaliser le workbook en fonction des besoins de votre entreprise. Vous pouvez même créer des tableaux de bord qui associent de nombreuses sources de données pour vous permettre de visualiser vos données de manière unique. Étant donné que les classeurs Microsoft Sentinel sont basés sur les classeurs Azure Monitor, une documentation complète et de nombreux autres modèles sont déjà disponibles. Cet article est idéal pour découvrir comment [Créer des rapports interactifs avec les classeurs Azure Monitor](../azure-monitor/visualize/workbooks-overview.md).

Il existe également une vaste communauté de [classeurs Azure Monitor sur GitHub](https://github.com/microsoft/Application-Insights-Workbooks) où vous pouvez télécharger des modèles supplémentaires et partager vos propres modèles.

Pour plus d’informations sur l’utilisation et la personnalisation du classeur Renseignement sur les menaces, consultez [Utiliser des indicateurs de menace dans Microsoft Sentinel](work-with-threat-indicators.md#workbooks-provide-insights-about-your-threat-intelligence).

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez découvert les capacités de renseignement sur les menaces de Microsoft Sentinel, notamment le panneau Renseignement sur les menaces. Pour obtenir des conseils pratiques sur l’utilisation des capacités de renseignement sur les menaces de Microsoft Sentinel, consultez les articles suivants :

- Connecter Microsoft Sentinel aux [flux de renseignements sur les menaces STIX/TAXII](./connect-threat-intelligence-taxii.md).
- [Connecter des plateformes de renseignement sur les menaces](./connect-threat-intelligence-tip.md) à Microsoft Sentinel.
- Découvrir les [plateformes de renseignement sur les menaces, les flux TAXII et les enrichissements](threat-intelligence-integration.md) qui peuvent facilement être intégrés à Microsoft Sentinel.
- [Utiliser des indicateurs de menace](work-with-threat-indicators.md) tout au long de l’expérience Microsoft Sentinel.
- Détecter des menaces avec des règles d’analyse [intégrées](./detect-threats-built-in.md) ou [personnalisées](./detect-threats-custom.md) dans Microsoft Sentinel.
- [Examiner les incidents](./investigate-cases.md) dans Microsoft Sentinel.
