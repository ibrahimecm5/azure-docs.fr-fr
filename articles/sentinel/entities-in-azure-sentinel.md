---
title: Utiliser des entités pour classer et analyser les données dans Microsoft Sentinel | Microsoft Docs
description: Attribuez des classifications d’entité (utilisateurs, noms d’hôte, adresses IP) à des éléments de données dans Microsoft Sentinel et utilisez-les pour comparer, analyser et mettre en corrélation des données provenant de différentes sources.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5fe74357b404b516f1fac5285bbca9d7fe5d2afb
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521260"
---
# <a name="classify-and-analyze-data-using-entities-in-microsoft-sentinel"></a>Classer et analyser les données à l’aide d’entités dans Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="what-are-entities"></a>Que sont les entités ?

Lorsque des alertes sont envoyées à Microsoft Sentinel ou générées par celui-ci, elles contiennent des éléments de données que Sentinel peut reconnaître et classer en catégories en tant qu’**entités**. Lorsque Microsoft Sentinel identifie le type d’entité que représente un élément de données particulier, il sait quelles sont les bonnes questions à poser à son sujet, et il peut ensuite comparer les informations relatives à cet élément sur l’ensemble des sources de données et facilement le suivre et s’y référer tout au long de l’expérience Sentinel (analyse, investigation, correction, chasse, etc.). Les utilisateurs, les hôtes, les fichiers, les processus, les adresses IP et les URL sont des exemples courants d'entités.

### <a name="entity-identifiers"></a>Identificateurs d’entité

Microsoft Sentinel prend en charge un large éventail de types d’entités. Chaque type possède des attributs uniques, dont certains peuvent être utilisés pour identifier une entité particulière. Ces attributs, représentés sous forme de champs dans l'entité, sont appelés des **identificateurs**. Consultez la liste complète des entités prises en charge et de leurs identificateurs ci-dessous.

#### <a name="strong-and-weak-identifiers"></a>Identificateurs forts et faibles

Comme indiqué ci-dessus, chaque type d'entité est associé à des champs, ou à des ensembles de champs, qui permettent de l'identifier. Ces champs ou ensembles de champs peuvent être qualifiés d'**identificateurs forts** s'ils permettent d'identifier une entité de manière unique sans aucune ambiguïté, ou d'**identificateurs faibles** s'ils permettent d'identifier une entité dans certaines circonstances, sans toutefois garantir une identification unique dans tous les cas. Dans de nombreux cas, cependant, différents identificateurs faibles peuvent être combinés pour produire un identificateur fort.

Par exemple, les comptes d'utilisateur peuvent être identifiés en tant qu'entités **Compte** de plusieurs façons : à l'aide d'un seul **identificateur fort**, comme l'identificateur numérique d'un compte Azure AD (le champ **GUID**), ou de sa valeur **UPN (nom d'utilisateur principal**), ou encore en utilisant une combinaison d'**identificateurs faibles** tels que ses champs **Name** et **NTDomain**. Des sources de données différentes peuvent identifier le même utilisateur de manières différentes. Chaque fois que Microsoft Sentinel rencontre deux entités qu’il reconnaît comme étant la même entité sur la base de leurs identificateurs, il fusionne les deux entités en une seule afin qu’elles puissent être traitées correctement et de manière cohérente.

Cela dit, si l'un de vos fournisseurs de ressources crée une alerte stipulant qu'une entité n'est pas suffisamment identifiée (par exemple, en utilisant un seul **identificateur faible** comme un nom d'utilisateur sans le contexte de nom de domaine), l'entité utilisateur ne peut pas être fusionnée avec d'autres instances du même compte d'utilisateur. Ces autres instances sont alors identifiées comme une entité distincte, et ces deux entités restent séparées au lieu d'être unifiées.

Afin de minimiser le risque que cela se produise, vous devez vérifier que tous vos fournisseurs d'alertes identifient correctement les entités dans les alertes qu'ils produisent. En outre, la synchronisation d’entités de compte d’utilisateur avec Azure Active Directory peut créer un répertoire d’unification, qui sera en mesure de fusionner les entités de compte d’utilisateur.

#### <a name="supported-entities"></a>Entités prises en charge

Les types d’entités suivants sont actuellement identifiés dans Microsoft Sentinel :

- Compte d’utilisateur
- Host
- Adresse IP
- Programme malveillant
- Fichier
- Process
- Application cloud
- Nom de domaine
- Ressource Azure
- Hachage du fichier
- Clé de Registre
- Valeur de Registre
- Groupe de sécurité
- URL
- Appareil IoT
- Mailbox
- Cluster de messagerie
- Message électronique
- E-mail d'envoi

Vous pouvez consulter les identificateurs de ces entités ainsi que d'autres informations pertinentes dans [Informations de référence sur les entités](entities-reference.md).

## <a name="entity-mapping"></a>Mappage d’entités

Comment Microsoft Sentinel reconnaît-il une donnée comme identificateur d’une entité dans une alerte ?

Observons le traitement des données dans Microsoft Sentinel. Les données sont ingérées à partir de différentes sources par le biais de [connecteurs](connect-data-sources.md), que ce soit de service à service, sur la base d'un agent, ou à l'aide d'un service syslog et d'un redirecteur de journal. Les données sont stockées dans des tables de votre espace de travail Log Analytics. Ces tables sont ensuite interrogées à intervalles réguliers par les règles analytiques que vous avez définies et activées. L’une des nombreuses actions entreprises par ces règles d’analyse est le mappage des champs de données des tables aux entités reconnues par Microsoft Sentinel. Selon les mappages que vous définissez dans vos règles d’analyse, Microsoft Sentinel prend des champs dans les résultats renvoyés par votre requête, les reconnaît grâce aux identificateurs que vous avez spécifiés pour chaque type d’entité et leur applique le type d’entité identifié par ces identificateurs.

Quel est l'intérêt de tout cela ?

Lorsque Microsoft Sentinel est en mesure d’identifier des entités dans des alertes provenant de différents types de sources de données, et surtout s’il peut le faire à l’aide d’identificateurs forts communs à chaque source de données ou à un troisième schéma, il peut facilement mettre en corrélation toutes ces alertes et sources de données. Ces corrélations permettent de constituer une précieuse banque d'informations et d'insights sur les entités, ce qui vous confère une base solide pour vos opérations de sécurité.

Apprenez à [mapper des champs de données avec des entités](map-data-fields-to-entities.md).

Découvrez [les identificateurs qui permettent une identification forte d'une entité](entities-reference.md).

## <a name="entity-pages"></a>Pages d’entité

Lorsque vous rencontrez une entité (les entités sont actuellement limitées à des utilisateurs et des hôtes) dans une recherche, une alerte ou une investigation, vous pouvez sélectionner l’entité et être redirigé vers une **page d’entité**, à savoir une feuille de données remplie d’informations utiles sur cette entité. Les types d’informations que contient cette page incluent des faits de base sur l’entité, une chronologie d’événements notables liés à cette entité, ainsi que des insights sur le comportement de l’entité.

Les pages d’entité se composent de trois parties :

- Le volet gauche contient les informations d’identification de l’entité, collectées à partir de sources de données telles qu’Azure Active Directory, Azure Monitor et Microsoft Defender pour le cloud.

- Le panneau central affiche une chronologie graphique et textuelle des événements notables liés à l’entité, tels que des alertes, des signets et des activités. Les activités sont des agrégations d’événements notables de Log Analytics. Les requêtes qui détectent ces activités sont développées par des équipes de recherche en matière de sécurité de Microsoft.

- Le panneau de droite présente des insights comportementales sur l’entité. Ces insights permettent d’identifier rapidement des anomalies et autres menaces de sécurité. Les insights sont développés par des équipes de recherche en matière de sécurité Microsoft, et sont basées sur des modèles de détection d’anomalies.

### <a name="the-timeline"></a>La chronologie

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="Chronologie des pages d’entité":::

La chronologie est une partie importante de la contribution de la page d’entité à l’analyse comportementale dans Microsoft Sentinel. Elle présente l’historique des événements liés à l’entité, qui vous aide à comprendre l’activité de celle-ci dans un délai d’exécution spécifique.

Vous pouvez choisir le **délai d’exécution** parmi plusieurs options prédéfinies (par exemple, *dernières 24 heures*), ou définir un délai d’exécution personnalisé. En outre, vous pouvez définir des filtres qui limitent les informations de la chronologie à des types spécifiques d’événements ou d’alertes.

Les types d’éléments suivants sont inclus dans la chronologie :

- Alertes – toute alerte dans laquelle l’entité est définie en tant qu’**entité mappée**. Notez que, si votre organisation a créé des [alertes personnalisées utilisant des règles d’analyse](./detect-threats-custom.md), vous devez vous assurer que le mappage d’entité des règles est correctement effectué.

- Signets – signets incluant l’entité affichée sur la page.

- Activités – agrégation d’événements notables liés à l’entité.

### <a name="entity-insights"></a>Insights sur l’entité

Les insights d’entité sont des requêtes définie par les chercheurs en matière de sécurité de Microsoft pour aider vos analystes à investiguer plus efficacement. Les insights sont présentées dans la page de l’entité. Elles fournissent des informations de sécurité précieuses sur les hôtes et les utilisateurs, sous la forme de graphiques et de données tabulaires. Les informations étant disponibles ici, vous n’avez pas à passer par Log Analytics. Les insights contiennent des données relatives aux connexions, aux ajouts de groupes, aux événements anormaux, et ainsi de suite. Ils incluent également des algorithmes de Machine Learning avancés pour détecter les comportements anormaux.

Les insights reposent sur les sources de données suivantes :

- Syslog (Linux)
- SecurityEvent (Windows)
- AuditLogs (Azure AD)
- SigninLogs (Azure AD)
- OfficeActivity (Office 365)
- BehaviorAnalytics (UEBA Microsoft Sentinel)
- Heartbeat (Azure Monitor Agent)
- CommonSecurityLog (Microsoft Sentinel)

### <a name="how-to-use-entity-pages"></a>Comment utiliser les pages d’entité

Les pages d’entité sont conçues pour faire partie de plusieurs scénarios d’usage. Elles sont accessibles à partir de la gestion des incidents, du graphe d’investigation, des signets ou directement à partir de la page de recherche d’entité sous **Analyse du comportement des entités** dans le menu principal de Microsoft Sentinel.

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="Cas d’usage de page d’entité":::

## <a name="next-steps"></a>Étapes suivantes

Ce document vous a permis de vous familiariser avec l’utilisation des entités dans Microsoft Sentinel. Pour obtenir des conseils pratiques sur l’implémentation et pour utiliser les insights que vous avez acquises, consultez les articles suivants :

- [Activer l’analyse comportementale des entités](./enable-entity-behavior-analytics.md) dans Microsoft Sentinel.
- [Chasse des menaces de sécurité](./hunting.md).
