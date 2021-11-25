---
title: Détecter des menaces à l’aide de règles d’analyse intégrées dans Microsoft Sentinel | Microsoft Docs
description: Découvrez comment utiliser des règles de détection des menaces prêtes à l’emploi, basées sur des modèles prédéfinis, qui vous informent quand quelque chose de suspect se produit.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: ff11dc0344bdde74b5a84893aa1d90fe2744019d
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521526"
---
# <a name="detect-threats-out-of-the-box"></a>Détection des menaces prête à l’emploi

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Une fois que vous avez [connecté vos sources de données](quickstart-onboard.md) à Microsoft Sentinel, vous souhaitez être averti en cas d’activité suspecte. C’est pourquoi Microsoft Sentinel fournit des modèles intégrés et prêts à l’emploi pour vous aider à créer des règles de détection des menaces.

Ces modèles de règles ont été conçus par l’équipe d’analystes et d’experts en sécurité de Microsoft en fonction de menaces connues, de vecteurs d’attaque courants et de chaînes d’escalade d’activités suspectes. Les règles créées à partir de ces modèles rechercheront automatiquement toute activité qui semble suspecte dans votre environnement. La plupart des modèles peuvent être personnalisés pour rechercher des activités ou les filtrer, en fonction de vos besoins. Les alertes générées par ces règles vont créer des incidents que vous pouvez attribuer et examiner dans votre environnement.

Cet article vous aide à comprendre comment détecter les menaces avec Microsoft Sentinel :

> [!div class="checklist"]
> * Utiliser des détections de menaces prêtes à l’emploi
> * Automatiser les réponses aux menaces

## <a name="view-built-in-detections"></a>Afficher les détections intégrées

Pour afficher toutes les détections et les règles d’analyse dans Microsoft Sentinel, accédez à **Analyse** > **Modèles de règles**. Cet onglet contient toutes les règles intégrées de Microsoft Sentinel.

:::image type="content" source="media/tutorial-detect-built-in/view-oob-detections.png" alt-text="Utiliser des détections intégrées pour identifier les menaces avec Microsoft Sentinel":::

Les détections intégrées sont les suivantes :

| Type de règle | Description |
| --------- | --------- |
| **Sécurité Microsoft** | Les modèles de sécurité Microsoft créent automatiquement des incidents Microsoft Sentinel à partir des alertes générées dans d’autres solutions de sécurité Microsoft, en temps réel. Vous pouvez utiliser des règles de sécurité Microsoft comme modèle pour créer des règles ayant une logique similaire. <br><br>Pour plus d’informations sur les règles de sécurité, consultez [Créer automatiquement des incidents à partir d’alertes de sécurité Microsoft](create-incidents-from-alerts.md). |
| <a name="fusion"></a>**Fusion**<br>(certaines détections en préversion) | Microsoft Sentinel utilise le moteur de corrélation de Fusion, avec ses algorithmes évolutifs d’apprentissage automatique, pour détecter les attaques multiphases avancées en mettant en corrélation de nombreuses alertes et événements de faible fidélité sur plusieurs produits en incidents haute fidélité et actionnables. La fusion est activée par défaut. Comme la logique est cachée et donc non personnalisable, vous ne pouvez créer qu’une seule règle avec ce modèle. <br><br>Le moteur Fusion peut également corréler des alertes produites par des [règles analytiques planifiées](#scheduled) avec celles d’autres systèmes, en produisant à la fin des incidents haute-fidélité. |
| **Analytique comportementale du Machine Learning** | Les modèles d’analyse comportementale ML sont basés sur des algorithmes Microsoft Machine Learning propriétaires. Vous ne pouvez donc pas voir la logique interne de leur fonctionnement et du moment de leur exécution. <br><br>Comme la logique est cachée et donc non personnalisable, vous ne pouvez créer qu’une seule règle avec chaque modèle de ce type. |
| <a name="anomaly"></a>**Anomalie**<br>(Préversion) | Les modèles de règle d’anomalie utilisent SOC-ML (Machine Learning) pour détecter des types spécifiques de comportement anormal. Chaque règle a ses propres paramètres et seuils uniques, adaptés au comportement en cours d’analyse. <br><br>Même si ces configurations de règles ne peuvent pas être changées ni ajustées, vous pouvez dupliquer la règle, changer et ajuster la duplication. Dans ce cas, exécutez la duplication en mode **Flighting** et l’original simultanément en mode **Production**. Comparez ensuite les résultats et basculez la duplication en mode **Production** si et quand son ajustement vous convient. <br><br>Pour plus d’informations, consultez [Utiliser des anomalies SOC-ML pour détecter des menaces dans Microsoft Sentinel](soc-ml-anomalies.md) et [Utiliser des règles d’analyse de détection d’anomalie dans Microsoft Sentinel](work-with-anomaly-rules.md). |
| <a name="scheduled"></a>**Planifié** | Les règles d’analytique planifiées sont basées sur des requêtes intégrées écrites par des experts en sécurité de Microsoft. Vous pouvez voir la logique de la requête et y apporter des changements. Vous pouvez utiliser le modèle de règles planifiées et personnaliser la logique des requêtes et les paramètres de planification pour créer de nouvelles règles. <br><br>Plusieurs nouveaux modèles de règle d’analyse planifiée produisent des alertes que le moteur de fusion met en corrélation avec des alertes d’autres systèmes afin de produire des incidents de haute fidélité. Pour plus d’informations, consultez [Détection avancée des attaques multiphases](configure-fusion-rules.md#configure-scheduled-analytics-rules-for-fusion-detections).<br><br>**Conseil** : Les options de planification de règles incluent la configuration de la règle pour qu’elle s’exécute chaque nombre spécifié de minutes, d’heures ou de jours, l’horloge démarrant quand vous activez la règle. <br><br>Nous vous recommandons d’être attentif lorsque vous activez une règle d’analyse nouvelle ou modifiée pour vous assurer que les règles obtiendront la nouvelle pile d’incidents en temps opportun. Par exemple, vous pouvez exécuter une règle au moment où vos analystes du centre des opérations de sécurité commencent leur journée de travail, puis activer les règles.|
| <a name="nrt"></a>**Quasi-temps réel (NRT)**<br>(Préversion) | Les règles NRT sont un ensemble limité de règles planifiées, conçues pour s’exécuter une fois toutes les minutes, afin de vous fournir des informations aussi récentes que possible. <br><br>Elles fonctionnent principalement comme des règles planifiées et sont configurées de façon similaire, avec certaines limitations. Pour plus d’informations, consultez [Détecter rapidement les menaces avec des règles d’analyse en quasi-temps réel dans Microsoft Sentinel](near-real-time-rules.md). |
| | |

> [!IMPORTANT]
> - Les modèles de règles, comme indiqué ci-dessus, sont actuellement en **PRÉVERSION**, tout comme certains modèles de détection **Fusion** (pour voir lesquels, consultez [Détection des attaques multiphases avancées dans Microsoft Sentinel](fusion.md)). Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.
>
> - En créant et en activant des règles basées sur des modèles d’**analyse comportementale ML**, **vous autorisez Microsoft à copier les données ingérées en dehors de la zone géographique de votre espace de travail Microsoft Sentinel**, le cas échéant, en vue de leur traitement par des moteurs et modèles de Machine Learning.
>

## <a name="use-built-in-analytics-rules"></a>Utiliser des règles analytiques intégrées

Cette procédure décrit comment utiliser les modèles de règles analytiques intégrées.

**Pour utiliser des règles analytiques intégrées** :

1. Dans la page Microsoft Sentinel > **Analyse** > **Modèles de règles**, sélectionnez le nom du modèle, puis sélectionnez le bouton **Créer une règle** dans le volet d’informations pour créer une règle active basée sur ce modèle. 

    Chaque modèle possède une liste de sources de données requises. Lorsque vous ouvrez le modèle, la disponibilité des sources de données est automatiquement vérifiée. En cas de problème de disponibilité, le bouton **Créer une règle** peut être désactivé ou un avertissement à cet effet peut s’afficher.

    :::image type="content" source="media/tutorial-detect-built-in/use-built-in-template.png" alt-text="Panneau d’aperçu de la règle de détection":::

1. La sélection de **Créer une règle** ouvre l’Assistant création de règles en fonction du modèle sélectionné. Tous les détails sont remplis automatiquement et, grâce aux modèles **Planifié** ou **Sécurité Microsoft**, vous pouvez personnaliser la logique et les autres paramètres de règle pour mieux répondre à vos besoins spécifiques. Vous pouvez répéter ce processus pour créer des règles supplémentaires basées sur le modèle intégré. Après avoir suivi jusqu’au bout les étapes de l’Assistant Création de règles, vous aurez terminé la création d’une règle basée sur le modèle. Les nouvelles règles s’affichent dans l’onglet **Règles actives**.

    Pour plus d’informations sur la personnalisation de vos règles dans l’Assistant création de règles, consultez [Créer des règles analytiques personnalisées pour détecter les menaces](detect-threats-custom.md).

> [!TIP]
> - Veillez à **activer toutes les règles associées à vos sources de données connectées** afin de garantir une protection complète de votre environnement. La méthode la plus efficace pour activer les règles analytiques est directement à partir de la page du connecteur de données, qui liste toutes les règles associées. Pour plus d’informations, consultez [Connecter des sources de données](connect-data-sources.md).
> 
> - Vous pouvez également **envoyer des règles à Microsoft Sentinel via l’[API](/rest/api/securityinsights/) et [PowerShell](https://www.powershellgallery.com/packages/Az.SecurityInsights/0.1.0)** , même si cela demande davantage d’effort. 
> 
>     Lorsque vous utilisez l’API ou PowerShell, vous devez d’abord exporter les règles vers JSON avant de les activer. L’API ou PowerShell peuvent être utiles lors de l’activation de règles dans plusieurs instances de Microsoft Sentinel avec des paramètres identiques dans chaque instance.
> 
## <a name="export-rules-to-an-arm-template"></a>Exporter des règles vers un modèle ARM

Vous pouvez facilement [exporter votre règle vers un modèle Azure Resource Manager (ARM)](import-export-analytics-rules.md) si vous souhaitez gérer et déployer vos règles en tant que code. Vous pouvez également importer des règles à partir de fichiers modèles afin de les afficher et de les modifier dans l’interface utilisateur.

## <a name="next-steps"></a>Étapes suivantes

- Pour créez des règles personnalisées, utilisez des règles existantes en tant que modèles ou références. L’utilisation de règles existantes en tant que ligne de base permet de générer la majeure partie de la logique avant d’apporter les modifications nécessaires. Pour plus d’informations, consultez [Créer des règles analytiques personnalisées pour détecter des menaces](detect-threats-custom.md).

- Pour découvrir comment automatiser vos réponses aux menaces, consultez [Configurer des réponses automatisées aux menaces dans Microsoft Sentinel](tutorial-respond-threats-playbook.md).
