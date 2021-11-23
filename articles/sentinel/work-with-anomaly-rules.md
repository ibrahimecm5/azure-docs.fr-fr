---
title: Utiliser des règles d’analyse de détection d’anomalie dans Microsoft Sentinel | Microsoft Docs
description: Cet article explique comment afficher, créer, gérer, évaluer et affiner des règles d’analyse de détection d’anomalie dans Microsoft Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1469f89877c9a20009dd3e1178b77a7f001314ba
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518771"
---
# <a name="work-with-anomaly-detection-analytics-rules-in-microsoft-sentinel"></a>Utiliser des règles d’analyse de détection d’anomalie dans Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - Les règles d’anomalie sont actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

## <a name="view-soc-ml-anomaly-rule-templates"></a>Afficher les modèles de règles d’anomalie SOC-ML

La [fonctionnalité d’anomalies SOC-ML](soc-ml-anomalies.md) de Microsoft Sentinel fournit des [modèles d’anomalies intégrés](detect-threats-built-in.md#anomaly) pour une valeur immédiate prête à l’emploi. Ces modèles d’anomalies ont été développés pour être robustes en utilisant des milliers de sources de données et des millions d’événements, mais cette fonctionnalité vous permet également de modifier facilement les seuils et les paramètres des anomalies dans l’interface utilisateur. Les règles d’anomalie doivent être activées avant de générer des anomalies, que vous pouvez trouver dans la table **Anomalies** de la section **Journaux**.

1. Dans le menu de navigation de Microsoft Sentinel, sélectionnez **Analytique**.

1. Dans le panneau **Analytique**, sélectionnez l’onglet **Modèles de règles**.

1. Filtrez la liste sur les modèles **Anomalies** :

    1. Cliquez sur le filtre **Type de règle**, puis sur la liste déroulante qui apparaît dessous.

    1. Décochez la case **Sélectionner tout**, puis sélectionnez **Anomalie**.

    1. Si nécessaire, cliquez sur le haut de la liste déroulante pour la rétracter, puis cliquez sur **OK**.

## <a name="activate-anomaly-rules"></a>Activer les règles d’anomalie

Lorsque vous cliquez sur l’un des modèles de règles, les détails suivants s’affichent dans le volet d’informations, ainsi que le bouton **Créer une règle** :

- **Description** explique le fonctionnement de l’anomalie et les données dont elle a besoin.

- **Sources de données** indique le type de journaux qui doivent être ingérés afin d’être analysés.

- **Tactiques** désigne les tactiques de l’infrastructure MITRE ATT&CK couvertes par l’anomalie.

- **Paramètres** désigne les attributs configurables pour l’anomalie.

- **Seuil** désigne une valeur configurable qui indique le degré auquel un événement doit être inhabituel avant la création d’une anomalie.

- **Fréquence de la règle** désigne le temps entre les travaux de traitement des journaux qui recherchent les anomalies.

- **Version d’anomalie** indique la version du modèle qui est utilisée par une règle. Si vous souhaitez modifier la version utilisée par une règle qui est déjà active, vous devez recréer la règle.

- **Dernière mise à jour du modèle** correspond à la date à laquelle la version d’anomalie a été modifiée.

Procédez comme suit pour activer une règle :

1. Choisissez un modèle de règles qui n’est pas déjà étiqueté **EN COURS D’UTILISATION**. Cliquez sur le bouton **Créer une règle** pour ouvrir l’Assistant Création de règles.

    L’Assistant sera légèrement différent pour chaque modèle de règles, mais il comporte trois étapes ou onglets : **Général**, **Configuration** et **Vérifier et créer**.

    Vous ne pouvez modifier aucune des valeurs dans l’Assistant ; vous devez d’abord créer et activer la règle.

1. Parcourez les onglets, attendez le message « Validation réussie » sur l’onglet **Vérifier et créer** et sélectionnez le bouton **Créer**.

    Vous ne pouvez créer qu’une seule règle active à partir de chaque modèle. Une fois l’Assistant terminé, une règle d’anomalie active est créée sous l’onglet **Règles actives**, et le modèle (sous l’onglet **Modèles de règles**) est marqué **EN COURS D’UTILISATION**.

    > [!NOTE]
    > En supposant que les données requises soient disponibles, la nouvelle règle peut prendre jusqu’à 24 heures pour apparaître dans l’onglet **Règles actives**. Pour afficher les nouvelles règles, sélectionnez l’onglet Règles actives et filtrez-le de la même façon que vous avez filtré la liste Modèles de règles ci-dessus.

Une fois la règle d’anomalie activée, les anomalies détectées sont stockées dans la table **Anomalies** de la section **Journaux** de votre espace de travail Microsoft Sentinel.

Chaque règle d’anomalie a une période de formation, et les anomalies n’apparaîtront dans la table qu’à la fin de cette période de formation. Vous trouverez la période de formation dans la description de chaque règle d’anomalie.

## <a name="assess-the-quality-of-anomalies"></a>Évaluer la qualité des anomalies

Vous pouvez voir l’efficacité d’une règle d’anomalie en examinant un échantillon des anomalies créées par la règle au cours des dernières 24 heures. 

1. Dans le menu de navigation de Microsoft Sentinel, sélectionnez **Analytique**.

1. Dans le panneau **Analytique**, vérifiez que l’onglet **Règles actives** est sélectionné.

1. Filtrez la liste sur les règles **Anomalie** (comme ci-dessus).

1. Sélectionnez la règle que vous souhaitez évaluer et copiez son nom en haut du volet d’informations à droite.

1. Dans le menu de navigation de Microsoft Sentinel, sélectionnez **Journaux**.

1. Si une galerie **Requêtes** apparaît en haut, fermez-la.

1. Sélectionnez l’onglet **Tables** dans le volet gauche du panneau **Journaux**.

1. Définissez le filtre **Intervalle de temps** sur **Dernières 24 heures**.

1. Copiez la requête Kusto ci-dessous et collez-la dans la fenêtre de requête (où il est indiqué « Saisissez votre requête ici ou… ») :

    ```kusto
    Anomalies 
    | where AnomalyTemplateName contains "________________________________"
    ```
    Collez le nom de la règle que vous avez copié ci-dessus à la place des traits de soulignement entre guillemets.

1. Cliquez sur **Exécuter**. 

Lorsque vous obtenez quelques résultats, vous pouvez commencer à évaluer la qualité des anomalies. Si vous n’avez pas de résultats, essayez d’augmenter l’intervalle de temps.

Développez les résultats de chaque anomalie, puis développez le champ **AnomalyReasons**. Il vous indique la raison pour laquelle l’anomalie s’est déclenchée.

Le caractère « raisonnable » ou « utile » d’une anomalie peut dépendre des conditions de votre environnement, mais une raison courante pour laquelle une règle d’anomalie produit un trop grand nombre d’anomalies est que le seuil est trop bas.

## <a name="tune-anomaly-rules"></a>Ajuster les règles d’anomalie

Bien que les règles d’anomalie soient conçues pour une efficacité maximale dès leur création, chaque situation est unique et les règles d’anomalie doivent parfois être ajustées.

Comme vous ne pouvez pas modifier une règle active originale, vous devez d’abord dupliquer une règle d’anomalie active, puis personnaliser la copie.

La règle d’anomalie originale continuera de fonctionner jusqu’à ce que vous la désactiviez ou la supprimiez.

Cela est prévu pour vous donner la possibilité de comparer les résultats générés par la configuration d’origine et la nouvelle. Les règles dupliquées sont désactivées par défaut. Vous ne pouvez faire qu’une seule copie personnalisée d’une règle d’anomalie donnée. Les tentatives de création d’une deuxième copie échoueront.

1. Pour modifier la configuration d’une règle d’anomalie, sélectionnez la règle d’anomalie dans l’onglet **Règles actives**.

1. Cliquez avec le bouton droit n’importe où sur la ligne de la règle, ou cliquez sur le bouton de sélection (…) à la fin de la ligne, puis cliquez sur **Dupliquer**.

1. La nouvelle copie de la règle aura le suffixe « - Personnalisé » dans le nom de la règle. Pour personnaliser cette règle, sélectionnez-la et cliquez sur **Modifier**.

1. La règle s’ouvre dans l’Assistant Règle d’analyse. Vous pouvez y modifier les paramètres de la règle et son seuil. Les paramètres qui peuvent être modifiés varient selon le type d’anomalie et l’algorithme.

    Vous pouvez afficher un aperçu des résultats à la suite de vos modifications dans le **volet Aperçu des résultats**. Cliquez sur un **ID d’anomalie** dans l’aperçu des résultats pour voir pourquoi le modèle ML identifie cette anomalie.

1. Activez la règle personnalisée pour générer des résultats. Certaines de vos modifications peuvent nécessiter une nouvelle exécution de la règle. Vous devez donc attendre qu’elle se termine et revenir en arrière pour vérifier les résultats sur la page Journaux. La règle d’anomalie personnalisée s’exécute par défaut en mode **Flighting** (test). Par défaut , la règle originale continue à s’exécuter en mode **Production**.

1. Pour comparer les résultats, revenez à la table Anomalies dans **Journaux** pour [évaluer la nouvelle règle comme précédemment](#assess-the-quality-of-anomalies). Recherchez uniquement les lignes avec le nom de la règle originale, ainsi que le nom de la règle dupliquée avec « - Personnalisé » ajouté à celui-ci dans la colonne **AnomalyTemplateName**.

    Si vous êtes satisfait des résultats de la règle personnalisée, vous pouvez revenir à l’onglet **Règles actives**, cliquer sur la règle personnalisée, puis sur le bouton **Modifier** et, dans l’onglet **Général**, faire basculer la règle du mode **Flighting** au mode **Production**. La règle originale passera automatiquement en mode **Flighting**, car vous ne pouvez pas avoir deux versions de la même règle en production en même temps. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à utiliser des règles d’analyse de détection d’anomalie SOC-ML dans Microsoft Sentinel.

- Obtenez des informations générales sur [SOC-ML](soc-ml-anomalies.md).
- Explorez d’autres [types de règles d’analyse](detect-threats-built-in.md).
