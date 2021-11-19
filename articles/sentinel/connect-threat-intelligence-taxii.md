---
title: Connecter Microsoft Sentinel aux flux de renseignements sur les menaces STIX/TAXII | Microsoft Docs
description: Découvrez comment connecter Microsoft Sentinel à des flux de renseignements sur les menaces standard pour importer des indicateurs de menace.
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
ms.openlocfilehash: 570d296b7f6f8a2831d4f758be5b85108da5d8a2
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521773"
---
# <a name="connect-microsoft-sentinel-to-stixtaxii-threat-intelligence-feeds"></a>Connecter Microsoft Sentinel aux flux de renseignements sur les menaces STIX/TAXII

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

**Voir aussi** : [Connecter votre plateforme de renseignement sur les menaces (TIP) à Microsoft Sentinel](connect-threat-intelligence-tip.md)

La norme du secteur la plus largement adoptée pour la transmission du renseignement sur les menaces est une [combinaison du format de données STIX et du protocole TAXII](https://oasis-open.github.io/cti-documentation/). Si votre organisation reçoit des indicateurs de menace de solutions prenant en charge la version actuelle de STIX/TAXII (2.0 ou 2.1), vous pouvez utiliser le **connecteur de données Threat Intelligence – TAXII** pour transférer vos indicateurs de menace dans Microsoft Sentinel. Ce connecteur permet à un client TAXII intégré à Microsoft Sentinel d’importer le renseignement sur les menaces à partir de serveurs TAXII 2.x.

:::image type="content" source="media/connect-threat-intelligence-taxii/threat-intel-taxii-import-path.png" alt-text="Chemin d’importation de TAXII":::

En savoir plus sur le [renseignement sur les menaces](understand-threat-intelligence.md) dans Microsoft Sentinel, et plus particulièrement sur les [flux de renseignements sur les menaces TAXII](threat-intelligence-integration.md#taxii-threat-intelligence-feeds) qui peuvent être intégrés à Microsoft Sentinel.

## <a name="prerequisites"></a>Prérequis  

- Vous devez disposer d’autorisations en lecture et en écriture sur l’espace de travail Microsoft Sentinel pour stocker vos indicateurs de menace.
- Vous devez disposer d’un **URI racine d’API** et d’un **ID de collection** pour TAXII 2.0 ou TAXII 2.1.

## <a name="instructions"></a>Instructions

Effectuez les étapes suivantes pour importer des indicateurs de menace au format STIX dans Microsoft Sentinel à partir d’un serveur TAXII :

1. Obtenir la racine d’API et l’ID de collection du serveur TAXII

1. Pour activer le connecteur de données Threat Intelligence – TAXII dans Microsoft Sentinel

### <a name="get-the-taxii-server-api-root-and-collection-id"></a>Obtenir la racine d’API et l’ID de collection du serveur TAXII

Les serveurs TAXII 2.x publient les racines d’API, qui sont des URL hébergeant des collections de renseignement sur les menaces. Vous pouvez généralement trouver la racine d’API et l’ID de collection dans les pages de documentation du fournisseur de renseignements sur les menaces qui héberge le serveur TAXII. 

> [!NOTE]
> Dans certains cas, le fournisseur ne publiera qu’une URL appelée « Point de terminaison de détection ». Vous pouvez utiliser l’utilitaire cURL pour parcourir le point de terminaison de détection et demander la racine d’API, comme [indiqué ci-dessous](#find-the-api-root).

### <a name="enable-the-threat-intelligence---taxii-data-connector-in-microsoft-sentinel"></a>Pour activer le connecteur de données Threat Intelligence – TAXII dans Microsoft Sentinel

Pour importer des indicateurs de menace dans Microsoft Sentinel à partir d’un serveur TAXII, effectuez les étapes suivantes :

1. Depuis le [portail Azure](https://portal.azure.com/), accédez au service **Microsoft Sentinel**.

1. Choisissez l’**espace de travail** dans lequel vous voulez importer des indicateurs de menace à partir du serveur TAXII.

1. Sélectionnez **Connecteurs de données** dans le menu, sélectionnez **Threat Intelligence - TAXII** dans la galerie des connecteurs, sélectionnez le bouton **Ouvrir la page du connecteur**.

1. Entrez un **nom convivial** pour cette collection du serveur TAXII, l’**URL racine d’API**, l’**ID de collection**, un **nom d’utilisateur** (le cas échéant) et un **mot de passe** (le cas échéant), et choisissez le groupe d’indicateurs et la fréquence d’interrogation que vous souhaitez. Sélectionnez le bouton **Ajouter**.

    :::image type="content" source="media/connect-threat-intelligence-taxii/threat-intel-configure-taxii-servers.png" alt-text="Configurer des serveurs TAXII":::
 
Vous devriez recevoir la confirmation qu’une connexion au serveur TAXII a été établie et vous pouvez répéter la dernière étape ci-dessus autant de fois que vous le souhaitez pour vous connecter à plusieurs collections à partir d’un ou plusieurs serveurs TAXII.

Après quelques minutes, les indicateurs de menace doivent commencer à circuler dans cet espace de travail Microsoft Sentinel. Vous pouvez trouver les nouveaux indicateurs dans le panneau **Renseignement sur les menaces**, accessible depuis le menu de navigation de Microsoft Sentinel.

### <a name="find-the-api-root"></a>Rechercher la racine d’API

Voici un exemple d’utilisation de l’utilitaire de ligne de commande [cURL](https://en.wikipedia.org/wiki/CURL), qui est fourni dans Windows et la plupart des distributions Linux, pour découvrir la racine d’API et parcourir les collections d’un serveur TAXII, à partir du seul point de terminaison de détection. En utilisant le point de terminaison de détection du serveur TAXII 2.0 ThreatStream d’[Anomali Limo](https://www.anomali.com/community/limo), vous pouvez demander l’URI de la racine d’API, puis les collections.

1.  Dans un navigateur, accédez au point de terminaison de détection du serveur TAXII 2.0 ThreatStream à l’adresse https://limo.anomali.com/taxii pour récupérer la racine d’API. Authentifiez-vous avec le nom d’utilisateur et le mot de passe `guest`.

    Vous recevrez la réponse suivante :

    ```json
    {
        "api_roots":
        [
            "https://limo.anomali.com/api/v1/taxii2/feeds/",
            "https://limo.anomali.com/api/v1/taxii2/trusted_circles/",
            "https://limo.anomali.com/api/v1/taxii2/search_filters/"
        ],
        "contact": "info@anomali.com",
        "default": "https://limo.anomali.com/api/v1/taxii2/feeds/",
        "description": "TAXII 2.0 Server (guest)",
        "title": "ThreatStream Taxii 2.0 Server"
    }
    ```

2.  Utilisez l’utilitaire cURL et la racine d’API (https://limo.anomali.com/api/v1/taxii2/feeds/) ) de la réponse précédente, en ajoutant « `collections/`  » à la racine d’API pour parcourir la liste des ID de collection hébergés sur la racine d’API :

    ```json
    curl -u guest https://limo.anomali.com/api/v1/taxii2/feeds/collections/
    ```
    Après vous être authentifié à nouveau avec le mot de passe « guest », vous recevrez la réponse suivante :

    ```json
    {
        "collections":
        [
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "107",
                "title": "Phish Tank"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "135",
                "title": "Abuse.ch Ransomware IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "136",
                "title": "Abuse.ch Ransomware Domains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "150",
                "title": "DShield Scanning IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "200",
                "title": "Malware Domain List - Hotlist"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "209",
                "title": "Blutmagie TOR Nodes"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "31",
                "title": "Emerging Threats C&C Server"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "33",
                "title": "Lehigh Malwaredomains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "41",
                "title": "CyberCrime"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "68",
                "title": "Emerging Threats - Compromised"
            }
        ]
    }
    ```

Vous disposez maintenant de toutes les informations dont vous avez besoin pour connecter Microsoft Sentinel à une ou plusieurs collections de serveurs TAXII fournies par Anomali Limo.

| **Racine d’API** (https://limo.anomali.com/api/v1/taxii2/feeds/) | ID de la collection |
| ------------------------------------------------------------ | ------------: |
| **Phish Tank**                                               | 107           |
| **Adresses IP de ransomware Abuse.ch**                                  | 135           |
| **Domaines de ransomware Abuse.ch**                              | 136           |
| **Adresses IP d’analyse DShield**                                     | 150           |
| **Liste de domaines de programmes malveillants - Liste critique**                            | 200           |
| **Nœuds TOR de Blutmagie**                                      | 209           |
| **Menaces émergentes - Serveur de commande et contrôle**                              |  31           |
| **Domaines de programmes malveillants Lehigh**                                    |  33           |
| **Cybercrime**                                               |  41           |
| **Menaces émergentes - Compromis**                           |  68           |
|

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter Microsoft Sentinel à des flux de renseignements sur les menaces à l’aide du protocole TAXII. Pour en savoir plus sur Microsoft Sentinel, consultez les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Microsoft Sentinel](./detect-threats-built-in.md).
