---
title: Connecter des alertes de sécurité à Microsoft Sentinel
description: Découvrez comment connecter des alertes de sécurité de Microsoft Defender pour le Cloud et les diffuser en continu dans Microsoft Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 33d43aa25a8e51c0330c2eaba213ef8d5644e62f
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522115"
---
# <a name="connect-security-alerts-from-microsoft-defender-for-cloud"></a>Connecter les alertes de sécurité de Microsoft Defender pour le cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="background"></a>Arrière-plan

[Microsoft Defender for Cloud](../security-center/azure-defender.md), la plateforme cloud intégrée de protection de charge de travail (CWPP) de [Defender pour le Cloud](../security-center/security-center-introduction.md), est un outil de gestion de la sécurité qui vous permet de détecter les menaces et d’y répondre rapidement sur les charges de travail cloud hybride.

Ce connecteur vous permet de diffuser vos alertes de sécurité de Defender pour le Cloud dans Microsoft Sentinel, afin que vous puissiez visualiser les alertes de sécurité, les analyser et y répondre, ainsi que les incidents qu’elles génèrent, dans un contexte de menace organisationnel plus large.

Comme Defender pour le Cloud lui-même est activé par abonnement, le connecteur Defender pour le Cloud est également activé ou désactivé séparément pour chaque abonnement.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

### <a name="alert-synchronization"></a>Synchronisation des alertes

- Quand vous connectez Microsoft Defender pour le cloud à Microsoft Sentinel, l’état des alertes de sécurité qui sont ingérées dans Microsoft Sentinel est synchronisé entre les deux services. Ainsi, par exemple, lorsqu’une alerte est fermée dans Defender pour le cloud, cette alerte s’affiche également comme étant fermée dans Microsoft Sentinel.

- Le changement d’état d’une alerte dans Defender pour le cloud n’affecte *pas* l’état des **incidents** Microsoft Sentinel qui contiennent l’alerte Microsoft Sentinel, mais uniquement l’état de l’alerte.

### <a name="bi-directional-alert-synchronization"></a>Synchronisation bidirectionnelle des alertes

- L’activation de la **synchronisation bidirectionnelle** synchronisera automatiquement l’état des alertes de sécurité avec celui des incidents Microsoft Sentinel qui contiennent ces alertes. Ainsi, lorsqu’un incident Microsoft Sentinel contenant des alertes de sécurité est fermé, l’alerte d’origine correspondante est automatiquement fermée dans Microsoft Defender pour le Cloud.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’autorisations en lecture et en écriture dans l’espace de travail Microsoft Sentinel.

- Vous devez avoir le rôle Lecteur Sécurité dans les abonnements des journaux que vous diffusez.

- Vous devez activer au moins un plan au sein de Microsoft Defender pour le Cloud pour chaque abonnement pour lequel vous souhaitez activer le connecteur. Pour activer les plans Microsoft Defender sur un abonnement, vous devez avoir le rôle **Administrateur de la sécurité** pour cet abonnement.

- Pour activer la synchronisation bidirectionnelle, vous devez avoir le rôle **Contributeur** ou **Administrateur de la sécurité** sur l’abonnement concerné.

## <a name="connect-to-microsoft-defender-for-cloud"></a>Se connecter à Microsoft Defender pour le Cloud

1. Dans Microsoft Sentinel, sélectionnez **Connecteurs de données** dans le menu de navigation.

1. Dans la galerie des connecteurs de données, sélectionnez **Microsoft  Defender pour le Cloud**, puis sélectionnez **Ouvrir la page du connecteur** dans le volet d’informations.

1. Sous **Configuration**, vous verrez une liste des abonnements de votre locataire et l’état de leur connexion à Microsoft Defender pour le Cloud. Sélectionnez le bouton bascule **État** à côté de chaque abonnement dont vous souhaitez diffuser les alertes dans Microsoft Sentinel. Si vous souhaitez connecter plusieurs abonnements à la fois, vous pouvez le faire en cochant les cases à côté des abonnements concernés, puis en sélectionnant le bouton **Connecter** dans la barre située au-dessus de la liste.

    > [!NOTE]
    > - Les cases à cocher et les boutons bascule **Connecter** ne sont actifs que sur les abonnements pour lesquels vous disposez des autorisations requises.
    > - Le bouton **Connecter** n’est actif que si la case d’au moins un abonnement a été cochée.

1. Pour activer la synchronisation bidirectionnelle sur un abonnement, localisez l’abonnement dans la liste et choisissez **Activé** dans la liste déroulante de la colonne **Synchronisation bidirectionnelle**. Pour activer la synchronisation bidirectionnelle sur plusieurs abonnements à la fois, cochez les cases correspondantes et sélectionnez le bouton **Activer la synchronisation bidirectionnelle** dans la barre située au-dessus de la liste.

    > [!NOTE]
    > - Les cases à cocher et les listes déroulantes ne sont actives que sur les abonnements pour lesquels vous disposez des [autorisations requises](#prerequisites).
    > - Le bouton **Activer la synchronisation bidirectionnelle** n’est actif que si la case d’au moins un abonnement a été cochée.

1. Dans la colonne **Plans Microsoft Defender** de la liste, vous pouvez voir si les plans Microsoft Defender sont activés sur votre abonnement (condition préalable à l’activation du connecteur). La valeur de cette colonne pour chaque abonnement sera soit vide (ce qui signifie qu’aucun plan Defender n’est activé), soit « Tous activés », soit « Certains activés ». Ceux qui indiquent « Certains activés » auront également un lien **Activer tout** que vous pourrez sélectionner et qui vous mènera au tableau de bord de configuration de Microsoft Defender pour le Cloud pour cet abonnement, où vous pourrez choisir les plans Defender à activer. Le bouton de lien **Activer Microsoft Defender pour tous les abonnements** dans la barre située au-dessus de la liste vous mènera à votre page de démarrage de Microsoft Defender pour le Cloud, où vous pouvez choisir sur quels abonnements activer Microsoft Defender pour le Cloud.

    :::image type="content" source="./media/connect-azure-security-center/azure-defender-config.png" alt-text="Capture d’écran de la configuration du connecteur Microsoft Defender pour Cloud":::

1. Vous pouvez décider que les alertes provenant de Microsoft Defender pour le Cloud génèrent automatiquement des incidents dans Microsoft Sentinel. Sous **Créer des incidents**, sélectionnez **Activé** pour activer la règle d’analyse par défaut qui [crée automatiquement des incidents à partir d’alertes](create-incidents-from-alerts.md). Vous pouvez ensuite modifier cette règle sous **Analytique** sous l’onglet **Règles actives**.

    > [!TIP]
    > Lorsque vous configurez des [règles d’analyse personnalisées](detect-threats-custom.md) pour des alertes provenant de Microsoft Defender pour le Cloud, tenez compte de la gravité de l’alerte pour éviter d’ouvrir des incidents pour des alertes d’information. 
    >
    > Les alertes d’information dans Microsoft Defender pour le Cloud ne représentent pas un risque de sécurité en soi et ne sont pertinentes que dans le contexte d’un incident existant et ouvert. Pour plus d’informations, consultez [Alertes et incidents de sécurité dans Microsoft Defender pour le cloud](../security-center/security-center-alerts-overview.md).
    > 
    

## <a name="find-and-analyze-your-data"></a>Rechercher et analyser vos données

> [!NOTE]
> La synchronisation des alertes *dans les deux sens* peut prendre quelques minutes. Les modifications de l’état des alertes peuvent ne pas être affichées immédiatement.

- Les alertes de sécurité sont stockées dans la table *SecurityAlert* de votre espace de travail Log Analytics.

- Pour interroger les alertes de sécurité dans Log Analytics, copiez le code suivant dans votre fenêtre de requête comme point de départ :

    ```kusto
    SecurityAlert 
    | where ProductName == "Azure Security Center"
    ```

- Consultez l’onglet **Étapes suivantes** dans la page du connecteur pour obtenir d’autres exemples de requêtes utiles, des modèles de règles d’analyse et des recommandations de classeurs.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter Microsoft Defender pour le Cloud à Microsoft Sentinel et à synchroniser les alertes entre eux. Pour en savoir plus sur Microsoft Sentinel, consultez les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Microsoft Sentinel](detect-threats-built-in.md).
- Écrivez vos propres règles pour [détecter les menaces](detect-threats-custom.md).
