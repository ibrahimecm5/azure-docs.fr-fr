---
title: Provisionnement de journaux dans Azure Active Directory | Microsoft Docs
description: Présentation des journaux d’approvisionnement dans Azure Active Directory.
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
ms.date: 4/25/2021
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 167ba276023191abd894081e8540a83b8b4b0bee
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997188"
---
# <a name="provisioning-logs-in-azure-active-directory"></a>Provisionnement de journaux dans Azure Active Directory

En tant qu’administrateur informatique, vous souhaitez savoir comment fonctionne votre environnement informatique. Les informations sur l’intégrité de votre système vous permettent d’évaluer si vous devez répondre aux problèmes potentiels et, le cas échéant, de quelle manière. 

Pour vous aider à atteindre cet objectif, le portail Azure Active Directory vous donne accès à trois journaux d’activité :

- **[Connexions](concept-sign-ins.md)**  : Informations sur les connexions et la manière dont vos ressources sont utilisées par vos utilisateurs.
- **[Audit](concept-audit-logs.md)**  : Informations sur les modifications appliquées à votre locataire, telles que la gestion des utilisateurs et des groupes ou les mises à jour appliquées aux ressources de votre locataire.
- **[Approvisionnement](concept-provisioning-logs.md)**  : Activités réalisées par le service d’approvisionnement, telles que la création d’un groupe dans ServiceNow ou l’importation d’un utilisateur à partir de Workday.


Cet article présente une vue d’ensemble des journaux d’approvisionnement. 


## <a name="what-can-you-do-with-it"></a>Que pouvez-vous faire avec ?

Vous pouvez utiliser les journaux d’approvisionnement pour trouver des réponses à des questions telles que :

-  Quels groupes ont été créés avec succès dans ServiceNow ?

-  Quels utilisateurs ont été correctement supprimés d’Adobe ?

-  Quels utilisateurs de Workday ont été correctement crées dans Active Directory ? 


## <a name="who-can-access-it"></a>Qui peut y accéder ?

Les utilisateurs suivants peuvent accéder aux données des journaux de provisionnement :

- Propriétaires d’applications (journaux de leurs propres applications)

- Utilisateurs des rôles Administrateur de la sécurité, Lecteur de sécurité, Lecteur de rapports, Opérateur de sécurité, Administrateur d’application et Administrateur d’application cloud

- Utilisateurs dans un rôle personnalisé avec l’[autorisation provisioningLogs](../roles/custom-enterprise-app-permissions.md#full-list-of-permissions)

- Administrateurs généraux

## <a name="what-azure-ad-license-do-you-need"></a>De quelle licence Azure AD avez-vous besoin ?

Il faut qu’une licence Azure AD Premium soit associée à votre locataire pour que vous puissiez voir le rapport d’activité de l’approvisionnement. Pour mettre à niveau votre édition d’Azure AD, consultez [Bien démarrer avec Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md). 


## <a name="how-can-you-access-it"></a>Comment pouvez-vous y accéder ? 

Pour accéder aux données du journal, vous disposez des options suivantes :

- Le portail Azure

- Diffusion en continu des journaux de provisionnement dans [Azure Monitor](../app-provisioning/application-provisioning-log-analytics.md), méthode qui permet une conservation étendue des données et la création de tableaux de bord, d’alertes et de requêtes personnalisés

- Interrogation de l’[API Microsoft Graph](/graph/api/resources/provisioningobjectsummary) pour obtenir les journaux de provisionnement.

- Téléchargement des journaux de provisionnement sous la forme d’un fichier CSV ou JSON



## <a name="where-can-you-find-it-in-the-azure-portal"></a>Où le trouver dans le portail Azure ?

Le portail Azure vous offre plusieurs options pour accéder au journal. Par exemple, dans le menu Azure Active Directory, vous pouvez ouvrir le journal dans la section **Surveillance**.  

![Ouvrir les journaux d’approvisionnement](./media/concept-sign-ins/sign-ins-logs-menu.png)

En outre, vous pouvez accéder directement aux journaux des connexions à l’aide de ce lien : [https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)












Vous pouvez accéder aux journaux de provisionnement en sélectionnant **Journaux de provisionnement** dans la section **Supervision** du volet **Azure Active Directory** sur le [Portail Azure](https://portal.azure.com). L’affichage de certains enregistrements de provisionnement sur le portail peut prendre jusqu’à deux heures.

![Capture d’écran montrant les sélections pour accéder aux journaux de provisionnement.](./media/concept-provisioning-logs/access-provisioning-logs.png "Journaux d’approvisionnement")



## <a name="what-is-the-default-view"></a>Qu’est-ce que la vue par défaut ?

Un journal d’approvisionnement comporte un affichage de liste par défaut qui indique :

- L’identité
- L’action
- Le système source
- Le système cible
- Le statut
- La date


![Capture d’écran montrant les colonnes par défaut dans un journal de provisionnement.](./media/concept-provisioning-logs/default-columns.png "Colonnes par défaut")

Vous pouvez personnaliser le mode Liste en sélectionnant **Colonnes** dans la barre d’outils.

![Capture d’écran montrant le bouton de personnalisation des colonnes.](./media/concept-provisioning-logs/column-chooser.png "Sélecteur de colonnes")

Cette zone permet d’afficher des champs supplémentaires et de supprimer des champs déjà affichés.

![Capture d’écran montrant les colonnes disponibles, dont certaines sélectionnées.](./media/concept-provisioning-logs/available-columns.png "Colonnes disponibles")

Sélectionnez un élément dans la vue sous forme de liste pour obtenir des informations plus détaillées.

![Capture d’écran montrant des informations détaillées.](./media/concept-provisioning-logs/steps.png "Filtrer")


## <a name="filter-provisioning-activities"></a>Filtrer les activités d’approvisionnement

Vous pouvez filtrer vos données d'approvisionnement. Certaines valeurs de filtre sont renseignées dynamiquement en fonction de votre locataire. Si, par exemple, votre locataire ne comporte pas d’événements de type « créer », aucune option de filtre **Créer** n’apparaît.

Dans l'affichage par défaut, vous pouvez sélectionner les filtres suivants :

- **Identité**
- **Date**
- **État**
- **Action**


![Capture d’écran montrant les valeurs de filtre.](./media/concept-provisioning-logs/default-filter.png "Filtrer")

Le filtre **Identité** vous permet de spécifier le nom ou l’identité qui vous intéresse. Cette identité peut être un utilisateur, un groupe, un rôle ou un autre objet. 

Vous pouvez effectuer une recherche par nom ou ID de l’objet. L’ID varie selon le scénario. Par exemple, lors du provisionnement d’un objet d’Azure AD vers Salesforce, l’ID source correspond à l’ID d’objet de l’utilisateur dans Azure AD. L’ID cible, lui, correspond à l’ID de l’utilisateur dans Salesforce. En cas de provisionnement de Workday vers Active Directory, l’ID source est l’ID employé du travailleur Workday. 

> [!NOTE]
> Il se peut que le nom de l’utilisateur ne soit pas toujours présent dans la colonne **Identité**. Il y aura toujours un ID. 


Le filtre **Date** vous permet de définir un intervalle de temps pour les données renvoyées. Les valeurs possibles sont les suivantes :

- 1 mois
- 7 jours
- 30 jours
- 24 heures
- Intervalle de temps personnalisé

Lorsque vous sélectionnez un délai d’exécution personnalisé, vous pouvez configurer une date de début et une date de fin.

Le filtre **État** vous permet de sélectionner les états suivants :

- **Tout**
- **Success**
- **Échec**
- **Ignoré**

Le filtre **Action** permet de filtrer les actions suivantes :

- **Créer** 
- **Mettre à jour**
- **Supprimer**
- **Désactiver**
- **Autres**

En plus des filtres de l’affichage par défaut, il est possible de définir les suivants.

![Capture d’écran montrant les champs qui peuvent être ajoutés comme filtres.](./media/concept-provisioning-logs/add-filter.png "Choisir un champ")

- **ID de tâche** : un ID de tâche unique est associé à chacune des applications pour lesquelles le provisionnement est activé.   

- **ID de cycle** : l’ID de cycle identifie de façon unique le cycle de provisionnement. Vous pouvez partager cet ID avec le support produit pour rechercher le cycle dans lequel cet événement s’est produit.

- **ID de modification** : l’ID de modification est un identificateur unique de l’événement de provisionnement. Vous pouvez partager cet ID avec le support produit pour rechercher l’événement de provisionnement.   

- **Système source** : vous pouvez spécifier l’emplacement à partir duquel l’identité est provisionnée. Par exemple, lors du provisionnement d’un objet d’Azure AD vers ServiceNow, le système source est Azure AD. 

- **Système cible** : vous pouvez spécifier l’emplacement vers lequel l’identité est provisionnée. Par exemple, lors du provisionnement d’un objet d’Azure AD vers ServiceNow, le système cible est ServiceNow. 

- **Application** : vous pouvez afficher uniquement les enregistrements des applications dont le nom d’affichage contient une chaîne spécifique.

## <a name="provisioning-details"></a>Détails de l’approvisionnement 

Lorsque vous sélectionnez un élément dans la vue de la liste d’approvisionnement, vous pouvez obtenir plus de détails sur cet élément. Les détails sont regroupés dans les onglets suivants.

![Capture d’écran montrant quatre onglets contenant les détails de provisionnement.](./media/concept-provisioning-logs/provisioning-tabs.png "Onglets")

- **Étapes** : procédure suivie pour provisionner un objet. L’approvisionnement d’un objet peut être constitué de quatre étapes :
  
  1. Importer l’objet
  1. Déterminer si l’objet est concerné
  1. Faire correspondre l’objet entre la source et la cible
  1. Provisionner l’objet (créer, mettre à jour, supprimer ou désactiver)

  ![Capture d’écran montrant la procédure de provisionnement dans l’onglet Étapes.](./media/concept-provisioning-logs/steps.png "Filtrer")

- **Résolution des problèmes et recommandations** : code d’erreur et raison. Les informations sur l’erreur ne sont disponibles qu’en cas de défaillance.

- **Propriétés modifiées** : ancienne valeur et nouvelle valeur. En l’absence d’ancienne valeur, cette colonne est vide.

- **Résumé**: vue d’ensemble des événements et des identificateurs de l’objet dans les systèmes source et cible.

## <a name="download-logs-as-csv-or-json"></a>Télécharger les journaux au format CSV ou JSON

Vous pouvez télécharger les journaux de provisionnement pour plus tard en y accédant sur le Portail Azure et en sélectionnant **Télécharger**. Le fichier sera filtré en fonction des critères sélectionnés. Choisissez des filtres aussi précis que possible pour réduire la taille et le temps de téléchargement. 

Le téléchargement CSV comprend trois fichiers :

* **ProvisioningLogs** : Télécharge tous les journaux, à l’exception des étapes de provisionnement et des propriétés modifiées.
* **ProvisioningLogs_ProvisioningSteps** : Contient les étapes de provisionnement et l’ID de la modification. Vous pouvez vous servir de l’ID de modification pour joindre l’événement aux deux autres fichiers.
* **ProvisioningLogs_ModifiedProperties** : Contient les attributs qui ont été modifiés et l’ID de la modification. Vous pouvez vous servir de l’ID de modification pour joindre l’événement aux deux autres fichiers.

#### <a name="open-the-json-file"></a>Ouverture du fichier JSON
Pour ouvrir le fichier JSON, utilisez un éditeur de texte comme [Microsoft Visual Studio Code](https://aka.ms/vscode). Visual Studio Code facilite la lecture du fichier grâce à la coloration syntaxique. Vous pouvez également ouvrir le fichier JSON en utilisant des navigateurs dans un format non modifiable, par exemple [Microsoft Edge](https://aka.ms/msedge). 

#### <a name="prettify-the-json-file"></a>Mise en forme du fichier JSON
Le fichier JSON est téléchargé dans un format minimisé afin de réduire la taille du téléchargement. Ce format peut rendre la charge utile difficile à lire. Prenez connaissance des deux options possibles pour agrémenter le fichier :

- Utiliser [Visual Studio Code pour mettre en forme le fichier JSON](https://code.visualstudio.com/docs/languages/json#_formatting)

- Utiliser PowerShell pour mettre en forme le fichier JSON. Ce script génère le fichier JSON dans un format comportant des tabulations et des espaces : 

  ` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

  `$JSONContent | ConvertTo-Json > <PATH TO OUTPUT THE JSON FILE>`

#### <a name="parse-the-json-file"></a>Analyse du fichier JSON

Voici quelques exemples de commandes à utiliser sur le fichier JSON avec PowerShell. Vous pouvez faire appel à n’importe quel langage de programmation que vous connaissez.  

Tout d’abord, [lisez le fichier JSON](/powershell/module/microsoft.powershell.utility/convertfrom-json) en exécutant cette commande :

` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

Vous pouvez maintenant analyser les données suivant votre scénario. Voici quelques exemples : 

- Afficher tous les ID de tâche du fichier JSON :

  `foreach ($provitem in $JSONContent) { $provitem.jobId }`

- Afficher tous les ID de modification des événements dont l’action était « créer » :

  `foreach ($provitem in $JSONContent) { `
  `   if ($provItem.action -eq 'Create') {`
  `       $provitem.changeId `
  `   }`
  `}`

## <a name="what-you-should-know"></a>Ce que vous devez savoir

Voici quelques conseils et considérations à prendre en compte pour le provisionnement des rapports :

- Le Portail Azure stocke les données de provisionnement des rapports pendant 30 jours avec une édition Premium et pendant 7 jours avec une édition gratuite. Il est possible de publier les journaux de provisionnement dans [Log Analytics](../app-provisioning/application-provisioning-log-analytics.md) à des fins de rétention au-delà de 30 jours. 

- Vous pouvez utiliser l’attribut l’ID de modification comme identificateur unique. C’est utile pour interagir avec le support produit, par exemple.

- Il est possible que vous voyiez des événements ignorés pour les utilisateurs qui ne sont pas concernés. Cela est prévu, en particulier lorsque l’étendue de synchronisation est définie sur tous les utilisateurs et groupes. Le service évalue tous les objets du locataire, y compris ceux qui ne sont pas concernés. 

- Les journaux d’approvisionnement ne sont actuellement pas disponibles dans le cloud Government. Si vous ne parvenez pas à accéder aux journaux de provisionnement, utilisez les journaux d’audit comme solution temporaire de contournement. 

- Les journaux de provisionnement n’affichent pas les importations de rôle (s’applique à AWS, Salesforce et Zendesk). Vous trouverez les journaux des importations de rôle dans les journaux d’audit. 

## <a name="error-codes"></a>Codes d’erreur

Appuyez-vous sur le tableau suivant pour mieux comprendre comment résoudre les erreurs que vous trouvez dans les journaux de provisionnement. Pour tous les codes d’erreur manquants, faites-nous part de vos commentaires en suivant le lien en bas de cette page. 

|Code d'erreur|Description|
|---|---|
|Conflict, EntryConflict|Corrigez les valeurs d’attribut en conflit dans Azure AD ou l’application. Vous pouvez également passer en revue votre configuration d’attributs de correspondance si le compte d’utilisateur en conflit était censé être mis en correspondance et pris en charge. Pour plus d’informations sur la configuration des attributs de correspondance, consultez la [documentation](../app-provisioning/customize-application-attributes.md).|
|TooManyRequests|L’application cible a rejeté cette tentative de mise à jour de l’utilisateur, car elle est surchargée et reçoit trop de requêtes. Il n’y a rien à faire. Cette tentative sera automatiquement supprimée. Microsoft a également été informé de ce problème.|
|InternalServerError |L’application cible a retourné une erreur inattendue. Il se peut qu’un problème de service lié à l’application cible l’empêche de fonctionner. Cette tentative sera automatiquement mise hors service dans 40 minutes.|
|InsufficientRights, MethodNotAllowed, NotPermitted, Unauthorized| Azure AD s’est authentifié auprès de l’application cible, mais n’a pas été autorisé à effectuer la mise à jour. Passez en revue toutes les instructions fournies par l’application cible et le [tutoriel](../saas-apps/tutorial-list.md) de l’application correspondante.|
|UnprocessableEntity|L’application cible a renvoyé une réponse inattendue. Il se peut que la configuration de l’application cible ne soit pas correcte ou qu’un problème de service lié à l’application cible l’empêche de fonctionner.|
|WebExceptionProtocolError |Une erreur de protocole HTTP s’est produite lors de la connexion à l’application cible. Il n’y a rien à faire. Cette tentative sera automatiquement mise hors service dans 40 minutes.|
|InvalidAnchor|Un utilisateur qui a été précédemment créé ou mis en correspondance par le service d’approvisionnement n’existe plus. Vérifiez que l’utilisateur existe. Pour forcer une nouvelle correspondance de tous les utilisateurs, utilisez l’API Microsoft Graph pour [redémarrer le travail](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true). <br><br>Le redémarrage du provisionnement déclenche un cycle initial, ce qui peut prendre du temps. Le redémarrage du provisionnement supprime également le cache utilisé par le service de provisionnement pour fonctionner. De ce fait, tous les utilisateurs et groupes du locataire devront être réévalués, et certains événements de provisionnement risquent d’être abandonnés.|
|NotImplemented | L’application cible a retourné une réponse inattendue. Il se peut que la configuration de l’application ne soit pas correcte ou qu’un problème de service lié à l’application cible l’empêche de fonctionner. Passez en revue toutes les instructions fournies par l’application cible et le [tutoriel](../saas-apps/tutorial-list.md) de l’application correspondante. |
|MandatoryFieldsMissing, MissingValues |L’utilisateur n’a pas pu être créé, car des valeurs requises sont manquantes. Corrigez les valeurs d’attribut manquantes dans l’enregistrement source ou vérifiez dans votre configuration d’attributs de correspondance qu’aucun champ obligatoire n’est omis. [En savoir plus](../app-provisioning/customize-application-attributes.md) sur la configuration des attributs correspondants.|
|SchemaAttributeNotFound |L’opération n’a pas pu être effectuée, car l’un des attributs spécifiés n’existe pas dans l’application cible. Consultez la [documentation](../app-provisioning/customize-application-attributes.md) sur la personnalisation des attributs et vérifiez que votre configuration est correcte.|
|InternalError |Une erreur de service interne s’est produite au sein du service de provisionnement Azure AD. Il n’y a rien à faire. Cette tentative sera automatiquement mise hors service dans 40 minutes.|
|InvalidDomain |L’opération n’a pas pu être effectuée, car une valeur d’attribut contient un nom de domaine non valide. Mettez à jour le nom de domaine sur l’utilisateur ou ajoutez-le à la liste autorisée dans l’application cible. |
|Délai d'expiration |L’opération n’a pas pu aboutir, car l’application cible a mis trop de temps à répondre. Il n’y a rien à faire. Cette tentative sera automatiquement mise hors service dans 40 minutes.|
|LicenseLimitExceeded|L’utilisateur n’a pas pu être créé dans l’application cible, car il n’existe aucune licence disponible pour cet utilisateur. Procurez-vous des licences supplémentaires pour l’application cible. Vous pouvez également passer en revue vos affectations d’utilisateurs et votre configuration de mappage des attributs pour vérifier que les utilisateurs possèdent les attributs appropriés.|
|DuplicateTargetEntries  |L’opération n’a pas pu aboutir, car plusieurs utilisateurs ont été trouvés dans l’application cible avec les attributs de correspondance configurés. Supprimez l’utilisateur en doublon de l’application cible ou [reconfigurez vos mappages d’attributs](../app-provisioning/customize-application-attributes.md).|
|DuplicateSourceEntries | L’opération n’a pas pu aboutir, car plusieurs utilisateurs ont été trouvés avec les attributs de correspondance configurés. Supprimez l’utilisateur en doublon ou [reconfigurez vos mappages d’attributs](../app-provisioning/customize-application-attributes.md).|
|ImportSkipped | Lors de l’évaluation d’un utilisateur, le système tente de l’importer à partir du système source. Cette erreur se produit généralement lorsque la propriété de correspondance définie dans les mappages d’attributs n’a pas été attribuée à l’utilisateur importé. En l’absence de valeur sur l’objet utilisateur pour l’attribut de correspondance, le système ne peut pas évaluer l’étendue ni la correspondance, ni exporter les modifications. Il est à noter que la présence de cette erreur n’indique pas que l’utilisateur est concerné, car son étendue n’a pas encore été évaluée.|
|EntrySynchronizationSkipped | Le service d'approvisionnement a interrogé le système source et identifié l'utilisateur. Aucune autre mesure n'a été prise à l'égard de l'utilisateur et il a été ignoré. Il se peut que l’utilisateur ne soit pas concerné ou existe déjà dans le système cible sans qu’aucune autre modification soit nécessaire.|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| Une requête GET visant à récupérer un utilisateur ou un groupe a reçu plusieurs utilisateurs ou groupes dans la réponse. Le système s’attend à ne recevoir qu’un seul utilisateur ou groupe dans la réponse. [Par exemple](../app-provisioning/use-scim-to-provision-users-and-groups.md#get-group), si vous effectuez une requête GET pour récupérer un groupe et indiquez un filtre pour exclure des membres, et que votre point de terminaison SCIM (System for Cross-domain Identity Management) retourne les membres, vous recevez cette erreur.|

## <a name="next-steps"></a>Étapes suivantes

* [Vérifier l’état de l’approvisionnement d’utilisateurs](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [Problèmes lors de la configuration de l’approvisionnement des utilisateurs pour une application relevant de la galerie Azure AD](../app-provisioning/application-provisioning-config-problem.md)
* [API Graph pour les journaux de provisionnement](/graph/api/resources/provisioningobjectsummary)
