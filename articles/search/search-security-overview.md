---
title: Présentation de la sécurité
titleSuffix: Azure Cognitive Search
description: Découvrez les fonctionnalités de sécurité de Recherche cognitive Azure pour protéger les points de terminaison, le contenu et les opérations.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/15/2021
ms.custom: references_regions
ms.openlocfilehash: a4ed9b5a7d33433a14bb7e6800ac54326184a808
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347082"
---
# <a name="security-overview-for-azure-cognitive-search"></a>Vue d’ensemble de la sécurité dans Recherche cognitive Azure

Cet article décrit les fonctionnalités de sécurité de Recherche cognitive Azure qui protègent les données et les opérations.

## <a name="network-traffic-patterns"></a>Modèles de trafic réseau

Un service de recherche est hébergé sur Azure et généralement accessible via des connexions de réseau public. La compréhension des modèles d’accès du service peut vous aider à concevoir une stratégie de sécurité qui empêche efficacement l’accès non autorisé au contenu pouvant faire l’objet d’une recherche.

Recherche cognitive a trois modèles de trafic réseau de base :

+ Demandes entrantes effectuées par un client au service de recherche (modèle prédominant)
+ Demandes sortantes émises par le service de recherche pour d’autres services sur Azure et ailleurs
+ Demandes de service à service internes sur le réseau principal Microsoft sécurisé

Les demandes entrantes vont de la création d’objets, au chargement et à l’interrogation des données. Pour l’accès entrant aux données et aux opérations, vous pouvez implémenter une progression des mesures de sécurité, en commençant par les clés API sur la demande. Vous pouvez ensuite compléter avec des règles de trafic entrant dans un pare-feu IP ou créer des points de terminaison privés qui protègent entièrement votre service de l’Internet public.

Les demandes sortantes peuvent inclure des opérations de lecture et d’écriture. L’agent principal d’un appel sortant est un indexeur et un composant d’ensembles de compétences. Pour les indexeurs, les opérations de lecture incluent le [craquage de documents](search-indexer-overview.md#document-cracking) et l’ingestion de données. Un indexeur peut également écrire dans stockage Azure lors de la création de bases de connaissances, de la persistance des enrichissements mis en cache et de la persistance des sessions de débogage. Enfin, une compétence peut également inclure des compétences personnalisées qui exécutent du code externe, par exemple dans Azure Functions ou dans une application Web.

Les demandes internes incluent des appels de service à service pour des tâches telles que la journalisation des diagnostics, le chiffrement, l’authentification et l’autorisation via Azure Active Directory, les connexions de point de terminaison privées et les demandes adressées à Cognitive Services pour les compétences intégrées.

## <a name="network-security"></a>Sécurité du réseau

<a name="service-access-and-authentication"></a>

Les fonctionnalités de sécurité du trafic entrant protègent le point de terminaison du service de recherche via des niveaux croissants de sécurité et de complexité. Recherche cognitive utilise [l’authentification basée sur les clés](search-security-api-keys.md), où toutes les demandes requièrent une clé API pour l’accès authentifié.

Si vous le souhaitez, vous pouvez implémenter des couches supplémentaires de contrôle en définissant des règles de pare-feu qui limitent l’accès à des adresses IP spécifiques. Pour une protection avancée, vous pouvez activer Azure Private Link pour protéger votre point de terminaison de service de tout le trafic Internet.

### <a name="connect-over-the-public-internet"></a>Connecter par le biais de l’Internet public

Par défaut, un point de terminaison de service de recherche est accessible via le cloud public, en utilisant une authentification basée sur des clés pour l’administration ou l’accès aux requêtes au point de terminaison du service de recherche. Des clés sont requises. La soumission d’une clé valide est considérée comme la preuve que la requête provient d’une entité approuvée. L’authentification basée sur une clé est traitée dans la section suivante. Sans clés d’API, vous obtiendrez les réponses 401 et 404 sur la demande.

### <a name="connect-through-ip-firewalls"></a>Connecter par le biais de pare-feu IP

Pour contrôler davantage l’accès à votre service de recherche, vous pouvez créer des règles de pare-feu de trafic entrant qui autorisent l’accès à une adresse IP spécifique ou à une plage d’adresses IP. Toutes les connexions clientes doivent être effectuées via une adresse IP autorisée, sans quoi la connexion est refusée.

:::image type="content" source="media/search-security-overview/inbound-firewall-ip-restrictions.png" alt-text="Exemple de diagramme d’architecture pour l’accès restreint à des adresses IP":::

Vous pouvez utiliser le portail pour [configurer l’accès du trafic entrant](service-configure-firewall.md).

Vous pouvez aussi utiliser les API REST de gestion. À compter de la version 13-03-2020 de l’API avec le paramètre [IpRule](/rest/api/searchmanagement/2020-08-01/services/create-or-update#iprule), vous pouvez limiter l’accès à votre service en identifiant les adresses IP, individuellement ou dans une plage, qui doivent pouvoir accéder à votre service de recherche.

### <a name="connect-to-a-private-endpoint-network-isolation-no-internet-traffic"></a>Connecter à un point de terminaison privé (isolement réseau, aucun trafic Internet)

Vous pouvez établir un [point de terminaison privé](../private-link/private-endpoint-overview.md) pour le service Recherche cognitive Azure afin de permettre à un client sur un [réseau virtuel](../virtual-network/virtual-networks-overview.md) d’accéder de façon sécurisée aux données d’un index de recherche via une [liaison privée](../private-link/private-link-overview.md).

Le points de terminaison privé utilise une adresse IP de l’espace d’adressage du réseau virtuel pour les connexions à votre service de recherche. Le trafic entre le client et le service Search traverse le réseau virtuel et une liaison privée sur le réseau principal de Microsoft, ce qui élimine l’exposition sur l’Internet public. Un réseau virtuel permet une communication sécurisée entre des ressources, avec votre réseau local ainsi qu’avec Internet.

:::image type="content" source="media/search-security-overview/inbound-private-link-azure-cog-search.png" alt-text="Exemple de diagramme d’architecture pour l’accès au point de terminaison privé":::

Bien que cette solution soit la plus sécurisée, l’utilisation de services supplémentaires représente un coût supplémentaire : veillez donc à avoir une compréhension claire des avantages avant de la mettre en place. Pour plus d’informations sur les coûts, consultez la [page Tarification](https://azure.microsoft.com/pricing/details/private-link/). Pour plus d’informations sur la façon dont ces composants fonctionnent ensemble, regardez la vidéo en haut de cet article. L’option du point de terminaison privé est présentée à partir de 5:48 dans la vidéo. Pour obtenir des instructions sur la configuration du point de terminaison, consultez [Créer un point de terminaison privé pour Recherche cognitive Azure](service-create-private-endpoint.md).

### <a name="outbound-connections-to-external-services"></a>Connexions sortantes vers des services externes

Les indexeurs et les ensembles de compétences sont des objets qui peuvent établir des connexions externes. Vous fournissez des informations de connexion dans le cadre de la définition de l’objet, à l’aide de l’un de ces mécanismes.

+ Informations d’identification dans la chaîne de connexion

+ Identité managée dans la chaîne de connexion

  Vous pouvez configurer une identité managée pour effectuer une recherche dans un service approuvé lors de l’accès à des données des services Stockage Azure, Azure SQL, Cosmos DB ou d’autres sources de données Azure. Une identité managée est un substitut pour les informations d’identification ou les clés d’accès sur la connexion. Pour plus d’informations sur cette fonctionnalité, consultez [Se connecter à une source de données en utilisant une identité managée](search-howto-managed-identities-data-sources.md).

## <a name="authentication"></a>Authentication

Pour les demandes entrantes adressées au service de recherche, l’authentification s’effectue à l’aide d’une [clé API](search-security-api-keys.md) (chaîne composée de chiffres et de lettres générés de manière aléatoire) qui prouve que la demande émane d’une source digne de confiance. En guise d’alternative, il existe une nouvelle prise en charge de l’authentification Azure Active Directory et de l’autorisation basée sur les rôles, [actuellement en version préliminaire](search-security-rbac.md).

Les demandes sortantes effectuées par un indexeur sont soumises à une authentification par le service externe. Le sous-service d’indexeur du service Recherche cognitive peut être un service approuvé sur Azure qui se connecte à d’autres services à l’aide d’une identité managée. Pour plus d’informations, consultez [Configurer une connexion d’indexeur à une source de données à l’aide d’une identité managée](search-howto-managed-identities-data-sources.md).

## <a name="authorization"></a>Autorisation

Le service Recherche cognitive fournit différents modèles d’autorisation pour la gestion de contenu et le management des services. 

### <a name="authorization-for-content-management"></a>Autorisation pour la gestion de contenu

L’autorisation d’accès au contenu et aux opérations liées au contenu est un accès en écriture, tel que le confère la [clé API](search-security-api-keys.md) fournie sur la demande. La clé API est un mécanisme d’authentification qui autorise également l’accès en fonction du type de clé API.

+ clé d’administration (autorisant l’accès en lecture/écriture pour les opérations de création/lecture/mise à jour/suppression sur le service de recherche) créée lors de l’approvisionnement du service ;

+ clé de requête (autorisant l’accès en lecture seule à la collection documents d’un index) créée en fonction des besoins et conçue pour les applications clientes qui émettent des requêtes.

Dans le code de l’application, vous spécifiez le point de terminaison et une clé API pour autoriser l’accès au contenu et aux options. Un point de terminaison peut être le service proprement dit, la collection d’index, un index spécifique, une collection de documents ou un document spécifique. Quand ils sont chaînés, le point de terminaison, l’opération (par exemple, une demande de création ou de mise à jour) et le niveau d’autorisation (droits complets ou de lecture seule en fonction de la clé) constituent ensemble la formule de sécurité qui protège le contenu et les opérations.

> [!NOTE]
> L’autorisation pour les opérations de plan de données à l’aide du contrôle d’accès en fonction du rôle (RBAC) Azure est désormais en préversion. Vous pouvez utiliser cette fonctionnalité en préversion si vous souhaitez [utiliser des attributions de rôles au lieu de clés API](search-security-rbac.md).

### <a name="controlling-access-to-indexes"></a>Contrôle de l’accès aux index

Dans Recherche cognitive Azure, les index individuels ne sont pas des objets sécurisables. Au lieu de cela, l’accès à un index est déterminé au niveau de la couche de service (accès en lecture ou en écriture en fonction de la clé API que vous fournissez), avec le contexte d’une opération.

Pour l’accès en lecture seule, vous pouvez structurer les demandes de requête pour qu’elles se connectent à l’aide d’une [clé de requête](search-security-api-keys.md) et incluent l’index spécifique que votre application utilise. Dans une demande de requête, il est impossible de joindre des index ou d’accéder simultanément à plusieurs index. Ainsi, toutes les demandes ciblent un index unique par définition. Par conséquent, la structure de la demande de requête proprement dite (une clé plus un index unique cible) définit la limite de sécurité.

Il n’existe aucune différence entre l’accès administrateur et l’accès développeur aux index : tous deux doivent disposer d’un accès en écriture pour pouvoir créer, supprimer et mettre à jour des objets gérés par le service. Toute personne disposant d’une [clé d’administration](search-security-api-keys.md) pour votre service peut lire, modifier ou supprimer un index de ce service. En ce qui concerne la protection contre la suppression accidentelle ou malveillante d’index, votre contrôle de code source en interne pour les ressources de code est la solution appropriée pour annuler des suppressions ou des modifications d’index indésirables. Recherche cognitive Azure dispose d’un système de basculement dans le cluster pour garantir sa disponibilité, mais il ne stocke pas et n’exécute pas le code propriétaire que vous avez utilisé pour créer ou charger des index.

Pour les solutions d’architecture mutualisée qui nécessitent des limites de sécurité au niveau des index, ces solutions incluent généralement un niveau intermédiaire, que les clients utilisent pour gérer l’isolation des index. Pour plus d’informations sur les cas d’usage d’architecture mutualisée, consultez [Modèles de conception pour les applications SaaS mutualisées et Recherche cognitive Azure](search-modeling-multitenant-saas-applications.md).

### <a name="controlling-access-to-documents"></a>Contrôle de l’accès aux documents

Si vous avez besoin d’un contrôle précis par utilisateur sur les résultats de la recherche, vous pouvez créer des filtres de sécurité sur vos requêtes, en retournant des documents associés à une identité de sécurité donnée. 

Conceptuellement équivalent à la « sécurité au niveau des lignes », l’autorisation d’accès au contenu dans l’index n’est pas prise en charge en mode natif à l’aide de rôles prédéfinis ou d’attributions de rôles qui mappent à des entités dans Azure Active Directory. Les autorisations utilisateur sur des données dans des systèmes externes tels que Cosmos DB ne sont pas transférées avec ces données, car celles-ci sont indexées par le service Recherche cognitive.

Parmi les solutions de contournement pour les solutions qui nécessitent une « sécurité au niveau des lignes » figurent la création d’un champ dans la source de données, qui représente un groupe de sécurité ou une identité d’utilisateur, puis l’utilisation de filtres dans le service Recherche cognitive pour réduire de manière sélective des résultats de recherche de documents et de contenus en fonction des identités. Le tableau suivant décrit les deux approches permettant de filtrer les résultats de recherche de contenu non autorisé.

| Approche | Description |
|----------|-------------|
|[Filtrage de sécurité basé sur les filtres d’identité](search-security-trimming-for-azure-search.md)  | Cet article décrit le workflow de base pour l’implémentation du contrôle d’accès basé sur l’identité de l’utilisateur. Il décrit l’ajout d’identificateurs de sécurité à un index, puis le filtrage relatif à ce champ qui permet d’omettre les résultats de contenu non autorisé. |
|[Filtrage de sécurité basé sur les identités Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | Cet article est une extension de l’article précédent, indiquant les étapes à suivre pour récupérer des identités à partir d’Azure Active Directory (Azure AD), l’un des [services gratuits](https://azure.microsoft.com/free/) de la plateforme cloud Azure. |

### <a name="authorization-for-service-management"></a>Autorisation pour le management des services

Les opérations de management des services sont autorisées via un [contrôle d’accès en fonction du rôle Azure (RBAC Azure)](../role-based-access-control/overview.md). Le RBAC Azure est un système d’autorisation basé sur [Azure Resource Manager](../azure-resource-manager/management/overview.md) pour l’approvisionnement de ressources Azure. 

Dans Recherche cognitive Azure , Resource Manager est utilisé pour créer ou supprimer le service, gérer les clés API et mettre à l’échelle le service. Ainsi, les attributions de rôles Azure déterminent qui peut effectuer ces tâches, qu’elles utilisent le [portail](search-manage.md), [PowerShell](search-manage-powershell.md) ou les [API REST de gestion](/rest/api/searchmanagement).

[Trois rôles de base](search-security-rbac.md) sont définis pour l’administration du service de recherche. Les attributions de rôles peuvent être effectuées à l’aide de toute méthodologie prise en charge (portail, PowerShell, etc.) et sont honorées dans l’ensemble du service. Les rôles Propriétaire et Contributeur peuvent exercer toute une série de fonctions d’administration. Vous pouvez attribuer le rôle Lecteur à des utilisateurs qui ne voient que des informations essentielles.

> [!Note]
> En utilisant des mécanismes à l’échelle d’Azure, vous pouvez verrouiller un abonnement ou une ressource pour empêcher la suppression accidentelle ou non autorisée de votre service de recherche par les utilisateurs disposant de droits d’administration. Pour plus d’informations, consultez [Verrouiller les ressources pour en empêcher la suppression](../azure-resource-manager/management/lock-resources.md).

<a name="encryption"></a>

## <a name="data-protection"></a>Protection des données

Au niveau de la couche de stockage, le chiffrement des données est intégré pour l’ensemble du contenu géré par le service enregistré sur le disque, dont les index, les cartes de synonymes et les définitions d’indexeurs, de sources de données et d’ensembles de compétences. Si vous le souhaitez, vous pouvez ajouter des clés gérées par le client (CMK) pour un chiffrement supplémentaire du contenu indexé. Pour les services créés après le 1er août 2020, le chiffrement CMK s’étend aux données sur les disques temporaires, pour le « double chiffrement » complet du contenu indexé.

### <a name="data-in-transit"></a>Données en transit

Dans Recherche cognitive Azure, le chiffrement commence aux connexions et aux transmissions, et s’étend au contenu stocké sur disque. Pour les services de recherche sur l’Internet public, Recherche cognitive Azure écoute sur le port HTTPS 443. Toutes les connexions client-à-service utilisent le chiffrement TLS 1.2. Les versions antérieures (1.0 et 1.1) ne sont pas prises en charge.

### <a name="encrypted-data-at-rest"></a>Données chiffrées au repos

Pour les données gérées en interne par le service de recherche, le tableau suivant décrit les [modèles de chiffrement de données](../security/fundamentals/encryption-models.md). Certaines fonctionnalités, telles que la base de connaissances, l’enrichissement incrémentiel et l’indexation basée sur un indexeur, lisent ou écrivent dans des structures de données dans d’autres services Azure. Ces services disposent de leur propre niveau de prise en charge du chiffrement, distinct de Recherche cognitive Azure.

| Modèle | Clés&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Spécifications&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Restrictions | S’applique à |
|------------------|-------|-------------|--------------|------------|
| chiffrement côté serveur | Clés managées par Microsoft | Aucune (intégré) | Aucune, disponible pour tous les niveaux de service, dans toutes les régions, pour le contenu créé après le 24 janvier 2018. | Contenu (index et cartes de synonymes) et définitions (indexeurs, sources de données, ensembles de compétences) |
| chiffrement côté serveur | clés gérées par le client | Azure Key Vault | Disponible pour les niveaux de service facturables, dans toutes les régions, pour le contenu créé après janvier 2019. | Contenu (index et cartes de synonymes) sur les disques de données |
| double chiffrement côté serveur | clés gérées par le client | Azure Key Vault | Disponible sur les niveaux de service facturables, dans certaines régions, sur les services de recherche après le 1er août 2020. | Contenu (index et cartes de synonymes) sur les disques de données et les disques temporaires |

### <a name="service-managed-keys"></a>Clés gérées par le service

Le chiffrement géré par le service est une opération interne de Microsoft qui est basée sur [Azure Storage Service Encryption](../storage/common/storage-service-encryption.md) et qui utilise le [chiffrement AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 bits. Il se produit automatiquement lors de toutes les indexations, y compris lors des mises à jour incrémentielles des index qui ne sont pas entièrement chiffrés (créés avant janvier 2018).

### <a name="customer-managed-keys-cmk"></a>Clés gérées par le client (CMK)

Les clés gérées par le client nécessitent un service facturable supplémentaire, Azure Key Vault, qui peut se trouver dans une autre région que l’instance Recherche cognitive Azure, mais qui doit être sous le même abonnement. L’activation du chiffrement CMK a pour effet d’augmenter la taille de l’index et dégrader les performances des requêtes. Sur la base des observations effectuées à ce jour, vous pouvez vous attendre à une augmentation de 30 à 60 % des temps de requête, même si les performances réelles varient en fonction de la définition d’index et des types de requêtes. En raison de cet incidence sur les performances, nous vous recommandons de n’activer cette fonctionnalité que sur les index qui en ont réellement besoin. Pour plus d’informations, consultez [Configurer des clés de chiffrement gérées par le client dans Recherche cognitive Azure](search-security-manage-encryption-keys.md).

<a name="double-encryption"></a>

### <a name="double-encryption"></a>Double chiffrement

Dans Recherche cognitive Azure, le double chiffrement est une extension de CMK. Il s’agit d’un chiffrement en deux temps (une fois par CMK, et une nouvelle fois par des clés gérées par le service) et de portée globale, incluant un stockage à long terme sur un disque de données et un stockage à court terme sur des disques temporaires. Le double chiffrement est implémenté dans les services créés après des dates spécifiques. Pour plus d’informations, consultez [Double chiffrement](search-security-manage-encryption-keys.md#double-encryption).

## <a name="security-management"></a>Gestion de la sécurité

### <a name="api-keys"></a>Clés API

Le recours à une authentification basée sur des clés API signifie que vous devez disposer d’un plan pour la régénération de la clé d’administration à intervalles réguliers, conformément aux meilleures pratiques de sécurité Azure. Il y a, au maximum, deux clés d’administration par service de recherche. Pour plus d’informations sur la sécurisation et la gestion des clés API, consultez [Créer et gérer des clés API](search-security-api-keys.md).

#### <a name="activity-and-diagnostic-logs"></a>Journaux d’activités et de diagnostic

Le service Recherche cognitive ne journalisant pas les identités des utilisateurs, vous ne pouvez pas consulter de journaux pour obtenir des informations sur un utilisateur spécifique. En revanche, le service journalise des opérations de création/lecture/mise à jour/suppression susceptibles de permettre d’établir une corrélation avec d’autres journaux pour comprendre des actions spécifiques.

À l’aide d’alertes et de l’infrastructure de journalisation dans Azure, vous pouvez revenir sur des pics de volume de requête ou d’autres actions qui s’écartent des charges de travail attendues. Pour plus d’informations sur la configuration des journaux, consultez [Collecter et analyser des données de journal](search-monitor-logs.md) et [Surveiller les demandes de requête](search-monitor-queries.md).

### <a name="certifications-and-compliance"></a>Certifications et conformité

Le service Recherche cognitive Azure intervient dans des audits réguliers, et a été certifié par rapport à un certain nombre de standards mondiaux, régionaux et sectoriels pour le cloud public et Azure Government. Pour obtenir la liste complète, téléchargez le livre blanc [**Microsoft Azure Compliance Offerings**](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) depuis la page des rapports d’audit officiels.

Pour la conformité, vous pouvez utiliser [Azure Policy](../governance/policy/overview.md) pour mettre en œuvre les meilleures pratiques de haute sécurité d’[Azure Security Benchmark](../security/benchmarks/introduction.md). Azure Security Benchmark est un ensemble de recommandations de sécurité, codifiées en contrôles de sécurité qui correspondent aux principales actions que vous devez prendre pour atténuer les menaces pesant sur les services et les données. Il existe actuellement 11 contrôles de sécurité, dont la [Sécurité réseau](../security/benchmarks/security-control-network-security.md), [Journalisation et surveillance](../security/benchmarks/security-control-logging-monitoring.md), et [Protection des données](../security/benchmarks/security-control-data-protection.md), pour n’en nommer que quelques-uns.

Azure Policy est une capacité intégrée à Azure qui vous permet de gérer la conformité de plusieurs normes, y compris celles d’Azure Security Benchmark. Pour les critères de référence bien connus, Azure Policy fournit des définitions intégrées qui fournissent à la fois des critères et une réponse actionnable en cas de non-conformité.

Pour Recherche cognitive Azure, il existe actuellement une définition intégrée. Elle concerne la journalisation des diagnostics. Grâce à cette intégration, vous pouvez attribuer une stratégie qui identifie tout service de recherche auquel il manque la journalisation des diagnostics, puis l’active. Pour plus d’informations, consultez [Contrôles de conformité réglementaire d’Azure Policy pour Recherche cognitive Azure](security-controls-policy.md).

## <a name="watch-this-video"></a>Regardez cette vidéo

Regardez cette vidéo rapide pour obtenir une vue d’ensemble de l’architecture de la sécurité et de chaque catégorie de fonctionnalités.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

## <a name="see-also"></a>Voir aussi

+ [Concepts de base de la sécurité Azure](../security/fundamentals/index.yml)
+ [Sécurité Azure](https://azure.microsoft.com/overview/security)
+ [Microsoft Defender pour le cloud](../security-center/index.yml)
