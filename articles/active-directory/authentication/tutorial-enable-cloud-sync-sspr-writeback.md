---
title: Activer la réécriture du mot de passe de la synchronisation cloud Azure Active Directory Connect
description: Dans ce tutoriel, vous apprenez à activer la réécriture et la réinitialisation du mot de passe en libre-service Azure AD au moyen de la synchronisation cloud Azure AD Connect pour synchroniser les modifications dans un environnement Active Directory Domain Services local.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 10/25/2021
ms.author: justinha
author: justinha
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4, ignite-fall-2021
ms.openlocfilehash: 17bc0c700aee1a351371f35e7c84e95312214088
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550303"
---
# <a name="tutorial-enable-cloud-sync-self-service-password-reset-writeback-to-an-on-premises-environment-preview"></a>Tutoriel : Activer la réécriture et la réinitialisation de mot de passe en libre-service Azure de la synchronisation cloud dans un environnement local (préversion)

La synchronisation cloud Azure Active Directory Connect peut synchroniser les modifications de mot de passe Azure AD en temps réel entre les utilisateurs dans les domaines Active Directory Domain Services (AD DS) locaux déconnectés. La préversion publique de la synchronisation cloud Azure AD Connect peut s’exécuter côte à côte avec [Azure Active Directory Connect](tutorial-enable-sspr-writeback.md) au niveau du domaine pour simplifier la réécriture du mot de passe pour d’autres scénarios, tels que les utilisateurs qui se trouvent dans des domaines déconnectés en raison d’une division ou d’une fusion de l’entreprise. Vous pouvez configurer chaque service dans différents domaines pour cibler différents groupes d’utilisateurs en fonction de leurs besoins. La synchronisation cloud Azure Active Directory Connect utilise l’agent de provisionnement cloud Azure AD léger pour simplifier la configuration de la réécriture de réinitialisation de mot de passe en libre-service et offrir une méthode sécurisée pour envoyer les modifications de mot de passe dans le cloud à un annuaire local. 

La réécriture de réinitialisation de mot de passe en libre-service de la synchronisation cloud Azure Active Directory Connect est prise en charge dans le cadre d’une préversion publique. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis 

- Un locataire Azure AD avec au moins un abonnement Azure AD Premium P1 ou une licence d’évaluation activée. Si nécessaire, [créez-en un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Un compte avec l’un des éléments suivants :
  - Rôles [Administrateur de stratégie d’authentification](../roles/permissions-reference.md#authentication-policy-administrator) et [Administrateur d'identités hybride](../roles/permissions-reference.md#hybrid-identity-administrator)
  - Rôle [Administrateur général](../roles/permissions-reference.md#global-administrator) 
- Azure AD configuré pour la réinitialisation du mot de passe en libre-service. Si nécessaire, suivez le tutoriel pour activer la réinitialisation de mot de passe en libre-service Azure AD. 
- Un environnement AD DS local configuré avec la synchronisation cloud Azure AD Connect version 1.1.587 ou ultérieure. Si nécessaire, configurez la synchronisation cloud Azure AD Connect à l’aide de [ce tutoriel](tutorial-enable-sspr.md). 
- L’activation de la réécriture du mot de passe dans la synchronisation cloud Azure AD Connect nécessite l’exécution de scripts PowerShell signés.
  - Assurez-vous que la stratégie d’exécution de PowerShell autorise l’exécution de scripts. 
  - La stratégie d’exécution recommandée lors de l’installation est « RemoteSigned ». 
  - Pour plus d’informations sur la définition de la stratégie d’exécution de PowerShell, consultez [Set-ExecutionPolicy](/powershell/module/microsoft.powershell.security/set-executionpolicy). 


## <a name="deployment-steps"></a>Étapes du déploiement

1. [Configurer des autorisations de compte de service pour la synchronisation cloud Azure AD Connect](#configure-azure-ad-connect-cloud-sync-service-account-permissions)
1. [Activer la réécriture du mot de passe dans la synchronisation cloud Azure AD Connect](#enable-password-writeback-in-azure-ad-connect-cloud-sync)
1. [Activer la réécriture du mot de passe pour SSPR](#enable-password-writeback-for-sspr)
 
### <a name="configure-azure-ad-connect-cloud-sync-service-account-permissions"></a>Configurer des autorisations de compte de service pour la synchronisation cloud Azure AD Connect 

Les autorisations pour la synchronisation cloud sont configurées par défaut. Si les autorisations doivent être réinitialisées, consultez la section sur la [résolution des problèmes](#troubleshooting) pour plus d’informations sur les autorisations spécifiques requises pour la réécriture du mot de passe et leur définition à l’aide de PowerShell. 

### <a name="enable-password-writeback-in-azure-ad-connect-cloud-sync"></a>Activer la réécriture du mot de passe dans la synchronisation cloud Azure AD Connect

Pour la préversion publique, vous devez activer la réécriture du mot de passe dans la synchronisation cloud Azure AD Connect à l’aide de l’applet de commande Set-AADCloudSyncPasswordWritebackConfiguration et des informations d’identification d’administrateur général du locataire : 

```powershell
Import-Module 'C:\\Program Files\\Microsoft Azure AD Connect Provisioning Agent\\Microsoft.CloudSync.Powershell.dll' 
Set-AADCloudSyncPasswordWritebackConfiguration -Enable $true -Credential $(Get-Credential)
``` 

### <a name="enable-password-writeback-for-sspr"></a>Activer la réécriture du mot de passe pour SSPR 

Avec la réécriture du mot de passe activée dans la synchronisation cloud Azure AD Connect, vérifiez et configurez la réinitialisation de mot de passe en libre-service Azure AD pour la réécriture du mot de passe. Lorsque vous autorisez l’utilisation par SSPR de la réécriture du mot de passe, les utilisateurs qui changent ou réinitialisent leur mot de passe bénéficient également de la synchronisation du mot de passe mis à jour dans l’environnement AD DS local. 

Pour vérifier et activer la réécriture du mot de passe dans la réinitialisation de mot de passe en libre-service, procédez comme suit : 

1. Connectez-vous au portail Azure à l’aide d’un compte d’administrateur général. 
1. Accédez à Azure Active Directory, cliquez sur **Réinitialisation de mot de passe**, puis choisissez **Intégration locale**. 
1. Vérifiez que la configuration de l’agent de synchronisation cloud Azure AD Connect est terminée.
1. Définissez **Réécriture du mot de passe dans votre répertoire local ?** sur **Oui**. 
1. Définissez **Autoriser les utilisateurs à déverrouiller les comptes sans réinitialiser leur mot de passe ?** sur **Oui**.
   
   ![Capture d’écran montrant comment activer la réécriture de mot de passe.](media/tutorial-enable-sspr-cloud-sync-writeback/writeback.png)

1. Quand vous êtes prêt, sélectionnez **Enregistrer**. 

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous décidez de ne plus utiliser la fonctionnalité de réécriture de mot de passe SSPR que vous avez configurée dans ce document, effectuez les étapes suivantes : 

1. Connectez-vous au portail Azure à l’aide d’un compte d’administrateur général. 
1. Recherchez et sélectionnez Azure Active Directory, puis sélectionnez **Réinitialisation de mot de passe** et choisissez **Intégration locale**. 
1. Définissez **Réécriture du mot de passe dans votre répertoire local ?** sur **Non**. 
1. Définissez **Autoriser les utilisateurs à déverrouiller les comptes sans réinitialiser leur mot de passe ?** sur **Non**. 

Depuis votre serveur de synchronisation cloud Azure AD Connect, exécutez `Set-AADCloudSyncPasswordWritebackConfiguration` à l’aide des informations d’identification d’administrateur général pour désactiver la réécriture du mot de passe avec la synchronisation cloud Azure AD Connect. 

```powershell
Import-Module ‘C:\\Program Files\\Microsoft Azure AD Connect Provisioning Agent\\Microsoft.CloudSync.Powershell.dll’ 
Set-AADCloudSyncPasswordWritebackConfiguration -Enable $false -Credential $(Get-Credential)
```

## <a name="supported-operations"></a>Opérations prises en charge

Les mots de passe sont réécrits dans les situations suivantes pour les utilisateurs finaux et les administrateurs.


| Compte        | Opérations prises en charge | 
|----------------|------------------------|
| Utilisateurs finaux      |  Tout changement volontaire de mot de passe en libre-service de l’utilisateur final<br>Tout changement forcé de mot de passe en libre-service de l’utilisateur final (par exemple, expiration du mot de passe)<br>Toute réinitialisation de mot de passe en libre-service de l’utilisateur final émanant du portail de réinitialisation de mot de passe |
| Administrateurs |  Tout changement volontaire de mot de passe en libre-service de l’administrateur<br>Tout changement forcé de mot de passe en libre-service de l’administrateur (par exemple, expiration du mot de passe)<br>Toute réinitialisation de mot de passe en libre-service de l’administrateur émanant du portail de réinitialisation de mot de passe<br> Toute réinitialisation de mot de passe de l’utilisateur final lancée par l’administrateur depuis le portail Azure<br>Toute réinitialisation de mot de passe de l’utilisateur final lancée par l’administrateur à partir de l’API Microsoft Graph.                       |

## <a name="unsupported-operations"></a>Opérations non prises en charge

Les mots de passe ne sont pas réécrits dans les situations suivantes.

| Compte        | Opérations non prises en charge | 
|----------------|------------------------|
| Utilisateurs finaux      | Toute réinitialisation de mot de passe de l’utilisateur final lancée par ce dernier à l’aide de cmdlets PowerShell ou l’API Microsoft Graph.                        |
| Administrateurs | Toute réinitialisation de mot de passe utilisateur final initiée par l’administrateur à l’aide des cmdlets PowerShell.<br>Toute réinitialisation de mot de passe de l’utilisateur final lancée par l’administrateur depuis le Centre d’administration Microsoft 365<br>Un administrateur ne peut pas utiliser l’outil de réinitialisation du mot de passe pour réinitialiser son propre mot de passe ou celui d’un autre administrateur dans Azure AD pour la réécriture du mot de passe.                        |

## <a name="validation-scenarios"></a>Scénarios de validation

Essayez les opérations suivantes pour valider des scénarios à l’aide de la réécriture du mot de passe. Tous les scénarios de validation nécessitent la synchronisation cloud et l’utilisateur est dans la portée de la réécriture du mot de passe.  


|Scénario|Détails |
|--------|--------|
| Réinitialiser le mot de passe à partir de la page de connexion | Avoir deux utilisateurs de forêts et domaines déconnectés qui exécutent la réinitialisation de mot de passe en libre-service. Vous pouvez également avoir Azure AD Connect et la synchronisation cloud déployés côte à côte et avoir un utilisateur dans la portée de la configuration de la synchronisation cloud et un autre dans la portée d’Azure AD Connect et demander à ces utilisateurs de réinitialiser leur mot de passe. |
| Forcer la modification du mot de passe expiré | Avoir deux utilisateurs de forêts et de domaines déconnectés qui changent les mots de passe expirés. Vous pouvez également avoir Azure AD Connect et la synchronisation cloud déployés côte à côte et avoir un utilisateur dans la portée de la configuration de la synchronisation cloud et un autre dans la portée d’Azure AD Connect. |
| Modification normale du mot de passe | Avoir deux utilisateurs de forêts et domaines déconnectés qui exécutent un changement de mot de passe de routine. Vous pouvez également avoir Azure AD Connect et la synchronisation cloud côte à côte et avoir un utilisateur dans la portée de la configuration de la synchronisation cloud et un autre dans la portée d’Azure AD Connect.  |
| L’administrateur réinitialise le mot de passe de l’utilisateur | Avoir deux utilisateurs de forêts et domaines déconnectés qui réinitialisent leur mot de passe à partir du portail d’administration Azure ou du portail pour les travailleurs de première ligne. Vous pouvez également avoir Azure AD Connect et la synchronisation cloud côte à côte et avoir un utilisateur dans la portée de la configuration de la synchronisation cloud et un autre dans la portée d’Azure AD Connect.  |
| Déverrouillage de compte en libre-service | Avoir deux utilisateurs de forêts et domaines déconnectés qui déverrouillent des comptes dans le portail SSPR réinitialisant le mot de passe. Vous pouvez également avoir Azure AD Connect et la synchronisation cloud côte à côte et avoir un utilisateur dans la portée de la configuration de la synchronisation cloud et un autre dans la portée d’Azure AD Connect. |

## <a name="troubleshooting"></a>Dépannage

Le compte de service administré du groupe de synchronisation cloud Azure AD Connect doit avoir les autorisations suivantes définies pour réécrire les mots de passe par défaut : 

- Réinitialiser le mot de passe
- Autorisations d’écriture sur lockoutTime
- Autorisations d’écriture sur pwdLastSet
- Droits étendus pour « Ne pas faire expirer le mot de passe » sur l’objet racine de chaque domaine de cette forêt, s’il n’est pas déjà défini. 

Si ces autorisations ne sont pas définies, vous pouvez définir l’autorisation PasswordWriteBack sur le compte de service à l’aide de l’applet de commande Set-AADCloudSyncPermissions et des informations d’identification de l’administrateur d’entreprise local : 

```powershell
Import-Module ‘C:\\Program Files\\Microsoft Azure AD Connect Provisioning Agent\\Microsoft.CloudSync.Powershell.dll’ 
Set-AADCloudSyncPermissions -PermissionType PasswordWriteBack -EACredential $(Get-Credential)
```

Lorsque vous mettez à jour des autorisations, la réplication de ces autorisations pour tous les objets de l’annuaire peut durer une heure ou plus. 

Si vous n’attribuez pas ces autorisations, la réécriture peut sembler être configurée correctement, mais les utilisateurs peuvent rencontrer des erreurs quand ils mettent à jour leurs mots de passe locaux à partir du cloud. Les autorisations doivent être appliquées à Cet objet et tous ceux descendants pour que l’option « Ne pas faire expirer le mot de passe » soit proposée. 

Si les mots de passe de certains comptes d’utilisateur ne sont pas réécrits dans l’annuaire local, vérifiez que l’héritage n’est pas désactivé pour le compte dans l’environnement local AD DS. Les autorisations d’écriture pour les mots de passe doivent être appliquées aux objets descendants pour que la fonctionnalité fonctionne correctement. 

Les stratégies de mot de passe dans l’environnement AD DS local peuvent empêcher le traitement correct des réinitialisations de mot de passe. Si vous testez cette fonctionnalité et souhaitez réinitialiser le mot de passe pour les utilisateurs plusieurs fois par jour, la stratégie de groupe pour Durée de vie minimale du mot de passe doit être définie sur 0. Ce paramètre se trouve sous Configuration ordinateur > Stratégies > Paramètres Windows > Paramètres de sécurité > Stratégies de compte dans gpmc.msc. 

Si vous mettez à jour la stratégie de groupe, attendez la réplication de la stratégie mise à jour ou utilisez la commande gpupdate /force. 

Pour que les mots de passe soient changés immédiatement, l’âge minimum du mot de passe doit être défini sur 0. Toutefois, si les utilisateurs adhèrent aux stratégies locales et que le paramètre Durée de vie minimale du mot de passe est défini sur une valeur supérieure à zéro, la réécriture du mot de passe ne fonctionne pas une fois les stratégies locales évaluées. 

Pour plus d’informations sur la façon de valider ou de configurer les autorisations appropriées, consultez [Configurer les autorisations de compte pour Azure AD Connect](tutorial-enable-sspr-writeback.md#configure-account-permissions-for-azure-ad-connect). 

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la synchronisation cloud et pour obtenir une comparaison entre Azure AD Connect et la synchronisation cloud, consultez [Qu’est-ce que la synchronisation cloud Azure AD Connect ?](../cloud-sync/what-is-cloud-sync.md).
- Pour obtenir un tutoriel sur la configuration de la réécriture du mot de passe à l’aide d’Azure AD Connect, consultez [Tutoriel : Activer la réécriture de réinitialisation de mot de passe en libre-service dans un environnement local](tutorial-enable-sspr-writeback.md).
