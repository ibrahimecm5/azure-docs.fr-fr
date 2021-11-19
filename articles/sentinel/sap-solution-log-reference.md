---
title: Solution SAP Microsoft Sentinel - Informations de référence disponibles sur les journaux | Microsoft Docs
description: Découvrez les journaux SAP disponibles dans la solution SAP Microsoft Sentinel.
author: batamig
ms.author: bagold
ms.service: microsoft-sentinel
ms.topic: reference
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.subservice: microsoft-sentinel
ms.openlocfilehash: 5d02b40794e226238896b7327a47d9cfb2acbe0e
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517992"
---
# <a name="microsoft-sentinel-sap-solution-logs-reference-public-preview"></a>Informations de référence sur les journaux de la solution SAP Microsoft Sentinel (préversion publique)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cet article décrit les journaux SAP disponibles à partir du connecteur de données SAP Microsoft Sentinel, y compris les noms des tables dans Microsoft Sentinel, les objectifs des journaux et les schémas de journalisation détaillés. Les descriptions des champs de schéma sont basées sur les descriptions des champs de la [documentation SAP pertinente](https://help.sap.com/).

Cet article s’adresse aux utilisateurs SAP avancés.

> [!NOTE]
> Lors de l’utilisation de l’interface XBP 3.0, la solution SAP Microsoft Sentinel utilise des services *non libérés*. Ces services n’affectent pas le comportement du système back-end ou du connecteur.
>
> Pour « libérer » ces services, implémentez [Note SAP 2910263 - Fonctions XBP non libérées](https://launchpad.support.sap.com/#/notes/2910263).

> [!IMPORTANT]
> La solution SAP Microsoft Sentinel est actuellement en PRÉVERSION. Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>

## <a name="abap-application-log"></a>Journal des applications ABAP

- **Nom dans Microsoft Sentinel** : `ABAPAppLog_CL`

- **Documentation SAP associée** : [Portail d’aide SAP](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcc9f36611d3a6510000e835363f.html)

- **Objectif du journal** : enregistre la progression de l’exécution d’une application afin que vous puissiez la reconstruire ultérieurement si nécessaire.

    Disponible en utilisant RFC avec un service personnalisé basé sur les services standard de l’interface XBP. Ce journal est généré par client.


### <a name="abapapplog_cl-log-schema"></a>Schéma du journal ABAPAppLog_CL

| Champ                 | Description                    |
| --------------------- | ------------------------------ |
| AppLogDateTime        | Date et heure du journal des applications      |
| CallbackProgram       | Programme de rappel               |
| CallbackRoutine       | Routine de rappel               |
| CallbackType          | Type de rappel                  |
| ClientID              | ID client ABAP (MANDT)         |
| ContextDDIC           | Structure DDIC de contexte         |
| ExternalID            | ID de journal externe                |
| Hôte                  | Hôte                           |
| Instance              | Instance ABAP, avec la syntaxe suivante :   `<HOST>_<SYSID>_<SYSNR>`              |
| InternalMessageSerial | Série de messages du journal des applications |
| LevelofDetail         | Niveau de détail                |
| LogHandle             | Handle du journal des applications         |
| LogNumber             | Numéro de journal                     |
| MessageClass          | Message, classe                  |
| MessageNumber         | Numéro de message                 |
| MessageText           | Texte du message                   |
| MessageType           | type de message                   |
| Object                | Objet journal des applications         |
| OperationMode         | Mode de fonctionnement                 |
| ProblemClass          | Classe de problème                  |
| ProgramName           | Nom du programme                   |
| SortCriterion         | Critère de tri                 |
| StandardText          | Texte standard                  |
| SubObject             | Sous-objet du journal des applications     |
| SystemID              | ID système                      |
| SystemNumber          | Numéro système                  |
| TransactionCode       | Code de transaction               |
| Utilisateur                  | Utilisateur                           |
| UserChange            | Modification de l’utilisateur                    |
| | |



## <a name="abap-change-documents-log"></a>Journal des documents de modification ABAP

- **Nom dans Microsoft Sentinel** : `ABAPChangeDocsLog_CL`

- **Documentation SAP associée** : [Portail d’aide SAP](https://help.sap.com/viewer/6f51f5216c4b10149358d088a0b7029c/7.01.22/en-US/b8686150ed102f1ae10000000a44176f.html)

- **Objectif du journal** : enregistre :

    - Le serveur d’applications (AS) SAP NetWeaver ABAP journalise les modifications apportées aux objets de données métier dans les documents de modification.

    - D’autres entités dans le système SAP, comme les données utilisateur, les rôles, les adresses.

    Disponible en utilisant RFC avec un service personnalisé basé sur les services standard. Ce journal est généré par client.

### <a name="abapchangedocslog_cl-log-schema"></a>Schéma de journal ABAPChangeDocsLog_CL


| Champ                    | Description                 |
| ------------------------ | ---------------------------- |
| ActualChangeNum          | Numéro de modification réel         |
| ChangedTableKey          | Clé de table modifiée            |
| ChangeNumber             | Numéro de modification                |
| ClientID                 | ID client ABAP (MANDT)       |
| CreatedfromPlannedChange | Créé à partir d’une modification planifiée, avec la syntaxe suivante : `(‘X’ , ‘ ‘)`|
| CurrencyKeyNew           | Clé de devise : nouvelle valeur      |
| CurrencyKeyOld           | Clé de devise : ancienne valeur      |
| FieldName                | Nom du champ                   |
| FlagText                 | Texte d’indicateur                    |
| Hôte                     | Hôte                         |
| Instance                 | Instance ABAP, avec la syntaxe suivante : `<HOST>_<SYSID>_<SYSNR>` |
| Langage                 | Langage                     |
| ObjectClass              | Classe d’objet, comme `BELEG`, `BPAR`, `PFCG`, `IDENTITY` |
| ObjectID                 | ID objet  |
| PlannedChangeNum         | Numéro de modification planifiée  |
| SystemID                 | ID système    |
| SystemNumber             | Numéro système     |
| TableName                | Nom de la table        |
| TransactionCode          | Code de transaction   |
| TypeofChange_Header      | Type d’en-tête de modification, y compris : <br>`U` = Changer ; `I` = Insérer ; `E` = Supprimer un seul doc ; `D` = Supprimer ; `J` = Insérer un seul doc |
| TypeofChange_Item        | Type d’élément de modification, y compris : <br>`U` = Changer ; `I` = Insérer ; `E` = Supprimer un seul doc ; `D` = Supprimer ; `J` = Insérer un seul doc |
| UOMNew                   | Unité de mesure : nouvelle valeur  |
| UOMOld                   | Unité de mesure : ancienne valeur |
| Utilisateur                     | Utilisateur  |
| ValueNew                 | Contenu du champ : nouvelle valeur |
| ValueOld                 | Contenu du champ : ancienne valeur |
| Version                  | Version          |
| | |

## <a name="abap-cr-log"></a>Journal ABAP CR

- **Nom dans Microsoft Sentinel** : `ABAPCRLog_CL`

- **Documentation SAP associée** : [Portail d’aide SAP](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcd5f36611d3a6510000e835363f.html)

- **Objectif du journal** : inclut les journaux du système de modification et de transport (CTS), y compris les objets d’annuaire et les personnalisations où des modifications ont été apportées.

    Disponible en utilisant RFC avec un service personnalisé basé sur les tables standard et les services standard. Ce journal est généré avec des données de tous les clients.

> [!NOTE]
> En plus de la journalisation des applications, des documents de modification et de l’enregistrement des tables, toutes les modifications apportées à votre système de production à l’aide du système modification et de transport sont documentées dans les journaux CTS et TMS.
>


### <a name="abapcrlog_cl-log-schema"></a>Schéma du journal ABAPCRLog_CL

| Champ        | Description                       |
| ------------ | --------------------------------- |
| Category     | Catégorie (Workbench, personnalisation) |
| ClientID     | ID client ABAP (MANDT)            |
| Description  | Description                       |
| Host         | Hôte                              |
| Instance     | Instance ABAP, avec la syntaxe suivante : `<HOST>_<SYSID>_<SYSNR>` |
| ObjectName   | Nom d’objet                       |
| ObjectType   | Type d’objet                       |
| Propriétaire        | Propriétaire                             |
| Requête      | Demande de modification                    |
| Statut       | Statut                            |
| SystemID     | ID système                         |
| SystemNumber | Numéro système                     |
| TableKey     | Clé de table                         |
| TableName    | Nom de la table                        |
| NomVue     | Nom de l’affichage                         |
| | |

## <a name="abap-db-table-data-log"></a>Journal des données de table ABAP DB

- **Nom dans Microsoft Sentinel** : `ABAPTableDataLog_CL`

- **Documentation SAP associée** : [Portail d’aide SAP](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcd2f36611d3a6510000e835363f.html)

- **Objectif du journal** : fournit la journalisation pour les tables qui sont critiques ou sensibles aux audits.

    Disponible en utilisant RFC avec un service personnalisé. Ce journal est généré avec des données de tous les clients.

### <a name="abaptabledatalog_cl-log-schema"></a>Schéma du journal ABAPTableDataLog_CL

| Champ            | Description                           |
| ---------------- | ------------------------------------- |
| DBLogID          | ID du journal DB                             |
| Hôte             | Hôte                                  |
| Instance         | Instance ABAP, avec la syntaxe suivante : `<HOST>_<SYSID>_<SYSNR>` |
| Langage         | Langage                              |
| LogKey           | Clé de journal                               |
| NewValue         | Nouvelle valeur du champ                       |
| OldValue         | Ancienne valeur du champ                       |
| OperationTypeSQL | Type d’opération, `Insert`, `Update`, `Delete` |
| Programme          | Nom du programme                          |
| SystemID         | ID système                             |
| SystemNumber     | Numéro système                         |
| TableField       | Champ de table                           |
| TableName        | Nom de la table                            |
| TransactionCode  | Code de transaction                      |
| UserName         | Utilisateur                                  |
| VersionNumber    | Numéro de version                        |
| | |

## <a name="abap-gateway-log"></a>Journal de passerelle ABAP

- **Nom dans Microsoft Sentinel** : `ABAPOS_GW_CL`

- **Documentation SAP associée** : [Portail d’aide SAP](https://help.sap.com/viewer/62b4de4187cb43668d15dac48fc00732/7.5.7/en-US/48b2a710ca1c3079e10000000a42189b.html)

- **Objectif du journal** : supervise les activités de la passerelle. Disponible par le service web de contrôle SAP. Ce journal est généré avec des données de tous les clients.

### <a name="abapos_gw_cl-log-schema"></a>Schéma du journal ABAPOS_GW_CL

| Champ        | Description      |
| ------------ | ---------------- |
| Host         | Hôte             |
| Instance     | Instance ABAP, avec la syntaxe suivante : `<HOST>_<SYSID>_<SYSNR>`   |
| MessageText  | Texte du message     |
| Gravité     | Gravité du message : `Debug`, `Info`, `Warning`, `Error`  |
| SystemID     | ID système        |
| SystemNumber | Numéro système    |
| | |

## <a name="abap-icm-log"></a>Journal ABAP ICM

- **Nom dans Microsoft Sentinel** : `ABAPOS_ICM_CL`

- **Documentation SAP associée** : [Portail d’aide SAP](https://help.sap.com/viewer/683d6a1797a34730a6e005d1e8de6f22/7.52.4/en-US/a10ec40d01e740b58d0a5231736c434e.html)

- **Objectif du journal** : enregistre les demandes entrantes et sortantes et compile les statistiques des requêtes http.

    Disponible par le service web de contrôle SAP. Ce journal est généré avec des données de tous les clients.

### <a name="abapos_icm_cl-log-schema"></a>Schéma du journal ABAPOS_ICM_CL

| Champ        | Description      |
| ------------ | ---------------- |
| Host         | Hôte             |
| Instance     | Instance ABAP, avec la syntaxe suivante : `<HOST>_<SYSID>_<SYSNR>`   |
| MessageText  | Texte du message     |
| Gravité     | Gravité du message, y compris : `Debug`, `Info`, `Warning`, `Error`   |
| SystemID     | ID système        |
| SystemNumber | Numéro système    |
| | |

## <a name="abap-job-log"></a>Journal des tâches ABAP

- **Nom dans Microsoft Sentinel** : `ABAPJobLog_CL`

- **Documentation SAP associée** : [Portail d’aide SAP](https://help.sap.com/viewer/b07e7195f03f438b8e7ed273099d74f3/7.31.19/en-US/4b2bc0974c594ba2e10000000a42189c.html)

- **Objectif du journal** : combine tous les journaux des travaux de traitement en arrière-plan (SM37).

    Disponible en utilisant RFC avec un service personnalisé basé sur les services standard des interfaces XBP. Ce journal est généré avec des données de tous les clients.

### <a name="abapjoblog_cl-log-schema"></a>Schéma du journal ABAPJobLog_CL


| Champ               | Description                      |
| ------------------- | -------------------------------- |
| ABAPProgram         | Programme ABAP                     |
| BgdEventParameters  | Paramètres d’événement en arrière-plan      |
| BgdProcessingEvent  | Événement de traitement en arrière-plan      |
| ClientID            | ID client ABAP (MANDT)           |
| DynproNumber        | Numéro Dynpro                    |
| GUIStatus           | État GUI                       |
| Hôte                | Hôte                             |
| Instance            | Instance ABAP (HOST_SYSID_SYSNR), avec la syntaxe suivante : `<HOST>_<SYSID>_<SYSNR>` |
| JobClassification   | Classification du travail               |
| JobCount            | Nombre de travaux                        |
| JobGroup            | Groupe de travaux                        |
| JobName             | Nom du travail                         |
| JobPriority         | priorité de travail                     |
| MessageClass        | Message, classe                    |
| MessageNumber       | Numéro de message                   |
| MessageText         | Texte du message                     |
| MessageType         | type de message                     |
| ReleaseUser         | Utilisateur de validation du travail                 |
| SchedulingDateTime  | Date et heure de la planification             |
| StartDateTime       | Date et heure de début                  |
| SystemID            | ID système                        |
| SystemNumber        | Numéro système                    |
| TargetServer        | Serveur cible                    |
| Utilisateur                | Utilisateur                             |
| UserReleaseInstance | Instance ABAP - version utilisateur     |
| WorkProcessID       | ID du processus de travail                  |
| WorkProcessNumber   | Numéro du processus de travail              |
| | |

## <a name="abap-security-audit-log"></a>Journal d’audit de sécurité ABAP

- **Nom dans Microsoft Sentinel** : `ABAPAuditLog_CL`

- **Documentation SAP associée** : [Portail d’aide SAP](https://help.sap.com/viewer/280f016edb8049e998237fcbd80558e7/7.5.7/en-US/4d41bec4aa601c86e10000000a42189b.html)

- **Objectif du journal** : enregistre les données suivantes :

    - Modifications relatives à la sécurité dans l’environnement du système SAP, comme les modifications apportées aux enregistrements des utilisateurs principaux
    - Informations qui fournissent un niveau de données plus élevé, comme les tentatives de connexion réussies et infructueuses
    - Informations qui permettent de reconstruire une série d’événements, comme les démarrages de transaction réussis ou ayant échoué

    Disponible à l’aide des interfaces RFC XAL/SAL. SAL est disponible à partir de la version Basis 7.50. Ce journal est généré avec des données de tous les clients.

### <a name="abapauditlog_cl-log-schema"></a>Schéma du journal ABAPAuditLog_CL

| Champ                      | Description                     |
| -------------------------- | ------------------------------- |
| ABAPProgramName            | Nom du programme, SAL uniquement                    |
| AlertSeverity              | Gravité de l’alerte                  |
| AlertSeverityText          | Texte de gravité d’alerte, SAL uniquement             |
| AlertValue                 | Valeur d’alerte                     |
| AuditClassID               | ID de classe d’audit, SAL uniquement                 |
| ClientID                   | ID client ABAP (MANDT)          |
| Computer                   | Ordinateur de l’utilisateur, SAL uniquement                   |
| Courrier                      | E-mail de l’utilisateur                      |
| Hôte                       | Hôte                                                   |
| Instance                   | Instance ABAP, avec la syntaxe suivante : `<HOST>_<SYSID>_<SYSNR>`                  |
| MessageClass               | Message, classe                   |
| MessageContainerID         | ID de conteneur de message, XAL uniquement            |
| MessageID                  | ID de message, tel que `‘AU1’,’AU2’…`                     |
| MessageText                | Texte du message                    |
| MonitoringObjectName       | Nom d’objet MTE Monitor, XAL uniquement        |
| MonitorShortName           | Nom court MTE Monitor, XAL uniquement          |
| SAPProcesType              | Journal système : type de processus SAP, SAL uniquement    |
| B* - Traitement en arrière-plan |                                 |
| D* - Traitement de dialogue     |                                 |
| U* - Mettre à jour les tâches         |                                 |
| SAPWPName                  | Journal système : numéro de processus de travail, SAL uniquement |
| SystemID                   | ID système                       |
| SystemNumber               | Numéro système                   |
| TerminalIPv6               | Adresse IP de l’ordinateur utilisateur, SAL uniquement |
| TransactionCode            | Code de transaction, SAL uniquement |
| Utilisateur                       | Utilisateur                            |
| Variable1                  | Variable de message 1              |
| Variable2                  | Variable de message 2              |
| Variable3                  | Variable de message 3              |
| Variable4                  | Variable de message 4              |
| | |

## <a name="abap-spool-log"></a>Journal ABAP Spool

- **Nom dans Microsoft Sentinel** : `ABAPSpoolLog_CL`

- **Documentation SAP associée** : [Portail d’aide SAP](https://help.sap.com/viewer/290ce8983cbc4848a9d7b6f5e77491b9/7.52.1/en-US/4eae791c40f72045e10000000a421937.html)

- **Objectif du journal** : sert de journal principal pour l’impression SAP avec l’historique des demandes de spool. (SP01).

    Disponible en utilisant RFC avec un service personnalisé basé sur les tables standard. Ce journal est généré avec des données de tous les clients.

### <a name="abapspoollog_cl-log-schema"></a>Schéma du journal ABAPSpoolLog_CL

| Champ                               | Description                                |
| ----------------------------------- | ------------------------------------------ |
| ArchiveStatus                       | État de l’archive                             |
| ArchiveType                         | Type d’archive                               |
| ArchivingDevice                     | Appareil d’archivage                           |
| AutoRereoute                        | Rediriger automatiquement                              |
| ClientID                            | ID client ABAP (MANDT)                     |
| CountryKey                          | Clé pays                                |
| DeleteSpoolRequestAuto              | Supprimer automatiquement la demande de spool                  |
| DelFlag                             | Indicateur de suppression                              |
| department                          | department                                 |
| DocumentType ;                        | Type du document                              |
| ExternalMode                        | Mode externe                              |
| FormatType                          | Type de format                                |
| Hôte                                | Hôte                                       |
| Instance                            | Instance ABAP, avec la syntaxe suivante : `<HOST>_<SYSID>_<SYSNR>` |
| NumofCopies                         | Nombre de copies                           |
| OutputDevice                        | Appareil de sortie                              |
| PrinterLongName                     | Nom long de l’imprimante                          |
| PrintImmediately                    | Imprimer immédiatement                          |
| PrintOSCoverPage                    | Imprimer la page OSCover                         |
| PrintSAPCoverPage                   | Imprimer la page SAPCover                        |
| Priorité                            | Priorité                                   |
| RecipientofSpoolRequest             | Destinataire de la demande de spool                 |
| SpoolErrorStatus                    | État d’erreur du spool                         |
| SpoolRequestCompleted               | Demande de spool terminée                    |
| SpoolRequestisALogForAnotherRequest | La demande spool est un journal pour une autre demande |
| SpoolRequestName                    | Nom de la demande de spool                         |
| SpoolRequestNumber                  | Numéro de demande de spool                       |
| SpoolRequestSuffix1                 | Demande de spool, suffixe1                      |
| SpoolRequestSuffix2                 | Demande de spool, suffixe2                      |
| SpoolRequestTitle                   | Titre de la demande de spool                        |
| SystemID                            | ID système                                  |
| SystemNumber                        | Numéro système                              |
| TelecommunicationsPartner           | Partenaire de télécommunications                 |
| TelecommunicationsPartnerE          | Partenaire de télécommunications E               |
| TemSeGeneralcounter                 | Compteur Temse                              |
| TemseNumAddProtectionRule           | Temse, numéro d’ajout de règle de protection              |
| TemseNumChangeProtectionRule        | Temse, numéro de modification de règle de protection           |
| TemseNumDeleteProtectionRule        | Temse, numéro de suppression de règle de protection           |
| TemSeObjectName                     | Nom d’objet Temse                          |
| TemSeObjectPart                     | Partie de l’objet Temse                          |
| TemseReadProtectionRule             | Temse, lecture de règle de protection                 |
| Utilisateur                                | Utilisateur                                       |
| ValueAuthCheck                      | Vérification de l’authentification de la valeur                           |
| | |

## <a name="apab-spool-output-log"></a>Journal de sortie APAB Spool

- **Nom dans Microsoft Sentinel** : `ABAPSpoolOutputLog_CL`

- **Documentation SAP associée** : [Portail d’aide SAP](https://help.sap.com/viewer/290ce8983cbc4848a9d7b6f5e77491b9/7.52.1/en-US/4eae779e40f72045e10000000a421937.html)

- **Objectif du journal** : sert de journal principal pour l’impression SAP avec l’historique des demandes de sortie de spool. (SP02).

    Disponible en utilisant RFC avec un service personnalisé basé sur les tables standard. Ce journal est généré avec des données de tous les clients.

### <a name="abapspooloutputlog_cl-log-schema"></a>Schéma du journal ABAPSpoolOutputLog_CL

| Champ                              | Description                               |
| ---------------------------------- | ----------------------------------------- |
| AppServer                          | Serveur d’applications                        |
| ClientID                           | ID client ABAP (MANDT)                    |
| Comment                            | Comment                                   |
| CopyCount                          | Nombre de copies                                |
| CopyCounter                        | Compteur de copies                              |
| department                         | department                                |
| ErrorSpoolRequestNumber            | Numéro de demande d’erreur                      |
| FormatType                         | Type de format                               |
| Hôte                               | Hôte                                      |
| HostName                           | Nom de l’hôte                                 |
| HostSpoolerID                      | ID du spouleur de l’hôte                          |
| Instance                           | Instance ABAP                             |
| LastPage                           | Dernière page                                 |
| NumofCopies                        | Nombre de copies                              |
| OutputDevice                       | Appareil de sortie                             |
| OutputRequestNumber                | Numéro de demande de sortie                     |
| OutputRequestStatus                | État de la demande de sortie                     |
| PhysicalFormatType                 | Type de format physique                      |
| PrinterLongName                    | Nom long de l’imprimante                         |
| PrintRequestSize                   | Taille de la demande d’impression                        |
| Priorité                           | Priorité                                  |
| ReasonforOutputRequest             | Motif de la demande de sortie                 |
| RecipientofSpoolRequest            | Destinataire de la demande de spool                 |
| SpoolNumberofOutputReqProcessed    | Nombre de demandes de sortie traitées     |
| SpoolNumberofOutputReqWithErrors   | Nombre de demandes de sortie avec des erreurs   |
| SpoolNumberofOutputReqWithProblems | Nombre de demandes de sortie avec des problèmes |
| SpoolRequestNumber                 | Numéro de demande de spool                      |
| StartPage                          | Page de démarrage                                |
| SystemID                           | ID système                                 |
| SystemNumber                       | Numéro système                             |
| TelecommunicationsPartner          | Partenaire de télécommunications                |
| TemSeGeneralcounter                | Compteur Temse                             |
| Titre                              | Titre                                     |
| Utilisateur                               | Utilisateur                                      |
| | |


## <a name="abap-syslog"></a>ABAP SysLog

- **Nom dans Microsoft Sentinel** : `ABAPOS_Syslog_CL`

- **Documentation SAP associée** : [Portail d’aide SAP](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcbaf36611d3a6510000e835363f.html)

- **Objectif du journal** : enregistre l’ensemble des erreurs système, des avertissements, des verrous utilisateur ABAP SAP NetWeaver AS en raison des échecs de connexion des utilisateurs connus et de traitement des messages.

    Disponible par le service web de contrôle SAP. Ce journal est généré avec des données de tous les clients.

### <a name="abapos_syslog_cl-log-schema"></a>Schéma du journal ABAPOS_Syslog_CL


| Champ            | Description            |
| ---------------- | ---------------------- |
| ClientID         | ID client ABAP (MANDT) |
| Hôte             | Hôte                   |
| Instance         | Instance ABAP, avec la syntaxe suivante : `<HOST>_<SYSID>_<SYSNR> ` |
| MessageNumber    | Numéro de message         |
| MessageText      | Texte du message           |
| Gravité         | Gravité du message, l’une des valeurs suivantes : `Debug`, `Info`, `Warning`, `Error`        |
| SystemID         | ID système              |
| SystemNumber     | Numéro système          |
| TransacationCode | Code de transaction       |
| Type             | Type de processus SAP       |
| Utilisateur             | Utilisateur                   |
| | |


## <a name="abap-workflow-log"></a>Journal ABAP Workflow

- **Nom dans Microsoft Sentinel** : `ABAPWorkflowLog_CL`

- **Documentation SAP associée** : [Portail d’aide SAP](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcccf36611d3a6510000e835363f.html)

- **Objectif du journal** : SAP Business Workflow (WebFlow Engine) vous permet de définir des processus métier qui ne sont pas encore mappés dans le système SAP.

    Par exemple, les processus métier non mappés peuvent être des procédures de publication ou d’approbation simples, ou des processus métier plus complexes, comme la création de documents de base, puis la coordination des services associés.

    Disponible en utilisant RFC avec un service personnalisé basé sur les tables standard et les services standard. Ce journal est généré par client.

### <a name="abapworkflowlog_cl-log-schema"></a>Schéma du journal ABAPWorkflowLog_CL


| Champ               | Description                      |
| ------------------- | -------------------------------- |
| ActualAgent         | Agent réel                     |
| Adresse             | Adresse                          |
| ApplicationArea     | Domaine d'application                 |
| CallbackFunction    | Fonction de rappel                |
| ClientID            | ID client ABAP (MANDT)           |
| CreationDateTime    | Date/heure de création               |
| Creator             | Creator                          |
| CreatorAddress      | Adresse du créateur                  |
| ErrorType           | Type d’erreur                       |
| ExceptionforMethod  | Exception pour la méthode             |
| Hôte                | Hôte                             |
| Instance            | Instance ABAP (HOST_SYSID_SYSNR), avec la syntaxe suivante : `<HOST>_<SYSID>_<SYSNR>` |
| Langage            | Langage                         |
| LogCounter          | Compteur du journal                      |
| MessageNumber       | Numéro de message                   |
| MessageType         | type de message                     |
| MethodUser          | Utilisateur de méthode                      |
| Priorité            | Priorité                         |
| SimpleContainer     | Conteneur simple, compressé sous la forme d’une liste d’entités Clé-Valeur pour l’élément de travail |
| Statut              | Statut                           |
| SuperWI             | Super WI                         |
| SystemID            | ID système                        |
| SystemNumber        | Numéro système                    |
| TaskID              | ID de la tâche                          |
| TasksClassification | Classifications des tâches            |
| TaskText            | Texte de la tâche                        |
| TopTaskID           | ID de la tâche supérieure                      |
| UserCreated         | Créé par l’utilisateur                     |
| WIText              | Texte de l’élément de travail                   |
| WIType              | Type d'élément de travail                   |
| WorkflowAction      | Action workflow                  |
| WorkItemID          | ID d'élément de travail                     |
| | |





## <a name="abap-workprocess-log"></a>Journal ABAP WorkProcess

- **Nom dans Microsoft Sentinel** : `ABAPOS_WP_CL`

- **Documentation SAP associée** : [Portail d’aide SAP](https://help.sap.com/viewer/d0739d980ecf42ae9f3b4c19e21a4b6e/7.3.15/en-US/46fb763b6d4c5515e10000000a1553f6.html)

- **Objectif du journal** : combine tous les journaux des processus de travail. (valeur par défaut : `dev_*`).

    Disponible par le service web de contrôle SAP. Ce journal est généré avec des données de tous les clients.

### <a name="abapos_wp_cl-log-schema"></a>Schéma du journal ABAPOS_WP_CL


| Champ        | Description         |
| ------------ | ------------------- |
| Host         | Hôte                |
| Instance     | Instance ABAP, avec la syntaxe suivante : `<HOST>_<SYSID>_<SYSNR>`   |
| MessageText  | Texte du message     |
| Gravité     | Gravité du message : `Debug`, `Info`, `Warning`, `Error`  |
| SystemID     | ID système           |
| SystemNumber | Numéro système       |
| WPNumber     | Numéro du processus de travail |
| | |


## <a name="hana-db-audit-trail"></a>Piste d’audit HANA DB

- **Nom dans Microsoft Sentinel** : `Syslog`

- **Documentation SAP associée** : [Général](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/48fd6586304c4f859bf92d64d0cd8b08.html) |  [Piste d’audit](https://help.sap.com/viewer/b3ee5778bc2e4a089d3299b82ec762a7/2.0.03/en-US/0a57444d217649bf94a19c0b68b470cc.html)

- **Objectif du journal** : enregistre les actions ou les tentatives d’actions des utilisateurs dans la base de données SAP HANA. Par exemple, vous permet d’enregistrer et de superviser l’accès en lecture aux données sensibles.

    Disponible par l’agent Sentinel Linux pour Syslog. Ce journal est généré avec des données de tous les clients.

### <a name="syslog-log-schema"></a>Schéma du journal Syslog

| Champ         | Description  |
| ------------- | ------------ |
| Computer      | Nom de l’hôte    |
| HostIP        | Adresse IP hôte      |
| HostName      | Nom de l’hôte    |
| ProcessID     | ID du processus   |
| ProcessName   | Nom du processus : `HDB*` |
| SeverityLevel | Alerte        |
| SourceSystem  |   SE du système source, `Linux`           |
| SyslogMessage | Message de piste d’audit non analysé      |
| | |

## <a name="java-files"></a>Fichiers JAVA

- **Nom dans Microsoft Sentinel** : `JavaFilesLogsCL`

- **Documentation SAP associée** : [Général](https://help.sap.com/viewer/2f8b1599655d4544a3d9c6d1a9b6546b/7.5.9/en-US/485059dfe31672d4e10000000a42189c.html) | [Journal d’audit de sécurité Java](https://help.sap.com/viewer/1531c8a1792f45ab95a4c49ba16dc50b/7.5.9/en-US/4b6013583840584ae10000000a42189c.html)

- **Objectif du journal** : combine tous les journaux basés sur des fichiers Java, y compris le journal d’audit de sécurité et les journaux Système (processus de cluster et de serveur), Performances et Passerelle. Inclut également les journaux Traces Développeur et Trace par défaut.

    Disponible par le service web de contrôle SAP. Ce journal est généré avec des données de tous les clients.

### <a name="javafileslogscl-log-schema"></a>Schéma du journal JavaFilesLogsCL


| Champ            | Description          |
| ---------------- | -------------------- |
| Application      | Application Java     |
| ClientID         | ID client           |
| CSNComponent     | Composant CSN, tel que `BC-XI-IBD` |
| DCComponent      | Composant DC, tel que `com.sap.xi.util.misc` |
| DSRCounter       | Compteur DSR          |
| DSRRootContentID | GUID du contexte DSR     |
| DSRTransaction   | GUID de transaction DSR |
| Hôte             | Hôte                 |
| Instance         | Instance Java, avec la syntaxe suivante : `<HOST>_<SYSID>_<SYSNR>` |
| Emplacement         | Classe Java           |
| LogName          | Nom de journal Java, par exemple : `Available`, `defaulttrace`, `dev*`, `security`, etc.
| MessageText      | Texte du message         |
| MNo              | Numéro de message       |
| Pid              | ID du processus           |
| Programme          | Nom du programme         |
| session          | session              |
| Gravité         | Gravité du message, y compris : `Debug`, `Info`, `Warning`, `Error`     |
| Solution         | Solution             |
| SystemID         | ID système            |
| SystemNumber     | Numéro système        |
| ThreadName       | Nom du thread          |
| Thrown           | Exception levée     |
| TimeZone         | Fuseau horaire             |
| Utilisateur             | Utilisateur                 |
| | |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

- [Déployer la solution Microsoft Sentinel pour SAP](sap-deploy-solution.md)
- [Exigences de SAP détaillées pour la solution SAP Microsoft Sentinel](sap-solution-detailed-requirements.md)
- [Déployer le connecteur de données SAP Microsoft Sentinel avec SNC](sap-solution-deploy-snc.md)
- [Options de configuration pour experts, déploiement local et sources de journaux SAPControl](sap-solution-deploy-alternate.md)
- [Solution SAP Microsoft Sentinel : contenu de sécurité intégré](sap-solution-security-content.md)
- [Résolution des problèmes de déploiement de votre solution SAP Microsoft Sentinel](sap-deploy-troubleshoot.md)
