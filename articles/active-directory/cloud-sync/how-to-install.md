---
title: Installer l’agent de provisionnement Azure AD Connect
description: Découvrez comment installer l’agent de provisionnement Azure AD Connect et le configurer sur le Portail Azure.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/19/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 294e66b8b7cef3cec9931b5369562a51b138ab28
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131448668"
---
# <a name="install-the-azure-ad-connect-provisioning-agent"></a>Installer l’agent de provisionnement Azure AD Connect
Cet article vous guide tout au long du processus d’installation de l’agent d’approvisionnement Azure Active Directory (Azure AD) Connecte, et explique comment le configurer au départ dans le portail Azure.

>[!IMPORTANT]
>Les instructions d’installation suivantes supposent que tous les [prérequis](how-to-prerequisites.md) ont été respectés.

>[!NOTE]
>Cet article traite de l’installation de l’agent d’approvisionnement à l’aide de l’Assistant. Pour plus d’informations sur l’installation de l’agent d’approvisionnement d’Azure AD Connect à l’aide d’une interface de ligne de commande (CLI), consultez [Installation de l’agent d’approvisionnement Azure AD Connect à l’aide d’une CLI et de PowerShell](how-to-install-pshell.md).

Pour consulter plus d’informations et un exemple, regardez la vidéo suivante.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWK5mR]

## <a name="group-managed-service-accounts"></a>Group Managed Service Accounts
Un compte de service managé de groupe (gMSA) est un compte de domaine managé qui fournit la gestion automatique des mots de passe, la gestion simplifiée du nom de principal du service (SPN) et la possibilité de déléguer la gestion à d’autres administrateurs. Il étend également cette fonctionnalité sur plusieurs serveurs. La synchronisation cloud Azure AD Connect prend en charge et recommande l’utilisation d’un compte de service géré de groupe pour l’exécution de l’agent. Pour plus d’informations sur un groupe Compte de service géré, voir [Groupe Comptes de service géré](how-to-prerequisites.md#group-managed-service-accounts).


### <a name="upgrade-an-existing-agent-to-use-the-gmsa"></a>Mettre à niveau un agent existant pour utiliser gMSA
Pour mettre à niveau un agent existant afin d’utiliser le compte de service géré de groupe créé pendant l’installation, mettez à jour le service de l’agent vers la dernière version en exécutant AADConnectProvisioningAgent.msi. À présent, exécutez à nouveau l’assistant d’installation et fournissez les informations d’identification pour créer le compte lorsque vous y êtes invité.

## <a name="install-the-agent"></a>Installer l’agent

Pour installer l’agent :

 1. Connectez-vous au serveur que vous allez utiliser à l’aide des autorisations d’administrateur d’entreprise.
 1. Connectez-vous au portail Azure, puis accédez à **Azure Active Directory**.
 1. Dans le menu de gauche, sélectionnez **Azure AD Connect**.
 1. Sélectionnez **Gérer la synchronisation cloud** > **Passer en revue tous les agents**.
 1. Téléchargez l’agent de provisionnement Azure AD Connect à partir du portail Azure.
 
    ![Capture d’écran montrant le téléchargement de l’agent local.](media/how-to-install/install-9.png)</br>
 1. Acceptez les conditions générales et sélectionnez **Télécharger**.
 1. Exécutez le programme d’installation du provisionnement Azure AD Connect AADConnectProvisioningAgentSetup.msi.
 1. Dans l’écran **Package Agent d’approvisionnement Microsoft Azure AD Connect**, acceptez les termes du contrat de licence et sélectionnez **Installer**.
 
    ![Capture de l’écran Microsoft Azure AD Connect Provisioning Agent Package.](media/how-to-install/install-1.png)</br>
 1. Une fois cette opération terminée, l’Assistant Configuration démarre. Connectez-vous avec votre compte d’administrateur général Azure AD.
 1. Dans l’écran **Configurer le compte de service**, sélectionnez **Créer gMSA** ou **Utiliser un gMSA personnalisé**. Si vous autorisez l’agent à créer le compte, il se nommera provAgentgMSA$. Si vous choisissez **Utiliser un gMSA personnalisé**, vous serez invité à spécifier ce compte.
 1. Entrez les informations d’identification de l’administrateur du domaine pour créer le compte de service géré de groupe qui sera utilisé pour exécuter le service Agent. Sélectionnez **Suivant**.
  
    ![Capture d’écran montrant l’option Créer gMSA.](media/how-to-install/install-12.png)</br>
 1. Dans l’écran **Connecter Active Directory**, sélectionnez **Ajouter un répertoire**. Ensuite, connectez-vous avec votre compte Administrateur Active Directory. Cette opération ajoute votre répertoire local. 
 1. Vous pouvez également gérer la préférence des contrôleurs de domaine que l’agent utilisera en cochant la case **Sélectionner la priorité du contrôleur de domaine** et en classant la liste des contrôleurs de domaine. Sélectionnez **OK**.
 
    ![Capture d’écran montrant le classement des contrôleurs de domaine.](media/how-to-install/install-2a.png)</br>
 1. Sélectionnez **Suivant**.
 
    ![Capture de l’écran « Connexion à Active Directory ».](media/how-to-install/install-3a.png)</br>
 1. Dans l’écran **Installation de l’agent**, confirmez les paramètres et le compte qui sera créé, puis sélectionnez **Confirmer**.
 
    ![Capture d’écran de l’option de confirmation des paramètres.](media/how-to-install/install-11.png)</br>
 1. Une fois cette opération terminée, vous devriez voir le message **Your agent installation is complete** (l’installation de votre agent est terminée). Sélectionnez **Quitter**.
 
    ![Capture de l’écran « Configuration terminée ».](media/how-to-install/install-4a.png)</br>
 1. Si vous l’écran initial **Package Agent d’approvisionnement Microsoft Azure AD Connect** est toujours affiché, sélectionnez **Fermer**.

## <a name="verify-agent-installation"></a>Vérifier l’installation de l’agent
La vérification de l’agent se produit dans le portail Azure et sur le serveur local qui exécute l’agent.

### <a name="azure-portal-agent-verification"></a>Vérification de l’agent dans le portail Azure
Pour vérifier que l’agent est visible par Azure :

 1. Connectez-vous au portail Azure.
 1. À gauche, sélectionnez **Azure Active Directory** > **Azure AD Connect** Au centre, sélectionnez **Gérer la synchronisation cloud**.

    ![Capture d’écran montrant le portail Azure](media/how-to-install/install-6.png)</br>

 1. Sur l’écran **Synchronisation cloud Azure AD Connect**, sélectionnez **Passer en revue tous les agents**.

    ![Capture d’écran qui montre l’option Passer en revue tous les agents.](media/how-to-install/install-7.png)</br>
 
 1. L’écran **Agents de provisionnement locaux** affiche les agents que vous avez installés. Vérifiez que l’agent en question est présent et qu’il est marqué comme étant *actif*.

    ![Capture d’écran montrant l’écran Agents de provisionnement locaux.](media/how-to-install/verify-1.png)</br>

### <a name="on-the-local-server"></a>Sur le serveur local
Pour vérifier que l’agent est en cours d’exécution :

1. Connectez-vous au serveur avec un compte Administrateur.
1. Ouvrez **Services** en y accédant ou en sélectionnant **Démarrer** > **Exécuter** > **Services.msc**.
1. Sous **Services**, assurez-vous que le **Programme de mise à jour de l’agent Microsoft Azure AD Connect** et l’**agent d’approvisionnement Microsoft Azure AD Connect** sont présents, et que leur état est *En cours d’exécution*.

    ![Capture d’écran montrant l’écran Services.](media/how-to-install/troubleshoot-1.png)

>[!IMPORTANT]
>L’agent a été installé mais il faut le configurer et l’activer pour qu’il commence à synchroniser les utilisateurs. Pour configurer un nouvel agent, consultez [Création d’une configuration pour la synchronisation cloud Azure AD Connect](how-to-configure.md).

### <a name="enable-password-writeback-in-azure-ad-connect-cloud-sync"></a>Activer la réécriture du mot de passe dans la synchronisation cloud Azure AD Connect 

Pour utiliser la réécriture du mot de passe et activer le service SSPR pour détecter l’agent de synchronisation cloud, vous devez utiliser la cmdlet `Set-AADCloudSyncPasswordWritebackConfiguration` et les informations d’identification d’administrateur général du locataire : 

  ```   
   Import-Module "C:\\Program Files\\Microsoft Azure AD Connect Provisioning Agent\\Microsoft.CloudSync.Powershell.dll" 
   Set-AADCloudSyncPasswordWritebackConfiguration -Enable $true -Credential $(Get-Credential)
  ```

Pour plus d’informations sur l’utilisation de la réécriture du mot de passe avec la synchronisation cloud Azure AD Connect, consultez :


## <a name="next-steps"></a>Étapes suivantes 

- [Présentation du provisionnement](what-is-provisioning.md)
- [Qu’est-ce que la synchronisation cloud Azure AD Connect ?](what-is-cloud-sync.md)
- [Création d’une configuration pour la synchronisation cloud Azure AD Connect](how-to-configure.md)

