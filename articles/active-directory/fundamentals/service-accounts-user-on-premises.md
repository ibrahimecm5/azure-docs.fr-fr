---
title: Sécuriser des comptes de service basés sur l’utilisateur | Azure Active Directory
description: Guide de sécurisation des comptes de service basés sur l’utilisateur.
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
ms.openlocfilehash: 6dc3ec13715fe0c58ffbf23cf377f0ea5d29d6de
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292392"
---
# <a name="secure-user-based-service-accounts-in-active-directory"></a>Sécuriser des comptes de service basés sur l’utilisateur dans Active Directory

L’utilisation de comptes d’utilisateurs locaux est l’approche traditionnelle pour aider à sécuriser les services qui s’exécutent sous Windows. Utilisez ces comptes en dernier recours lorsque les comptes de service géré de groupe (gMSA) et les comptes de service géré autonomes (sMSA) ne sont pas pris en charge par votre service. Pour plus d’informations sur la sélection du meilleur type de compte à utiliser, consultez [Présentation des comptes de service locaux](service-accounts-on-premises.md). 

Vous pouvez également étudier la possibilité de déplacer votre service pour utiliser un compte de service Azure, tel qu’une identité managée ou un principal de service. 

Vous pouvez créer des comptes d’utilisateur locaux pour fournir un contexte de sécurité aux services et les autorisations dont les comptes ont besoin pour accéder aux ressources locales et réseau. Les comptes d’utilisateur locaux nécessitent une gestion manuelle des mots de passe, comme tout autre compte d’utilisateur Active Directory. Les administrateurs de service et de domaine sont tenus d’observer des processus stricts de gestion des mots de passe afin de préserver la sécurité de ces comptes.

Lorsque vous utilisez un compte d’utilisateur en tant que compte de service, utilisez-le pour un seul service uniquement. Donnez-lui un nom qui indique clairement qu’il s’agit d’un compte de service et à quel service il est destiné. 

## <a name="benefits-and-challenges"></a>Avantages et défis

Les comptes d’utilisateurs locaux peuvent offrir des avantages considérables. Il s’agit du type de compte le plus polyvalent à utiliser avec des services. Les comptes d’utilisateur utilisés comme comptes de service peuvent être contrôlés par toutes les stratégies qui régissent les comptes d’utilisateur normaux. Cependant, utilisez-les uniquement si vous ne pouvez pas utiliser un MSA. Déterminez également si un compte d’ordinateur est une meilleure option. 

Les défis associés à l’utilisation de comptes d’utilisateur locaux sont résumés dans le tableau suivant :

| Problème | Limitation des risques |
| - | - |
| La gestion des mots de passe est un processus manuel qui peut entraîner une sécurité plus faible et un temps d’arrêt du service.| <li>Vérifiez que la complexité et les modifications des mots de passe sont régies par un processus robuste qui garantit des mises à jour régulières avec des mots de passe forts.<li>Coordonnez les modifications des mots de passe avec une mise à jour des mots de passe sur le service, ce qui contribuera à réduire les temps d’arrêt du service. |
| Il peut s’avérer difficile d’identifier les comptes d’utilisateur locaux qui agissent en tant que comptes de service. | <li>Documentez et tenez à jour les enregistrements des comptes de service qui sont déployés dans votre environnement.<li>Effectuez le suivi du nom du compte et des ressources auxquelles il a accès.<li>Envisagez d’ajouter le préfixe « svc- » à tous les comptes d’utilisateur qui sont utilisés comme comptes de service. |
| | |


## <a name="find-on-premises-user-accounts-used-as-service-accounts"></a>Rechercher les comptes d’utilisateur locaux utilisés comme comptes de service

Les comptes d’utilisateur locaux sont comme n’importe quel autre compte d’utilisateur Active Directory. Il peut être difficile de trouver ces comptes, car aucun attribut unique d’un compte d’utilisateur ne l’identifie comme un compte de service. 

Nous vous recommandons de créer une convention d’affectation de noms facilement identifiable pour tout compte d’utilisateur que vous utilisez comme compte de service. Par exemple, vous pouvez ajouter « svc- » comme préfixe et nommer le service « svc-HRDataConnector ».

Vous pouvez utiliser certains des critères suivants pour rechercher ces comptes de service. Toutefois, cette approche risque de ne pas trouver tous les comptes, par exemple :

* Comptes approuvés pour la délégation.  
* Comptes avec des noms de principal du service.  
* Comptes dont les mots de passe sont définis pour ne jamais expirer.

Pour rechercher les comptes d’utilisateur locaux que vous avez créés pour les services, vous pouvez exécuter les commandes PowerShell suivantes.

Pour rechercher les comptes approuvés pour la délégation :

```PowerShell

Get-ADObject -Filter {(msDS-AllowedToDelegateTo -like '*') -or (UserAccountControl -band 0x0080000) -or (UserAccountControl -band 0x1000000)} -prop samAccountName,msDS-AllowedToDelegateTo,servicePrincipalName,userAccountControl | select DistinguishedName,ObjectClass,samAccountName,servicePrincipalName, @{name='DelegationStatus';expression={if($_.UserAccountControl -band 0x80000){'AllServices'}else{'SpecificServices'}}}, @{name='AllowedProtocols';expression={if($_.UserAccountControl -band 0x1000000){'Any'}else{'Kerberos'}}}, @{name='DestinationServices';expression={$_.'msDS-AllowedToDelegateTo'}}

```

Pour rechercher les comptes qui ont des noms de principal du service :

```PowerShell

Get-ADUser -Filter * -Properties servicePrincipalName | where {$_.servicePrincipalName -ne $null}

```

Pour rechercher les comptes dont les mots de passe sont définis pour ne jamais expirer :

```PowerShell

Get-ADUser -Filter * -Properties PasswordNeverExpires | where {$_.PasswordNeverExpires -eq $true}

```

Vous pouvez également auditer l’accès aux ressources sensibles et archiver les journaux d’audit dans un système SIEM (Informations de sécurité et gestion d’événements). En utilisant des systèmes tels qu’Azure Log Analytics ou Microsoft Sentinel, vous pouvez rechercher et analyser les comptes de service.

## <a name="assess-the-security-of-on-premises-user-accounts"></a>Évaluer la sécurité des comptes d’utilisateur locaux

Vous pouvez évaluer la sécurité des comptes d’utilisateur locaux qui sont utilisés comme comptes de service en utilisant les critères suivants :

* Quelle est la stratégie de gestion des mots de passe ?  
* Le compte est-il membre d’un groupe privilégié ?  
* Le compte dispose-t-il d’autorisations d’accès en lecture/écriture sur des ressources importantes ?

### <a name="mitigate-potential-security-issues"></a>Atténuer les problèmes de sécurité potentiels

Les problèmes de sécurité potentiels et leurs atténuations pour les comptes d’utilisateurs locaux sont résumés dans le tableau suivant :

| Problème de sécurité | Limitation des risques |
| - | - |
| Gestion des mots de passe| <li>Vérifiez que la complexité et la modification des mots de passe sont régies par un processus robuste qui inclut des mises à jour régulières et des exigences strictes en matière de mots de passe.<li>Coordonnez les modifications des mots de passe avec une mise à jour des mots de passe pour réduire les temps d’arrêt du service. |
| Le compte est membre de groupes privilégiés| <li>Vérifiez les appartenances aux groupes.<li>Supprimez le compte des groupes privilégiés.<li>Accordez au compte uniquement les droits et les autorisations dont il a besoin pour exécuter son service (consultez le fournisseur du service). Par exemple, vous pouvez être en mesure de refuser la connexion locale ou de refuser la connexion interactive. |
| Le compte dispose d’autorisations d’accès en lecture/écriture sur les ressources sensibles| <li>Auditez l’accès aux données sensibles.<li>Archivez les journaux d’audit dans un système SIEM (Azure Log Analytics ou Microsoft Sentinel) à des fins d’analyse.<li>Corrigez les autorisations de ressource si un niveau d’accès indésirable est détecté. |
| | |


## <a name="move-to-more-secure-account-types"></a>Passer à des types de comptes plus sécurisés

Microsoft vous déconseille d’utiliser des comptes d’utilisateur locaux comme comptes de service. Pour tout service utilisant ce type de compte, évaluez s’il peut être configuré pour utiliser un gMSA ou un sMSA.

En outre, évaluez si le service lui-même peut être déplacé vers Azure afin que des types de comptes de service plus sécurisés puissent être utilisés. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la sécurisation des comptes de service, consultez les articles suivants :

* [Présentation des comptes de service locaux](service-accounts-on-premises.md)  
* [Sécuriser les comptes de service gérés de groupe](service-accounts-group-managed.md)  
* [Sécuriser les comptes de service gérés autonomes](service-accounts-standalone-managed.md)  
* [Sécuriser les comptes d’ordinateur](service-accounts-computer.md)  
* [Administrer les comptes de service locaux](service-accounts-govern-on-premises.md)

 
