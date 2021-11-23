---
title: Sécuriser des comptes de service géré autonomes | Azure Active Directory
description: Guide de sécurisation des comptes de service géré autonomes.
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
ms.openlocfilehash: 8237e746e6f48640ed1aca8d2637eb128172a297
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292430"
---
# <a name="secure-standalone-managed-service-accounts"></a>Sécuriser les comptes de service gérés autonomes

Les comptes de service géré autonomes (sMSA) sont des comptes de domaine managé qui vous permettent de sécuriser un ou plusieurs services exécutés sur un serveur. Ils ne peuvent pas être réutilisés sur plusieurs serveurs. Les sMSA offrent une gestion automatique des mots de passe, une gestion simplifiée du nom de principal du service (SPN) et la possibilité de déléguer la gestion à d’autres administrateurs. 

Dans Active Directory, les sMSA sont liés à un serveur spécifique qui exécute un service. Vous pouvez trouver ces comptes dans le composant logiciel enfichable Utilisateurs et ordinateurs Active Directory de la console de gestion Microsoft.

![Capture d'écran du composant logiciel enfichable Utilisateurs et ordinateurs Active Directory illustrant l'UO de comptes de service géré.](./media/securing-service-accounts/secure-standalone-msa-image-1.png)

Les comptes de service géré ont été introduits avec le schéma Active Directory de Windows Server 2008 R2, et ils requièrent au minimum Windows Server 2008 R2. 

## <a name="benefits-of-using-smsas"></a>Avantages de l’utilisation de sMSA

Les sMSA offrent une plus grande sécurité que les comptes d'utilisateur utilisés comme comptes de service. Parallèlement, ils contribuent à réduire la charge administrative en offrant les avantages suivants :

* **Définition de mots de passe forts** : les sMSA utilisent des mots de passe complexes de 240 octets générés de manière aléatoire. La complexité et la longueur des mots de passe de sMSA réduisent la probabilité qu’un service soit compromis par des attaques par force brute ou par dictionnaire.

* **Recyclage régulier des mots de passe** : Windows modifie automatiquement le mot de passe du sMSA tous les 30 jours. Les administrateurs de service et de domaine n’ont pas besoin de planifier des modifications de mot de passe ni de gérer les temps d’arrêt associés.

* **Simplification de la gestion des licences** : les noms de principal de service sont automatiquement mis à jour si le niveau fonctionnel du domaine (DFL) est Windows Server 2008 R2. Par exemple, le nom du principal de service est automatiquement mis à jour dans les cas suivants :
   * Changement de nom du compte de l'ordinateur hôte  
   * Modification du nom DNS de l'ordinateur hôte  
   * Ajout ou suppression d'autres paramètres sam-accountname ou dns-hostname à l'aide de [PowerShell](/powershell/module/activedirectory/set-adserviceaccount)

## <a name="when-to-use-smsas"></a>Quand l’utiliser des sMSA

Les sMSA peuvent simplifier les tâches de gestion et de sécurité. Utilisez des sMSA lorsqu'un ou plusieurs services sont déployés sur un même serveur et que vous n'avez pas la possibilité d'utiliser un gMSA (compte de service géré de groupe). 

> [!NOTE] 
> Bien que vous puissiez utiliser des sMSA pour plusieurs services, nous recommandons que chaque service ait sa propre identité à des fins d'audit. 

Si le créateur du logiciel n'est pas en mesure de vous indiquer s'il peut utiliser un MSA, vous devez tester votre application. Pour ce faire, créez un environnement de test et assurez-vous qu'il peut accéder à toutes les ressources requises. Pour plus d'informations, consultez [Créer et installer un sMSA](/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting).

### <a name="assess-the-security-posture-of-smsas"></a>Évaluer la posture de sécurité de sMSA

Les sMSA sont par nature plus sécurisés que les comptes d’utilisateur standard, qui nécessitent une gestion continue des mots de passe. Toutefois, il est important de tenir compte de l’étendue de l’accès des sMSA dans le cadre de leur posture de sécurité globale.

Pour savoir comment atténuer les problèmes de sécurité potentiels posés par les sMSA, consultez le tableau suivant :

| Problème de sécurité| Limitation des risques |
| - | - |
| Le sMSA est membre de groupes privilégiés. | <li>Supprimez le sMSA des groupes avec privilèges élevés, par exemple Administrateurs de domaine.<li>Utilisez le modèle *avec le moins de privilèges* et accordez uniquement au sMSA les droits et autorisations dont il a besoin pour exécuter ses services.<li>Si vous n’êtes pas sûr des autorisations requises, consultez le créateur du service. |
| Le sMSA dispose d’un accès en lecture/écriture aux ressources sensibles. | <li>Auditez l’accès aux données sensibles.<li>Archivez les journaux d’audit dans un programme SIEM (Security Information and Event Management) tel qu’Azure Log Analytics ou Microsoft Sentinel à des fins d’analyse.<li>Corrigez les autorisations de ressource si un niveau d’accès indésirable est détecté. |
| Par défaut, la fréquence de substitution de mot de passe des sMSA est de 30 jours. | Vous pouvez utiliser la stratégie de groupe pour régler la durée, en fonction des exigences de l'entreprise en matière de sécurité. Pour définir la durée d’expiration du mot de passe, utilisez le chemin suivant :<br>*Configuration de l'ordinateur\Stratégies\Paramètres Windows\Paramètres de sécurité\Options de sécurité*. Sous Membre de domaine, choisissez **Ancienneté maximale du mot de passe du compte d'ordinateur**. |
| | |



### <a name="challenges-with-smsas"></a>Défis liés aux sMSA

Les défis associés aux sMSA sont les suivants :

| Problème| Limitation des risques |
| - | - |
| Les sMSA ne peuvent être utilisés que sur un seul serveur. | Utilisez un gMSA si vous devez utiliser le compte sur plusieurs serveurs. |
| Les sMSA ne peuvent pas être utilisés dans plusieurs domaines. | Utilisez un gMSA si vous devez utiliser le compte dans plusieurs domaines. |
| Toutes les applications ne prennent pas en charge les sMSA. | Utilisez un gMSA dans la mesure du possible. Sinon, utilisez un compte d'utilisateur standard ou un compte d'ordinateur, comme recommandé par le créateur de l'application. |
| | |


## <a name="find-smsas"></a>Rechercher des sMSA

Sur n'importe quel contrôleur de domaine, exécutez DSA.msc, puis développez le conteneur Comptes de service géré pour afficher tous les sMSA. 

Pour renvoyer tous les sMSA et gMSA du domaine Active Directory, exécutez la commande PowerShell suivante : 

`Get-ADServiceAccount -Filter *`

Pour ne renvoyer que les sMSA du domaine Active Directory, exécutez la commande suivante :

`Get-ADServiceAccount -Filter * | where { $_.objectClass -eq "msDS-ManagedServiceAccount" }`

## <a name="manage-smsas"></a>Gérer des sMSA

Pour gérer vos sMSA, vous pouvez utiliser les cmdlets PowerShell Active Directory suivantes :

`Get-ADServiceAccount`
`Install-ADServiceAccount`
`New-ADServiceAccount`
`Remove-ADServiceAccount`
`Set-ADServiceAccount`
`Test-ADServiceAccount`
`Uninstall-ADServiceAccount`

## <a name="move-to-smsas"></a>Passer à des sMSA

Si un service d'application prend en charge les sMSA mais pas les gMSA, et que vous utilisez actuellement un compte d'utilisateur ou un compte d'ordinateur pour le contexte de sécurité, vous devez [Créer et installer un sMSA](/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting) sur le serveur. 

L'idéal serait de déplacer les ressources vers Azure et d'utiliser des identités managées Azure ou des principaux de service.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la sécurisation des comptes de service, consultez les articles suivants :

* [Présentation des comptes de service locaux](service-accounts-on-premises.md)  
* [Sécuriser les comptes de service gérés de groupe](service-accounts-group-managed.md)  
* [Sécuriser les comptes d’ordinateur](service-accounts-computer.md)  
* [Sécuriser les comptes d’utilisateur](service-accounts-user-on-premises.md)  
* [Administrer les comptes de service locaux](service-accounts-govern-on-premises.md)
