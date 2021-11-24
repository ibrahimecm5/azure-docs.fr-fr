---
title: Sécurisation des comptes de service administré de groupe | Azure Active Directory
description: Guide de sécurisation des comptes d’ordinateur de service administré de groupe.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f934e016f23e988cbe8673f80e96c4583b6aaec5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292506"
---
# <a name="secure-group-managed-service-accounts"></a>Sécuriser les comptes de service gérés de groupe

Les comptes de service administré de groupe (gMSA, group Managed Service Account) sont des comptes de domaine managé qui permettent de sécuriser les services. Ils peuvent s’exécuter sur un seul serveur ou dans une batterie de serveurs, par exemple des systèmes qui se trouvent derrière un équilibreur de charge réseau ou un serveur IIS (Internet Information Services). Une fois que vous avez configuré vos services de façon à utiliser un principal gMSA, la gestion des mots de passe de ce compte est gérée par le système d’exploitation Windows.

## <a name="benefits-of-using-gmsas"></a>Avantages de l’utilisation de gMSA

Les comptes gMSA constituent une solution d’identité unique avec une sécurité accrue. En parallèle, pour réduire la charge administrative, ils offrent les avantages suivants :

* **Définition de mots de passe forts** : les comptes gMSA utilisent des mots de passe complexes de 240 octets générés de manière aléatoire. La complexité et la longueur des mots de passe de gMSA réduisent la probabilité qu’un service soit compromis par des attaques par force brute ou par dictionnaire.

* **Renouvellement régulier des mots de passe** : les comptes gMSA transfèrent la gestion des mots de passe au système d’exploitation Windows, qui les change tous les 30 jours. Les administrateurs de service et de domaine n’ont plus besoin de planifier de modifications de mot de passe ni de gérer les interruptions de service pour assurer la sécurité des comptes de service. 

* **Prise en charge du déploiement sur des fermes de serveurs** : la possibilité de déployer des comptes gMSA sur plusieurs serveurs permet de prendre en charge des solutions avec équilibrage de charge où plusieurs hôtes exécutent le même service. 

* **Prise en charge de la gestion simplifiée du nom de principal du service (SPN, Service Principal Name)** : vous pouvez configurer un nom SPN avec PowerShell lorsque vous créez un compte. En outre, les services qui prennent en charge les inscriptions automatiques de noms SPN peuvent les effectuer auprès du compte gMSA, sous réserve que les autorisations de celui-ci soient correctement définies. 

## <a name="when-to-use-gmsas"></a>Quand utiliser des gMSA

Utilisez des gMSA comme type de compte préféré pour les services locaux, à moins qu’un service, tel que le clustering de basculement, ne les prenne pas en charge.

> [!IMPORTANT]
> Vous devez tester votre service avec des comptes gMSA avant de le déployer en production. Pour cela, configurez un environnement de test de sorte que l’application puisse utiliser le compte gMSA, puis accéder aux ressources nécessaires. Pour plus d’informations, consultez [Prise en charge des comptes de service géré de groupe](/system-center/scom/support-group-managed-service-accounts).


Si un service ne prend pas en charge le recours aux comptes gMSA, la meilleure solution consiste à utiliser un compte de service administré autonome (sMSA, standalone Managed Service Account). Un compte sMSA offre les mêmes fonctionnalités qu’un compte gMSA, mais il est destiné au déploiement sur un seul serveur.

Si vous ne pouvez pas utiliser un compte gMSA ni un compte sMSA pris en charge par votre service, vous devez configurer le service de sorte qu’il s’exécute en tant que compte d’utilisateur standard. Les administrateurs de service et de domaine sont tenus d’observer des processus stricts de gestion des mots de passe afin de préserver la sécurité des comptes.

## <a name="assess-the-security-posture-of-gmsas"></a>Évaluer la posture de sécurité de gMSA

Les comptes gMSA sont par nature plus sécurisés que les comptes d’utilisateur standard, qui réclament une gestion continue des mots de passe. Toutefois, il est important de tenir compte de l’étendue d’accès d’un compte gMSA lorsque vous examinez l’état global de la sécurité.

Les problèmes de sécurité potentiels liés à l’utilisation de comptes gMSA et les atténuations associées figurent dans le tableau suivant :

| Problème de sécurité| Limitation des risques |
| - | - |
| Le gMSA est membre de groupes privilégiés. | <li>Vérifiez vos appartenances à des groupes. Pour cela, créez un script PowerShell permettant d’énumérer toutes les appartenances aux groupes. Vous pouvez ensuite filtrer le fichier CSV obtenu en fonction du nom de vos fichiers gMSA.<li>Supprimez le gMSA des groupes privilégiés.<li>Accordez au gMSA uniquement les droits et les autorisations dont il a besoin pour exécuter son service (consultez votre fournisseur de services). 
| Le gMSA dispose d’un accès en lecture/écriture aux ressources sensibles. | <li>Auditez l’accès aux données sensibles.<li>Archivez les journaux d’audit dans un système SIEM , tel que Azure Log Analytics ou Microsoft Sentinel, à des fins d’analyse.<li>Supprimez les autorisations de ressources inutiles si vous détectez un niveau d’accès indésirable. |
| | |


## <a name="find-gmsas"></a>Rechercher des gMSA

Votre organisation a peut-être déjà créé des comptes gMSA. Pour les récupérer, exécutez les cmdlets PowerShell suivantes :

```powershell
Get-ADServiceAccount 
Install-ADServiceAccount 
New-ADServiceAccount 
Remove-ADServiceAccount 
Set-ADServiceAccount 
Test-ADServiceAccount 
Uninstall-ADServiceAccount
```


Pour fonctionner efficacement, les comptes gMSA doivent se trouver dans l’unité d’organisation (UO) du compte de service administré.

  
![Capture d’écran d’un compte gMSA dans l’UO du compte de service administré.](./media/securing-service-accounts/secure-gmsa-image-1.png)

Pour rechercher des comptes MSA de service qui ne figurent pas dans la liste, exécutez les commandes suivantes :

```powershell

Get-ADServiceAccount -Filter *

# This PowerShell cmdlet will return all managed service accounts (both gMSAs and sMSAs). An administrator can differentiate between the two by examining the ObjectClass attribute on returned accounts.

# For gMSA accounts, ObjectClass = msDS-GroupManagedServiceAccount

# For sMSA accounts, ObjectClass = msDS-ManagedServiceAccount

# To filter results to only gMSAs:

Get-ADServiceAccount –Filter * | where-object {$_.ObjectClass -eq "msDS-GroupManagedServiceAccount"}
```

## <a name="manage-gmsas"></a>Gérer des gMSA

Pour gérer des comptes gMSA, vous pouvez utiliser les cmdlets PowerShell Active Directory suivantes :

`Get-ADServiceAccount`

`Install-ADServiceAccount`

`New-ADServiceAccount`

`Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Uninstall-ADServiceAccount`

> [!NOTE]
> À partir de Windows Server 2012, les cmdlets *-ADServiceAccount fonctionnent avec par défaut avec les gMSA. Pour plus d’informations sur l’utilisation des cmdlets précédentes, consultez [Bien démarrer avec les comptes de service administré de groupe](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts).

## <a name="move-to-a-gmsa"></a>Passer à un gMSA
Les comptes gMSA constituent le type de compte de service le plus sécurisé pour les besoins locaux. Si vous pouvez passer à un gMSA, vous devriez le faire. En outre, envisagez de déplacer vos services vers Azure et vos comptes de service vers Azure Active Directory. Pour passer à un compte gMSA, procédez comme suit :

1. Faites en sorte que la [clé racine du Service de distribution de clés (KDS, Key Distribution Service)](/windows-server/security/group-managed-service-accounts/create-the-key-distribution-services-kds-root-key) soit déployée dans la forêt. Il s’agit d’une opération unique.

1. [Créez un gMSA](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts).

1. Installez le nouveau compte gMSA sur chacun des hôtes qui exécutent le service.
   > [!NOTE] 
   > Pour plus d’informations sur la création et l’installation d’un compte gMSA sur un hôte, consultez [Bien démarrer avec les comptes de service administré de groupe](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11)) avant de configurer votre service de façon à utiliser un compte gMSA.

1. Remplacez votre identité de service par le compte gMSA et spécifiez un mot de passe vide.

1. Vérifiez que votre service fonctionne sous la nouvelle identité gMSA.

1. Supprimez l’ancienne identité du compte de service.

 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la sécurisation des comptes de service, consultez les articles suivants :

* [Présentation des comptes de service locaux](service-accounts-on-premises.md)  
* [Sécuriser les comptes de service gérés autonomes](service-accounts-standalone-managed.md)  
* [Sécuriser les comptes d’ordinateur](service-accounts-computer.md)  
* [Sécuriser les comptes d’utilisateur](service-accounts-user-on-premises.md)  
* [Administrer les comptes de service locaux](service-accounts-govern-on-premises.md)
