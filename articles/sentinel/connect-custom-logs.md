---
title: Collecter des données dans des formats de journaux personnalisés vers Microsoft Sentinel | Microsoft Docs
description: Collectez des données à partir de sources de données personnalisées et ingérez-les dans Microsoft Sentinel à l’aide de l’agent Log Analytics.
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
ms.openlocfilehash: 1a64f6e11abf8531c90968c2f24824f700ac73d9
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524585"
---
# <a name="collect-data-in-custom-log-formats-to-microsoft-sentinel-with-the-log-analytics-agent"></a>Collecter des données dans des formats de journaux personnalisés vers Microsoft Sentinel avec l’agent Log Analytics

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

De nombreuses applications consignent des données dans des fichiers texte au lieu des services de journalisation standard comme le Journal des événements Windows ou Syslog. Vous pouvez utiliser l’agent Log Analytics pour collecter des données dans des fichiers texte de formats non standard à partir d’ordinateurs Windows et Linux. Une fois la collecte terminée, vous pouvez analyser les données dans des champs individuels au sein de vos requêtes ou extraire les données lors de la collecte vers des champs individuels.

Cet article explique comment connecter vos sources de données à Microsoft Sentinel à l’aide de formats de journaux personnalisés. Pour plus d’informations sur les connecteurs de données pris en charge qui utilisent cette méthode, consultez [Informations de référence sur les connecteurs de données](data-connectors-reference.md).

Apprenez-en davantage sur les [journaux personnalisés dans la documentation d’Azure Monitor](../azure-monitor/agents/data-sources-custom-logs.md).

Comme dans Syslog, il existe deux étapes pour la configuration de la collecte de journaux personnalisés :

- Installez l’agent Log Analytics sur la machine Linux ou Windows qui générera les journaux.

- Configurez les paramètres de journalisation de votre application.

- Configurez l’agent Log Analytics à partir de Microsoft Sentinel.

## <a name="install-the-log-analytics-agent"></a>Installer l’agent Log Analytics

Installez l’agent Log Analytics sur la machine Linux ou Windows qui générera les journaux.

> [!NOTE]
> Certains fournisseurs recommandent d’installer l’agent Log Analytics sur un serveur de journaux distinct plutôt que directement sur l’appareil. Consultez la section de votre produit dans la page des [informations de référence sur les connecteurs de données](data-connectors-reference.md) ou dans la documentation de votre produit.

Sélectionnez l’onglet approprié ci-dessous, selon que votre connecteur a une page connecteur de données dans Microsoft Sentinel.

# <a name="from-a-specific-data-connector-page"></a>[À partir d’une page de connecteur de données spécifique](#tab/DCG)

1. Dans le menu de navigation de Microsoft Sentinel, sélectionnez **Connecteurs de données**.

1. Sélectionnez votre type d’appareil, puis sélectionnez **Ouvrir la page du connecteur**.

1. Installez et intégrez l’agent sur l’appareil qui génère les journaux. Choisissez Linux ou Windows, selon le cas.

    | Type de machine | Instructions |
    | --------- | --------- |
    | **Pour une machine virtuelle Linux Azure** | <ol><li>Sous **Choisissez l’emplacement d’installation de l’agent Linux**, développez **Installer l’agent sur une machine virtuelle Linux Azure**.<br><br><li>Sélectionnez le lien **Télécharger et installer l’agent pour les machines virtuelles Linux Azure >** .<br><br><li>Dans le panneau **Machines virtuelles**, sélectionnez une machine virtuelle sur laquelle installer l’agent, puis choisissez **Connecter**. Répétez cette étape pour chaque machine virtuelle à laquelle vous souhaitez vous connecter. |
    | **Pour toute autre machine Linux** | <ol><li>Sous **Choisissez l’emplacement d’installation de l’agent Linux**, développez **Installer l’agent sur une machine Linux non Azure**.<br><br><li>Sélectionnez le lien **Télécharger et installer l’agent pour les machines Linux non Azure >** .<br><br><li>Dans le panneau **Gestion des agents**, sélectionnez l’onglet **Serveurs Linux**, puis copiez la commande pour **Télécharger et intégrer l’agent pour Linux** et exécutez-la sur votre machine Linux.<br><br> Si vous souhaitez conserver une copie locale du fichier d’installation de l’agent Linux, sélectionnez le lien **Télécharger l’agent Linux** au-dessus de la commande « Télécharger et intégrer l’agent ».|
    | **Pour une machine virtuelle Windows Azure** | <ol><li>Sous **Choisissez l’emplacement d’installation de l’agent Windows**, développez **Installer l’agent sur une machine virtuelle Windows Azure**.<br><br><li>Sélectionnez le lien **Télécharger et installer l’agent pour les machines virtuelles Windows Azure >** .<br><br><li>Dans le panneau **Machines virtuelles**, sélectionnez une machine virtuelle sur laquelle installer l’agent, puis choisissez **Connecter**. Répétez cette étape pour chaque machine virtuelle à laquelle vous souhaitez vous connecter. |
    | **Pour toute autre machine Windows** | <ol><li>Sous **Choisissez l’emplacement d’installation de l’agent Windows**, développez **Installer l’agent sur une machine virtuelle Windows non Azure**.<br><br><li>Sélectionnez le lien **Télécharger et installer l’agent pour les machines Windows non Azure >** .<br><br><li>Dans le panneau **Gestion des agents**, sous l’onglet **Serveurs Windows**, sélectionnez le lien **Télécharger l’agent Windows** pour les systèmes 32 bits ou 64 bits, selon le cas. |

# <a name="other-data-sources"></a>[Autres sources de données](#tab/CUS)

1. Dans le menu de navigation de Microsoft Sentinel, sélectionnez **Paramètres**, puis l’onglet **Paramètres de l’espace de travail**.

1. Installez et intégrez l’agent sur l’appareil qui génère les journaux. Choisissez Linux ou Windows, selon le cas.

    | Type de machine | Instructions |
    | --------- | --------- |
    | **Machine virtuelle Azure (Windows ou Linux)** | <ol><li>Dans le menu de navigation de l’espace de travail Log Analytics, sélectionnez **Machines virtuelles**.<br><br><li>Dans le panneau **Machines virtuelles**, sélectionnez une machine virtuelle sur laquelle installer l’agent, puis choisissez **Connecter**.<br>Répétez cette étape pour chaque machine virtuelle à laquelle vous souhaitez vous connecter. |
    | **Toute autre machine Windows ou Linux** | <ol><li>Dans le menu de navigation de l’espace de travail Log Analytics, sélectionnez **Gestion des agents**.<br><br><li>Sélectionnez l’onglet **Serveurs Windows** ou **Serveurs Linux**, selon le cas.<br><br><li>Pour Windows, sélectionnez le lien **Télécharger l’agent Windows** pour les systèmes 32 bits ou 64 bits, selon le cas. Pour Linux, copiez la commande pour **Télécharger et intégrer l’agent pour Linux** et exécutez-la à partir de votre ligne de commande, ou sélectionnez le lien **Télécharger l’agent Linux** pour télécharger une copie locale du fichier d’installation. |

---

## <a name="configure-the-logs-to-be-collected"></a>Configurer les journaux à collecter

De nombreux types d’appareils ont des connecteurs de données propres qui s’affichent dans la page **Connecteurs de données** de Microsoft Sentinel. Certains de ces connecteurs impliquent des instructions supplémentaires spéciales pour configurer correctement la collecte des journaux dans Microsoft Sentinel. Ces instructions peuvent inclure l’implémentation d’un analyseur basé sur une fonction Kusto. 

Tous les connecteurs répertoriés dans Microsoft Sentinel affichent des instructions spécifiques sur leurs pages de connecteur respectives dans le portail, ainsi que dans leurs sections de la page [Informations de référence sur les connecteurs de données Microsoft Sentinel](data-connectors-reference.md).

Si votre produit n’est pas répertorié dans la page **Connecteurs de données**, consultez la documentation de votre fournisseur pour obtenir des instructions sur la configuration de la journalisation de votre appareil.

## <a name="configure-the-log-analytics-agent"></a>Configurer l’agent Log Analytics

1. Dans la page du connecteur, sélectionnez le lien **Ouvrir la configuration des journaux personnalisés de votre espace de travail**.

    Ou bien, dans le menu de navigation de l’espace de travail Log Analytics, sélectionnez **Journaux personnalisés**.

1. Dans l’onglet **Tables personnalisées**, sélectionnez **Ajouter un journal personnalisé**.

1. Dans l’onglet **Exemple**, téléchargez un exemple de fichier journal à partir de votre appareil (par exemple, access.log ou error.log). Ensuite, sélectionnez **Suivant**.

1. Dans l’onglet **Délimiteur d’enregistrement**, sélectionnez un délimiteur d’enregistrement, **Nouvelle ligne** ou **Timestamp** (consultez les instructions de cet onglet), puis sélectionnez **Suivant**.

1. Dans l’onglet **Chemins d’accès à la collection**, sélectionnez un type de chemin d’accès de Windows ou Linux, puis entrez le chemin d’accès aux journaux de votre appareil en fonction de votre configuration. Ensuite, sélectionnez **Suivant**.

1. Donnez un nom à votre journal personnalisé et éventuellement une description, puis sélectionnez **Suivant**.  
    Ne terminez pas votre nom par « _CL », car il sera ajouté automatiquement.


## <a name="find-your-data"></a>Recherche de données

Pour interroger les données de journaux personnalisés dans **Journaux**, tapez le nom que vous avez donné à votre journal personnalisé (se terminant par « _CL ») dans la fenêtre de la requête.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris comment collecter des données à partir de types de journaux personnalisés pour les ingérer dans Microsoft Sentinel. Pour en savoir plus sur Microsoft Sentinel, consultez les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Microsoft Sentinel](detect-threats-built-in.md).
- [Utilisez des classeurs](monitor-your-data.md) pour superviser vos données.
