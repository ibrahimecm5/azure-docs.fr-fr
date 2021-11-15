---
title: Journaux des connexions dans Azure Active Directory | Microsoft Docs
description: Présentation des journaux des connexions dans Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/25/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d783fcd44fac0c22b83023e1e50b24c209fed8c
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577179"
---
# <a name="sign-in-logs-in-azure-active-directory"></a>Journaux des connexions dans Azure Active Directory

En tant qu’administrateur informatique, vous souhaitez savoir comment fonctionne votre environnement informatique. Les informations sur l’intégrité de votre système vous permettent d’évaluer si vous devez répondre aux problèmes potentiels et, le cas échéant, de quelle manière. 

Pour vous aider à atteindre cet objectif, le portail Azure Active Directory vous donne accès à trois journaux d’activité :

- **[Connexions](concept-sign-ins.md)**  : Informations sur les connexions et la manière dont vos ressources sont utilisées par vos utilisateurs.
- **[Audit](concept-audit-logs.md)**  : Informations sur les modifications appliquées à votre locataire, telles que la gestion des utilisateurs et des groupes ou les mises à jour appliquées aux ressources de votre locataire.
- **[Approvisionnement](concept-provisioning-logs.md)**  : Activités réalisées par le service d’approvisionnement, telles que la création d’un groupe dans ServiceNow ou l’importation d’un utilisateur à partir de Workday.

Cet article présente une vue d’ensemble du rapport de connexions.


## <a name="what-can-you-do-with-it"></a>Que pouvez-vous faire avec ?

Vous pouvez utiliser les journaux des connexions pour trouver des réponses à des questions telles que :

- Quel est le modèle de connexion d’un utilisateur ?

- Combien d’utilisateurs se sont connectés au cours d’une semaine ?

- Quel est l’état de ces connexions ?


## <a name="who-can-access-it"></a>Qui peut y accéder ?

Vous pouvez toujours accéder à votre propre historique des connexions à l’aide de ce lien : [https://mysignins.microsoft.com](https://mysignins.microsoft.com).

Pour accéder au journal des connexions, vous devez être :

- Un administrateur général

- Un utilisateur dans l’un des rôles suivants :
    - Administrateur de sécurité

    - Lecteur de sécurité

    - Lecteur général

    - Lecteur de rapports



## <a name="what-azure-ad-license-do-you-need"></a>De quelle licence Azure AD avez-vous besoin ?

Le rapport d’activité de connexion est disponible avec [toutes les éditions d’Azure AD](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data). Si vous disposez d’une licence de Répertoire actif Azure P1 ou P2, vous pouvez également accéder au rapport d’activité de connexion via l’API Microsoft Graph.


## <a name="where-can-you-find-it-in-the-azure-portal"></a>Où le trouver dans le portail Azure ?

Le portail Azure vous offre plusieurs options pour accéder au journal. Par exemple, dans le menu Azure Active Directory, vous pouvez ouvrir le journal dans la section **Surveillance**.  

![Ouvrir les journaux de connexion](./media/concept-sign-ins/sign-ins-logs-menu.png)

En outre, vous pouvez accéder directement aux journaux des connexions à l’aide de ce lien : [https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)


## <a name="what-is-the-default-view"></a>Qu’est-ce que la vue par défaut ?

Un journal de connexion inclut un mode Liste par défaut, qui indique :

- La date de connexion
- L’utilisateur associé
- L’application à laquelle l’utilisateur s’est connecté
- L’état de connexion
- L’état de la détection de risque
- L’état de l’exigence de l’authentification multifacteur (MFA)

![Capture d’écran montrant les connexions Office 365 SharePoint Online.](./media/concept-sign-ins/sign-in-activity.png "Activité de connexion")

Vous pouvez personnaliser le mode Liste en cliquant sur **Colonnes** dans la barre d’outils.

![Capture d’écran montrant l’option Colonnes dans la page Connexions.](./media/concept-sign-ins/19.png "Activité de connexion")

La boîte de dialogue **Colonnes** vous permet d’accéder aux attributs sélectionnables. Dans un rapport de connexion, vous ne pouvez pas avoir de champs qui contiennent plusieurs valeurs pour une demande de connexion donnée sous forme de colonne. C’est par exemple le cas pour les détails d’authentification, les données d’accès conditionnel et l’emplacement réseau.   

![Capture d’écran montrant la boîte de dialogue Colonnes dans laquelle vous pouvez sélectionner des attributs.](./media/concept-sign-ins/columns.png "Activité de connexion")

Sélectionnez un élément dans la vue sous forme de liste pour obtenir des informations plus détaillées.

![Capture d’écran montrant une vue d’informations détaillées.](./media/concept-sign-ins/basic-sign-in.png "Activité de connexion")



## <a name="sign-in-error-code"></a>Code d’erreur de connexion

Si une connexion a échoué, vous pouvez obtenir plus d’informations sur la raison de cet échec dans la section **Informations de base** de l’élément de journal correspondant. 

![code d’erreur de connexion](./media/concept-all-sign-ins/error-code.png)
 
Bien que l’élément de journal vous fournisse la raison de l’échec, dans certains cas, vous pouvez obtenir plus d’informations en utilisant l’[outil de recherche de l’erreur de connexion](https://login.microsoftonline.com/error). Par exemple, s’il est disponible, cet outil vous propose des étapes de correction.  

![Outil de recherche du code d’erreur](./media/concept-all-sign-ins/error-code-lookup-tool.png)



## <a name="filter-sign-in-activities"></a>Filtrer les activités de connexion


Vous pouvez filtrer les données d’un journal pour les limiter à un niveau qui vous convient :

![Capture d’écran montrant l’option Ajouter des filtres.](./media/concept-sign-ins/04.png "Activité de connexion")

**ID de requête** : ID de la requête qui vous intéresse.

**Utilisateur** : nom ou nom d’utilisateur principal (UPN) de l’utilisateur qui vous intéresse.

**Application** : nom de l’application cible.
 
**État** : état de connexion qui vous intéresse :

- Succès

- Échec

- Interrompu


**Adresse IP** : adresse IP de l’appareil utilisé pour se connecter à votre locataire.

**Emplacement** : emplacement à partir duquel la connexion a été établie :

- City

- Département/Province

- Pays/région


**Ressource** : nom du service utilisé pour la connexion.


**ID de ressource** : ID du service utilisé pour la connexion.


**Application cliente** : type de l’application cliente utilisée pour se connecter à votre locataire :

![Filtre d’application cliente](./media/concept-sign-ins/client-app-filter.png)


> [!NOTE]
> Pour des raisons de confidentialité, Azure AD ne renseigne pas ce champ pour le locataire d’origine dans le cas d’un scénario interlocataire.


|Nom|Authentification moderne|Description|
|---|:-:|---|
|SMTP authentifié| |Utilisé par les clients POP et IMAP pour envoyer des e-mails.|
|Découverte automatique| |Utilisé par les clients Outlook et EAS pour rechercher des boîtes aux lettres dans Exchange Online et s’y connecter.|
|Exchange ActiveSync| |Ce filtre affiche toutes les tentatives de connexion où le protocole EAS a été utilisé.|
|Browser|![Coche bleue](./media/concept-sign-ins/check.png)|Affiche toutes les tentatives de connexion d’utilisateurs à l’aide de navigateurs web|
|Exchange ActiveSync| | Affiche toutes les tentatives de connexion d’utilisateurs avec des applications clientes utilisant Exchange ActiveSync pour se connecter à Exchange Online|
|Exchange Online PowerShell| |Utilisé pour se connecter à Exchange Online à l’aide de PowerShell à distance. Si vous bloquez l’authentification de base pour Exchange Online PowerShell, vous devez utiliser le module Exchange Online PowerShell pour vous connecter. Pour obtenir des instructions, consultez [Se connecter à Exchange Online PowerShell à l’aide de l’authentification multifacteur](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).|
|Exchange Web Services| |Interface de programmation utilisée par Outlook, Outlook pour Mac et des applications tierces.|
|IMAP4| |Un client de messagerie hérité qui utilise IMAP pour récupérer le courrier électronique.|
|MAPI sur HTTP| |Utilisé par Outlook 2010 et versions ultérieures.|
|Applications mobiles et clients de bureau|![Coche bleue](./media/concept-sign-ins/check.png)|Affiche toutes les tentatives de connexion d’utilisateurs à l’aide d’applications mobiles et de clients de bureau.|
|Carnet d’adresses en mode hors connexion| |Copie des collections de listes d’adresses téléchargées et utilisées par Outlook.|
|Outlook Anywhere (RPC sur HTTP)| |Utilisé par Outlook 2016 et versions antérieures.|
|Service Outlook| |Utilisé par l’application Courrier et Calendrier pour Windows 10.|
|POP3| |Un client de messagerie hérité qui utilise POP3 pour récupérer le courrier électronique.|
|Reporting Web Services| |Utilisé pour récupérer des données de rapports dans Exchange Online.|
|Autres clients| |Affiche toutes les tentatives de connexion d’utilisateurs où l’application cliente n’est pas incluse ou connue.|







**Système d’exploitation** : le système d’exploitation s’exécutant sur l’appareil a utilisé l’authentification auprès de votre locataire. 


**Explorateur d’appareils** : si la connexion a été établie à partir d’un navigateur, ce champ vous permet de filtrer par nom de navigateur.


**ID de corrélation** : ID de corrélation de l’activité.




**Accès conditionnel** : état des règles d’accès conditionnel appliquées

- **Non applicable** : aucune stratégie n’est appliquée à l’utilisateur et à l’application lors de la connexion.

- **Réussite** : une ou plusieurs stratégies d’accès conditionnel sont appliquées à l’utilisateur et à l’application (mais pas nécessairement les autres conditions) lors de la connexion. 

- **Échec** : la connexion a satisfait à la condition d’utilisateur et d’application d’au moins une stratégie d’accès conditionnel et les contrôles d’octroi ne sont pas satisfaisants ou ne sont pas configurés pour bloquer l’accès.









## <a name="download-sign-in-activities"></a>Télécharger les activités de connexion

Cliquez sur l’option **Télécharger** pour créer un fichier CSV ou JSON des 250 000 enregistrements les plus récents. Commencez par [télécharger les données de connexions](./howto-download-logs.md) si vous souhaitez les utiliser en dehors du portail Azure.  

![Télécharger](./media/concept-sign-ins/71.png "Téléchargement")

> [!IMPORTANT]
> Le nombre d’enregistrements que vous pouvez télécharger est limité par les [stratégies de rétention de rapport Azure Active Directory](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Raccourcis vers les données de connexions

Azure AD et le portail Azure vous offrent d’autres points d’entrée pour accéder aux données de connexions :

- Vue d’ensemble de la protection de la sécurité des identités
- Utilisateurs
- Groupes
- Applications d’entreprise

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Données de connexion des utilisateurs dans la protection de la sécurité des identités

Le graphique des connexions des utilisateurs figurant sur la page de présentation **Protection de la sécurité des identités** affiche les agrégations hebdomadaires des connexions. La valeur par défaut de cette période est de 30 jours.

![Capture d’écran montrant un graphique illustrant les connexions sur un mois.](./media/concept-sign-ins/06.png "Activité de connexion")

Lorsque vous cliquez sur un jour dans le graphique des connexions, vous obtenez une vue d’ensemble des activités de connexion correspondantes.

Chaque ligne de la liste des activités de connexion affiche :

* Qui s’est connecté ?
* Quelle application a été la cible de la connexion ?
* Quel est l’état de la connexion ?
* Quel est l’état MFA de la connexion ?

En cliquant sur un élément, vous obtenez plus d’informations sur l’opération de connexion :

- ID d'utilisateur
- Utilisateur
- Nom d’utilisateur
- ID de l'application
- Application
- Client
- Emplacement
- Adresse IP
- Date
- MFA obligatoire
- État de la connexion

> [!NOTE]
> Les adresses IP sont émises de manière à ce qu’il n’existe aucune connexion définitive entre une adresse IP et l’endroit où se trouve physiquement l’ordinateur avec cette adresse. Le mappage des adresses IP est compliqué par le fait que les fournisseurs mobiles et les VPN qui émettent des adresses IP à partir de pools centraux sont souvent très éloignés de l’endroit où l’appareil client est réellement utilisé. Actuellement, la conversion de l’adresse IP en un emplacement physique constitue la meilleure solution pour les suivis, les données de registre, les recherches inversées et d’autres informations.

Sur la page **Utilisateurs**, vous obtenez une vue d’ensemble complète de toutes les connexions des utilisateurs en cliquant sur **Connexions** dans la section **Activité**.

![Capture d’écran montrant la section Activité dans laquelle vous pouvez sélectionner des connexions.](./media/concept-sign-ins/08.png "Activité de connexion")

## <a name="authentication-details"></a>Informations sur l’authentification

L’onglet **Informations sur l’authentification** situé dans le rapport des connexions fournit les informations suivantes pour chaque tentative d’authentification :

- Une liste des stratégies d’authentification appliquées (par exemple, l’accès conditionnel, l’authentification multifacteur par utilisateur, les valeurs par défaut de sécurité)
- Liste des stratégies de durée de vie de session appliquées (par exemple, fréquence de connexion, mémoriser MFA, durée de vie des jetons configurable)
- La séquence des méthodes d’authentification utilisées pour la connexion
- Si la tentative d’authentification a réussi ou non
- Les raisons de la réussite ou de l’échec de la tentative d’authentification

Ces informations permettent aux administrateurs de dépanner chaque étape de la connexion d’un utilisateur et de suivre :

- Le volume des connexions protégées par l’authentification multifacteur 
- Raison de l’invite d’authentification en fonction des stratégies de durée de vie de session
- L’utilisation et le taux de réussite de chaque méthode d’authentification 
- L’utilisation de méthodes d’authentification sans mot de passe (par exemple, la connexion par téléphone sans mot de passe, FIDO2 et Windows Hello Entreprise) 
- La fréquence à laquelle les exigences d’authentification sont satisfaites par des revendications de jeton (où les utilisateurs ne sont pas invités de manière interactive à entrer un mot de passe, à entrer un mot de passe à usage unique par SMS, etc.)

Lorsque vous affichez le rapport des connexions, sélectionnez l’onglet **Informations sur l’authentification** : 

![Capture d’écran de l’onglet Informations sur l’authentification](media/concept-sign-ins/auth-details-tab.png)

>[!NOTE]
>Le **code de vérification OATH** est consigné comme méthode d’authentification pour les jetons matériels et logiciels OATH (par exemple, l’application Microsoft Authenticator).

>[!IMPORTANT]
>L’onglet **Informations sur l’authentification** peut initialement afficher des données incomplètes ou inexactes, jusqu’à ce que les informations du journal soient entièrement agrégées. Voici quelques exemples connus : 
>- Un message **Satisfaite par une revendication dans le jeton** s’affiche de manière incorrecte lors de la journalisation initiale des événements de connexion. 
>- La ligne **Authentification principale** n’est pas initialement consignée. 


## <a name="usage-of-managed-applications"></a>Utilisation des applications gérées

En disposant d’une vue centrée sur les applications de vos données de connexion, vous pouvez répondre aux questions telles que :

* Qui utilise mes applications ?
* Quelles sont les trois principales applications dans votre organisation ?
* Comment fonctionne mon application la plus récente ?

Le point d’entrée de ces données correspond aux trois principales applications de votre organisation. Les données sont contenues dans le rapport sur les 30 derniers jours dans la section **Vue d’ensemble** sous **Applications d’entreprise**.

![Capture d’écran montrant où vous pouvez sélectionner Vue d’ensemble.](./media/concept-sign-ins/10.png "Activité de connexion")

Les graphiques d’utilisation des applications affiche les agrégations hebdomadaires des connexions pour vos trois principales applications au cours d’une période donnée. La valeur par défaut de cette période est de 30 jours.

![Capture d’écran montrant l’utilisation de l’application pendant une période d’un mois.](./media/concept-sign-ins/graph-chart.png "Activité de connexion")

Si vous le souhaitez, vous pouvez définir la focalisation sur une application spécifique.

![Création de rapports](./media/concept-sign-ins/single-app-usage-graph.png "Signalement")

Lorsque vous cliquez sur un jour dans le graphique d’utilisation des applications, vous obtenez une liste détaillée des activités de connexion.

L’option **Connexions** vous fournit une vue d’ensemble complète de tous les événements de connexion à vos applications.

## <a name="microsoft-365-activity-logs"></a>Journaux d’activité Microsoft 365

Vous pouvez consulter les journaux d’activité Microsoft 365 dans le [centre d’administration Microsoft 365](/office365/admin/admin-overview/about-the-admin-center). N’oubliez pas que les journaux d’activité de Microsoft 365 et d’Azure AD partagent un nombre important de ressources d’annuaire. Seul le centre d’administration Microsoft 365 fournit une vue complète des journaux d’activité d’Microsoft 365. 

Vous pouvez également accéder par programme aux journaux d’activité de Microsoft 365 en utilisant les [API de gestion Microsoft 365](/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Étapes suivantes

* [Stratégies de conservation des données Azure AD](reference-reports-data-retention.md)
* [Latences de rapport Azure AD](reference-reports-latencies.md)
* [Rapport sur les connexions des applications de Microsoft](/troubleshoot/azure/active-directory/verify-first-party-apps-sign-in#application-ids-for-commonly-used-microsoft-applications)
