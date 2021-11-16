---
title: Présentation de la sécurité Azure Key Vault
description: Vue d’ensemble des fonctionnalités de sécurité et des meilleures pratiques pour Azure Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: ccd2e196f1e1a44a79f0d4f9f1f07e4a69f2b974
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053119"
---
# <a name="azure-key-vault-security"></a>Sécurité d’Azure Key Vault

Azure Key Vault protège les clés de chiffrement, les certificats (et les clés privées associées aux certificats) et les secrets (tels que les chaînes de connexion et les mots de passe) dans le cloud. Toutefois, lors du stockage de données sensibles et critiques pour l’entreprise, vous devez prendre des mesures afin d’optimiser la sécurité de vos coffres et des données qui y sont stockées.

Cet article fournit une vue d’ensemble des fonctionnalités de sécurité et des meilleures pratiques pour Azure Key Vault.

> [!NOTE]
> Pour obtenir la liste complète des recommandations en matière de sécurité d'Azure Key Vault, consultez [Base de référence de sécurité pour Azure Key Vault](security-baseline.md).

## <a name="network-security"></a>Sécurité du réseau

Vous pouvez réduire l’exposition de vos coffres en spécifiant les adresses IP qui y ont accès. Les points de terminaison de service de réseau virtuel pour Azure Key Vault permettent de restreindre l’accès à un réseau virtuel spécifié. Les points de terminaison vous permettent également de restreindre l’accès à une liste de plages d’adresses IPv4 (Internet Protocol version 4). L’accès est refusé à tout utilisateur se connectant à votre coffre de clés en dehors de ces sources.  Pour plus d’informations, consultez [Points de terminaison de service de réseau virtuel pour Azure Key Vault](overview-vnet-service-endpoints.md).

Une fois que les règles de pare-feu sont effectives, les utilisateurs peuvent lire les données de Key Vault seulement quand leurs requêtes proviennent de réseaux virtuels ou de plages d’adresses IPv4 autorisés. Ceci s’applique également à l’accès au coffre de clés à partir du portail Azure. Si des utilisateurs peuvent accéder à un coffre de clés à partir du portail Azure, il ne peuvent pas lister les clés/secrets/certificats si leur ordinateur client ne figure pas dans la liste autorisée. Pour connaître les étapes d’implémentation, consultez [Configurer les pare-feu et réseaux virtuels d’Azure Key Vault](network-security.md).

Le service Azure Private Link vous permet d’accéder à Azure Key Vault et aux services clients/partenaires hébergés sur Azure via un point de terminaison privé de votre réseau virtuel. Un point de terminaison privé Azure est une interface réseau qui vous connecte de façon privée et sécurisée à un service basé sur la technologie Azure Private Link. Le point de terminaison privé utilise une adresse IP privée de votre réseau virtuel, plaçant de fait le service dans votre réseau virtuel. Sachant que l’ensemble du trafic à destination du service peut être routé via le point de terminaison privé, il n’y a aucun besoin de passerelles, d’appareils NAT, de connexions ExpressRoute ou VPN ou d’adresses IP publiques. Le trafic entre votre réseau virtuel et le service transite par le réseau principal de Microsoft, éliminant ainsi toute exposition à l’Internet public. Vous pouvez vous connecter à une instance d’une ressource Azure, ce qui vous donne le plus haut niveau de granularité en matière de contrôle d’accès.  Pour connaître les étapes d’implémentation, consultez [Intégrer Key Vault à Azure Private Link](private-link-service.md).

## <a name="tls-and-https"></a>TLS et HTTPS

- Le serveur frontal Key Vault (plan de données) est un serveur multilocataire. Cela signifie que les coffres de clés de différents clients peuvent partager la même IP publique. À des fins d’isolement, chaque requête HTTP est authentifiée et autorisée indépendamment des autres requêtes.
- Vous pouvez identifier des versions antérieures de TLS pour signaler des vulnérabilités, mais, comme l’IP publique est partagée, il n’est pas possible pour l’équipe du service des coffres de clés de désactiver les anciennes versions de TLS pour les coffres de clés individuels au niveau du transport.
- Le protocole HTTPS permet au client de participer à la négociation TLS. **Les clients peuvent appliquer la version la plus récente de TLS** et, chaque fois qu’un client le fait, l’ensemble de la connexion utilise le niveau de protection correspondant. Les applications qui communiquent avec ou s’authentifient sur Azure Active Directory peuvent ne pas fonctionner comme prévu si elles ne peuvent pas utiliser TLS 1.2 ou une version récente pour communiquer.
- Malgré les vulnérabilités connues du protocole TLS, il n’existe aucune attaque connue qui permettrait à un agent malveillant d’extraire des informations de votre coffre de clés lorsque l’attaquant établit une connexion avec une version de TLS qui présente des vulnérabilités. L’attaquant doit toujours s’authentifier et s’autoriser et, tant que les clients légitimes se connectent toujours avec des versions récentes de TLS, il est impossible que des informations d’identification aient pu être divulguées à partir de vulnérabilités d’anciennes versions de TLS.

> [!NOTE]
> Pour Azure Key Vault, vérifiez que l’application qui accède au service Keyvault s’exécute sur une plateforme qui prend en charge TLS 1.2 ou une version récente. Si l’application dépend de .NET Framework, elle doit également être mise à jour. Vous pouvez également apporter les modifications de Registre mentionnées dans [cet article](https://docs.microsoft.com/troubleshoot/azure/active-directory/enable-support-tls-environment) pour activer explicitement l’utilisation de TLS 1.2 au niveau du système d’exploitation et pour .NET Framework.

## <a name="key-vault-authentication-options"></a>Options d’authentification Key Vault

Quand vous créez un coffre de clés dans un abonnement Azure, il est automatiquement associé au locataire Azure AD de l’abonnement. Tous les appelants dans les deux plans doivent s’inscrire auprès de ce locataire et s’authentifier pour accéder au coffre de clés. Dans les deux cas, les applications peuvent accéder au coffre de clés de trois manières :

- **Application uniquement** : L’application représente un principal du service ou une identité managée. Cette identité est le scénario le plus courant pour les applications qui doivent accéder régulièrement à des certificats, des clés ou des secrets à partir du coffre de clés. Pour que ce scénario fonctionne, la propriété `objectId` de l’application doit être spécifiée dans la stratégie d’accès et `applicationId` ne doit _pas_ être spécifiée ou doit être `null`.
- **Utilisateur uniquement** : l’utilisateur accède au coffre de clés à partir de n’importe quelle application inscrite dans le locataire. Azure PowerShell et le portail Azure sont des exemples de ce type d’accès. Pour que ce scénario fonctionne, la propriété `objectId` de l’utilisateur doit être spécifiée dans la stratégie d’accès et `applicationId` ne doit _pas_ être spécifiée ou doit être `null`.
- **Application-plus-utilisateur** (parfois appelé _identité composée)_  : l’utilisateur est tenu d’accéder au coffre de clés à partir d’une application spécifique _et_ l’application doit utiliser le flux OBO (Authentification On-Behalf-Of) pour emprunter l’identité de l’utilisateur. Pour que ce scénario fonctionne, l’`applicationId` et l’`objectId` doivent être spécifiés dans la stratégie d’accès. `applicationId` identifie l’application requise et `objectId` identifie l’utilisateur. Actuellement, cette option n’est pas disponible pour le plan de données Azure RBAC.

Pour tous les types d’accès, l’application s’authentifie auprès d’Azure AD. L’application utilise une [méthode d’authentification prise en charge](../../active-directory/develop/authentication-vs-authorization.md) en fonction du type d’application. L’application acquiert un jeton pour une ressource dans le plan pour accorder l’accès. La ressource est un point de terminaison dans le plan de gestion ou de données, en fonction de l’environnement Azure. L’application utilise le jeton et envoie une demande d’API REST à Key Vault. Pour en savoir plus, passez en revue le [flux d’authentification intégral](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Le modèle d’un mécanisme d’authentification unique auprès des deux plans présente plusieurs avantages :

- Les organisations peuvent contrôler de manière centralisée l’accès à tous leurs coffres de clés.
- Si un utilisateur part, il perd instantanément l’accès à tous les coffres de clés de l’organisation.
- Les organisations peuvent personnaliser l’authentification à l’aide des options dans Azure AD, par exemple pour activer l’authentification multifacteur afin de renforcer la sécurité.

Pour plus d’informations, consultez [Concepts de base de l’authentification Key Vault](authentication.md).

## <a name="access-model-overview"></a>Vue d’ensemble du modèle d’accès

L’accès à un coffre de clés est contrôlé par le biais de deux interfaces : le **plan de gestion** et le **plan de données**. Le plan de gestion vous permet de gérer le coffre de clés. Dans ce plan, vous pouvez notamment créer et supprimer des coffres de clés, récupérer des propriétés Key Vault et mettre à jour des stratégies d’accès. Le plan de données vous permet d’utiliser les données stockées dans un coffre de clés. Vous pouvez ajouter, supprimer et modifier des clés, des secrets et des certificats.

Les deux plans utilisent [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) pour l’authentification. Pour l’autorisation, le plan de gestion utilise le [contrôle d’accès en fonction du rôle (Azure RBAC) Azure](../../role-based-access-control/overview.md) et le plan de données utilise une [stratégie d’accès Key Vault](./assign-access-policy-portal.md) et [Azure RBAC pour les opérations du plan de données Key Vault](./rbac-guide.md).

Pour accéder à un coffre de clés dans l’un ou l’autre de ces plans, tout appelant (utilisateur ou application) doit être authentifié et autorisé. L’authentification établit l’identité de l’appelant. L’autorisation détermine les opérations que l’appelant peut exécuter. L’authentification auprès de Key Vault fonctionne conjointement avec [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md), qui est chargé d’authentifier l’identité de chaque **principal de sécurité** donné.

Un principal de sécurité est un objet qui représente un utilisateur, un groupe, un service ou une application demandant l’accès aux ressources Azure. Azure affecte un **ID d’objet** unique à chaque principal de sécurité.

- Un principal de sécurité **utilisateur** identifie une personne disposant d’un profil dans Azure Active Directory.
- Un principal de sécurité **groupe** identifie un ensemble d’utilisateurs créés dans Azure Active Directory. Tous les rôles et autorisations attribués au groupe sont accordés à tous les utilisateurs du groupe.
- Un **principal de service** est un type de principal de sécurité qui identifie une application ou un service, c’est-à-dire un morceau de code plutôt qu’un utilisateur ou un groupe. L’ID d’objet d’un principal de service, appelé **ID client**, lui sert de nom d’utilisateur. La **clé secrète client** ou le **certificat** du principal de service fonctionne comme un mot de passe. De nombreux services Azure prennent en charge l’attribution de l’[identité managée](../../active-directory/managed-identities-azure-resources/overview.md) avec la gestion automatisée de l’**ID client** et du **certificat**. L’identité managée est l’option la plus sécurisée et recommandée pour l’authentification dans Azure.

Pour plus d’informations sur l’authentification sur Key Vault, consultez [S’authentifier auprès d’Azure Key Vault](authentication.md).

## <a name="conditional-access"></a>Accès conditionnel 

Key Vault prend en charge les stratégies d’accès conditionnel Azure Active Directory. En utilisant des stratégies d’accès conditionnel, vous pouvez appliquer les contrôles d’accès appropriés au Key Vault pour garantir la sécurité de votre organisation sans pour autant freiner inutilement votre utilisateur.

Pour plus d’informations, consultez [Vue d’ensemble de l’accès conditionnel](../../active-directory/conditional-access/overview.md).

## <a name="privileged-access"></a>Accès privilégié

L’autorisation détermine les opérations que l’appelant peut exécuter. L’autorisation dans Key Vault utilise une combinaison de [contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../../role-based-access-control/overview.md) et de stratégies d’accès Azure Key Vault.

L’accès aux coffres se produit via deux interfaces ou plans. Il s’agit du plan de gestion et du plan de données.

- Le *plan de gestion* est l’endroit où vous gérez Key Vault lui-même. C’est l’interface utilisée pour créer et supprimer des coffres. Vous pouvez également lire les propriétés de coffre de clés et gérer les stratégies d’accès.
- Le *plan de données* vous permet d’utiliser les données stockées dans un coffre de clés. Vous pouvez ajouter, supprimer et modifier des clés, des secrets et des certificats.

Les applications accèdent aux plans par le biais de points de terminaison. Les contrôles d’accès pour les deux plans fonctionnent indépendamment. Pour permettre à une application d’utiliser des clés dans un coffre de clés, vous accordez l’accès au plan de données à l’aide d’une stratégie d’accès Key Vault ou d’Azure RBAC. Pour permettre à un utilisateur de lire les propriétés et les étiquettes d’un coffre de clés, mais pas d’accéder aux données (clés, secrets ou certificats), vous accordez l’accès au plan de gestion avec Azure RBAC.

Le tableau suivant présente les points de terminaison pour les plans de gestion et de données.

| Plan&nbsp;d’accès | Points de terminaison d’accès | Opérations | Mécanisme de contrôle&nbsp;d’accès |
| --- | --- | --- | --- |
| Plan de gestion | **Mondial :**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government :**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany :**<br> management.microsoftazure.de:443 | Créer, lire, mettre à jour et supprimer des coffres de clés<br><br>Définir des stratégies d’accès Key Vault<br><br>Définir des étiquettes Key Vault | Azure RBAC |
| Plan de données | **Mondial :**<br> &lt;nom du coffre&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;nom du coffre&gt;.vault.azure.cn:443<br><br> **Azure US Government :**<br> &lt;nom du coffre&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany :**<br> &lt;nom du coffre&gt;.vault.microsoftazure.de:443 | Clés : chiffrer, déchiffrer, wrapKey, unwrapKey, signer, vérifier, obtenir, lister, créer, mettre à jour, importer, supprimer, récupérer, sauvegarder, restaurer, purger<br><br> Certificats : managecontacts, getissuers, listissuers, setissurs, deleteissuers, manageissuers, obtenir, lister, créer, importer, mettre à jour, supprimer, récupérer, sauvegarder, restaurer, purger<br><br>  Secrets : obtenir, lister, définir, supprimer, récupérer, sauvegarder, restaurer, purger | Stratégie d’accès Key Vault ou Azure RBAC |

### <a name="managing-administrative-access-to-key-vault"></a>Gestion de l’accès administratif à Key Vault

Lorsque vous créez un coffre de clés dans un groupe de ressources, vous gérez l’accès à l’aide d’Azure AD. Vous autorisez des utilisateurs ou des groupes à gérer les coffres de clés dans un groupe de ressources. Vous pouvez accorder l’accès à un niveau d’étendue spécifique en attribuant les rôles Azure appropriés. Pour permettre à un utilisateur de gérer des coffres de clés, vous attribuez un rôle `key vault Contributor` prédéfini à l’utilisateur dans une étendue spécifique. Les niveaux d’étendue suivants peuvent être attribués à un rôle Azure :

- **Abonnement**: Un rôle Azure attribué au niveau d’un abonnement s’applique à tous les groupes de ressources et à toutes les ressources au sein de cet abonnement.
- **Groupe de ressources** : Un rôle Azure attribué au niveau d’un groupe de ressources s’applique à toutes les ressources de ce groupe de ressources.
- **Ressource spécifique** : Un rôle Azure attribué pour une ressource spécifique s’applique à cette ressource. Dans ce cas, la ressource est un coffre de clés spécifique.

Il existe plusieurs rôles prédéfinis. Si un rôle prédéfini ne répond pas à vos besoins, vous pouvez définir votre propre rôle. Pour plus d’informations, consultez [Azure RBAC : pour les ressources Azure](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Si un utilisateur dispose d’autorisations `Contributor` sur un plan de gestion de coffre de clés, il peut s’accorder lui-même l’accès au plan de données en définissant une stratégie d’accès Key Vault. Vous devez contrôler étroitement qui dispose d’un accès accordé par le rôle `Contributor` à vos coffres de clés. Vérifiez que seules les personnes autorisées peuvent accéder à et gérer vos coffres de clés, vos clés, vos secrets et vos certificats.

### <a name="controlling-access-to-key-vault-data"></a>Contrôle de l’accès aux données de Key Vault

Les stratégies d’accès Key Vault accordent des autorisations distinctement aux clés, secrets ou certificats. Vous pouvez uniquement accorder un accès utilisateur à des clés (pas à des secrets). Les autorisations d’accès aux clés, secrets et certificats sont gérées au niveau du coffre.

> [!IMPORTANT]
> Les stratégies d’accès Key Vault ne prennent pas en charge les autorisations granulaires au niveau des objets, comme une clé, un secret ou un certificat spécifique. Quand un utilisateur est autorisé à créer et à supprimer des clés, il peut effectuer ces opérations sur toutes les clés de ce coffre de clés.

Vous pouvez définir des stratégies d’accès pour un coffre de clés à l’aide du [portail Azure](assign-access-policy-portal.md), de l’interface [Azure CLI](assign-access-policy-cli.md), d’[Azure PowerShell](assign-access-policy-powershell.md) ou des [API REST de gestion Key Vault](/rest/api/keyvault/).

## <a name="logging-and-monitoring"></a>Enregistrement et surveillance

La journalisation de Key Vault enregistre les informations sur les activités effectuées sur votre coffre. Pour plus d’informations, consultez [Journalisation de Key Vault](logging.md).

Vous pouvez intégrer Key Vault à Event Grid pour être averti lorsque l’état d’une clé, d’un certificat ou d’un secret stocké dans le coffre de clés a changé. Pour plus d’informations, consultez [Supervision de Key Vault avec Azure Event Grid](event-grid-overview.md).

Il est également important de surveiller l’intégrité de votre coffre de clés pour vous assurer que votre service fonctionne comme prévu. Pour savoir comment procéder, consultez [Surveillance et alertes Azure Key Vault](alert.md).

## <a name="backup-and-recovery"></a>Sauvegarde et récupération

La suppression réversible et la protection contre la suppression définitive d’Azure Key Vault permettent de récupérer des coffres et des objets de coffre supprimés. Pour plus d’informations, consulter [Vue d’ensemble de la suppression réversible d’Azure Key Vault](soft-delete-overview.md).

Vous devez également effectuer des sauvegardes régulières de votre coffre, notamment lors de la mise à jour, de la suppression ou de la création d’objets au sein d’un coffre.  

## <a name="next-steps"></a>Étapes suivantes

- [Base de référence de sécurité pour Azure Key Vault](security-baseline.md)
- [Bonnes pratiques relatives à Azure Key Vault](security-baseline.md)
- [Points de terminaison de service de réseau virtuel pour Azure Key Vault](overview-vnet-service-endpoints.md)
- [RBAC Azure : Rôles intégrés](../../role-based-access-control/built-in-roles.md)
