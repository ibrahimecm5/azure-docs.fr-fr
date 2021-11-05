---
title: Prérequis pour la synchronisation cloud Azure AD Connect dans Azure AD
description: Cet article décrit les prérequis et la configuration matérielle requise pour la synchronisation cloud.
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
ms.openlocfilehash: 31850f855cdf109e2337898736d273237ff65058
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131018198"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-sync"></a>Prérequis pour la synchronisation cloud Azure AD Connect
Cet article fournit des conseils sur la façon de choisir et d’utiliser la synchronisation cloud Azure Active Directory (Azure AD) Connect en tant que solution d’identité.

## <a name="cloud-provisioning-agent-requirements"></a>Conditions requises de l’agent de provisionnement cloud
Vous avez besoin des éléments suivants pour utiliser la synchronisation cloud Azure AD Connect :

- Les informations d’identification Administrateur de domaine ou Administrateur d’entreprise pour créer le compte de service managé de groupe (gMSA) Azure AD Connect Cloud Sync pour exécuter le service agent. 
- Un compte d’administrateur d’identité hybride pour votre locataire Azure AD qui n’est pas un utilisateur invité.
- Un serveur local pour l’agent de provisionnement avec Windows 2016 ou ultérieur.  Ce serveur doit être un serveur de niveau 0 basé sur le [modèle de niveau d’administration Active Directory](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material).  L’installation de l’agent sur un contrôleur de domaine est prise en charge.
- La haute disponibilité fait référence à la capacité de la synchronisation cloud Azure AD Connect à fonctionner en continu sans défaillance pendant une longue période.  Si plusieurs agents actifs sont installés et en cours d’exécution, la synchronisation cloud Azure AD Connect peut continuer à fonctionner même en cas d’échec d’un agent.  Microsoft recommande d’avoir trois agents actifs installés à des fins de haute disponibilité.
- Des configurations de pare-feu locales.

## <a name="group-managed-service-accounts"></a>Group Managed Service Accounts
Un compte de service managé de groupe est un compte de domaine managé qui fournit la gestion automatique des mots de passe, la gestion simplifiée du nom de principal du service (SPN), la possibilité de déléguer la gestion à d’autres administrateurs et cette fonctionnalité s’étend sur plusieurs serveurs.  Azure AD Connect Cloud Sync prend en charge et utilise un gMSA pour l’exécution de l’agent.  Vous serez invité à fournir des informations d’identification d’administration lors de l’installation, afin de créer ce compte.  Le compte s’affiche sous la forme (domain\provAgentgMSA$).  Pour plus d’informations sur un gMSA, consultez [Comptes de service managés de groupe](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) 

### <a name="prerequisites-for-gmsa"></a>Prérequis pour gMSA :
1.  Le schéma Active Directory dans la forêt du domaine gMSA doit être mis à jour vers Windows Server 2012 ou version ultérieure.
2.  Les [modules RSAT PowerShell](/windows-server/remote/remote-server-administration-tools) sur un contrôleur de domaine
3.  Au moins un contrôleur de domaine dans le domaine doit exécuter Windows Server 2012 ou version ultérieure.
4.  Un serveur joint à un domaine sur lequel l’agent est en cours d’installation doit être configuré avec Windows Server 2016 ou version ultérieure.

### <a name="custom-gmsa-account"></a>Compte gMSA personnalisé
Si vous créez un compte gMSA personnalisé, vous devez vous assurer que le compte dispose des autorisations suivantes.

|Type |Nom |Accès |S'applique à| 
|-----|-----|-----|-----|
|Allow |Compte gMSA |Lire toutes les propriétés |Objets appareil descendants| 
|Allow |Compte gMSA|Lire toutes les propriétés |Objets InetOrgPerson descendants| 
|Allow |Compte gMSA |Lire toutes les propriétés |Objets ordinateur descendants| 
|Allow |Compte gMSA |Lire toutes les propriétés |Objets foreignSecurityPrincipal descendants| 
|Allow |Compte gMSA |Contrôle total |Objets groupe descendants| 
|Allow |Compte gMSA |Lire toutes les propriétés |Objets utilisateur descendants| 
|Allow |Compte gMSA |Lire toutes les propriétés |Objets contact descendants| 
|Allow |Compte gMSA |Supprimer des objets utilisateur|Cet objet et tous les objets descendants| 

Pour connaître les étapes de la mise à niveau d’un agent existant afin d’utiliser un compte gMSA, consultez [Comptes de service managés de groupe](how-to-install.md#group-managed-service-accounts).

#### <a name="create-gmsa-account-with-powershell"></a>Créer un compte gMSA avec PowerShell
Vous pouvez utiliser le script PowerShell suivant pour créer un compte gMSA personnalisé.  Vous pouvez ensuite utiliser les [applets de commande gMSA de synchronisation cloud](how-to-gmsa-cmdlets.md) pour appliquer des autorisations plus précises.

```powershell
# Filename:    1_SetupgMSA.ps1
# Description: Creates and installs a custom gMSA account for use with Azure AD Connect cloud sync.
#
# DISCLAIMER:
# Copyright (c) Microsoft Corporation. All rights reserved. This 
# script is made available to you without any express, implied or 
# statutory warranty, not even the implied warranty of 
# merchantability or fitness for a particular purpose, or the 
# warranty of title or non-infringement. The entire risk of the 
# use or the results from the use of this script remains with you.
#
#
#
#
# Declare variables
$Name = 'provAPP1gMSA'
$Description = "Azure AD Cloud Sync service account for APP1 server"
$Server = "APP1.contoso.com"
$Principal = Get-ADGroup 'Domain Computers'

# Create service account in Active Directory
New-ADServiceAccount -Name $Name `
-Description $Description `
-DNSHostName $Server `
-ManagedPasswordIntervalInDays 30 `
-PrincipalsAllowedToRetrieveManagedPassword $Principal `
-Enabled $True `
-PassThru

# Install the new service account on Azure AD Cloud Sync server
Install-ADServiceAccount -Identity $Name
```

Pour plus d’informations sur l’utilisation des applets de commande ci-dessus, consultez [Prise en main des comptes de service administrés de groupe](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11)?redirectedfrom=MSDN).

### <a name="in-the-azure-active-directory-admin-center"></a>Dans le Centre d’administration Azure Active Directory

1. Créez un compte d’administrateur d’identité hybride « cloud uniquement » dans votre locataire Azure AD. De cette façon, vous pouvez gérer la configuration de votre locataire si vos services locaux venaient à échouer ou ne plus être disponibles. Découvrez comment [ajouter un compte d’administrateur d’identité hybride de type cloud uniquement](../fundamentals/add-users-azure-active-directory.md). Cette étape est essentielle si vous voulez éviter de vous retrouver en dehors de votre locataire.
1. Ajoutez un ou plusieurs [noms de domaine personnalisés](../fundamentals/add-custom-domain.md) à votre locataire Azure AD. Vos utilisateurs peuvent se connecter à l’aide de l’un de ces noms de domaine.

### <a name="in-your-directory-in-active-directory"></a>Dans votre annuaire dans Azure Active Directory

Exécutez l’[outil IdFix](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) afin de préparer les attributs d’annuaire pour la synchronisation.

### <a name="in-your-on-premises-environment"></a>Dans votre environnement local

1. Identifiez un serveur hôte joint à un domaine exécutant Windows Server 2016 ou ultérieur, avec au minimum 4 Go de RAM et .NET 4.7.1 + Runtime.

2. La stratégie d’exécution de PowerShell sur le serveur local doit être définie sur Undefined ou sur RemoteSigned.

3. S’il existe un pare-feu entre vos serveurs et Azure AD, configurez les éléments suivants :
    - Assurez-vous que les agents peuvent effectuer des requêtes *sortantes* sur Azure AD sur les ports suivants :

      | Numéro de port | Utilisation |
      | --- | --- |
      | **80** | Télécharge les listes de révocation de certificats lors de la validation du certificat TLS/SSL.  |
      | **443** | Gère toutes les communications sortantes avec le service. |
      | **8080** (facultatif) | Les agents signalent leur état toutes les 10 minutes sur le port 8080, si le port 443 n’est pas disponible. Cet état est affiché sur le portail Azure AD. |

    - Si votre pare-feu applique les règles en fonction des utilisateurs d’origine, ouvrez ces ports au trafic provenant des services Windows exécutés en tant que service réseau.
    - Si votre pare-feu ou proxy vous permet de spécifier des suffixes approuvés, ajoutez des connexions à \*.msappproxy.net et \*.servicebus.windows.net. Dans le cas contraire, autorisez l’accès aux [plages d’adresses IP du centre de données Azure](https://www.microsoft.com/download/details.aspx?id=41653), qui sont mises à jour chaque semaine.
    - Vos agents doivent accéder à login.windows.net et à login.microsoftonline.com pour l’inscription initiale. Par conséquent, ouvrez également votre pare-feu pour ces URL.
    - Pour valider le certificat, débloquez les URL suivantes : mscrl.microsoft.com:80, crl.microsoft.com:80, ocsp.msocsp.com:80 et www\.microsoft.com:80. Ces URL sont utilisées pour la validation de certificat avec d’autres produits Microsoft : elles seront peut-être déjà débloquées.

    >[!NOTE]
    > L’installation de l’agent de provisionnement cloud sur Windows Server Core n’est pas prise en charge.

### <a name="additional-requirements"></a>Autres conditions requises

- [Microsoft .NET Framework 4.7.1](https://dotnet.microsoft.com/download/dotnet-framework/net471) 

#### <a name="tls-requirements"></a>Exigences relatives à TLS

> [!NOTE]
> Le protocole TLS (Transport Layer Security) est un protocole qui fournit des communications sécurisées. La modification des paramètres TLS affecte l’ensemble de la forêt. Pour plus d’informations, consultez [Mise à jour pour activer TLS 1.1 et TLS 1.2 en tant que protocoles sécurisés par défaut dans WinHTTP sur Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi).

Le protocole TLS 1.2 doit être activé sur le serveur Windows qui hébergera l’agent de provisionnement cloud Azure AD Connect avant son installation.

Pour activer TLS 1.2, procédez comme suit.

1. Définissez les clés de Registre suivantes :

    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Redémarrez le serveur.
## <a name="ntlm-requirement"></a>Configuration NTLM requise

Vous ne devez pas activer NTLM sur le serveur Windows Server qui exécute l’agent de provisionnement Azure AD Connect et s’il est activé, vous devez le désactiver. 

## <a name="known-limitations"></a>Limitations connues

Les limitations connues sont les suivantes :

### <a name="delta-synchronization"></a>Synchronisation d’écart

- Le filtre d’étendue du groupe pour la synchronisation delta ne prend pas en charge plus de 50 000 membres.
- Lorsque vous supprimez un groupe utilisé dans le cadre d’un filtre d’étendue de groupe, les utilisateurs qui sont membres du groupe ne sont pas supprimés. 
- Lorsque vous renommez l’unité d’organisation ou le groupe qui se trouve dans l’étendue, la synchronisation delta ne supprime pas les utilisateurs.

### <a name="provisioning-logs"></a>Journaux de provisionnement
- Les journaux d’approvisionnement ne font pas clairement la différence entre les opérations de création et de mise à jour.  Vous pouvez voir une opération de création pour une mise à jour et une opération de mise à jour pour une création.

### <a name="group-re-naming-or-ou-re-naming"></a>Renommer le groupe ou renommer l’unité d’organisation
- Si vous renommez un groupe ou une unité d’organisation dans AD dans le cadre d’une configuration donnée, le travail de synchronisation cloud ne pourra pas reconnaître le changement de nom dans AD. Le travail ne sera pas mis en quarantaine et restera sain.

### <a name="scoping-filter"></a>Filtre d’étendue
Quand vous utilisez le filtre d’étendue des unités d’organisation
- Vous pouvez uniquement synchroniser jusqu’à 59 unités d’organisation distinctes pour une configuration donnée. 
- Les unités d’organisation imbriquées sont prises en charge : cela signifie que vous **pouvez** synchroniser une unité d’organisation contenant 130 unités d’organisation imbriquées, mais que vous **ne pouvez pas** synchroniser 60 unités d’organisation distinctes dans la même configuration. 

### <a name="password-hash-sync"></a>Synchronisation de hachage de mot de passe
- L’utilisation de la synchronisation de hachage de mot de passe avec InetOrgPerson n’est pas prise en charge.


## <a name="next-steps"></a>Étapes suivantes 

- [Présentation du provisionnement](what-is-provisioning.md)
- [Qu’est-ce que la synchronisation cloud Azure AD Connect ?](what-is-cloud-sync.md)
