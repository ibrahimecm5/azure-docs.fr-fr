---
title: Connecter des données Microsoft 365 Defender à Microsoft Sentinel | Microsoft Docs
description: Découvrez comment ingérer des données d’incidents, d’alertes et d’événements bruts de Microsoft 365 Defender dans Microsoft Sentinel.
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
ms.openlocfilehash: edef094a6d4db97208124a97732f1ec354f2106c
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522058"
---
# <a name="connect-data-from-microsoft-365-defender-to-microsoft-sentinel"></a>Connecter des données de Microsoft 365 Defender à Microsoft  Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> **Microsoft 365 Defender** était anciennement connu sous le nom de **Protection Microsoft contre les menaces** ou **MTP**.
>
> **Microsoft Defender for Endpoint** était précédemment appelé **Microsoft Defender Advanced Threat Protection**, ou **MDATP**.
>
> **Microsoft Defender pour Office 365** était anciennement connu sous le nom **Office 365 Advanced Threat Protection**.
>
> Vous pouvez voir les anciens noms encore en cours d’utilisation pendant un certain temps.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="background"></a>Arrière-plan

Le connecteur [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) de Microsoft Sentinel avec intégration d’incidents vous permet de diffuser l’ensemble des incidents et alertes M365D dans Microsoft Sentinel, et de garder les incidents synchronisés entre les deux portails. Les incidents M365D incluent l’ensemble de leurs alertes, entités et autres informations pertinentes. Ils sont enrichis par des alertes des services de composants de M365D **Microsoft Defender pour Endpoint**, **Microsoft Defender pour Identity**, **Microsoft defender pour Office 365** et **Microsoft Defender pour les applications Cloud**, et regroupent ces alertes.

Le connecteur vous permet également de diffuser des événements de **repérage avancé** de Microsoft Defender pour point de terminaison et Microsoft Defender pour Office 365 vers Microsoft Sentinel. Vous pouvez ainsi copier les requêtes de repérage avancé des composants Defender dans Microsoft Sentinel, enrichir des alertes Sentinel avec des données d'événement brutes des composants Defender afin de fournir des insights supplémentaires, et stocker les journaux avec une rétention accrue dans Log Analytics.

Pour plus d’informations sur l’intégration d’incidents et la collecte d’événements de repérage avancé, consultez [Intégration de Microsoft 365 Defender avec Microsoft Sentinel](microsoft-365-defender-sentinel-integration.md#advanced-hunting-event-collection).

> [!IMPORTANT]
>
> Le connecteur Microsoft 365 Defender est actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’une licence valide pour Microsoft 365 Defender, comme décrit dans [Prérequis pour Microsoft 365 Defender](/microsoft-365/security/mtp/prerequisites). 

- Vous devez être **administrateur général** ou **administrateur de la sécurité** dans Azure Active Directory.

## <a name="connect-to-microsoft-365-defender"></a>Se connecter à Microsoft 365 Defender

1. Dans Microsoft Sentinel, sélectionnez **Connecteurs de données**, **Microsoft 365 Defender (préversion)** dans la galerie, puis **Ouvrir la page du connecteur**.

1. Sous **Configuration**, dans la section **Connecter les incidents et les alertes**, sélectionnez le bouton **Connecter les incidents et les alertes**.

1. Pour éviter la duplication des incidents, il est recommandé d’activer la case à cocher **Désactiver toutes les règles de création d’incident Microsoft pour ces produits**.

    > [!NOTE]
    > Lorsque vous activez le connecteur Microsoft 365 Defender, tous les connecteurs des composants M365D (mentionnés au début de cet article) sont automatiquement connectés en arrière-plan. Pour déconnecter l’un des connecteurs des composants, vous devez commencer par déconnecter le connecteur Microsoft 365 Defender.

1. Pour interroger les données d'incident de Microsoft 365 Defender, utilisez l'instruction suivante dans la fenêtre de requête :
    ```kusto
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    ```

1. Si vous souhaitez collecter des événements de repérage avancé de Microsoft Defender pour point de terminaison ou Microsoft Defender pour Office 365, vous pouvez collecter les types d’événements suivants à partir de leurs tables de repérage avancé correspondantes.

    1. Activez les cases à cocher des tables contenant les types d’événements que vous souhaitez collecter :

       # <a name="defender-for-endpoint"></a>[Defender pour point de terminaison](#tab/MDE)

       | Nom de la table | Types d’événements |
       |-|-|
       | **[DeviceInfo](/microsoft-365/security/defender/advanced-hunting-deviceinfo-table)** | Informations sur l’ordinateur, notamment les informations sur le système d’exploitation |
       | **[DeviceNetworkInfo](/microsoft-365/security/defender/advanced-hunting-devicenetworkinfo-table)** | Propriétés réseau des appareils, y compris les adaptateurs physiques, les adresses IP et MAC, ainsi que les réseaux et domaines connectés |
       | **[DeviceProcessEvents](/microsoft-365/security/defender/advanced-hunting-deviceprocessevents-table)** | Création de processus et événements associés |
       | **[DeviceNetworkInfo](/microsoft-365/security/defender/advanced-hunting-devicenetworkevents-table)** | Connexion réseau et événements associés |
       | **[DeviceFileEvents](/microsoft-365/security/defender/advanced-hunting-devicefileevents-table)** | Création de fichier, modification et autres événements du système de fichiers |
       | **[DeviceRegistryEvents](/microsoft-365/security/defender/advanced-hunting-deviceregistryevents-table)** | Création et modification des entrées du Registre |
       | **[DeviceLogonEvents](/microsoft-365/security/defender/advanced-hunting-devicelogonevents-table)** | Connexions et autres événements d’authentification sur les appareils |
       | **[DeviceImageLoadEvents](/microsoft-365/security/defender/advanced-hunting-deviceimageloadevents-table)** | Événements de chargement de DLL |
       | **[DeviceEvents](/microsoft-365/security/defender/advanced-hunting-deviceevents-table)** | Plusieurs types d’événements, y compris les événements déclenchés par des contrôles de sécurité, tels que l’Antivirus Microsoft Defender et la protection contre les attaques |
       | **[DeviceFileCertificateInfo](/microsoft-365/security/defender/advanced-hunting-DeviceFileCertificateInfo-table)** | Informations de certificat des fichiers signés obtenus à partir des événements de vérification de certificat sur des points de terminaison |
       |

       # <a name="defender-for-office-365"></a>[Defender pour Office 365](#tab/MDO)

       | Nom de la table | Types d’événements |
       |-|-|
       | **[EmailAttachmentInfo](/microsoft-365/security/defender/advanced-hunting-emailattachmentinfo-table)** | Informations sur les fichiers joints aux e-mails |
       | **[EmailEvents](/microsoft-365/security/defender/advanced-hunting-emailevents-table)** | Événements liés aux e-mails dans Microsoft 365, notamment les événements de remise et de blocage des e-mails |
       | **[EmailPostDeliveryEvents](/microsoft-365/security/defender/advanced-hunting-emailpostdeliveryevents-table)** | Événements de sécurité qui se produisent après la livraison, après que Microsoft 365 a remis les e-mails à la boîte aux lettres du destinataire |
       | **[EmailUrlInfo](/microsoft-365/security/defender/advanced-hunting-emailurlinfo-table)** | Informations sur les URL des e-mails |
       |

       ---

    1. Cliquez sur **Appliquer les modifications**.

    1. Pour interroger les tables de recherche avancée de menaces dans Log Analytics, entrez le nom de la table de la liste ci-dessus dans la fenêtre de requête.

## <a name="verify-data-ingestion"></a>Vérifier l’ingestion de données

Le graphique de données de la page du connecteur indique que vous ingérez des données. Vous remarquerez qu’il affiche trois lignes, respectivement pour les incidents, les alertes et les événements, et que la ligne des événements est une agrégation de volume d’événements de toutes les tables activées. Une fois le connecteur activé, vous pouvez utiliser les requêtes KQL suivantes pour générer des graphiques plus spécifiques.

Utilisez la requête KQL suivante pour obtenir un graphique des incidents Microsoft 365 Defender entrants :

```kusto
let Now = now(); 
(range TimeGenerated from ago(14d) to Now-1d step 1d 
| extend Count = 0 
| union isfuzzy=true ( 
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now) 
) 
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now) 
| sort by TimeGenerated 
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events") 
| render timechart 
```

Utilises la requête KQL suivante pour générer un graphique du volume d’événements pour une seule table (remplacez la table *DeviceEvents* par la table requise de votre choix) :

```kusto
let Now = now();
(range TimeGenerated from ago(14d) to Now-1d step 1d
| extend Count = 0
| union isfuzzy=true (
    DeviceEvents
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now)
)
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now)
| sort by TimeGenerated
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events")
| render timechart
```

Sous l’onglet **Étapes suivantes**, vous trouverez des classeurs, exemples de requêtes et modèles de règle d’analyse utiles qui ont été inclus. Vous pouvez les exécuter sur place ou les modifier et les enregistrer.

## <a name="next-steps"></a>Étapes suivantes

Ce document vous a montré comment intégrer des incidents de Microsoft 365 Defender et des données d’événement de repérage avancé de Microsoft Defender pour point de terminaison et Microsoft Defender pour Office 365 dans Microsoft Sentinel, à l’aide du connecteur Microsoft 365 Defender. Pour en savoir plus sur Microsoft Sentinel, consultez les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Microsoft Sentinel](./detect-threats-built-in.md).
