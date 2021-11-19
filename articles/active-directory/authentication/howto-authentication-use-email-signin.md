---
title: Se connecter à Azure AD avec un e-mail comme autre ID de connexion
description: Découvrez comment permettre aux utilisateurs de se connecter à Azure Active Directory avec leur e-mail comme autre ID de connexion
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/07/2021
ms.author: justinha
author: calui
manager: daveba
ms.reviewer: calui
ms.openlocfilehash: e7b34e98776f252c2122d58601c8067df208b2f2
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486747"
---
# <a name="sign-in-to-azure-ad-with-email-as-an-alternate-login-id-preview"></a>Se connecter à Azure AD avec un e-mail comme autre ID de connexion (préversion)

> [!NOTE]
> La connexion à Azure AD avec un e-mail comme autre ID de connexion est une fonctionnalité d’Azure Active Directory disponible en préversion publique. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

De nombreuses organisations souhaitent permettre aux utilisateurs de se connecter à Azure Active Directory (Azure AD) en utilisant les informations d’identification de leur environnement d’annuaire local. En vertu de cette approche, dite d’authentification hybride, les utilisateurs ne doivent se souvenir que d’un seul ensemble d’informations d’identification.

Certaines organisations n’ont pas adopté l’authentification hybride pour les raisons suivantes :

* Par défaut, le nom d’utilisateur principal (UPN) Azure AD a la même valeur que l’UPN local.
* Le changement de l’UPN Azure AD provoque une discordance entre les environnements local et Azure AD, ce qui peut entraîner des problèmes avec certains services et applications.
* Pour des raisons commerciales ou de conformité, l’organisation ne souhaite pas utiliser l’UPN local pour se connecter à Azure AD.

Pour faciliter l’adoption de l’authentification hybride, vous pouvez configurer Azure AD de façon à permettre aux utilisateurs de se connecter en utilisant leur e-mail comme autre ID de connexion. Par exemple, si *Contoso* a été rebaptisé *Fabrikam*, au lieu de continuer à se connecter avec l’UPN `ana@contoso.com` hérité, il est possible d’utiliser une adresse e-mail comme autre ID de connexion. Pour accéder à une application ou un service, les utilisateurs se connectent à Azure AD avec leur e-mail non-UPN (par exemple, `ana@fabrikam.com`).

![E-mail utilisé comme autre ID de connexion.](media/howto-authentication-use-email-signin/email-alternate-login-id.png)

Cet article explique comment activer et utiliser une adresse e-mail comme ID de connexion de substitution.

## <a name="before-you-begin"></a>Avant de commencer

Voici ce que vous devez savoir sur l’utilisation d’un e-mail comme autre ID de connexion :

* La fonctionnalité est disponible dans l’édition Azure Active Directory Free et éditions supérieures.
* La fonctionnalité permet aux utilisateurs Azure AD authentifiés dans le cloud de se connecter à l’aide de l’attribut *ProxyAddresses*, en plus du nom d’utilisateur principal (UPN). Pour en savoir plus sur la façon dont cela s’applique aux scénarios Azure AD B2B dans la section [B2B](#b2b-guest-user-sign-in-with-an-email-address).
* Lorsqu’un utilisateur se connecte avec un e-mail non UPN, les revendications `unique_name` et `preferred_username` (le cas échéant) dans le [jeton d’ID](../develop/id-tokens.md) renvoient l’e-mail non UPN.
* La fonctionnalité prend en charge l’authentification managée avec la synchronisation de la synthèse du mot de passe (PHS) ou l’authentification directe (PTA).
* Il y a deux options possibles pour configurer la fonctionnalité :
    * [Stratégie de découverte du domaine d’accueil (HRD)](#enable-user-sign-in-with-an-email-address) : utilisez cette option pour activer la fonctionnalité sur l’ensemble du locataire. Des privilèges d’administrateur général sont requis.
    * [Stratégie de déploiement par étapes](#enable-staged-rollout-to-test-user-sign-in-with-an-email-address) : utilisez cette option pour tester la fonctionnalité avec des groupes Azure AD spécifiques. Des privilèges d’administrateur général sont requis.

## <a name="preview-limitations"></a>Limitations de la version préliminaire

Dans la préversion actuelle, les limitations suivantes s’appliquent à l’utilisation d’un e-mail comme autre ID de connexion :

* **Expérience utilisateur** : les utilisateurs peuvent voir leur UPN, même quand ils sont connectés avec leur e-mail non-UPN. L’exemple de comportement suivant peut être observé :
    * L’utilisateur est invité à se connecter avec l’UPN lorsqu’il est dirigé vers la connexion Azure AD avec `login_hint=<non-UPN email>`.
    * Quand un utilisateur se connecte avec un e-mail non-UPN et qu’il entre un mot de passe incorrect, la page *Entrez votre mot de passe* change pour afficher l’UPN.
    * Sur certains sites et applications Microsoft, tels que Microsoft Office, le contrôle du *gestionnaire de comptes*, qui apparaît généralement en haut à droite, peut afficher l’UPN de l’utilisateur au lieu de l’e-mail non-UPN utilisé pour la connexion.

* **Flux non pris en charge** : certains flux ne sont actuellement pas compatibles avec les e-mails non-UPN, notamment :
    * Identity Protection ne fait pas correspondre les e-mails non-UPN avec la détection du risque *Informations d’identification fuitées*. Cette détection de risque utilise l’UPN pour faire correspondre les informations d’identification qui ont été divulguées. Pour plus d’informations, consultez [Détection et correction des riques Azure AD Identity Protection][identity-protection].
    * Quand un utilisateur s’est connecté avec un e-mail non-UPN, il ne peut pas changer son mot de passe. La réinitialisation de mot de passe en libre-service (SSPR) fonctionne en principe comme prévu. Pendant le processus de réinitialisation de mot de passe en libre-service (SSPR), l’utilisateur peut voir son UPN s’il vérifie son identité avec un e-mail non UPN.

* **Scénarios non pris en charge** : les scénarios suivants ne sont pas pris en charge. Connexion avec un e-mail non UPN à :
    * [Appareils joints Azure AD hybrides](../devices/concept-azure-ad-join-hybrid.md)
    * [Appareils joints Azure AD](../devices/concept-azure-ad-join.md)
    * [Appareils inscrits sur Azure AD](../devices/concept-azure-ad-register.md)
    * [Applications à l’aide des informations d’identification de mot de passe du propriétaire de la ressource (ROPC)](../develop/v2-oauth-ropc.md)
    * Applications à l’aide de l’authentification héritée telle que POP3 et SMTP
    * Skype Entreprise
    * Microsoft Office sur macOS
    * Portail d’administration Microsoft 365

* **Applications non prises en charge** : certaines applications tierces peuvent ne pas fonctionner comme prévu si elles partent du principe que les revendications `unique_name` ou `preferred_username` sont immuables ou correspondent toujours à un attribut utilisateur spécifique, tel que UPN.

* **Enregistrement** : les modifications apportées à la configuration de la fonctionnalité dans la stratégie de découverte du domaine d’accueil ne sont pas explicitement indiquées dans les journaux d’audit. En outre, le champ *Type d’identificateur de connexion* dans les journaux de connexion peut ne pas être toujours précis et ne doit pas être utilisé pour déterminer si la fonctionnalité a été utilisée pour la connexion.

* **Stratégie de déploiement par étapes** : les limitations suivantes s’appliquent uniquement lorsque la fonctionnalité est activée à l’aide de la stratégie de déploiement par étapes :
    * La fonctionnalité ne fonctionne pas comme prévu pour les utilisateurs qui sont inclus dans plusieurs stratégies de déploiement par étapes.
    * La stratégie de déploiement par étapes prend en charge un maximum de 10 groupes par fonctionnalité.
    * La stratégie de déploiement par étapes ne prend pas en charge les groupes imbriqués.
    * La stratégie de déploiement par étapes ne prend pas en charge les groupes dynamiques.
    * Le contact d’objets à l’intérieur du groupe bloque l’ajout du groupe à une stratégie de déploiement par étapes.

* **Valeurs dupliquées** : au sein d’un abonné, un UPN d’utilisateur du cloud uniquement peut avoir la même valeur que l’adresse proxy d’un autre utilisateur, synchronisée à partir du répertoire local. Dans ce scénario, si la fonctionnalité est activée, l’utilisateur du cloud uniquement ne pourra pas se connecter au moyen de son UPN. Pour plus d’informations sur ce problème, consultez la section [Dépanner](#troubleshoot).

## <a name="overview-of-alternate-login-id-options"></a>Vue d’ensemble des options pour l’ID de connexion alternatif
Pour se connecter à Azure AD, les utilisateurs entrent une valeur qui identifie leur compte de manière unique. Historiquement, vous pouviez uniquement utiliser l’UPN Azure AD en tant qu’identifiant de connexion.

Pour les organisations dont l’UPN local correspond à l’adresse e-mail de connexion préférée de l’utilisateur, cette approche est intéressante. Ces organisations définissent l’UPN Azure AD sur la même valeur que l’UPN local, et les utilisateurs bénéficient d’une expérience de connexion cohérente.

### <a name="alternate-login-id-for-ad-fs"></a>ID de connexion alternatif pour AD FS

Cela étant, dans certaines organisations, l’UPN local n’est pas utilisé comme identifiant de connexion. Dans les environnements locaux, vous configurez l’instance AD DS locale de façon à autoriser la connexion avec un ID de connexion alternatif. Définir l’UPN Azure AD sur la même valeur que l’UPN local n’est pas possible, car le cas échéant, Azure AD imposerait aux utilisateurs de se connecter avec cette valeur.

### <a name="alternate-login-id-in-azure-ad-connect"></a>ID de connexion alternatif dans Azure AD Connect

La solution de contournement à ce problème consiste à définir l’UPN Azure AD sur l’adresse e-mail que l’utilisateur envisage d’utiliser pour se connecter. Cette approche fonctionne, mais elle produit des UPN différents entre AD local et Azure AD. Qui plus est, cette configuration n’est pas compatible avec toutes les charges de travail Microsoft 365.

### <a name="email-as-an-alternate-login-id"></a>E-mail utilisé comme autre ID de connexion

Une autre approche consiste à synchroniser les UPN Azure AD et locaux avec la même valeur, puis à configurer Azure AD pour permettre aux utilisateurs de se connecter à Azure AD à l’aide d’une adresse e-mail vérifiée. Pour ce faire, vous définissez une ou plusieurs adresses e-mail dans l’attribut *ProxyAddresses* de l’utilisateur dans le répertoire local. Les valeurs *ProxyAddresses* sont ensuite synchronisées automatiquement avec Azure AD à l’aide d’Azure AD Connect.


| Option | Description |
|---|---|
| [ID de connexion alternatif pour AD FS](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) | Activez la connexion avec un attribut alternatif (par exemple, l’e-mail) pour les utilisateurs d’AD FS. |
| [ID de connexion alternatif dans Azure AD Connect](../hybrid/plan-connect-userprincipalname.md#alternate-login-id) | Synchronisez un attribut alternatif (par exemple, l’e-mail) en tant qu’UPN Azure AD. |
| E-mail utilisé comme autre ID de connexion | Activez la connexion avec l’attribut *ProxyAddresses* du domaine vérifié pour les utilisateurs d’Azure AD. |

## <a name="synchronize-sign-in-email-addresses-to-azure-ad"></a>Synchroniser les adresses e-mail de connexion sur Azure AD

L’authentification traditionnelle d’Active Directory Domain Services (AD DS) ou des services de fédération Active Directory (AD FS) s’effectue directement sur votre réseau et est gérée par votre infrastructure AD DS. Avec une authentification hybride, les utilisateurs peuvent se connecter directement à Azure AD.

Pour prendre en charge cette approche d’authentification hybride, vous synchronisez votre environnement AD DS local sur Azure AD à l’aide d’[Azure AD Connect][azure-ad-connect] et le configurez pour utiliser PHS ou PTA. Pour plus d’informations, consultez [Choisir la méthode d’authentification adaptée à votre solution d’identité hybride Azure AD][hybrid-auth-methods].

Dans les deux options de configuration, l’utilisateur envoie son nom d’utilisateur et son mot de passe à Azure AD qui valide les informations d’identification et émet un ticket. Lorsque les utilisateurs se connectent à Azure AD, cela évite à votre organisation de devoir héberger et gérer une infrastructure AD FS.

L’un des attributs utilisateur automatiquement synchronisés par Azure AD Connect est *ProxyAddresses*. Si un utilisateur a une adresse électronique définie dans l’environnement AD DS local comme faisant partie de l’attribut *ProxyAddresses*, cette adresse est automatiquement synchronisée avec Azure AD. Cette adresse e-mail peut ensuite être utilisée directement dans le processus de connexion Azure AD en tant qu’ID de connexion alternatif.

> [!IMPORTANT]
> Seuls les adresses e-mail de domaines vérifiés pour le locataire sont synchronisées sur Azure AD. Chaque locataire Azure AD dispose d’un ou plusieurs domaines vérifiés dont vous avez prouvé la propriété, et qui sont liés de manière unique à votre locataire.
>
> Pour plus d’informations, consultez [Ajouter et vérifier un nom de domaine personnalisé dans Azure AD][verify-domain].

## <a name="b2b-guest-user-sign-in-with-an-email-address"></a>Connexion B2B des utilisateurs invités avec une adresse e-mail

![Diagramme des adresses e-mail utilisées comme ID de connexion de substitution pour la connexion B2B des utilisateurs invités.](media/howto-authentication-use-email-signin/email-alternate-login-id-b2b.png)

L’utilisation d’une adresse e-mail comme ID de connexion secondaire s’applique à la [collaboration B2B (business-to-business) Azure AD](../external-identities/what-is-b2b.md) selon le modèle « Apportez vos propres identifiants de connexion ». Lorsque l’utilisation d’une adresse e-mail comme ID de connexion secondaire est activée dans le locataire principal, les utilisateurs Azure AD peuvent effectuer des connexions d’invité avec une adresse e-mail non UPN sur le point de terminaison avec locataire de la ressource. Aucune action n’est requise du locataire de ressources pour activer cette fonctionnalité.

## <a name="enable-user-sign-in-with-an-email-address"></a>Permettre à un utilisateur de se connecter avec une adresse e-mail

> [!NOTE]
> Cette option de configuration utilise la stratégie de découverte du domaine d’accueil (HRD). Pour plus d’informations, consultez [Type de ressource homeRealmDiscoveryPolicy](/graph/api/resources/homeRealmDiscoveryPolicy?view=graph-rest-1.0&preserve-view=true).

Une fois que les utilisateurs auxquels l’attribut *ProxyAddresses* est appliqué sont synchronisés avec Azure AD à l’aide d’Azure AD Connect, vous devez activer la fonctionnalité pour que les utilisateurs se connectent avec leur adresse e-mail en guise d’ID de connexion alternatif pour votre locataire. Cette fonctionnalité indique aux serveurs de connexion Azure AD de vérifier l’identifiant de connexion non seulement par rapport aux valeurs d’UPN, mais aussi par rapport aux valeurs de *ProxyAddresses* pour l’adresse e-mail.

Actuellement, pendant la période de préversion, vous pouvez activer la fonctionnalité de connexion avec une adresse e-mail en guise d’ID de connexion alternatif uniquement en utilisant PowerShell. Vous devez avoir des autorisations d’*administrateur général* pour effectuer les étapes suivantes :

1. Ouvrez une session PowerShell en tant qu’administrateur, puis installez le module *AzureADPreview* à l’aide de la cmdlet [Install-Module][Install-Module] :

    ```powershell
    Install-Module AzureADPreview
    ```

    Si vous y êtes invité, sélectionnez **Y** pour installer NuGet ou pour installer à partir d’un dépôt non approuvé.

1. Connectez-vous à votre locataire Azure AD en tant qu’*administrateur général* à l’aide de l’applet de commande [Connect-AzureAD][Connect-AzureAD] :

    ```powershell
    Connect-AzureAD
    ```

    La commande retourne des informations sur votre compte, votre environnement et votre ID de locataire.

1. Vérifiez si *HomeRealmDiscoveryPolicy* existe déjà dans votre locataire en utilisant l’applet de commande [Get-AzureADPolicy][Get-AzureADPolicy] comme suit :

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

1. Si aucune stratégie n’est actuellement configurée, la commande ne retourne rien. Si une stratégie est retournée, ignorez cette étape et passez à l’étape suivante pour mettre à jour une stratégie existante.

    Pour ajouter la stratégie *HomeRealmDiscoveryPolicy* au locataire, utilisez la cmdlet [New-AzureADPolicy][New-AzureADPolicy] et définissez l’attribut *AlternateIdLogin* sur *"Enabled": true* comme dans l’exemple suivant :

    ```powershell
    $AzureADPolicyDefinition = @(
      @{
         "HomeRealmDiscoveryPolicy" = @{
            "AlternateIdLogin" = @{
               "Enabled" = $true
            }
         }
      } | ConvertTo-JSON -Compress
    )
    $AzureADPolicyParameters = @{
      Definition            = $AzureADPolicyDefinition
      DisplayName           = "BasicAutoAccelerationPolicy"
      IsOrganizationDefault = $true
      Type                  = "HomeRealmDiscoveryPolicy"
    }
    New-AzureADPolicy @AzureADPolicyParameters
    ```

    Une fois la stratégie créée, la commande retourne l’ID de stratégie, comme illustré dans l’exemple de sortie suivant :

    ```powershell
    Id                                   DisplayName                 Type                     IsOrganizationDefault
    --                                   -----------                 ----                     ---------------------
    5de3afbe-4b7a-4b33-86b0-7bbe308db7f7 BasicAutoAccelerationPolicy HomeRealmDiscoveryPolicy True
    ```

1. Si une stratégie est déjà configurée, vérifiez si l’attribut *AlternateIdLogin* est activé, comme dans l’exemple de sortie de stratégie suivant :

    ```powershell
    Id : 5de3afbe-4b7a-4b33-86b0-7bbe308db7f7
    OdataType :
    AlternativeIdentifier :
    Definition : {{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}}
    DisplayName : BasicAutoAccelerationPolicy
    IsOrganizationDefault : True
    KeyCredentials : {}
    Type : HomeRealmDiscoveryPolicy
    ```

    Si la stratégie existe mais que l’attribut *AlternateIdLogin* n’est pas présent ou activé, ou s’il existe sur la stratégie d’autres attributs que vous souhaitez conserver, mettez à jour la stratégie existante à l’aide de la cmdlet [Set-AzureADPolicy][Set-AzureADPolicy].

    > [!IMPORTANT]
    > Lorsque vous mettez à jour la stratégie, veillez à inclure les anciens paramètres et le nouvel attribut  *AlternateIdLogin*.

    L’exemple suivant ajoute l’attribut  *AlternateIdLogin* et conserve l’attribut  *AllowCloudPasswordValidation* qui peut avoir déjà été défini :

    ```powershell
    $AzureADPolicyDefinition = @(
      @{
         "HomeRealmDiscoveryPolicy" = @{
            "AllowCloudPasswordValidation" = $true
            "AlternateIdLogin" = @{
               "Enabled" = $true
            }
         }
      } | ConvertTo-JSON -Compress
    )
    $AzureADPolicyParameters = @{
      ID                    = "b581c39c-8fe3-4bb5-b53d-ea3de05abb4b"
      Definition            = $AzureADPolicyDefinition
      DisplayName           = "BasicAutoAccelerationPolicy"
      IsOrganizationDefault = $true
      Type                  = "HomeRealmDiscoveryPolicy"
    }
    
    Set-AzureADPolicy @AzureADPolicyParameters
    ```

    Vérifiez que la stratégie mise à jour affiche vos modifications et que l’attribut *AlternateIdLogin* est désormais activé :

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

Une fois la stratégie appliquée, il peut falloir jusqu’à une heure pour qu’elle se propage et que les utilisateurs puissent se connecter avec leur ID de connexion alternatif.

## <a name="enable-staged-rollout-to-test-user-sign-in-with-an-email-address"></a>Activer le lancement intermédiaire pour tester la connexion de l’utilisateur avec une adresse e-mail  

> [!NOTE]
>Cette option de configuration utilise une stratégie de déploiement par étapes. Pour plus d’informations, consultez [Type de ressource featureRolloutPolicy](/graph/api/resources/featurerolloutpolicy?preserve-view=true&view=graph-rest-1.0).

La stratégie de déploiement par étapes permet aux administrateurs de locataire d’activer des fonctionnalités pour des groupes Azure AD spécifiques. Il est recommandé aux administrateurs du locataire d’utiliser le lancement intermédiaire pour tester la connexion de l’utilisateur avec une adresse e-mail. Lorsque les administrateurs sont prêts à déployer cette fonctionnalité sur la totalité de leur locataire, ils doivent utiliser une [stratégie de découverte du domaine d’accueil (HRD)](#enable-user-sign-in-with-an-email-address).  


Vous devez avoir des autorisations d’*administrateur général* pour effectuer les étapes suivantes :

1. Ouvrez une session PowerShell en tant qu’administrateur, puis installez le module *AzureADPreview* à l’aide de la cmdlet [Install-Module][Install-Module] :

    ```powershell
    Install-Module AzureADPreview
    ```

    Si vous y êtes invité, sélectionnez **Y** pour installer NuGet ou pour installer à partir d’un dépôt non approuvé.

1. Connectez-vous à votre locataire Azure AD en tant qu’*administrateur général* à l’aide de l’applet de commande [Connect-AzureAD][Connect-AzureAD] :

    ```powershell
    Connect-AzureAD
    ```

    La commande retourne des informations sur votre compte, votre environnement et votre ID de locataire.

1. Répertoriez toutes les stratégies de lancement intermédiaires existantes à l’aide de la cmdlet suivante :
   
   ```powershell
   Get-AzureADMSFeatureRolloutPolicy
   ``` 

1. S’il n’existe aucune stratégie de lancement intermédiaire pour cette fonctionnalité, créez une stratégie de lancement intermédiaire et prenez note de l’ID de stratégie :

   ```powershell
   $AzureADMSFeatureRolloutPolicy = @{
      Feature    = "EmailAsAlternateId"
      DisplayName = "EmailAsAlternateId Rollout Policy"
      IsEnabled   = $true
   }
   New-AzureADMSFeatureRolloutPolicy @AzureADMSFeatureRolloutPolicy
   ```

1. Recherchez l’ID directoryObject du groupe à ajouter à la stratégie de lancement intermédiaire. Notez la valeur retournée pour le paramètre *ID*, car elle sera utilisée à l’étape suivante.
   
   ```powershell
   Get-AzureADMSGroup -SearchString "Name of group to be added to the staged rollout policy"
   ```

1. Ajoutez le groupe à la stratégie de lancement intermédiaire, comme indiqué dans l’exemple suivant. Remplacez la valeur du paramètre *-Id* par la valeur retournée pour l’ID de stratégie à l’étape 4 et remplacez la valeur du paramètre *-RefObjectId* par l’*ID* noté à l’étape 5. Il peut falloir jusqu’à 1 heure avant que les utilisateurs membres du groupe puissent se connecter à Azure AD en utilisant leur e-mail en guise d’ID de connexion alternatif.

   ```powershell
   Add-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID&quot; -RefObjectId &quot;GROUP_OBJECT_ID"
   ```
   
Pour les nouveaux membres ajoutés au groupe, ce délai peut aller jusqu’à 24 heures.

### <a name="removing-groups"></a>Suppression de groupes

Pour supprimer un groupe d’une stratégie de lancement intermédiaire, exécutez la commande suivante :

```powershell
Remove-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID&quot; -ObjectId &quot;GROUP_OBJECT_ID" 
```

### <a name="removing-policies"></a>Suppression de stratégies

Pour supprimer une stratégie de lancement intermédiaire, commencez par désactiver la stratégie, puis supprimez-la du système :

```powershell
Set-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID" -IsEnabled $false 
Remove-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID"
```

## <a name="test-user-sign-in-with-an-email-address"></a>Tester la connexion des utilisateurs avec une adresse e-mail

Pour tester la possibilité de connexion des utilisateurs au moyen de l’e-mail, accédez à [https://myprofile.microsoft.com][my-profile] et connectez-vous avec un e-mail non UPN (par exemple, `balas@fabrikam.com` ). L’expérience de connexion doit être similaire à celle de la connexion avec l’UPN.

## <a name="troubleshoot"></a>Dépanner

Si des utilisateurs rencontrent des difficultés à se connecter avec leur adresse e-mail, essayez les étapes de dépannage suivantes :

1. Assurez-vous qu’au moins une heure s’est écoulée depuis l’activation de l’e-mail comme ID de connexion alternatif. Si l’utilisateur a récemment été ajouté à un groupe pour la stratégie de déploiement par étapes, assurez-vous que son ajout date de plus de 24 heures.
1. Si vous utilisez la stratégie de découverte du domaine d’accueil, vérifiez que la ressource *HomeRealmDiscoveryPolicy* d’Azure AD a sa propriété *AlternateIdLogin* définie sur *"Enabled": true* et sa propriété *IsOrganizationDefault* définie sur *True* :

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```
    Si vous utilisez la stratégie de déploiement par étapes, vérifiez que la ressource *FeatureRolloutPolicy* d’Azure AD a sa propriété *IsEnabled* définie sur *True* :

    ```powershell
    Get-AzureADMSFeatureRolloutPolicy
    ```
1. Vérifiez que l’adresse e-mail du compte d’utilisateur est définie dans l’attribut *ProxyAddresses* dans Azure AD.

### <a name="conflicting-values-between-cloud-only-and-synced-users"></a>Valeurs conflictuelles entre les utilisateurs du cloud uniquement et les utilisateurs synchronisés

Au sein d’un locataire, l’UPN d’un utilisateur du cloud uniquement peut avoir la même valeur que l’adresse proxy d’un autre utilisateur, synchronisée à partir de l’annuaire local. Dans ce scénario, si la fonctionnalité est activée, l’utilisateur du cloud uniquement ne pourra pas se connecter au moyen de son UPN. Voici les étapes à suivre pour détecter les instances de ce problème.

1. Ouvrez une session PowerShell en tant qu’administrateur, puis installez le module *AzureADPreview* à l’aide de la cmdlet [Install-Module][Install-Module] :

    ```powershell
    Install-Module AzureADPreview
    ```

    Si vous y êtes invité, sélectionnez **Y** pour installer NuGet ou pour installer à partir d’un dépôt non approuvé.

1. Connectez-vous à votre locataire Azure AD en tant qu’*administrateur général* à l’aide de l’applet de commande [Connect-AzureAD][Connect-AzureAD] :

    ```powershell
    Connect-AzureAD
    ```

1. Obtenez les utilisateurs affectés.

    ```powershell
    # Get all users
    $allUsers = Get-AzureADUser -All $true
    
    # Get list of proxy addresses from all synced users
    $syncedProxyAddresses = $allUsers |
        Where-Object {$_.ImmutableId} |
        Select-Object -ExpandProperty ProxyAddresses |
        ForEach-Object {$_ -Replace "smtp:", ""}
    
    # Get list of user principal names from all cloud-only users
    $cloudOnlyUserPrincipalNames = $allUsers |
        Where-Object {!$_.ImmutableId} |
        Select-Object -ExpandProperty UserPrincipalName
    
    # Get intersection of two lists
    $duplicateValues = $syncedProxyAddresses |
        Where-Object {$cloudOnlyUserPrincipalNames -Contains $_}
    ``` 

1. Pour lister les utilisateurs affectés :

    ```powershell
    # Output affected synced users
    $allUsers |
        Where-Object {$_.ImmutableId -And ($_.ProxyAddresses | Where-Object {($duplicateValues | ForEach-Object {"smtp:$_"}) -Contains $_}).Length -GT 0} |
        Select-Object ObjectId, DisplayName, UserPrincipalName, ProxyAddresses, ImmutableId, UserType
    
    # Output affected cloud-only users
    $allUsers |
        Where-Object {!$_.ImmutableId -And $duplicateValues -Contains $_.UserPrincipalName} |
        Select-Object ObjectId, DisplayName, UserPrincipalName, ProxyAddresses, ImmutableId, UserType
    ```

1. Pour lister les utilisateurs affectés dans un fichier CSV :

    ```powershell
    # Output affected users to CSV
    $allUsers |
        Where-Object {
            ($_.ImmutableId -And ($_.ProxyAddresses | Where-Object {($duplicateValues | ForEach-Object {"smtp:$_"}) -Contains $_}).Length -GT 0) -Or
            (!$_.ImmutableId -And $duplicateValues -Contains $_.UserPrincipalName)
        } |
        Select-Object ObjectId, DisplayName, UserPrincipalName, @{n="ProxyAddresses"; e={$_.ProxyAddresses -Join ','}}, @{n="IsSyncedUser"; e={$_.ImmutableId.Length -GT 0}}, UserType |
        Export-Csv -Path .\AffectedUsers.csv -NoTypeInformation
    ```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur une identité hybride, par exemple, un proxy Azure AD App ou Azure AD Domain Services, consultez [Identité hybride Azure AD pour l’accès aux charges de travail locales et leur gestion][hybrid-overview].

Pour plus d’informations sur les opérations d’identité hybrides, voyez comment fonctionnent les synchronisations par [hachage de mot de passe][phs-overview] et par [authentification directe][pta-overview].

<!-- INTERNAL LINKS -->
[verify-domain]: ../fundamentals/add-custom-domain.md
[hybrid-auth-methods]: ../hybrid/choose-ad-authn.md
[azure-ad-connect]: ../hybrid/whatis-azure-ad-connect.md
[hybrid-overview]: ../hybrid/cloud-governed-management-for-on-premises.md
[phs-overview]: ../hybrid/how-to-connect-password-hash-synchronization.md
[pta-overview]: ../hybrid/how-to-connect-pta-how-it-works.md
[identity-protection]: ../identity-protection/overview-identity-protection.md#risk-detection-and-remediation

<!-- EXTERNAL LINKS -->
[Install-Module]: /powershell/module/powershellget/install-module
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
[Get-AzureADPolicy]: /powershell/module/azuread/get-azureadpolicy
[New-AzureADPolicy]: /powershell/module/azuread/new-azureadpolicy
[Set-AzureADPolicy]: /powershell/module/azuread/set-azureadpolicy
[my-profile]: https://myprofile.microsoft.com
