---
title: 'Tutoriel : Migrer le provisionnement de synchronisation Okta vers la synchronisation basée sur Azure AD Connect'
titleSuffix: Active Directory
description: Dans ce tutoriel, découvrez comment migrer le provisionnement de synchronisation Okta vers la synchronisation basée sur Azure AD Connect.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: 0902fbd761cb66eb5dbee4a5e3406ecba688c0b7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451480"
---
# <a name="tutorial-migrate-okta-sync-provisioning-to-azure-ad-connect-based-synchronization"></a>Tutoriel : Migrer le provisionnement de synchronisation Okta vers la synchronisation basée sur Azure AD Connect

Dans ce tutoriel, vous allez découvrir comment votre organisation peut actuellement migrer l’approvisionnement d’utilisateurs d’Okta vers Azure Active Directory (Azure AD) et migrer la synchronisation utilisateur ou la synchronisation universelle vers Azure AD Connect. Cette fonctionnalité permet d’effectuer un approvisionnement supplémentaire en Azure AD et Office 365.

La migration des plateformes de synchronisation n’est pas une modification minime. Chaque étape du processus mentionné dans cet article doit être validée par rapport à votre propre environnement avant de supprimer le Azure AD Connect du mode de préproduction ou d’activer l’agent d’approvisionnement cloud Azure AD.

## <a name="prerequisites"></a>Prérequis

Lorsque vous passez de l’approvisionnement Okta à Azure AD, vous avez deux possibilités. Vous pouvez utiliser un serveur Azure AD Connect ou l’approvisionnement cloud Azure AD. Pour comprendre les différences entre les deux, consultez l’[article de comparaison de Microsoft](../cloud-sync/what-is-cloud-sync.md#comparison-between-azure-ad-connect-and-cloud-sync).

L’approvisionnement cloud Azure AD est le chemin de migration le plus connu pour les clients Okta qui utilisent la synchronisation universelle ou utilisateur. Les agents d’approvisionnement cloud sont légers. Ils peuvent être installés sur ou à proximité des contrôleurs de domaine, comme les agents de synchronisation d’annuaires Okta. Ne les installez pas sur le même serveur.

Utilisez un serveur Azure AD Connect si votre organisation doit tirer parti des technologies suivantes quand vous synchronisez des utilisateurs :

- Synchronisation des appareils : joindre Azure AD Hybride ou Hello entreprise
- Authentification directe
- Prise en charge de plus de 150 000 objets
- Prise en charge de l’écriture différée

>[!NOTE]
>Toutes les conditions préalables doivent être prises en considération lorsque vous installez Azure AD Connect ou l’approvisionnement cloud Azure AD. Pour en savoir plus avant de poursuivre l’installation, consultez [Configuration requise pour Azure AD Connect](../hybrid/how-to-connect-install-prerequisites.md).

## <a name="confirm-immutableid-attribute-synchronized-by-okta"></a>Confirmer l’attribut ImmutableID synchronisé par Okta

ImmutableID est l’attribut principal utilisé pour lier les objets synchronisés à leurs équivalents locaux. Okta prend la valeur objectGUID Active Directory d’un objet local et la convertit en une chaîne encodée en Base64. Ensuite, marque par défaut cette chaîne dans le champ ImmutableID de Azure AD.

Vous pouvez vous connecter à Azure AD PowerShell et examiner la valeur ImmutableID actuelle. Si vous n’avez jamais utilisé le module Azure AD PowerShell, exécutez `Install-Module AzureAD` dans une session PowerShell d’administration avant d’exécuter les commandes suivantes :

```Powershell
Import-module AzureAD
Connect-AzureAD
```

Si vous disposez déjà du module, vous pouvez recevoir un avertissement pour effectuer une mise à jour vers la dernière version si elle est obsolète.

Une fois le module installé, importez-le et suivez les étapes ci-dessous pour vous connecter au service Azure AD :

1. Entrez vos informations d’identification d’administrateur général dans la fenêtre d’authentification moderne.

   ![Capture d’écran qui montre le module d’importation.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/import-module.png)

1. Après vous être connecté au client, vérifiez les paramètres de vos valeurs ImmutableID. L’exemple illustré utilise les valeurs par défaut Okta de objectGUID à ImmutableID.

   ![Capture d’écran montrant les valeurs par défaut Okta de objectGUID à ImmutableID.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/okta-default-objectid.png)

1. Il existe plusieurs façons de confirmer manuellement la conversion locale d’objectGUID en base64. Pour une validation individuelle, utilisez l’exemple suivant :

   ```PowerShell
   Get-ADUser onpremupn | fl objectguid
   $objectguid = 'your-guid-here-1010'
   [system.convert]::ToBase64String(([GUID]$objectGUID).ToByteArray())
   ```

   ![Capture d’écran montrant comment changer manuellement l’objectGUID Okta en ImmutableID.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/manual-objectguid.png)

## <a name="mass-validation-methods-for-objectguid"></a>Méthodes de validation de masse pour objectGUID

Avant de passer à Azure AD Connect, il est essentiel de vérifier que les valeurs ImmutableID dans Azure AD vont exactement correspondre à leurs valeurs locales.

L’exemple récupère *tous* les utilisateurs Azure AD locaux, puis exporte une liste de leurs valeurs objectGUID et ImmutableID déjà calculées dans un fichier CSV.

1. Exécutez les commandes suivantes dans PowerShell sur un contrôleur de domaine local :

   ```PowerShell
   Get-ADUser -Filter * -Properties objectGUID | Select-Object
   UserPrincipalName, Name, objectGUID, @{Name = 'ImmutableID';
   Expression = {
   [system.convert\]::ToBase64String(([GUID\]\$_.objectGUID).ToByteArray())
   } } | export-csv C:\\Temp\\OnPremIDs.csv
   ```

   ![Capture d’écran montrant les commandes locales du contrôleur de domaine.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/domain-controller.png)

1. Exécutez les commandes suivantes dans une session PowerShell Azure AD pour recueillir les valeurs déjà synchronisées :

   ```powershell
   Get-AzureADUser -all $true | Where-Object {$_.dirsyncenabled -like
   "true"} | Select-Object UserPrincipalName, @{Name = 'objectGUID';
   Expression = {
   [GUID][System.Convert]::FromBase64String($_.ImmutableID) } },
   ImmutableID | export-csv C:\\temp\\AzureADSyncedIDS.csv
   ```

   ![Capture d’écran montrant une session Azure AD PowerShell.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/azure-ad-powershell.png)

   Une fois que vous avez les deux exportations, vérifiez que le ImmutableID de chaque utilisateur correspond.

   >[!IMPORTANT]
   >Si vos valeurs ImmutableID dans le cloud ne correspondent pas aux valeurs objectGUID, vous avez modifié les valeurs par défaut pour la synchronisation Okta. Vous avez probablement choisi un autre attribut pour déterminer ImmutableID. Avant de passer à la section suivante, il est essentiel d’identifier l’attribut source qui remplit les valeurs ImmutableID. Veillez à mettre à jour l’attribut Okta avant de désactiver la synchronisation Okta.

## <a name="install-azure-ad-connect-in-staging-mode"></a>Installer Azure AD Connect en mode intermédiaire

Une fois que vous avez préparé votre liste de cibles source et de destination, il est temps d’installer le serveur Azure AD Connect. Si vous avez choisi d’utiliser l’approvisionnement cloud d’Azure AD Connect, vous pouvez ignorer cette section.

1. Poursuivez le [téléchargement et l’installation de Azure AD Connect](../hybrid/how-to-connect-install-custom.md) sur le serveur que vous avez choisi.

1. Sur la page **Identification des utilisateurs**, sous **Sélectionnez le mode d’identification des utilisateurs avec Azure AD**, sélectionnez l’option **Choisir un attribut spécifique**. Sélectionnez ensuite **ms-DS-ConsistencyGUID** si vous n’avez pas modifié les valeurs par défaut Okta.

   >[!WARNING]
   >Il s’agit de l’étape la plus importante de cette page. Avant de sélectionner **Suivant**, assurez-vous que l’attribut que vous sélectionnez pour le point d’ancrage source est celui qui remplit *actuellement* vos utilisateurs Azure AD existants. Si vous sélectionnez le mauvais attribut, vous devez désinstaller et réinstaller Azure AD Connect pour resélectionner cette option.
   
   ![Capture d’écran montrant mS-DS-ConsistencyGuid.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/consistency-guid.png)

1. Dans la page **Configurer**, veillez à activer la case à cocher **Activer le mode de préproduction**. Sélectionnez **Installer**.

   ![Capture d’écran qui montre la case à cocher Activer le mode de préproduction.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/enable-staging-mode.png)

1. Lorsque la configuration est terminée, sélectionnez **Quitter**.

   Avant de quitter le mode de préproduction, vérifiez que les valeurs ImmutableID correspondent correctement.

1. Ouvrez le Service de synchronisation en tant qu’**Administrateur**.

   ![Capture d'écran qui montre l'ouverture du service de synchronisation.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/open-sync-service.png)

1. Vérifiez que la **Synchronisation complète** de l’espace connecteur domain.onmicrosoft.com a des utilisateurs affichés sous l’onglet **Connecteurs avec des mises à jour de Flow**.

   ![Capture d’écran montrant les connecteurs avec l’onglet Mises à jour de Flow.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/connector-flow-update.png)

1. Vérifiez qu’il n’y a pas de suppressions en attente dans l’exportation. Sélectionnez l’onglet **Connecteurs**, puis mettez en surbrillance l’espace connecteur domain.onmicrosoft.com. Ensuite, sélectionnez **Rechercher l’espace de connecteur**.

   ![Capture d’écran montrant l’action Rechercher l’espace de connecteur.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/search-connector-space.png)

1. Dans la boîte de dialogue **Rechercher espace de connecteur**, sélectionnez la liste déroulante **Portée** et sélectionnez **Exportation en attente**.

   ![Capture d’écran montrant l’état Exportation en attente.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/pending-export.png)

1. Sélectionnez **Supprimer**, puis **Rechercher**. Si tous les objets ont une correspondance correcte, il ne doit y avoir aucun enregistrement correspondant pour les **suppressions**. Enregistrez tous les objets en attente de suppression et leurs valeurs locales.

   ![Capture d’écran montrant les enregistrements correspondants supprimés.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/delete-matching-records.png)

1. Désactivez la case à cocher **Supprimer**, puis sélectionnez **Ajouter** et **Modifier**, suivi d’une recherche. Vous devez voir les fonctions de mise à jour pour tous les utilisateurs en cours de synchronisation sur Azure AD via Okta. Ajoutez tous les nouveaux objets que Okta n’est pas en train de synchroniser, mais qui existent dans la structure de l’unité d’organisation (UO) qui a été sélectionnée lors de l’installation de Azure AD Connect.

   ![Capture d’écran qui illustre l’ajout d’un nouvel objet.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/add-new-object.png)

1. Double-cliquer sur les mises à jour indique ce que Azure AD Connect communique avec Azure AD.

1. S’il existe des fonctions d’**ajout** pour un utilisateur qui existe déjà dans Azure AD, son compte local ne correspond pas à son compte cloud. AD Connect a déterminé qu’il va créer un nouvel objet et enregistrer les nouveaux ajouts qui sont inattendus. Veillez à corriger la valeur ImmutableID dans Azure AD avant de quitter le mode intermédiaire.

   Dans cet exemple, Okta avait marqué l’attribut **mail** sur le compte de l’utilisateur, même si la valeur locale n’a pas été correctement remplie. Lorsque Azure AD Connect prend le compte de John Smith, l’attribut **mail** est supprimé de son objet.

   Vérifiez que vos mises à jour incluent toujours tous les attributs attendus dans Azure AD. Si plusieurs attributs sont en cours de suppression, vous devrez peut-être remplir manuellement ces valeurs AD locales avant de supprimer le mode intermédiaire.

   ![Capture d’écran montrant le remplissage des valeurs d’ajout locales.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/on-premises-ad-values.png)

   >[!NOTE]
   >Avant de passer à l’étape suivante, assurez-vous que tous les attributs utilisateur sont correctement synchronisés et qu’ils apparaissent dans l’onglet **Exportation en attente** comme prévu. S’ils sont supprimés, assurez-vous que la correspondance de leur ImmutableID et que l’utilisateur se trouve dans l’une des UO sélectionnées pour la synchronisation.

## <a name="install-azure-ad-cloud-sync-agents"></a>Installer les agents Azure AD Cloud

Une fois que vous avez préparé votre liste de cibles source et de destination, il est temps [d’installer et configurer les agents de synchronisation du cloud Azure AD](../cloud-sync/tutorial-single-forest.md). Si vous avez choisi d’utiliser le serveur Azure AD Connect, ignorez cette section.

## <a name="disable-okta-provisioning-to-azure-ad"></a>Désactivation de l’approvisionnement Okta pour Azure AD

Une fois que l’installation de Azure AD Connect a été vérifiée et que vos exportations en attente sont dans l’ordre, il est temps de désactiver l’approvisionnement Okta pour Azure AD.

1. Accédez à votre portail Okta, sélectionnez **Applications**, puis votre application Okta utilisée pour approvisionner les utilisateurs en Azure AD. Ouvrez l’onglet **Approvisionnement**, puis sélectionnez la section **Intégration**.

   ![Capture d’écran montrant la section Intégration dans Okta.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/integration-section.png)

1. Sélectionnez **Modifier**, désactivez l’option **Activer l’intégration d’API**, puis sélectionnez **Enregistrer**.

   ![Capture d’écran montrant la modification de l’activation de l’intégration d’API dans Okta.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/edit-api-integration.png)

   >[!NOTE]
   >Si vous avez plusieurs applications Office 365 qui gèrent l’approvisionnement pour Azure AD, assurez-vous qu’elles sont toutes désactivées.

## <a name="disable-staging-mode-in-azure-ad-connect"></a>Désactivation du mode intermédiaire dans Azure AD Connect

Après la désactivation de l’approvisionnement Okta, le serveur Azure AD Connect est prêt à commencer la synchronisation des objets. Si vous avez choisi d’utiliser les agents de synchronisation Cloud Azure AD, ignorez cette section.

1. Exécutez à nouveau l’Assistant Installation à partir du bureau, puis sélectionnez **Configurer**.

   ![Capture d’écran montrant le serveur Azure AD Connect.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/azure-ad-connect-server.png)

1. Sélectionnez **Configurer le mode de préproduction**, puis cliquez sur **Suivant**. Entrez vos informations d'identification d'administrateur général.

   ![Capture d’écran montrant l’option de configuration du mode de préproduction.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/configure-staging-mode.png)

1. Désactivez l’option **Activer le mode de préproduction**, puis sélectionnez **Suivant**.

   ![Capture d’écran montrant la désactivation de l’option Activer le mode de préproduction.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/uncheck-enable-staging-mode.png)

1. Sélectionnez **Configurer** pour continuer.

   ![Capture d'écran qui montre la sélection du bouton Configurer.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/ready-to-configure.png)

1. Une fois la configuration terminée, ouvrez le **Service de synchronisation** en tant qu’administrateur. Affichez **l’exportation** sur le connecteur domain.onmicrosoft.com. Vérifiez que l’ensemble des ajouts, mises à jour et suppressions s’effectuent comme prévu.

   ![Capture d’écran montrant la vérification du service de synchronisation.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/verify-sync-service.png)

Vous avez maintenant effectué une migration vers l’approvisionnement basé sur le serveur Azure AD Connect. Les mises à jour et les expansions de l’ensemble de fonctionnalités de Azure AD Connect peuvent être effectuées en réexécutant l’assistant d’installation.

## <a name="enable-cloud-sync-agents"></a>Activer les agents de synchronisation Cloud

Après la désactivation de l’approvisionnement Okta, l’agent de synchronisation cloud Azure AD est prêt à commencer la synchronisation des objets. Retournez au [portail Azure AD](https://aad.portal.azure.com/).

1. Modifiez le **Profil de configuration** sur **Activé**.

1. Revenez au menu d’approvisionnement et sélectionnez **Journaux**.

1. Évaluer que le connecteur de provisionnement a été correctement mis à jour sur place. Les agents de synchronisation cloud ne sont pas destructifs. Ils échouent à leurs mises à jour si une correspondance n’a pas été effectuée correctement.

1. Si un utilisateur ne correspond pas, effectuez les mises à jour nécessaires pour lier les valeurs ImmutableID. Ensuite, redémarrez la synchronisation du provisionnement cloud.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la migration d’Okta vers Azure AD, consultez :

- [Migrer des applications d’Okta vers Azure AD](migrate-applications-from-okta-to-azure-active-directory.md)
- [Migrer la fédération Okta vers l’authentification managée Azure AD](migrate-okta-federation-to-azure-active-directory.md)
- [Migrer les stratégies de connexion Okta vers l’accès conditionnel Azure AD](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)
