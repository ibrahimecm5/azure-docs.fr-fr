---
title: Créer des incidents à partir d’alertes dans Microsoft Sentinel | Microsoft Docs
description: Découvrez comment créer des incidents à partir d’alertes dans Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 8f68294e2b1ab473e17552e708bf82a1bad6d8a5
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523636"
---
# <a name="automatically-create-incidents-from-microsoft-security-alerts"></a>Créer automatiquement des incidents à partir d’alertes de sécurité Microsoft

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Les alertes déclenchées dans des solutions de sécurité Microsoft qui sont connectées à Microsoft Sentinel, comme Microsoft Defender for Cloud Apps et Microsoft Defender pour Identity (anciennement Azure ATP), ne créent pas automatiquement d’incidents dans Microsoft Sentinel. Par défaut, quand vous connectez une solution Microsoft à Microsoft Sentinel, toute alerte générée dans ce service sera stockée sous forme de données brutes dans Microsoft Sentinel, dans la table Security Alert de votre espace de travail Microsoft Sentinel. Vous pouvez ensuite utiliser ces données comme n’importe quelle autre donnée brute que vous connectez à Microsoft Sentinel.

Vous pouvez facilement configurer Microsoft Sentinel pour créer automatiquement des incidents chaque fois qu’une alerte est déclenchée dans une solution de sécurité Microsoft connectée en suivant les instructions mentionnées dans cet article.

## <a name="prerequisites"></a>Prérequis

Vous devez [connecter des solutions de sécurité Microsoft](connect-data-sources.md#data-connection-methods) pour activer la création d’incident à partir d’alertes de service de sécurité.

## <a name="using-microsoft-security-incident-creation-analytics-rules"></a>Utilisation de règles d’analytique de création d’incidents de sécurité Microsoft

Utilisez les règles intégrées disponibles dans Microsoft Sentinel pour choisir les solutions de sécurité Microsoft connectées qui doivent créer automatiquement des incidents Microsoft Sentinel en temps réel. Vous pouvez également modifier les règles afin de définir des options plus spécifiques pour le filtrage des alertes générées par la solution de sécurité Microsoft en vue de créer des incidents dans Microsoft Sentinel. Par exemple, vous pouvez choisir de créer automatiquement des incidents Microsoft Sentinel uniquement à partir d’alertes Microsoft Defender pour le cloud (anciennement Azure Security Center) dont le niveau de gravité est élevé.

1. Dans le portail Azure, sous Microsoft Sentinel, sélectionnez **Analyse**.

1. Sélectionnez l’onglet **Modèles de règle** pour afficher toutes les règles d’analytique intégrées.

    ![Modèles de règle](media/incidents-from-alerts/rule-templates.png)

1. Choisissez le modèle de règle analytique **Sécurité Microsoft** que vous voulez utiliser, puis sélectionnez **Créer une règle**.

    ![Règle d’analytique de sécurité](media/incidents-from-alerts/security-analytics-rule.png)

1. Vous pouvez modifier les détails de la règle et choisir de filtrer les alertes qui vont créer des incidents par niveau de gravité d’alerte ou sur le texte contenu dans le nom de l’alerte.  
      
    Par exemple, si vous choisissez **Microsoft Defender pour le cloud** (qui peut encore être appelé *Microsoft Defender pour le cloud*) dans le champ **Service de sécurité Microsoft** et que vous choisissez **Elevée** dans le champ **Filtrer par gravité**, seules les alertes de sécurité dont le niveau de gravité est élevé créeront automatiquement des incidents dans Microsoft Sentinel.  

    ![Assistant Créer une règle](media/incidents-from-alerts/create-rule-wizard.png)

1. Vous pouvez également créer une règle de **sécurité Microsoft** qui filtre les alertes de différents services de sécurité Microsoft en cliquant sur **+Créer**, puis en sélectionnant **Règle de création d’incident Microsoft**.

    ![Règle de création d’incident](media/incidents-from-alerts/incident-creation-rule.png)

    Vous pouvez créer plusieurs règles d’analytique **Microsoft Sécurité** par type de **service de sécurité Microsoft**. Cela ne crée pas d’incidents en double, car chaque règle est utilisée comme filtre. Même si une alerte correspond à plusieurs règles d’analyse **Sécurité Microsoft**, elle crée un seul incident Microsoft Sentinel.

## <a name="enable-incident-generation-automatically-during-connection"></a>Activer la génération automatique d’incidents pendant la connexion

Quand vous connectez une solution de sécurité Microsoft, vous pouvez choisir si vous voulez que les alertes de la solution de sécurité génèrent automatiquement des incidents dans Microsoft Sentinel.

1. Connectez une source de données de solution de sécurité Microsoft. 

   ![Générer des incidents de sécurité](media/incidents-from-alerts/generate-security-incidents.png)

1. Sous **Créer des incidents**, sélectionnez **Activer** pour activer la règle analytique par défaut qui crée automatiquement des incidents à partir des alertes générées dans le service de sécurité connecté. Vous pouvez ensuite modifier cette règle sous **Analytique**, puis **Règles actives**.

## <a name="next-steps"></a>Étapes suivantes

- Pour utiliser Microsoft Sentinel, vous devez disposer d’un abonnement à Microsoft Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/free/).
- Découvrez comment [intégrer vos données à Microsoft Sentinel](quickstart-onboard.md) et [obtenir une visibilité de vos données et des menaces](get-visibility.md).
