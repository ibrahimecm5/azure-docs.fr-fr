---
title: Exigences de SAP détaillées pour la solution SAP Microsoft Sentinel | Microsoft Docs
description: En savoir plus sur la configuration SAP requise pour la solution SAP Microsoft Sentinel.
author: batamig
ms.author: bagold
ms.service: microsoft-sentinel
ms.topic: reference
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.subservice: microsoft-sentinel
ms.openlocfilehash: f826ac6f3d5ec00ca2cf11948a1a18d0cb95de32
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521108"
---
# <a name="microsoft-sentinel-sap-solution-detailed-sap-requirements-public-preview"></a>Exigences de SAP détaillées pour la solution SAP Microsoft Sentinel (préversion publique)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

La [procédure par défaut pour le déploiement de la solution SAP Microsoft Sentinel](sap-deploy-solution.md) inclut les demandes de modification SAP et les notes SAP nécessaires et fournit un rôle intégré avec toutes les autorisations requises.

Cet article répertorie en détail les demandes de modification, les notes et les autorisations SAP requises.

Utilisez cet article comme référence si vous êtes un administrateur ou si vous [déployez la solution SAP manuellement](sap-solution-deploy-alternate.md). Cet article s’adresse aux utilisateurs SAP avancés.


> [!IMPORTANT]
> La solution SAP Microsoft Sentinel est actuellement en PRÉVERSION. Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>

> [!NOTE]
> Des exigences supplémentaires sont indiquées si vous déployez votre connecteur de données SAP à l'aide d'une connexion SNC sécurisée. Pour plus d’informations, consultez [Déployer le connecteur de données SAP Microsoft Sentinel avec SNC](sap-solution-deploy-snc.md).
>
## <a name="recommended-virtual-machine-sizing"></a>Dimensionnement recommandé des machines virtuelles

Le tableau suivant décrit le dimensionnement recommandé pour votre machine virtuelle, en fonction de votre utilisation prévue :

|Utilisation  |Dimensionnement recommandé  |
|---------|---------|
|**Spécification minimale**, par exemple pour un environnement lab     |   Une machine virtuelle *Standard_B2s*      |
|**Connecteur standard** (par défaut)     |   Une machine virtuelle *DS2_v2*, avec : <br>– 2 cœurs<br>– 8 Go de mémoire      |
|**Plusieurs connecteurs**     |Une machine virtuelle *Standard_B4ms*, avec : <br>– 4 cœurs<br>– 16 Go de mémoire         |
|     |         |

## <a name="required-sap-log-change-requests"></a>Demandes de modification de journaux SAP requises

Les demandes de modification de journaux SAP suivantes sont requises pour la solution SAP, en fonction de votre version de SAP Basis :

- **Pour les versions 7.5 et ultérieures de SAP Basis**, installez NPLK900144
- **Pour les versions inférieures**, installez NPLK900146
- **Pour créer un rôle SAP avec les autorisations requises**, pour toute version de SAP Basis prise en charge, installez NPLK900163. Pour plus d’informations, consultez [Configurer votre système SAP](sap-deploy-solution.md#configure-your-sap-system) et [Autorisations ABAP requises](#required-abap-authorizations).

> [!NOTE]
> Les demandes de modification de journaux SAP requises exposent les modules de fonction RFC personnalisés qui sont nécessaires pour le connecteur et ne modifient aucun objet standard ou personnalisé.
>

## <a name="required-sap-notes"></a>Notes SAP requises

Si vous disposez de la version 7.5 ou d’une version antérieure de SAP Basis, installez les notes SAP suivantes :

|Versions de SAP Basis  |Note requise |
|---------|---------|
|– 750 SP01 à SP12<br>– 751 SP01 à SP06<br>– 752 SP01 à SP03     |  2641084 : accès en lecture standardisé pour les données du journal d’audit de sécurité       |
|– 700 à 702<br>– 710 à 711, 730, 731, 740 et 750     | 2173545 : DM : CHANGEDOCUMENT_READ_ALL        |
|– 700 à 702<br>– 710 à 711, 730, 731 et 740<br>– 750 à 752     | 2502336 : DM (Document de modification) : RSSCD100, lecture uniquement à partir de l’archive, pas de la base de données        |
|     |         |

Accédez aux notes SAP à partir du [site Launchpad de l’assistance SAP](https://support.sap.com/en/index.html).
## <a name="requires-sap-ports-access"></a>Requiert l’accès aux ports SAP :
L’hôte de l’environnement SAP, via les ports TCP suivants : 32xx, 5xx13 et 33xx, où xx est le numéro de l’instance SAP.

## <a name="required-abap-authorizations"></a>Autorisations ABAP requises

Le tableau suivant répertorie les autorisations ABAP requises pour que l’utilisateur SAP principal puisse connecter Microsoft Sentinel aux journaux SAP. Pour plus d’informations, consultez [Configurer votre système SAP](sap-deploy-solution.md#configure-your-sap-system).

Les autorisations requises sont répertoriées par type de journal. Vous n’avez besoin des autorisations indiquées que pour les types de journaux que vous envisagez d’ingérer dans Microsoft Sentinel.

> [!TIP]
> Pour créer le rôle avec toutes les autorisations requises, déployez la demande de modification SAP NPLK900163 sur votre système SAP. Cette demande de modification crée le rôle **/MSFTSEN/SENTINEL_CONNECTOR** et vous, généralement le propriétaire de SAP Basis ou du rôle, devez attribuer ce rôle à l’utilisateur ABAP qui se connecte à Microsoft Sentinel.
>

| Objet d’autorisation | Champ | Valeur |
| -------------------- | ----- | ----- |
| **Tous les journaux RFC** | | |
| S_RFC | FUGR | /OSP/SYSTEM_TIMEZONE |
| S_RFC | FUGR | ARFC |
| S_RFC | FUGR | STFC |
| S_RFC | FUGR | RFC1 |
| S_RFC | FUGR | SDIFRUNTIME  |
| S_RFC | FUGR | SMOI |
| S_RFC | FUGR | SYST |
| S_RFC | FUGR/FUNC | SRFC/RFC_SYSTEM_INFO |
| S_RFC | FUGR/FUNC | THFB/TH_SERVER_LIST |
| S_TCODE | TCD | SM51 |
| **Journal des applications ABAP**  | | |
| S_APPL_LOG | ACTVT | Affichage |
| S_APPL_LOG | ALG_OBJECT | * |
| S_APPL_LOG | ALG_SUBOBJ | * |
| S_RFC | FUGR | SXBP_EXT |
| S_RFC | FUGR | /MSFTSEN/_APPLOG |
| **Journal des documents de modification ABAP** | | |
| S_RFC | FUGR | /MSFTSEN/_CHANGE_DOCS |
| **Journal ABAP CR** | | |
| S_RFC | FUGR | CTS_API |
| S_RFC | FUGR | /MSFTSEN/_CR |
| S_TRANSPRT | ACTVT | Affichage |
| S_TRANSPRT | TTYPE | * |
| **Journal des données de table ABAP DB** | | |
| S_RFC | FUGR | /MSFTSEN/_TD |
| S_TABU_DIS | ACTVT | Affichage |
| S_TABU_DIS | DICBERCLS | &NC& |
| S_TABU_DIS | DICBERCLS | + Tout objet requis pour la journalisation |
| S_TABU_NAM | ACTVT | Affichage |
| S_TABU_NAM | TABLE | + Tout objet requis pour la journalisation |
| S_TABU_NAM | TABLE | DBTABLOG |
| **Journal des tâches ABAP** | | |
| S_RFC | FUGR | SXBP |
| S_RFC | FUGR | /MSFTSEN/_JOBLOG |
| **Journal des tâches ABAP, journal des applications ABAP** | | |
| S_XMI_PRD | INTERFACE | XBP |
| **Journal d’audit de sécurité ABAP – XAL** | | |
| Tous les RFC | S_RFC | FUGR |
| S_ADMI_FCD | S_ADMI_FCD | AUDD |
| S_RFC | FUGR | SALX |
| S_USER_GRP | ACTVT | Affichage |
| S_USER_GRP | CLASS | * |
| S_XMI_PRD | INTERFACE | XAL |
| **Journal d’audit de sécurité ABAP – XAL, journal des tâches ABAP, journal des applications ABAP** | | |
| S_RFC | FUGR | SXMI |
| S_XMI_PRD | EXTCOMPANY | Microsoft |
| S_XMI_PRD | EXTPRODUCT | Microsoft Sentinel |
| **Journal d’audit de sécurité ABAP – SAL** | | |
| S_RFC | FUGR | RSAU_LOG |
| S_RFC | FUGR | /MSFTSEN/_AUDITLOG |
| **Journal de spools ABAP, journal de sortie de spool ABAP** | | |
| S_RFC | FUGR | /MSFTSEN/_SPOOL |
| **Journal de flux de travail ABAP** | | |
| S_RFC | FUGR | SWRR |
| S_RFC | FUGR | /MSFTSEN/_WF |
| **Données utilisateur** | | |
| S_RFC | FUNC | RFC_READ_TABLE |
| | |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

- [Déployer la solution Microsoft Sentinel pour SAP](sap-deploy-solution.md)
- [Déployer le connecteur de données SAP Microsoft Sentinel avec SNC](sap-solution-deploy-snc.md)
- [Options de configuration pour experts, déploiement local et sources de journaux SAPControl](sap-solution-deploy-alternate.md)
- [Informations de référence sur les journaux de la solution SAP Microsoft Sentinel](sap-solution-log-reference.md)
- [Solution SAP Microsoft Sentinel : contenu de sécurité disponible](sap-solution-security-content.md)
- [Résolution des problèmes de déploiement de votre solution SAP Microsoft Sentinel](sap-deploy-troubleshoot.md)
