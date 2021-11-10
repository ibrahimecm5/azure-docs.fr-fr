---
title: Journaux de connexion dans Azure Active Directory - préversion | Microsoft Docs
description: Présentation des journaux de connexion dans Azure Active Directory, y compris des nouvelles fonctionnalités de la préversion.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 06/23/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 209b9f66a9d7d8d004162e465c85311886b423fa
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997207"
---
# <a name="sign-in-logs-in-azure-active-directory---preview"></a>Journaux de connexion dans Azure Active Directory - préversion

En tant qu’administrateur informatique, vous souhaitez savoir comment fonctionne votre environnement informatique. Les informations sur l’intégrité de votre système vous permettent d’évaluer si vous devez répondre aux problèmes potentiels et, le cas échéant, de quelle manière. 

Pour vous aider à atteindre cet objectif, le portail Azure Active Directory vous donne accès à trois journaux d’activité :

- **[Connexion](concept-sign-ins.md)**  : Informations sur les connexions et la manière dont vos ressources sont utilisées par vos utilisateurs.
- **[Audit](concept-audit-logs.md)**  : Informations sur les modifications appliquées à votre locataire, telles que la gestion des utilisateurs et des groupes ou les mises à jour appliquées aux ressources de votre locataire.
- **[Approvisionnement](concept-provisioning-logs.md)**  : Activités réalisées par le service d’approvisionnement, telles que la création d’un groupe dans ServiceNow ou l’importation d’un utilisateur à partir de Workday.


Le journal classique sur les connexions dans Azure Active Directory fournit une vue d’ensemble des connexions utilisateur interactives. En plus de celui-ci, vous avez désormais accès à trois journaux de connexion supplémentaires actuellement en préversion :

- Connexions utilisateur non interactives

- Connexions de principal de service

- Identités managées pour les connexions aux ressources Azure

Cet article présente une vue d’ensemble du rapport d’activité de connexion avec la préversion des connexions non interactives, d’applications et d’identités managées pour les ressources Azure. Pour plus d’informations sur le rapport de connexion sans les fonctionnalités en version préliminaire, consultez [Journaux de connexion dans le portail Azure Active Directory](concept-sign-ins.md).



## <a name="what-can-you-do-with-it"></a>Que pouvez-vous faire avec ?

Le journal des connexions contient des réponses à des questions telles que les suivantes :

- Quel est le modèle de connexion d’un utilisateur, d’une application ou d’un service ?

- Combien d’utilisateurs, d’applications ou de services se sont-ils connectés au cours d’une semaine ?

- Quel est l’état de ces connexions ?


## <a name="who-can-access-the-data"></a>Qui peut accéder aux données ?

- Les utilisateurs ayant le rôle Administrateur de sécurité, Lecteur Sécurité et Lecteur de rapports

- Les administrateurs généraux

- Tous les utilisateurs (non administrateurs) peuvent accéder à leurs propres connexions 

## <a name="what-azure-ad-license-do-you-need"></a>De quelle licence Azure AD avez-vous besoin ?

Votre client doit avoir une licence Azure AD Premium associée pour afficher les activités de connexion. Consultez [Bien démarrer avec Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) pour mettre à niveau votre édition d’Azure Active Directory. Quelques jours seront nécessaires pour que les données s’affichent dans les journaux après la mise à niveau vers une licence premium s’il n’y a aucune activité de données avant la mise à niveau.




## <a name="where-can-you-find-it-in-the-azure-portal"></a>Où le trouver dans le portail Azure ?

Le portail Azure vous offre plusieurs options pour accéder au journal. Par exemple, dans le menu Azure Active Directory, vous pouvez ouvrir le journal dans la section **Surveillance**.  

![Ouvrir les journaux de connexion](./media/concept-sign-ins/sign-ins-logs-menu.png)

En outre, vous pouvez accéder directement au journal de connexion à l’aide de ce lien : [https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)

Sur la page des connexions, vous pouvez basculer entre les deux affichages suivants :

- **Connexions utilisateur interactives** – Connexions où un utilisateur fournit un facteur d’authentification, tel qu’un mot de passe, une réponse via une application d’authentification multifacteur, un facteur biométrique ou un code QR.

- **Connexions utilisateur non interactives** – Connexions établies par un client pour le compte d’un utilisateur. Ces connexions ne nécessitent ni interaction, ni facteur d’authentification de la part de l’utilisateur. L’authentification et l’autorisation se font à l’aide de jetons d’accès et d’actualisation ne nécessitent pas que l’utilisateur entre des informations d’identification.

- **Connexions de principal de service** – Connexions établies par des principaux d’applications et de service n’impliquant aucune intervention de l’utilisateur. Dans ces connexions, l’application ou le service fournit des informations d’identification pour son propre compte afin de s’authentifier ou d’accéder à des ressources.

- **Connexions avec identités managées pour ressources Azure** – Connexions établies par des ressources Azure dont les secrets sont gérés par Azure. Pour plus d’informations, consultez [Que sont les identités managées pour les ressources Azure ?](../managed-identities-azure-resources/overview.md) 


![Types de journaux de connexion](./media/concept-all-sign-ins/sign-ins-report-types.png)



Chaque onglet de la page des connexions affiche les colonnes par défaut ci-dessous. Certains onglets contiennent des colonnes supplémentaires :

- Date de la connexion

- ID de la demande

- Nom ou ID de l’utilisateur

- Nom ou ID de l’application

- État de la connexion

- Adresse IP de l’appareil utilisé pour la connexion



### <a name="interactive-user-sign-ins"></a>Connexions utilisateur interactives


Les connexions utilisateur interactives sont des connexions où un utilisateur fournit un facteur d’authentification à Azure AD ou interagit directement avec Azure AD ou une application auxiliaire telle que l’application Microsoft Authenticator. Les facteurs que les utilisateurs fournissent incluent des mots de passe, des réponses à des demandes d’authentification multifacteur, des facteurs biométriques ou des codes QR qu’un utilisateur fournit à Azure AD ou à une application auxiliaire.

> [!NOTE]
> Ce journal comprend également des connexions fédérées de fournisseurs d’identité qui sont fédérés à Azure AD.  



> [!NOTE] 
> Le journal des connexions utilisateur interactives contenait auparavant des connexions non interactives de clients Microsoft Exchange. Bien que ces connexions ne soient pas interactives, elles étaient incluses dans le journal des connexions utilisateur interactives pour une meilleure visibilité. Une fois le journal des connexions utilisateur non interactives entré en préversion publique en novembre 2020, les journaux des connexions non interactives ont été déplacés vers le rapport de connexions utilisateur non interactives pour une plus grande précision. 


**Taille du rapport :** petite <br> 
**Exemples :**

- Un utilisateur fournit un nom d’utilisateur et un mot de passe dans l’écran de connexion à Azure AD.

- Un utilisateur transmet une réponse reçue par SMS à une demande d’authentification multifacteur.

- Un utilisateur effectue un geste d’identification biométrique pour déverrouiller son PC Windows avec Windows Hello Entreprise.

- Un utilisateur est fédéré à Azure AD avec une assertion SAML AD FS.


En plus des champs par défaut, le journal des connexions interactives affiche les informations suivantes : 

- Emplacement de la connexion

- Application ou non de l’accès conditionnel



Vous pouvez personnaliser le mode Liste en cliquant sur **Colonnes** dans la barre d’outils.

![Colonnes de connexion utilisateur interactive](./media/concept-all-sign-ins/columns-interactive.png "Colonnes de connexion utilisateur interactive")





La personnalisation de la vue vous permet d’afficher des champs supplémentaires ou de supprimer des champs affichés.

![Toutes les colonnes interactives](./media/concept-all-sign-ins/all-interactive-columns.png)


Sélectionnez un élément dans la liste pour obtenir des informations plus détaillées sur la connexion associée.

![Activité de connexion](./media/concept-all-sign-ins/interactive-user-sign-in-details.png "Connexions utilisateur interactives")



### <a name="non-interactive-user-sign-ins"></a>Connexions utilisateur non interactives

Les connexions utilisateur non interactives sont des connexions établies par une application cliente ou des composants du système d’exploitation pour le compte d’un utilisateur. À l’instar des connexions utilisateur interactives, ces connexions sont établies pour le compte d’un utilisateur. Contrairement aux connexions utilisateur interactives, ces connexions ne nécessitent pas que l’utilisateur fournisse un facteur d’authentification. Au lieu de cela, l’appareil ou l’application cliente utilisent un jeton ou du code pour s’authentifier ou accéder à une ressource pour le compte d’un utilisateur. En général, l’utilisateur perçoit ces connexions comme se produisant en arrière-plan de son activité.


**Taille du rapport :** grand <br>
**Exemples :** 

- Une application cliente utilise un jeton d’actualisation OAuth 2.0 pour obtenir un jeton d’accès.

- Un client utilise un code d’autorisation OAuth 2.0 pour obtenir un jeton d’accès et un jeton d’actualisation.

- Un utilisateur effectue une authentification unique (SSO) auprès d’une application web ou Windows sur un PC joint à Azure AD (sans fournir de facteur d’authentification ni interagir avec une invite Azure AD).

- Un utilisateur se connecte à une deuxième application Microsoft Office tandis qu’il a une session ouverte sur un appareil mobile à l’aide d’une FOCI (famille d’ID client).




En plus des champs par défaut, le journal des connexions non-interactives affiche les informations suivantes : 

- ID de ressource

- Nombre de connexions regroupées




Vous ne pouvez pas personnaliser les champs affichés dans ce rapport.


![Colonnes désactivées](./media/concept-all-sign-ins/disabled-columns.png "Colonnes désactivées")

Pour faciliter la synthèse des données, les événements de connexion non interactive sont regroupés. Les clients créent souvent de nombreuses connexions non interactives pour le compte d’un même utilisateur sur une brève période, qui partagent les mêmes caractéristiques, à l’exception de l’heure à laquelle la connexion a été tentée. Par exemple, un client peut obtenir un jeton d’accès une fois par heure pour le compte d’un utilisateur. Si l’utilisateur ou le client ne changent pas d’état, l’adresse IP, la ressource et toutes les autres informations sont les mêmes pour chaque demande de jeton d’accès. Quand Azure AD journalise plusieurs connexions identiques à l’exception de la date et de l’heure, ces connexions en provenance de la même entité sont agrégées dans une seule ligne. Une ligne avec plusieurs connexions identiques (à l’exception de la date et de l’heure) a une valeur supérieure à 1 dans la colonne du nombre de connexions. Vous pouvez développer la ligne pour voir les différentes connexions et leurs horodatages. Les connexions sont agrégées dans les utilisateurs non interactifs lorsque les données suivantes correspondent :


- Application

- Utilisateur

- Adresse IP

- Statut

- ID de ressource


Vous pouvez :

- développer un nœud pour afficher les éléments d’un groupe ;  

- cliquer sur un élément pour afficher tous ses détails. 


![Détails de connexion utilisateur non interactive](./media/concept-all-sign-ins/non-interactive-sign-ins-details.png)




## <a name="service-principal-sign-ins"></a>Connexions de principal de service

Contrairement aux connexions utilisateur interactives et non interactives, les connexions de principal de service n’impliquent pas d’utilisateur. Au lieu de cela, il s’agit de connexions établies par n’importe quel compte non-utilisateur, par exemple, des applications ou des principaux de service (à l’exception des connexions d’identités managées qui sont incluses uniquement dans le journal homonyme). Dans le cadre de ces connexions, l’application ou le service fournit ses propres informations d’identification, telles qu’un certificat ou un secret d’application, pour s’authentifier ou accéder à des ressources.


**Taille du rapport :** grand <br>
**Exemples :**

- Un principal de service utilise un certificat pour s’authentifier et accéder au Microsoft Graph. 

- Une application utilise une clé secrète client pour s’authentifier dans le flux des informations d’identification de client OAuth. 


Ce rapport contient une liste par défaut qui affiche les éléments suivants :

- Date de la connexion

- ID de la demande

- Nom ou ID de principal de service

- Statut

- Adresse IP

- Nom de la ressource

- ID de ressource

- Nombre de connexions

Vous ne pouvez pas personnaliser les champs affichés dans ce rapport.

![Colonnes désactivées](./media/concept-all-sign-ins/disabled-columns.png "Colonnes désactivées")

Pour faciliter la synthèse des données dans les journaux de connexion du principal de service, les événements de connexion de celui-ci sont regroupés. Les connexions à partir de la même entité dans les mêmes conditions sont agrégées dans une seule ligne. Vous pouvez développer la ligne pour voir les différentes connexions et leurs horodatages. Les connexions sont agrégées dans le rapport du principal de service lorsque les données suivantes correspondent :

- Nom ou ID de principal de service

- Statut

- Adresse IP

- Nom ou ID de la ressource

Vous pouvez :

- développer un nœud pour afficher les éléments d’un groupe ;  

- cliquer sur un élément pour afficher tous ses détails. 


![Détails des colonnes](./media/concept-all-sign-ins/service-principals-sign-ins-view.png "Détails de colonne")




## <a name="managed-identity-for-azure-resources-sign-ins"></a>Connexions avec identité managée pour ressources Azure 

Les connexions avec identité managée pour ressources Azure sont des connexions établies par des ressources dont les secrets sont gérés par Azure pour simplifier la gestion des informations d’identification.

**Taille du rapport :** Petite <br> 
**Exemples :**

Une machine virtuelle avec des informations d’identification managées utilise Azure AD pour obtenir un jeton d’accès.   


Ce rapport contient une liste par défaut qui affiche les éléments suivants :


- ID de l’identité managée

- Nom de l’identité managée

- Ressource

- ID de ressource

- Nombre de connexions regroupées

Vous ne pouvez pas personnaliser les champs affichés dans ce rapport.

Pour faciliter la synthèse des données, les journaux de connexion avec identités managées pour ressources Azure et les événements de connexion non interactifs sont regroupés. Les connexions à partir de la même entité sont agrégées dans une seule ligne. Vous pouvez développer la ligne pour voir les différentes connexions et leurs horodatages. Les connexions sont agrégées dans le rapport d’identités managées quand toutes les données suivantes correspondent :

- Nom ou ID de l’identité managée

- Statut

- Nom ou ID de la ressource

Sélectionnez un élément dans la liste pour afficher toutes les connexions regroupées sous un nœud.

Sélectionnez un élément regroupé pour afficher tous les détails de la connexion. 


## <a name="sign-in-error-code"></a>Code d’erreur de connexion

Si une connexion a échoué, vous pouvez obtenir plus d’informations sur la raison de cet échec dans la section **Informations de base** de l’élément de journal correspondant. 

![Capture d’écran montrant une vue d’informations détaillées.](./media/concept-all-sign-ins/error-code.png)
 
Bien que l’élément de journal vous fournisse la raison de l’échec, dans certains cas, vous pouvez obtenir plus d’informations en utilisant l’[outil de recherche de l’erreur de connexion](https://login.microsoftonline.com/error). Par exemple, s’il est disponible, cet outil vous propose des étapes de correction.  

![Outil de recherche du code d’erreur](./media/concept-all-sign-ins/error-code-lookup-tool.png)



## <a name="filter-sign-in-activities"></a>Filtrer les activités de connexion

En définissant un filtre, vous pouvez réduire l’étendue des données de connexion retournées. Azure AD vous offre une large gamme de filtres supplémentaires que vous pouvez définir. Lorsque vous définissez votre filtre, vous devez toujours prêter une attention particulière à votre filtre de **plage de dates** de configuré. Un filtre de plage de dates approprié garantit qu’Azure AD retourne uniquement les données qui vous intéressent vraiment.     

Le filtre de **plage de dates** vous permet de définir un intervalle de temps pour les données renvoyées.
Les valeurs possibles sont les suivantes :

- Un mois

- Sept jours

- Vingt-quatre heures

- Custom

![Filtre de plage de dates](./media/concept-all-sign-ins/date-range-filter.png)





### <a name="filter-user-sign-ins"></a>Filtrer les connexions utilisateur

Le filtre est le même pour les des connexions interactives et non interactives. C’est pourquoi le filtre que vous avez configuré pour les connexions interactives est conservé pour les connexions non interactives, et inversement. 






## <a name="access-the-new-sign-in-activity-logs"></a>Accéder aux nouveaux journaux d’activité de connexion 

Le rapport d’activité de connexion dans le Portail Azure offre une méthode simple pour activer et désactiver le rapport en préversion. Si les journaux en préversion sont activés, un nouveau menu s’affiche, qui vous permet d’accéder à tous les types de rapports d’activité de connexion.     


Pour accéder aux nouveaux journaux de connexion avec des connexions d’application non interactives : 

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**.

    ![Sélectionner Azure AD](./media/concept-all-sign-ins/azure-services.png)

2. Dans la section **Surveillance**, cliquez **Connexions**.

    ![Sélectionner Connexions](./media/concept-all-sign-ins/sign-ins.png)

3. Cliquez sur la barre de **Préversion**.

    ![Activer le nouvel affichage](./media/concept-all-sign-ins/enable-new-preview.png)

4. Pour revenir à l’affichage par défaut, cliquez de nouveau sur la barre **Préversion**. 

    ![Restaurer l’affichage classique](./media/concept-all-sign-ins/switch-back.png)







## <a name="download-sign-in-activity-logs"></a>Télécharger les journaux d’activité de connexion

Lorsque vous téléchargez un rapport d’activité de connexion, les conditions suivantes sont réunies :

- Vous pouvez télécharger le rapport de connexion en tant que fichier CSV ou JSON.

- Vous pouvez télécharger jusqu’à 100 000 enregistrements. Si vous souhaitez télécharger davantage de données, utilisez l’API de création de rapports.

- Votre téléchargement est basé sur la sélection de filtre que vous avez effectuée.

- Le nombre d’enregistrements que vous pouvez télécharger est limité par les [stratégies de rétention de rapport Azure Active Directory](reference-reports-data-retention.md). 


![Télécharger les journaux](./media/concept-all-sign-ins/download-reports.png "Télécharger les journaux")


Chaque téléchargement au format CSV se compose de six fichiers différents :

- Connexions interactives

- Détails d’authentification des connexions interactives

- Connexions non interactives

- Détails d’authentification des connexions non interactives

- Connexions de principal de service

- Connexions avec identité managée pour ressources Azure

Chaque téléchargement au format JSON se compose de quatre fichiers différents :

- Connexions interactives (inclut des détails d’authentification)

- Connexions non interactives (inclut des détails d’authentification)

- Connexions de principal de service

- Connexions avec identité managée pour ressources Azure

![Télécharger des fichiers](./media/concept-all-sign-ins/download-files.png "Télécharger des fichiers")


## <a name="return-log-data-with-microsoft-graph"></a>Retourner des données de journal avec Microsoft Graph

Outre l’utilisation du portail Azure, vous pouvez interroger les journaux de connexion à l’aide de l’API Microsoft Graph pour retourner différents types d’informations de connexion. Pour éviter d’éventuels problèmes de performances, limitez votre requête aux données qui vous intéressent. 

L’exemple suivant limite la requête par le nombre d’enregistrements, sur une période spécifique et par type d’événement de connexion :

```msgraph-interactive
GET https://graph.microsoft.com/beta/auditLogs/signIns?$top=100&$filter=createdDateTime ge 2020-09-10T06:00:00Z and createdDateTime le 2020-09-17T06:00:00Z and signInEventTypes/any(t: t eq 'nonInteractiveUser')
```

Les paramètres de requête de l’exemple fournissent les résultats suivants :

- Le paramètre [$top](/graph/query-parameters#top-parameter) renvoie les 100 premiers résultats.
- Le paramètre [$filter](/graph/query-parameters#filter-parameter) limite le laps de temps pour que les résultats soient renvoyés et utilise la propriété signInEventTypes pour retourner uniquement les connexions utilisateur non interactives.

Les valeurs suivantes sont disponibles pour le filtrage par différents types de connexions : 

- interactiveUser
- nonInteractiveUser
- servicePrincipal 
- managedIdentity

## <a name="next-steps"></a>Étapes suivantes

* [Codes d’erreur du rapport d’activité de connexion](./concept-sign-ins.md)
* [Stratégies de conservation des données Azure AD](reference-reports-data-retention.md)
* [Latences de rapport Azure AD](reference-reports-latencies.md)
