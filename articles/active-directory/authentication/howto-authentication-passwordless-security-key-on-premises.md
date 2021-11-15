---
title: Connexion par clé de sécurité sans mot de passe à des ressources locales – Azure Active Directory
description: Découvrez comment activer la connexion par clé de sécurité sans mot de passe à des ressources locales à l’aide d’Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce8869a15a1c94ab2f3227780de8eb37bbf52b6f
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505419"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-by-using-azure-ad"></a>Activer la connexion par clé de sécurité sans mot de passe à des ressources locales à l’aide d’Azure AD 

Ce document explique comment activer l’authentification sans mot de passe auprès de ressources locales pour des environnements disposant d’appareils Windows 10 des types *Joint à Azure Active Directory (Azure AD)* et *Joint à Azure AD hybride*. Cette fonctionnalité d’authentification sans mot de passe fournit une authentification unique (SSO) directe auprès de ressources locales lorsque vous utilisez des clés de sécurité compatibles avec Microsoft.

## <a name="use-sso-to-sign-in-to-on-premises-resources-by-using-fido2-keys"></a>Utiliser l’authentification unique pour se connecter à des ressources locales à l’aide de clés FIDO2

Azure AD peut émettre des Ticket Granting Tickets (TGT) Kerberos pour un ou plusieurs de vos domaines Active Directory. Cette fonctionnalité permet aux utilisateurs de se connecter à Windows avec des informations d’identification modernes telles que des clés de sécurité FIDO2 pour accéder à des ressources Active Directory traditionnelles. Les tickets de service Kerberos et l’autorisation continuent d’être contrôlés par vos contrôleurs de domaine Active Directory locaux.

Un objet serveur Kerberos Azure AD est créé dans votre instance Active Directory locale, puis publié en toute sécurité sur Azure Active Directory. L’objet n’est associé à aucun serveur physique. Il s’agit simplement d’une ressource qu’Azure Active Directory peut utiliser afin de générer des TGT Kerberos pour votre domaine Active Directory.

:::image type="Image" source="./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png" alt-text="Diagramme montrant comment obtenir un TGT à partir d’Azure AD et d’Active Directory Domain Services." lightbox="./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png":::

1. L’utilisateur se connecte à un appareil Windows 10 avec une clé de sécurité FIDO2 et s’authentifie auprès d’Azure AD.
1. Azure AD recherche dans l’annuaire une clé de serveur Kerberos correspondant au domaine Active Directory local de l’utilisateur.

   Azure AD génère un TGT Kerberos pour le domaine Active Directory local de l’utilisateur. Le TGT comprend uniquement le SID de l’utilisateur, et aucune donnée d’autorisation.

1. Le TGT est renvoyé au client avec le jeton d’actualisation principal (PRT, Primary Refresh Token) Azure AD de l’utilisateur.
1. L’ordinateur du client contacte un contrôleur de domaine Active Directory local et échange le TGT partiel contre un TGT entièrement formé.
1. L’ordinateur du client disposant à présent d’un PRT Azure AD et d’un TGT Active Directory complet, il peut accéder aux ressources cloud et locales.

## <a name="prerequisites"></a>Prérequis

Avant de commencer à suivre les procédures décrites dans cet article, votre organisation doit suivre les instructions de la rubrique [Activer la connexion par clé de sécurité sans mot de passe à des appareils Windows 10](howto-authentication-passwordless-security-key.md).

Vous devez également respecter la configuration système suivante :

- Les appareils doivent exécuter Windows 10 version 2004 ou ultérieure.

- Vous devez exécuter [Azure AD Connect version 1.4.32.0 ou ultérieure](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect).
  - Pour plus d’informations sur les options d’authentification hybride Azure AD disponibles, consultez les articles suivants :
    - [Choisir la méthode d’authentification adaptée à votre solution d’identité hybride Azure AD](../hybrid/choose-ad-authn.md)
    - [Sélectionner le type d’installation à utiliser pour Azure AD Connect](../hybrid/how-to-connect-install-select-installation.md)

- Des correctifs logiciels doivent être installés sur vos contrôleurs de domaine Windows Server pour les serveurs suivants :
    - [Windows Server 2016](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
    - [Windows Server 2019](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

### <a name="supported-scenarios"></a>Scénarios pris en charge

Le scénario de cet article prend en charge l’authentification unique dans les deux cas suivants :

- Ressources Cloud telles que Microsoft 365 et d’autres applications compatibles avec SAML(Security Assertion Markup Language).
- Ressources locales et authentification intégrée Windows auprès des sites web. Les ressources peuvent inclure des sites web et des sites SharePoint qui requièrent une authentification IIS, et/ou des ressources qui utilisent l’authentification NTLM.

### <a name="unsupported-scenarios"></a>Scénarios non pris en charge

Les scénarios suivants ne sont pas pris en charge :

- Déploiement de Windows Server joint à Active Directory Domain Services (AD DS) (appareils locaux uniquement).
- Scénarios RDP (Remote Desktop Protocol), VDI (Virtual Desktop Infrastructure) et Citrix en utilisant une clé de sécurité.
- S/MIME en utilisant une clé de sécurité.
- *Exécuter en tant que* en utilisant une clé de sécurité.
- Connexion à un serveur en utilisant une clé de sécurité.

## <a name="install-the-azure-ad-kerberos-powershell-module"></a>Installer le module Azure AD Kerberos PowerShell

Le [module Azure AD Kerberos PowerShell](https://www.powershellgallery.com/packages/AzureADHybridAuthenticationManagement) fournit des fonctionnalités de gestion FIDO2 pour les administrateurs.

1. Ouvrez une invite PowerShell à l’aide de l’option Exécuter en tant qu’administrateur.
1. Installez le module Azure AD Kerberos PowerShell :

   ```powershell
   # First, ensure TLS 1.2 for PowerShell gallery access.
   [Net.ServicePointManager]::SecurityProtocol = [Net.ServicePointManager]::SecurityProtocol -bor [Net.SecurityProtocolType]::Tls12

   # Install the Azure AD Kerberos PowerShell Module.
   Install-Module -Name AzureADHybridAuthenticationManagement -AllowClobber
   ```

> [!NOTE]
> - Le module Azure AD Kerberos PowerShell utilise le [module PowerShell AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview) pour fournir des fonctionnalités de gestion Azure Active Directory avancées. Si le module [AzureAD PowerShell](https://www.powershellgallery.com/packages/AzureAD) est déjà installé sur votre ordinateur local, l’installation décrite ici risque d’échouer en raison d’un conflit. Pour éviter tout conflit au cours de l’installation, veillez à inclure l’indicateur d’option « -AllowClobber ».
> - Vous pouvez installer le module Azure AD Kerberos PowerShell sur tout ordinateur à partir duquel vous pouvez accéder à votre contrôleur de domaine Active Directory local, sans dépendre de la solution de Azure AD Connect.
> - Le module Azure AD PowerShell Kerberos est distribué via [PowerShell Gallery](https://www.powershellgallery.com/). PowerShell Gallery est le référentiel central pour le contenu PowerShell. Vous pouvez y trouver des modules PowerShell utiles contenant des commandes PowerShell et des ressources de Desired State Configuration (DSC).

## <a name="create-a-kerberos-server-object"></a>Créer un objet serveur Kerberos

Les administrateurs utilisent le module Azure AD Kerberos PowerShell pour créer un objet serveur Azure AD Kerberos dans leur répertoire local.

Exécutez les étapes suivantes dans chaque domaine et forêt de votre organisation contenant des utilisateurs Azure AD :

1. Ouvrez une invite PowerShell à l’aide de l’option Exécuter en tant qu’administrateur.
1. Exécutez les commandes PowerShell suivantes pour créer un objet serveur Kerberos Azure AD dans votre domaine Active Directory local et votre locataire Azure Active Directory.

   > [!NOTE]
   > Remplacez `contoso.corp.com` dans l’exemple suivant par le nom de votre domaine Active Directory local.

   ```powershell
   # Specify the on-premises Active Directory domain. A new Azure AD
   # Kerberos Server object will be created in this Active Directory domain.
   $domain = "contoso.corp.com"

   # Enter an Azure Active Directory global administrator username and password.
   $cloudCred = Get-Credential

   # Enter a domain administrator username and password.
   $domainCred = Get-Credential

   # Create the new Azure AD Kerberos Server object in Active Directory
   # and then publish it to Azure Active Directory.
   Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
   ```

   > [!NOTE]
   > Si vous utilisez une machine jointe à un domaine avec un compte disposant de privilèges d’administrateur de domaine, vous pouvez ignorer le paramètre « -DomainCredential ». Si le paramètre « -DomainCredential » n’est pas fourni, les informations d’identification de connexion Windows actuelles sont utilisées pour accéder à votre contrôleur de domaine Active Directory local.

   ```powershell
   # Specify the on-premises Active Directory domain. A new Azure AD
   # Kerberos Server object will be created in this Active Directory domain.
   $domain = "contoso.corp.com"

   # Enter an Azure Active Directory global administrator username and password.
   $cloudCred = Get-Credential

   # Create the new Azure AD Kerberos Server object in Active Directory
   # and then publish it to Azure Active Directory.
   # Use the current windows login credential to access the on-prem AD.
   Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred
   ```

   > [!NOTE]
   > Si votre organisation protège la connexion par mot de passe et applique des méthodes d’authentification modernes telles que l’authentification multifacteur, FIDO2 ou une technologie de carte à puce, vous devez utiliser le paramètre `-UserPrincipalName` avec le nom d’utilisateur principal d’un administrateur général.
   > - Remplacez `contoso.corp.com` dans l’exemple suivant par le nom de votre domaine Active Directory local.
   > - Remplacez `administrator@contoso.onmicrosoft.com` dans l’exemple suivant par le nom d’utilisateur principal (UPM) d’un administrateur général.

   ```powershell
   # Specify the on-premises Active Directory domain. A new Azure AD
   # Kerberos Server object will be created in this Active Directory domain.
   $domain = "contoso.corp.com"

   # Enter a UPN of an Azure Active Directory global administrator
   $userPrincipalName = "administrator@contoso.onmicrosoft.com"

   # Enter a domain administrator username and password.
   $domainCred = Get-Credential

   # Create the new Azure AD Kerberos Server object in Active Directory
   # and then publish it to Azure Active Directory.
   # Open an interactive sign-in prompt with given username to access the Azure AD.
   Set-AzureADKerberosServer -Domain $domain -UserPrincipalName $userPrincipalName -DomainCredential $domainCred
   ```

### <a name="view-and-verify-the-azure-ad-kerberos-server"></a>Afficher et vérifier le serveur Azure AD Kerberos

Vous pouvez afficher et vérifier le serveur Azure AD Kerberos que vous venez de créer à l’aide de la commande suivante :

```powershell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

Cette commande génère les propriétés du serveur Kerberos Azure AD. Vous pouvez examiner les propriétés pour vérifier que tout est en ordre.

> [!NOTE]
> L’exécution de la commande sur un autre domaine en fournissant les informations d’identification établit une connexion via NTLM, puis échoue. Si les utilisateurs se trouvent dans le groupe de sécurité Utilisateurs protégés dans Active Directory, procédez comme suit pour résoudre le problème : connectez-vous en tant qu’utilisateur d’un autre domaine dans **ADConnect** et n’entrez pas de « -domainCredential ». Le ticket Kereberos de l’utilisateur actuellement connecté est utilisé. Vous pouvez confirmer en exécutant `whoami /groups` pour valider le fait que l’utilisateur dispose des autorisations requises dans Active Directory pour exécuter la commande précédente.
 
| Propriété | Description |
| --- | --- |
| id | ID unique de l’objet contrôleur de domaine AD DS. Cet ID est parfois appelé *emplacement* ou *ID de branche*. |
| DomainDnsName | Nom de domaine DNS du domaine Active Directory. |
| ComputerAccount | Objet compte d’ordinateur de l’objet serveur Kerberos Azure AD (le contrôleur de domaine). |
| UserAccount | Objet compte d’utilisateur désactivé contenant la clé de chiffrement du TGT du serveur Kerberos Azure AD. Le nom de domaine de ce compte est `CN=krbtgt_AzureAD,CN=Users,<Domain-DN>`. |
| KeyVersion | Version de clé de la clé de chiffrement du TGT du serveur Kerberos Azure AD. La version est attribuée lors de la création de la clé. La version est ensuite incrémentée à chaque rotation de la clé. Les incréments sont basés sur les métadonnées de réplication et probablement supérieurs à un. Par exemple, la *KeyVersion* initiale pourrait être *192272*. À la première rotation de la clé, la version peut passer à *212621*. L’élément important à vérifier est que la *KeyVersion* de l’objet local et la *CloudKeyVersion* de l’objet cloud sont identiques. |
| KeyUpdatedOn | Date et heure auxquelles la clé de chiffrement du TGT du serveur Kerberos Azure AD a été mise à jour ou créée. |
| KeyUpdatedFrom | Contrôleur de domaine où la clé de chiffrement du TGT du serveur Kerberos Azure AD a été mise à jour pour la dernière fois. |
| CloudId | ID de l’objet Azure AD. Doit correspondre à l’ID de la première ligne de la table. |
| CloudDomainDnsName | *DomainDnsName* de l’objet Azure AD. Doit correspondre à *DomainDnsName* à partir de la deuxième ligne de la table. |
| CloudKeyVersion | *KeyVersion* de l’objet Azure AD. Doit correspondre à *KeyVersion* à partir de la cinquième ligne de la table. |
| CloudKeyUpdatedOn | *KeyUpdatedOn* de l’objet Azure AD. Doit correspondre à *KeyUpdatedOn* à partir de la sixième ligne de la table. |
| | |

### <a name="rotate-the-azure-ad-kerberos-server-key"></a>Opérer la rotation la clé du serveur Azure AD Kerberos

Les clés *krbtgt* de chiffrement du serveur Azure AD Kerberos doivent faire l’objet d’une rotation régulière. Il est recommandé de suivre la planification utilisée pour la rotation de toutes les autres clés *krbtgt* du contrôleur de domaine Active Directory.

> [!WARNING]
> D’autres outils peuvent opérer la rotation des clés *krbtgt*. Toutefois, vous devez utiliser les outils mentionnés dans ce document pour opérer la rotation des clés *krbtgt* de votre serveur Azure AD Kerberos. Cela garantit que les clés sont à jour à la fois dans l’Active Directory local et dans Azure AD.

```powershell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="remove-the-azure-ad-kerberos-server"></a>Supprimer le serveur Azure AD Kerberos

Si vous souhaitez annuler le scénario et supprimer le serveur Azure AD Kerberos tant de l’Active Directory local que d’Azure AD, exécutez la commande suivante :

```powershell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multiforest-and-multidomain-scenarios"></a>Scénarios multiforêt et multidomaine

L’objet serveur Azure AD Kerberos est représenté dans Azure AD en tant qu’objet *KerberosDomain*. Chaque domaine Active Directory local est représenté en tant qu’objet *KerberosDomain* unique dans Azure AD.

Imaginons que votre organisation dispose d’une forêt Active Directory avec deux domaines, `contoso.com` et `fabrikam.com`. Si vous choisissez d’autoriser Azure AD à émettre des TGT Kerberos pour l’ensemble de la forêt, il y aura deux objets *KerberosDomain* dans Azure AD, à savoir un objet *KerberosDomain* pour `contoso.com` et l’autre pour `fabrikam.com`. Si vous avez plusieurs forêts Active Directory, il existe un objet *KerberosDomain* pour chaque domaine dans chaque forêt.

Suivez les instructions fournies dans [Créer un objet serveur Kerberos](#create-a-kerberos-server-object) dans chaque domaine et forêt de votre organisation contenant des utilisateurs Azure AD.

## <a name="known-behavior"></a>Comportement connu

Si votre mot de passe a expiré, la connexion à l’aide de FIDO est bloquée. L’objectif est que les utilisateurs réinitialisent leur mot de passe avant de se connecter à l’aide de FIDO.

## <a name="troubleshooting-and-feedback"></a>Résolution des problèmes de commentaires

Si vous rencontrez des problèmes ou si vous souhaitez partager des commentaires sur cette fonctionnalité de connexion par clé de sécurité sans mot de passe, partagez via l’application Hub de commentaires Windows en procédant comme suit :

1. Lancez le **Hub de commentaires** et assurez-vous que vous êtes connecté.
1. Soumettez des commentaires en sélectionnant les catégories suivantes :
   - Catégorie : Sécurité et confidentialité
   - Sous-catégorie : FIDO
1. Pour capturer des journaux, utilisez l’option **Recréer mon problème**.

## <a name="passwordless-security-key-sign-in-faq"></a>FAQ sur la connexion par clé de sécurité sans mot de passe

Voici quelques réponses aux questions fréquemment posées sur la connexion sans mot de passe :

### <a name="does-passwordless-security-key-sign-in-work-in-my-on-premises-environment"></a>La connexion par clé de sécurité sans mot de passe fonctionne-t-elle dans mon environnement local ?

La fonctionnalité ne fonctionne pas dans un environnement AD DS purement local.

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Mon organisation exige une authentification à deux facteurs pour accéder aux ressources. Que puis-je faire pour prendre en charge cette exigence ?

Les clés de sécurité sont disponibles dans différents facteurs de forme. Contactez le fabricant officiel de l’appareil pour discuter de la façon dont ses appareils peuvent être activés à l’aide d’un code confidentiel ou d’une caractéristique biométrique comme deuxième facteur.

### <a name="can-administrators-set-up-security-keys"></a>Les administrateurs peuvent-ils configurer des clés de sécurité ?

Nous travaillons sur cette fonctionnalité pour la publication en disponibilité générale de cette fonctionnalité.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Où puis-je trouver des clés de sécurité conformes ?

Pour plus d’informations sur les clés de sécurité conformes, consultez [Clés de sécurité FIDO2](concept-authentication-passwordless.md#fido2-security-keys).

### <a name="what-can-i-do-if-i-lose-my-security-key"></a>Que puis-je faire si je perds ma clé de sécurité ?

Pour récupérer une clé de sécurité, connectez-vous au portail Azure, puis accédez à la page **Informations de sécurité** .

### <a name="what-can-i-do-if-im-unable-to-use-the-fido-security-key-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>Que puis-je faire si je ne parviens pas à utiliser la clé de sécurité FIDO immédiatement après avoir créé une machine jointe à Azure AD hybride  ?

Si vous effectuez une nouvelle installation de machine jointe à Azure AD hybride, après le processus de jonction de domaine et de redémarrage, vous devez vous connecter avec un mot de passe et attendre la synchronisation de la stratégie avant de pouvoir utiliser la clé de sécurité FIDO pour vous connecter.

- Vérifiez votre état actuel en exécutant `dsregcmd /status` dans une fenêtre d’invite de commandes, puis assurez-vous que les états **AzureAdJoined** et **DomainJoined** affichent *OUI*.
- Ce délai de synchronisation est une limitation connue des appareils joints à un domaine et n’est pas spécifique de FIDO.

### <a name="what-if-im-unable-to-get-single-sign-on-to-my-ntlm-network-resource-after-i-sign-in-with-fido-and-get-a-credential-prompt"></a>Que se passe-t-il si je ne parviens pas à effectuer l’authentification unique auprès de ma ressource réseau NTLM après m’être connecté avec FIDO et avoir obtenu une invite d’informations d’identification ?

Assurez-vous qu’un nombre suffisant de contrôleurs de domaine sont corrigés pour répondre dans les temps afin de servir votre demande de ressource. Pour déterminer si un contrôleur de domaine exécute la fonctionnalité, exécutez `nltest /dsgetdc:contoso /keylist /kdc`, puis examinez la sortie.

> [!NOTE]
> Le commutateur `/keylist` dans la commande `nltest` est disponible dans le client Windows 10 v2004 et versions ultérieures.


### <a name="do-fido2-security-keys-work-in-a-windows-login-with-rodc-present-in-the-hybrid-environment"></a>Les clés de sécurité FIDO2 fonctionnent-elles dans une connexion Windows quand un contrôleur de domaine en lecture seule est présent dans l’environnement hybride ?

Une connexion Windows par FIDO2 recherche un contrôleur de domaine accessible en écriture pour échanger le TGT de l’utilisateur. Tant que vous disposez d’au moins un contrôleur de domaine accessible en écriture par site, la connexion fonctionne correctement. 

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur l’authentification sans mot de passe](concept-authentication-passwordless.md)
