---
title: Gestion des comptes de service Active Directory
description: Principes et procédures de gestion du cycle de vie des comptes de service dans Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d3f6163cc8c5f642835562c631d1712f329a788
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292526"
---
# <a name="governing-azure-ad-service-accounts"></a>Gestion des comptes de service Azure

Il existe trois types de comptes de service dans Azure Active Directory (Azure AD) : les [identités managées](service-accounts-managed-identities.md), les [principaux de service](service-accounts-principal.md)et les comptes d’utilisateur utilisés comme comptes de service. Lorsque vous créez ces comptes de service pour un usage automatisé, ils sont autorisés à accéder à des ressources dans Azure et Azure AD. Ces ressources peuvent être des services Microsoft 365, des applications de type logiciel en tant que service (software as a service, SaaS), des applications personnalisées, des bases de données, des systèmes RH, etc. Gérer des comptes de service Azure AD signifie que vous gérez leur création, leurs autorisations et leur cycle de vie pour garantir la sécurité et la continuité.

> [!IMPORTANT] 
> Nous vous déconseillons d’utiliser des comptes d’utilisateur en tant que comptes de service, car ils sont intrinsèquement moins sécurisés. Cela inclut les comptes de service locaux synchronisés avec Azure AD, car ils ne sont pas convertis en principaux de service. Au lieu de cela, nous recommandons d’utiliser des identités managées ou des principaux de service. Notez qu’à ce stade, l’utilisation de stratégies d’accès conditionnel n’est pas possible avec des principaux de service, mais la fonctionnalité sera bientôt disponible.


## <a name="plan-your-service-account"></a>Planifier votre compte de service

Avant de créer un compte de service ou d’inscrire une application, documentez les informations clés du compte de service. Une fois les informations documentées, il est plus facile de surveiller et de gérer efficacement le compte. Nous vous recommandons de collecter les données suivantes et de les suivre dans votre base de données de gestion de la configuration centralisée (CMDB).

| Données| Description| Détails |
| - | - | - |
| Propriétaire| Utilisateur ou groupe responsable de la gestion et de la surveillance du compte de service.| Fournissez au propriétaire les autorisations nécessaires pour surveiller le compte et implémenter un moyen d’atténuer les problèmes. L’atténuation de problème peut être effectuée par le propriétaire ou par l’envoi d’une demande adressée au service informatique. |
| Objectif| Mode d’utilisation du compte.| Mappez le compte de service à un service, une application ou un script spécifiques. Évitez de créer des comptes de service à usages multiples. |
| Autorisations (étendues)| Jeu d’autorisations attendu.| Documentez les ressources auxquelles il aura accès et les autorisations sur ces ressources. |
| Lien CMDB| Lien vers les ressources auxquelles accéder et les scripts dans lesquels le compte de service est utilisé.| Veillez à documenter la ressource et les propriétaires de script afin de pouvoir communiquer les effets en amont et en aval nécessaires des modifications. |
| Évaluation des risques| Risque et impact commercial si le compte venait à être compromis.| Utilisez ces informations pour restreindre l’étendue des autorisations et déterminer qui doit avoir accès aux informations du compte. |
| Période à réviser| Planification de la révision du compte de service par le propriétaire.| Utilisez-la pour planifier les révisions et les communications en lien avec celles-ci. Documentez ce qui doit se produire si une révision n’est pas effectuée dans un délai spécifique après la période de révision planifiée. |
| Durée de vie| Durée de vie maximale attendue du compte.| Utilisez-la pour planifier les communications à destination du propriétaire, et pour finalement désactiver, puis supprimer les comptes. Dans la mesure du possible, définissez une date d’expiration pour les informations d’identification, à laquelle celles-ci ne peuvent pas être restaurées automatiquement. |
| Nom| Nom de compte standardisé.| Créez un schéma de nommage pour tous les comptes de service afin de pouvoir facilement rechercher, trier et filtrer les comptes de service. |


## <a name="use-the-principle-of-least-privileges"></a>Utiliser le principe du privilège minimum
Accordez au compte de service uniquement les autorisations nécessaires pour accomplir ses tâches. Si un compte de service a besoin d’autorisations de haut niveau, par exemple, un niveau de privilège d’administrateur général, évaluez pourquoi et essayez de réduire les autorisations nécessaires.

Nous vous recommandons les pratiques suivantes pour les privilèges de compte de service.

**autorisations**

* N’assignez pas de rôles intégrés aux comptes de service. Au lieu de cela, utilisez le [modèle d’octroi d’autorisation OAuth2 pour Microsoft Graph](/graph/api/resources/oauth2permissiongrant).

* Si un rôle privilégié doit être attribué au principal du service, envisagez d’attribuer un [rôle personnalisé](../roles/custom-create.md) avec des privilèges requis spécifiques en fonction de la durée.

* N’incluez pas de comptes de service en tant que membres de groupes dotés d’autorisations élevées. 

* [Utilisez PowerShell pour énumérer les membres des rôles privilégiés](/powershell/module/azuread/get-azureaddirectoryrolemember), par exemple   
`Get-AzureADDirectoryRoleMember` et filtrez sur l’objectType « Principal du service ».

   Ou utilisez  
‎   `Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

* [Utilisez des étendues OAuth 2.0](../develop/v2-permissions-and-consent.md) pour limiter la fonctionnalité à laquelle un compte de service peut accéder sur une ressource.
* Les principaux de service et les identités managées peuvent utiliser des étendues OAuth 2.0 dans un contexte délégué qui emprunte l’identité d’un utilisateur connecté, ou en tant que compte de service dans le contexte de l’application. Dans le contexte de l’application, aucun n’est connecté.

* Vérifiez les étendues que les comptes de service demandent pour les ressources afin de vous assurer qu’elles sont appropriées. Par exemple, si un compte demande l’étendue Files.ReadWrite.All, évaluez s’il n’a en réalité pas uniquement besoin de l’étendue File.Read.All. Pour plus d’informations sur les autorisations, consultez [Référence d’autorisation Microsoft Graph](/graph/permissions-reference).

* Assurez-vous que vous faites confiance au développeur de l’application ou de l’API disposant de l’accès demandé à vos ressources.

**Durée**

* Limitez les informations d’identification du compte de service (clé secrète client, certificat) à une période d’utilisation prévue.

* Planifiez des révisions périodiques de l’utilisation et de l’objectif des comptes de service. Vérifiez que les révisions sont effectuées avant l’expiration du compte.

Une fois que vous avez une compréhension claire de l’objectif, de l’étendue et des autorisations nécessaires, créez votre compte de service. 

[Créer et utiliser des identités managées](../../app-service/overview-managed-identity.md?tabs=dotnet)

[Créer et utiliser des principaux de service](../develop/howto-create-service-principal-portal.md)

Si possible, utilisez une identité managée. Si vous ne pouvez pas utiliser une identité managée, utilisez un principal de service. Si vous ne pouvez pas utiliser un principal du service, utilisez uniquement un compte d’utilisateur Azure AD.

 

## <a name="build-a-lifecycle-process"></a>Créer un processus de gestion du cycle de vie

La gestion du cycle de vie d’un compte de service commence par sa planification et se termine par sa suppression définitive. 

Cet article a déjà abordé la partie planification et création. Vous devez également surveiller le compte, réviser ses autorisations, déterminer son utilisation, puis le déprovisionner.

### <a name="monitor-service-accounts"></a>Surveiller des comptes de service

Surveillez de manière proactive vos comptes de service pour vous assurer que les modèles d’utilisation de ceux-ci correspondent aux modèles prévus et que le compte de service est toujours utilisé activement.

**Collectez et surveillez les connexions au compte de service à l’aide de l’une des méthodes suivantes :**

* utilisation des journaux de connexion Azure AD dans le portail Azure AD ;

* exportation des journaux de connexion Azure AD vers le service [Stockage Azure](../../storage/index.yml), [Azure Event Hubs](../../event-hubs/index.yml) ou [Azure Monitor](../../azure-monitor/logs/data-platform-logs.md).


![Capture d’écran montrant l’écran de connexion du principal de service.](./media/securing-service-accounts/service-accounts-govern-azure-1.png)

**Les renseignements que vous devez rechercher dans les journaux de connexion sont les suivants :**

* Existe-t-il des comptes de service qui ne se connectent plus au locataire ?

* Les modèles de connexion des comptes de service changent-ils ?

Nous vous recommandons d’exporter les journaux de connexion Azure AD et de les importer dans vos outils d’informations de sécurité et gestion d’événements (Security Information and Event Management, SIEM) existants, tels que Microsoft Sentinel. Utilisez votre outil SIEM pour créer des alertes et des tableaux de bord.

### <a name="review-service-account-permissions"></a>Réviser les autorisations du compte de service

Révisez régulièrement les autorisations accordées et les étendues auxquelles accèdent les comptes de service pour voir s’il est possible de les réduire ou de les éliminer.

* Utilisez [PowerShell](/powershell/module/azuread/get-azureadserviceprincipaloauth2permissiongrant) afin de [créer une automatisation de la vérification et de la documentation](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) des étendues du consentement accordé à un compte de service.

* Utilisez PowerShell pour [réviser les informations d’identification des principaux de service existants](https://github.com/AzureAD/AzureADAssessment) et vérifier leur validité.

* Ne définissez pas les informations d’identification du principal de service sur « Aucune expiration ».

* Autant que possible, utilisez des certificats ou des informations d’identification stockés dans Azure Key Vault.

L’exemple PowerShell gratuit de Microsoft collecte les autorisations OAuth2 et les informations d’identification du principal de service, les enregistre dans un fichier de valeurs séparées par des virgules (CSV) et fournit un exemple de tableau de bord Power BI pour interpréter et utiliser les données. Pour plus d’informations, consultez [AzureAD/AzureADAssessment : outils pour évaluer l’état et la configuration d’un locataire Azure AD (github.com)](https://github.com/AzureAD/AzureADAssessment).

### <a name="recertify-service-account-use"></a>Recertifier l’utilisation du compte de service

Établissez un processus de révision pour vous assurer que les comptes de service sont révisés par leurs propriétaires et par la sécurité ou l’équipe informatique à intervalles réguliers. 

**Le processus doit inclure les éléments suivants :**

* Comment déterminer le cycle de révision de chaque compte de service (doit être documenté dans votre CMDB).

* Communications adressées au propriétaire et à la sécurité ou aux équipes informatiques avant le début des révisions.

* Minutage et contenu des communications d’avertissement si la révision est manquée.

* Instructions sur la marche à suivre si les propriétaires ne révisent pas ou ne répondent pas. Par exemple, vous pouvez désactiver (pas supprimer) le compte jusqu’à ce que la révision soit terminée.

* Instructions relatives à la détermination des dépendances en amont et en aval, ainsi qu’à la notification d’effets éventuels aux autres propriétaires des ressources.

**La révision doit mettre à contribution le propriétaire et son partenaire informatique certifiant les points suivants :**

* Le compte est toujours nécessaire.

* Les autorisations accordées au compte sont adéquates et nécessaires, ou une modification est demandée.

* L’accès au compte et à ses informations d’identification est contrôlé.

* Les informations d’identification que le compte utilise sont appropriées eu égard au risque pour lequel le compte a été évalué (type d’informations d’identification et durée de vie de celles-ci).

* Le scoring de risque du compte n’a pas changé depuis la dernière recertification.

* Une mise à jour sur la durée de vie attendue du compte et sur la prochaine date de recertification.

### <a name="deprovision-service-accounts"></a>Déprovisionner des comptes de service

**Déprovisionnez des comptes de service dans les circonstances suivantes :****

* Le script ou l'application pour lequel le compte de service a été créé est mis hors service.

* La fonction au sein du script ou de l’application pour laquelle le compte de service est utilisé (par exemple, accès à une ressource spécifique) est mise hors service.


* Le compte de service est remplacé par un autre.

* Les informations d’identification ont expiré ou le compte n’est pas fonctionnel, et il n’y a aucune réclamation.

**Les processus de déprovisionnement doivent inclure les tâches suivantes.**

1. Une fois l’application ou le script associés déprovisionnés, [surveillez les connexions](../reports-monitoring/concept-sign-ins.md) et l’accès du compte de service aux ressources.

   * Si le compte est toujours actif, déterminez la façon dont il est utilisé avant de passer au étapes suivantes.
 
2. S’il s’agit d’une identité de service managée, désactivez la connexion du compte de service, mais ne supprimez pas celui-ci de l’annuaire.

3. Révoquez les attributions de rôles et les octrois de consentement OAuth2 pour le compte de service.

4. Après une période définie et une fois les propriétaires amplement avertis, supprimez le compte de service de l’annuaire.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la sécurisation des comptes de service Azure, consultez les rubriques suivantes :

[Présentation des comptes de service Azure](service-accounts-introduction-azure.md)

[Sécurisation des identités managées](service-accounts-managed-identities.md)

[Sécurisation des principaux de service](service-accounts-principal.md)




 
