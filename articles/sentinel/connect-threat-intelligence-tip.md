---
title: Connecter votre plateforme de renseignement sur les menaces à Microsoft Sentinel | Microsoft Docs
description: Apprenez à connecter votre plateforme de renseignement sur les menaces (TIP) ou votre flux personnalisé à Microsoft Sentinel et à envoyer des indicateurs de menace.
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
ms.openlocfilehash: 1490e7b1b0364918ec63bf39ad5e1c70120549ec
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523065"
---
# <a name="connect-your-threat-intelligence-platform-to-microsoft-sentinel"></a>Connecter votre plateforme de renseignement sur les menaces à Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

**Voir aussi** : [Connecter Microsoft Sentinel aux flux de renseignement sur les menaces STIX/TAXII](connect-threat-intelligence-taxii.md)

De nombreuses organisations utilisent des solutions de plateforme de renseignement sur les menaces (TIP) pour agréger les flux d’indicateurs de menace provenant de diverses sources, organiser les données au sein de la plateforme, puis choisir les indicateurs de menace à appliquer à différentes solutions de sécurité, comme les appareils réseau, les solutions EDR/XDR ou les solutions SIEM comme Microsoft Sentinel. Le **connecteur de données Plateformes de renseignement sur les menaces** vous permet d’utiliser ces solutions pour importer des indicateurs de menace dans Microsoft Sentinel. 

Étant donné que le connecteur de données TIP fonctionne avec l’[API Microsoft Graph Security tiIndicators](/graph/api/resources/tiindicator) pour effectuer cette opération, vous pouvez utiliser le connecteur pour envoyer des indicateurs à Microsoft Sentinel (et à d’autres solutions de sécurité Microsoft comme Microsoft 365 Defender) à partir de toute autre plateforme personnalisée de renseignement sur les menaces qui peut communiquer avec cette API.

:::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-import-path.png" alt-text="Chemin d’importation du renseignement sur les menaces":::

En savoir plus sur le [renseignement sur les menaces](understand-threat-intelligence.md) dans Microsoft Sentinel, et plus particulièrement sur les [plateformes de renseignement sur les menaces](threat-intelligence-integration.md#integrated-threat-intelligence-platform-products) qui peuvent être intégrées à Microsoft Sentinel.

## <a name="prerequisites"></a>Configuration requise  

- Vous devez disposer du rôle **Administrateur général** ou du rôle **Administrateur de la sécurité** Azure AD pour accorder des autorisations à votre produit TIP ou à toute autre application personnalisée qui utilise une intégration directe avec l’API Microsoft Graph Security tiIndicators.

- Vous devez disposer d’autorisations en lecture et en écriture sur l’espace de travail Microsoft Sentinel pour stocker vos indicateurs de menace.

## <a name="instructions"></a>Instructions

Effectuez les étapes suivantes pour importer des indicateurs de menace dans Microsoft Sentinel à partir de votre TIP intégrée ou de votre solution personnalisée de renseignement sur les menaces :
1.  Obtenir un ID d’application et un secret client à partir de votre instance Azure Active Directory
2.  Entrer ces informations dans votre solution de plateforme Threat Intelligence (TIP) ou votre application personnalisée
3.  Activer le connecteur de données Plateformes de renseignement sur les menaces dans Microsoft Sentinel

### <a name="sign-up-for-an-application-id-and-client-secret-from-your-azure-active-directory"></a>S’incrire pour obtenir un ID d’application et un secret client à partir de votre instance Azure Active Directory

Que vous travailliez avec une solution de plateforme Threat Intelligence (TIP) ou avec une solution personnalisée, l’API tiIndicators nécessite certaines informations de base pour vous permettre d’y connecter votre flux et de lui envoyer des indicateurs de menace. Les trois informations dont vous avez besoin sont les suivantes :

- ID d’application (client)
- ID de l’annuaire (locataire)
- Clé secrète client

Vous pouvez extraire ces informations de Azure Active Directory par le biais d’un processus appelé **Inscription d’application**, qui inclut les trois étapes suivantes :

- Inscrire une application dans Azure Active Directory
- Spécifier les autorisations exigées par l’application pour se connecter à l’API tiIndicators Microsoft Graph et envoyer des indicateurs de menace
- Obtenez le consentement de votre organisation pour accorder ces autorisations à cette application.

#### <a name="register-an-application-with-azure-active-directory"></a>Inscription d’une application auprès d’Azure Active Directory

1. Ouvrez le Portail Azure, puis accédez au service **Azure Active Directory**.
1. Sélectionnez **Inscriptions d’applications** dans le menu, puis sélectionnez **Nouvelle inscription**.
1. Choisissez un nom pour l’inscription de votre application, sélectionnez la case d’option **Locataire unique**, puis sélectionnez **Inscrire**. 

    :::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-register-application.png" alt-text="Inscrire une application":::

1. Dans l’écran qui s’affiche, copiez les valeurs **ID d’application (client)** et **ID de l’annuaire (locataire)** . Ce sont les deux premières informations dont vous aurez besoin ultérieurement pour configurer votre TIP ou votre solution personnalisée afin d’envoyer des indicateurs de menace à Microsoft Sentinel. La troisième, **Clé secrète client**, vient ensuite.

#### <a name="specify-the-permissions-required-by-the-application"></a>Spécifier les autorisations exigées par l’application

1. Revenez à la page principale du service **Azure Active Directory**.

1. Sélectionnez **Inscriptions d’applications** dans le menu, puis sélectionnez votre application nouvellement inscrite.

1. Sélectionnez **Autorisations de l’API** dans le menu, puis sélectionnez le bouton **Ajouter une autorisation**.

1. Dans la page **Sélectionner une API**, sélectionnez l’API **Microsoft Graph** et faites votre choix dans la liste d’autorisations Microsoft Graph.

1. À l’invite « Quel type d’autorisation votre application nécessite-t-elle ? » sélectionnez **Autorisations de l’application**. Il s’agit du type d’autorisation utilisé par les applications qui s’authentifient avec un ID d’application et des secrets d’application (clés API).

1. Sélectionnez **ThreatIndicators.ReadWrite.OwnedBy**, puis **Ajouter des autorisations** pour ajouter cette autorisation à la liste d’autorisations de votre application.

    :::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-api-permissions-1.png" alt-text="Spécifier des autorisations":::

#### <a name="get-consent-from-your-organization-to-grant-these-permissions"></a>Obtenir le consentement de votre organisation pour accorder ces autorisations

1. Pour obtenir le consentement, vous devez disposer d’un administrateur général Azure Active Directory qui sélectionne le bouton **Accorder un consentement d’administrateur pour votre locataire** dans la page des **autorisations d’API** de votre application. Si vous n’avez pas le rôle Administrateur général sur votre compte, ce bouton n’est pas disponible et vous devez demander à un Administrateur général de votre organisation d’effectuer cette étape. 

    :::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-api-permissions-2.png" alt-text="Accorder le consentement":::

1. Une fois le consentement accordé à votre application, vous devez voir une coche verte sous **Statut**.

Maintenant que votre application a été inscrite et que des autorisations ont été accordées, vous pouvez obtenir le dernier élément figurant sur votre liste : un secret client pour votre application.

1. Revenez à la page principale du service **Azure Active Directory**.

1. Sélectionnez **Inscriptions d’applications** dans le menu, puis sélectionnez votre application nouvellement inscrite.

1. Sélectionnez **Certificats et secrets** dans le menu, puis cliquez sur le bouton **Nouveau secret client** pour recevoir un secret (clé API) pour votre application.

    :::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-client-secret.png" alt-text="Obtenir un secret client":::

1. Cliquez sur le bouton **Ajouter** et **copiez la clé secrète client**.

    > [!IMPORTANT]
    > Vous devez copiez la **clé secrète client** avant de quitter cet écran. Vous ne pouvez pas récupérer ce secret si vous quittez cette page. Vous aurez besoin de cette valeur quand vous configurerez votre solution de plateforme Threat Intelligence (TIP) ou votre solution personnalisée.

### <a name="input-this-information-into-your-tip-solution-or-custom-application"></a>Entrer ces informations dans votre solution de plateforme Threat Intelligence (TIP) ou votre application personnalisée

Vous avez maintenant les trois informations dont vous avez besoin pour configurer votre TIP ou votre solution personnalisée afin d’envoyer des indicateurs de menace à Microsoft Sentinel.

- ID d’application (client)
- ID de l’annuaire (locataire)
- Clé secrète client

1. Entrez ces valeurs dans la configuration de votre solution de plateforme Threat Intelligence ou votre solution personnalisée, si nécessaire.

1. Pour le produit cible, spécifiez **Microsoft Sentinel**.

1. Pour l’action, spécifiez **alerte**.

Une fois cette configuration terminée, les indicateurs de menace sont envoyés à partir de votre TIP ou de votre solution personnalisée, par le biais de l’**API Microsoft Graph tiIndicators**, à destination de Microsoft Sentinel.

### <a name="enable-the-threat-intelligence-platforms-data-connector-in-microsoft-sentinel"></a>Activer le connecteur de données Plateformes de renseignement sur les menaces dans Microsoft Sentinel

La dernière étape du processus d’intégration consiste à activer le **connecteur de données Plateformes de renseignement sur les menaces** dans Microsoft Sentinel. L’activation du connecteur est ce qui permet à Microsoft Sentinel de recevoir les indicateurs de menace envoyés par votre TIP ou votre solution personnalisée. Ces indicateurs sont disponibles pour tous les espaces de travail Microsoft Sentinel de votre organisation. Effectuez les étapes suivantes pour activer le connecteur de données Plateformes Threat Intelligence pour chaque espace de travail :

1. Depuis le portail Azure, accédez au service **Microsoft Sentinel**.

1. Choisissez l’**espace de travail** dans lequel vous voulez importer les indicateurs de menace envoyés à partir de votre solution de plateforme Threat Intelligence (TIP) ou votre solution personnalisée.

1. Sélectionnez **Connecteurs de données** dans le menu, sélectionnez **Plateformes Threat Intelligence** dans la galerie des connecteurs, puis cliquez sur le bouton **Ouvrir la page du connecteur**.

1. Comme vous avez déjà effectué l’inscription de l’application et configuré votre solution de plateforme Threat Intelligence (TIP) ou votre solution personnalisée pour envoyer des indicateurs de menace, il ne vous reste plus qu’à cliquer sur le bouton **Se connecter**.

Après quelques minutes, les indicateurs de menace doivent commencer à circuler dans cet espace de travail Microsoft Sentinel. Vous pouvez trouver les nouveaux indicateurs dans le panneau **Renseignement sur les menaces**, accessible depuis le menu de navigation de Microsoft Sentinel.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter votre plateforme de renseignement sur les menaces à Microsoft Sentinel. Pour en savoir plus sur Microsoft Sentinel, consultez les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Microsoft Sentinel](./detect-threats-built-in.md).
