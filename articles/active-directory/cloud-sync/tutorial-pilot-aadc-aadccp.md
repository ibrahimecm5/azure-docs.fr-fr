---
title: Tutoriel - Piloter la synchronisation cloud Azure AD Connect pour une forêt AD synchronisée
description: Découvrez comment piloter la synchronisation cloud pour une forêt Active Directory test déjà synchronisée à l’aide de la synchronisation Azure Active Directory (Azure AD) Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b5e89c0f632630c9d4bf08afad24d2035507c9f
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989676"
---
# <a name="pilot-cloud-sync-for-an-existing-synced-ad-forest"></a>Piloter la synchronisation cloud pour une forêt AD synchronisée existante

Ce tutoriel vous guide tout au long du pilotage de la synchronisation cloud pour une forêt Active Directory test déjà synchronisée à l’aide de la synchronisation Azure Active Directory (Azure AD) Connect.

![Créer](media/tutorial-migrate-aadc-aadccp/diagram-2.png)

## <a name="considerations"></a>Considérations

Avant de suivre ce tutoriel, tenez compte des éléments suivants :

1. Vous devez connaître les bases de la synchronisation cloud.

2. Veillez à exécuter la synchronisation Azure AD Connect version 1.4.32.0 ou ultérieure et à configurer les règles de synchronisation stipulées. Pendant la procédure, vous allez supprimer un groupe ou une unité d’organisation de test de l’étendue de la synchronisation Azure AD Connect. Le déplacement d’objets en dehors de l’étendue provoque la suppression de ces objets dans Azure AD. Dans le cas d’objets utilisateur, les objets figurant dans Azure AD sont supprimés de façon réversible et peuvent être restaurés. Dans le cas d’objets de groupe, les objets figurant dans Azure AD sont supprimés définitivement et ne peuvent pas être restaurés. Un nouveau type de lien a été introduit dans la synchronisation Azure AD Connect, qui empêchera la suppression dans le cas d’un scénario de pilotage.

3. Vérifiez que ms-ds-consistencyGUID a été renseigné pour les objets figurant dans l’étendue pilote afin d’avoir une correspondance exacte entre la synchronisation cloud et les objets.

   > [!NOTE]
   > La synchronisation Azure AD Connect ne renseigne pas *ms-ds-consistencyGUID* par défaut pour les objets de groupe.

4. Il s’agit d’un scénario avancé. Veillez à suivre avec précision les étapes décrites dans ce tutoriel.

## <a name="prerequisites"></a>Prérequis

Voici les conditions préalables requises pour suivre ce didacticiel.

- Un environnement de test avec la synchronisation Azure AD Connect version 1.4.32.0 ou ultérieure
- Une unité d’organisation ou un groupe figurant dans l’étendue de la synchronisation et pouvant servir de pilote. Nous vous recommandons de commencer avec un ensemble réduit d’objets.
- Un serveur exécutant Windows Server 2012 R2 ou ultérieur, qui hébergera l’agent de provisionnement.
- L’ancre source pour la synchronisation Azure AD Connect doit être *objectGuid* ou *ms-ds-consistencyGUID*

## <a name="update-azure-ad-connect"></a>Mettre à jour Azure AD Connect

Au minimum, vous devez disposer d’[Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)1.4.32.0. Pour mettre à jour la synchronisation Azure AD Connect, suivez les étapes décrites dans [Azure AD Connect : Mettre à niveau vers la version la plus récente](../hybrid/how-to-upgrade-previous-version.md).  

## <a name="stop-the-scheduler"></a>Arrêter le planificateur

La synchronisation Azure AD Connect synchronise les modifications intervenant dans votre annuaire local à l’aide d’un planificateur. Pour pouvoir modifier et ajouter des règles personnalisées, vous pouvez désactiver le planificateur pour que les synchronisations ne s’exécutent pas pendant que vous travaillez à cette tâche.  Utiliser les étapes suivantes :

1. Sur le serveur qui exécute la synchronisation Azure AD Connect, ouvrez PowerShell avec des privilèges d’administrateur.
2. Exécutez `Stop-ADSyncSyncCycle`.  Appuyez sur Entrée.
3. Exécutez `Set-ADSyncScheduler -SyncCycleEnabled $false`.

>[!NOTE]
>Si vous exécutez votre propre planificateur personnalisé pour la synchronisation Azure AD Connect, désactivez le planificateur.

## <a name="create-custom-user-inbound-rule"></a>Créer une règle personnalisée de trafic entrant utilisateur

 1. Exécutez l’éditeur de synchronisation à partir d’un menu d’application dans le poste de travail comme indiqué ci-après :</br>
 ![Menu de l’Éditeur de règles de synchronisation](media/tutorial-migrate-aadc-aadccp/user-8.png)</br>

 2. Sélectionnez **Inbound** (Entrant) dans la liste déroulante Direction, puis cliquez sur **Add new rule** (Ajouter une nouvelle règle).
 ![Capture d’écran montrant la fenêtre « View and manage your synchronization rules » (Afficher et gérer vos règles de synchronisation) avec « Inbound » (Entrant) et le bouton « Add new rule » (Ajouter une nouvelle règle) sélectionnés.](media/tutorial-migrate-aadc-aadccp/user-1.png)</br>

 3. Dans la page **Description**, entrez les informations ci-dessous et cliquez sur **Suivant** :

    **Nom :** attribuez un nom explicite à la règle.<br>
    **Description :** ajoutez une description explicite.<br>
    **Connected System** (Système connecté) : choisissez le connecteur AD pour lequel vous écrivez la règle de synchronisation personnalisée.<br>
    **Connected System Object Type** (Type d’objet système connecté) : Utilisateur<br>
    **Metaverse Object Type** (Type d’objet métaverse) : Personne<br>
    **Link Type** (Type de lien) : Join<br>
    **Precedence** (Précédence) : indiquez une valeur unique dans le système.<br>
    **Tag** (Balise) : laissez ce champ vide.<br>
    ![Capture d’écran montrant la page « Create inbound synchronization rule - Description » (Créer une règle de synchronisation de trafic entrant - Description) avec des valeurs entrées.](media/tutorial-migrate-aadc-aadccp/user-2.png)</br>

 4. Dans la page **Scoping filter** (Filtre d’étendue), entrez l’unité d’organisation ou le groupe de sécurité sur lesquels vous souhaitez que le pilote s’appuie.  Pour filtrer sur l’unité d’organisation, ajoutez la partie UO du nom unique. Cette règle s’appliquera à tous les utilisateurs qui se trouvent dans cette unité d’organisation. Par conséquent, si DN se termine par « OU=CPUsers,DC=contoso,DC=com », vous devez ajouter ce filtre.  Cliquez ensuite sur **Suivant**.

    |Règle|Attribut|Opérateur|Valeur|
    |-----|----|----|-----|
    |Étendue d’unité d’organisation|DN|ENDSWITH|Nom unique de l’unité d’organisation.|
    |Étendue de groupe||ISMEMBEROF|Nom unique du groupe de sécurité.|

    ![Capture d’écran montrant la page «**Create inbound synchronization rule - Scoping filter** (Créer une règle de synchronisation de trafic - Filtre d’étendue) avec une valeur de filtre d’étendue entrée.](media/tutorial-migrate-aadc-aadccp/user-3.png)</br>

 5. Dans la page **Join rules** (Règles de jointure), cliquez sur **Suivant**.
 6. Dans la page **Transformations**, ajoutez une transformation constante : spécifiez True pour l’attribut cloudNoFlow. Cliquez sur **Add**.
 ![Capture d’écran montrant la page **Create inbound synchronization rule - Transformations** (Créer une règle de synchronisation de trafic entrant - Transformations) avec un flux **Constant transformation** (Transformation constante) ajouté.](media/tutorial-migrate-aadc-aadccp/user-4.png)</br>

Les mêmes étapes doivent être suivies pour tous les types d’objets (utilisateur, groupe et contact). Répétez les étapes par connecteur AD configuré/par forêt AD.

## <a name="create-custom-user-outbound-rule"></a>Créer une règle personnalisée de trafic sortant utilisateur

 1. Sélectionnez **Outbound** (Sortant) dans la liste déroulante Direction, puis cliquez sur **Add new rule** (Ajouter une nouvelle règle).
 ![Capture d’écran montrant la direction **Outbound** (Sortant) sélectionnée et le bouton **Add new rule** (Ajouter une nouvelle règle) mis en évidence.](media/tutorial-migrate-aadc-aadccp/user-5.png)</br>

 2. Dans la page **Description**, entrez les informations ci-dessous et cliquez sur **Suivant** :

    **Nom :** attribuez un nom explicite à la règle.<br>
    **Description :** ajoutez une description explicite.<br>
    **Connected System** (Système connecté) : Choisissez le connecteur Azure AD pour lequel vous écrivez la règle de synchronisation personnalisée.<br>
    **Connected System Object Type** (Type d’objet système connecté) : Utilisateur<br>
    **Metaverse Object Type** (Type d’objet métaverse) : Personne<br>
    **Link Type** (Type de lien) : JoinNoFlow<br>
    **Precedence** (Précédence) : indiquez une valeur unique dans le système.<br>
    **Tag** (Balise) : laissez ce champ vide.<br>

    ![Capture d’écran montrant la page **Description** avec des propriétés entrées.](media/tutorial-migrate-aadc-aadccp/user-6.png)</br>

 3. Dans la page **Scoping filter** (Filtre d’étendue), choisissez **cloudNoFlow** EQUAL **True**. Cliquez ensuite sur **Suivant**.
 ![Règle personnalisée](media/tutorial-migrate-aadc-aadccp/user-7.png)</br>

 4. Dans la page **Join rules** (Règles de jointure), cliquez sur **Suivant**.
 5. Dans la page **Transformations**, cliquez sur **Add** (Ajouter).

Les mêmes étapes doivent être suivies pour tous les types d’objets (utilisateur, groupe et contact).

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Installer l’agent de provisionnement Azure AD Connect

1. Connectez-vous au serveur que vous allez utiliser à l’aide des autorisations d’administrateur d’entreprise.  Si vous utilisez le tutoriel [Environnement de base Active Directory et Azure](tutorial-basic-ad-azure.md), il s’agit de CP1.

1. Téléchargez l’agent de provisionnement cloud Azure AD Connect en utilisant la procédure décrite [ici](how-to-install.md#install-the-agent).

1. Exécuter la synchronisation cloud Azure AD Connect (AADConnectProvisioningAgent.Installer) 1 . Dans l’écran de démarrage, **acceptez** les termes du contrat de licence et cliquez sur **Installer**.

    ![Capture de l’écran de démarrage **Agent de provisionnement Microsoft Azure AD Connect**.](media/how-to-install/install-1.png)</br>

1. Une fois cette opération terminée, l’Assistant de configuration démarre.  Connectez-vous avec votre compte d’administrateur général Azure AD.

1. Dans l’écran **Connexion à Active Directory**, cliquez sur **Ajout d’un annuaire**, puis connectez-vous avec votre compte d’administrateur Active Directory.  Cette opération ajoute votre annuaire local. Cliquez sur **Suivant**.

    ![Capture de l’écran **Connexion à Active Directory** avec une valeur d’annuaire entrée.](media/how-to-install/install-3a.png)

1. Dans l’écran **Configuration terminée**, cliquez sur **Confirmer**.  Cette opération inscrit et redémarre l’agent.

    ![Capture d’écran montrant le volet **Configuration terminée** avec le bouton **Confirmer** sélectionné.](media/how-to-install/install-4a.png)

1. Une fois cette opération terminée, vous devriez voir une notification : **La configuration de votre agent a été vérifiée avec succès.**  Vous pouvez sélectionner **Quitter**.

    ![Écran d’accueil](media/how-to-install/install-5.png)</br>

1. Si vous voyez encore l’écran de démarrage initial, cliquez sur **Fermer**.

## <a name="verify-agent-installation"></a>Vérifier l’installation de l’agent

La vérification de l’agent se produit dans le portail Azure et sur le serveur local qui exécute l’agent.

### <a name="azure-portal-agent-verification"></a>Vérification de l’agent dans le portail Azure

Pour vérifier que l’agent est visible par Azure, procédez comme suit :

1. Connectez-vous au portail Azure.
2. Sur la gauche, sélectionnez **Azure Active Directory**, cliquez sur **Azure AD Connect** et, au centre, sélectionnez **Gérer la synchronisation cloud**.</br>
![Azure portal](media/how-to-install/install-6.png)</br>

3. Dans l’écran **Synchronisation cloud Azure AD Connect**, cliquez sur **Passer en revue tous les agents**.
![Provisionnement Azure AD](media/how-to-install/install-7.png)</br>

4. Dans l’écran **Agents de provisionnement locaux**, vous voyez les agents que vous avez installés.  Vérifiez que l’agent en question est présent et qu’il est marqué comme étant **désactivé**.  L’agent est désactivé par défaut par défaut parmi les ![agents de provisionnement](media/how-to-install/verify-1.png)</br>

### <a name="on-the-local-server"></a>Sur le serveur local

Pour vérifier que l’agent est en cours d’exécution, effectuez les étapes suivantes :

1. Connectez-vous au serveur avec un compte administrateur.
2. Ouvrez **Services** en y accédant ou en accédant à Démarrer/Exécuter/Services.msc.
3. Sous **Services**, assurez-vous que le **Programme de mise à jour de l’agent Microsoft Azure AD Connect** et l’**Agent de provisionnement Microsoft Azure AD Connect** sont présents, et que leur état est **En cours d’exécution**.

    ![Services](media/how-to-install/troubleshoot-1.png)

## <a name="configure-azure-ad-connect-cloud-sync"></a>Configurer la synchronisation cloud Azure AD Connect

Pour configurer le provisionnement, procédez comme suit :

1. Connectez-vous au portail Azure AD.
2. Cliquez sur **Azure Active Directory**.
3. Cliquez sur **Azure AD Connect**.
4. Sélectionnez **Gérer la synchronisation cloud** ![Capture d’écran montrant le lien « Gérer la synchronisation cloud ».](media/how-to-configure/manage-1.png)</br>
5. Cliquez sur **Nouvelle configuration** ![Capture d’écran de l’écran Synchronisation cloud Azure AD Connect avec le lien « Nouvelle configuration » mis en évidence.](media/tutorial-single-forest/configure-1.png)</br>
6. Dans l’écran de configuration, entrez une adresse **e-mail de notification**, déplacez le sélecteur sur **Activer** et cliquez sur **Enregistrer**.
    ![Capture d’écran de l’écran de configuration avec l’e-mail de notification rempli et l’option Activer sélectionnée.](media/tutorial-single-forest/configure-2.png)</br>
7. Sous **Configurer**, sélectionnez **Tous les utilisateurs** pour modifier l’étendue de la règle de configuration.
    ![Capture d’écran de l’écran Configurer avec « Tous les utilisateurs » mis en évidence en regard de « Utilisateurs de l’étendue ».](media/how-to-configure/scope-2.png)</br>
8. Sur la droite, modifiez l’étendue pour inclure l’unité d’organisation spécifique que vous venez de créer : « OU=CPUsers,DC=contoso,DC=com ».
    ![Capture d’écran de l’écran Utilisateurs de l’étendue mettant en évidence l’étendue remplacée par l’unité d’organisation que vous avez créée.](media/tutorial-existing-forest/scope-2.png)</br>
9. Cliquez sur **Terminé**, puis sur **Enregistrer**.
10. L’étendue doit maintenant être définie sur une unité d’organisation.
    ![Capture d’écran de l’écran Configurer avec « 1 unité d’organisation » mis en évidence en regard de « Utilisateurs de l’étendue ».](media/tutorial-existing-forest/scope-3.png)</br>

## <a name="verify-users-are-provisioned-by-cloud-sync"></a>Vérifier que les utilisateurs sont provisionnés par la synchronisation cloud

Vous allez maintenant vérifier que les utilisateurs de l’annuaire local ont été synchronisés et figurent maintenant dans le locataire Azure AD.  Cette opération peut prendre quelques heures.  Pour vérifier que les utilisateurs sont provisionnés par la synchronisation cloud, effectuez les étapes suivantes :

1. Accédez au [portail Azure](https://portal.azure.com) et connectez-vous avec un compte qui dispose d’un abonnement Azure.
2. À gauche, sélectionnez **Azure Active Directory**
3. Cliquez sur **Azure AD Connect**.
4. Cliquez sur **Gérer la synchronisation cloud**
5. Cliquez sur le bouton **Journaux**.
6. Rechercher un nom d’utilisateur pour vérifier que l’utilisateur est provisionné par la synchronisation cloud

De plus, vous pouvez vérifier que l’utilisateur et le groupe existent dans Azure AD.

## <a name="start-the-scheduler"></a>Démarrer le planificateur

La synchronisation Azure AD Connect synchronise les modifications intervenant dans votre annuaire local à l’aide d’un planificateur. Maintenant que vous avez modifié les règles, vous pouvez redémarrer le planificateur.  Utiliser les étapes suivantes :

1. Sur le serveur qui exécute la synchronisation Azure AD Connect, ouvrez PowerShell avec des privilèges d’administrateur.
2. Exécutez `Set-ADSyncScheduler -SyncCycleEnabled $true`.
3. Exécutez `Start-ADSyncSyncCycle`, puis appuyez sur <kbd>Entrée</kbd>.

> [!NOTE]
> Si vous exécutez votre propre planificateur personnalisé pour la synchronisation Azure AD Connect, activez le planificateur.

Une fois le planificateur activé, Azure AD Connect arrêtera d’exporter les modifications apportées aux objets avec `cloudNoFlow=true` dans le métaverse, à moins qu’un attribut de référence (par exemple, `manager`) ne soit mis à jour. En cas de mise à jour d’un attribut de référence sur l’objet, Azure AD Connect ignorera le signal `cloudNoFlow` et exportera toutes les mises à jour sur l’objet.

## <a name="something-went-wrong"></a>Survenue d’un problème

Si le pilote ne fonctionne pas comme prévu, vous pouvez revenir à la configuration de la synchronisation Azure AD Connect en suivant les étapes ci-dessous :

1. Désactivez la configuration du provisionnement dans le portail Azure.
2. Désactivez toutes les règles de synchronisation personnalisées créées pour le provisionnement cloud à l’aide de l’Éditeur de règles de synchronisation. La désactivation doit entraîner une synchronisation complète sur tous les connecteurs.

## <a name="next-steps"></a>Étapes suivantes

- [Présentation du provisionnement](what-is-provisioning.md)
- [Qu’est-ce que la synchronisation cloud Azure AD Connect ?](what-is-cloud-sync.md)
