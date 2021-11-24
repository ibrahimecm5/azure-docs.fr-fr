---
title: Architecture de connectivité
titleSuffix: Azure SQL Managed Instance
description: Découvrez l’architecture de connectivité et de communication d’Azure SQL Managed Instance ainsi que la façon dont les composants redirigent le trafic vers l’instance managée.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: service-overview
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: mathoma, bonova
ms.date: 04/29/2021
ms.openlocfilehash: 04a275c0e4bc9c3eb52fee8b02ea2606e52d4958
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132554063"
---
# <a name="connectivity-architecture-for-azure-sql-managed-instance"></a>Architecture de connectivité d’Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Cet article explique le fonctionnement de la communication dans SQL Managed Instance. Il décrit également l’architecture de connectivité et la façon dont les composants redirigent le trafic vers une instance gérée.  

SQL Managed Instance se trouve à l’intérieur du réseau virtuel Azure et du sous-réseau dédié aux instances managées. Ce déploiement offre :

- Une adresse IP privée sécurisée.
- La capacité à connecter un réseau local à SQL Managed Instance.
- La capacité à connecter SQL Managed Instance à un serveur lié ou un autre magasin de données local.
- La capacité à connecter SQL Managed Instance à des ressources Azure.

> [!div class="nextstepaction"]
> [Enquête pour améliorer Azure SQL !](https://aka.ms/AzureSQLSurveyNov2021)

## <a name="communication-overview"></a>Vue d’ensemble des communications

Le diagramme suivant représente les entités connectées à SQL Managed Instance. Il montre également les ressources qui doivent communiquer avec une instance gérée. Le processus de communication situé dans la partie inférieure du diagramme représente les applications et les outils des clients qui se connectent à l’instance managée SQL en tant que sources de données.  

![Entités de l’architecture de connectivité](./media/connectivity-architecture-overview/connectivityarch001.png)

SQL Managed Instance est une offre PaaS (Platform as a Service). Azure utilise des agents automatisés (gestion, déploiement et maintenance) pour gérer ce service basé sur les flux de données de télémétrie. Azure étant responsable de la gestion, les clients ne peuvent pas accéder aux machines du cluster virtuel SQL Managed Instance à l’aide du protocole RDP (Remote Desktop Protocol).

Certaines opérations lancées par les applications ou les utilisateurs finaux peuvent nécessiter l’interaction de SQL Managed Instance avec la plateforme. C’est notamment le cas de la création d’une base de données SQL Managed Instance. Cette ressource est exposée via le portail Azure, PowerShell, Azure CLI et l’API REST.

Les instances managées SQL dépendent de services Azure tels que Stockage Azure pour les sauvegardes, Azure Event Hubs pour les données de télémétrie, Azure Active Directory (Azure AD) pour l’authentification, Azure Key Vault pour TDE (Transparent Data Encryption) et quelques services de plateforme Azure qui fournissent des fonctionnalités de sécurité et de prise en charge. SQL Managed Instance établit des connexions à ces services.

Toutes les communications sont chiffrées et signées avec des certificats. Pour s’assurer de la fiabilité des parties communicantes, les instances managées SQL vérifient en permanence ces certificats à l’aide de listes de révocation de certificat. Si les certificats sont révoqués, SQL Managed Instance ferme les connexions pour protéger les données.

## <a name="high-level-connectivity-architecture"></a>Architecture de la connectivité globale

À un niveau élevé, SQL Managed Instance est un ensemble de composants de service. Ces composants sont hébergés sur un ensemble dédié de machines virtuelles isolées qui s’exécutent au sein du sous-réseau de réseau virtuel du client. Ces machines forment un cluster virtuel.

Un cluster virtuel peut héberger plusieurs instances gérées. Le cas échéant, le cluster est automatiquement étendu ou réduit lorsque le client change le nombre d’instances approvisionnées dans le sous-réseau.

Les applications client peuvent se connecter à des instances managées SQL ainsi qu’interroger et mettre à jour des bases de données à l’intérieur du réseau virtuel, du réseau virtuel appairé ou du réseau connecté grâce à un VPN ou Azure ExpressRoute. Ce réseau doit utiliser un point de terminaison et une adresse IP privée.  

![Diagramme de l’architecture de connectivité](./media/connectivity-architecture-overview/connectivityarch002.png)

Les services de gestion et de déploiement Azure s’exécutent en dehors du réseau virtuel. SQL Managed Instance et les services Azure se connectent par le biais des points de terminaison disposant d’adresses IP publiques. Quand une instance managée SQL crée une connexion sortante, du côté de la réception, elle semble provenir de cette adresse IP publique en raison de la traduction d’adresses réseau (NAT).

Le trafic de gestion transite via le réseau virtuel du client. Cela signifie que les éléments de l’infrastructure du réseau virtuel peuvent nuire au trafic de gestion en provoquant un échec de l’instance, ce qui la rend indisponible.

> [!IMPORTANT]
> Pour améliorer l’expérience client et la disponibilité du service, Azure applique une stratégie d’intention de réseau sur les éléments de l’infrastructure de réseau virtuel Azure. Celle-ci peut affecter le fonctionnement de SQL Managed Instance. Ce mécanisme de plateforme transmet les exigences réseau aux utilisateurs de manière transparente. L’objectif principal de la stratégie est d’éviter une mauvaise configuration du réseau et de veiller au bon fonctionnement des instances managées SQL. Lorsque vous supprimez une instance gérée, la stratégie d’intention de réseau est également supprimée.

## <a name="virtual-cluster-connectivity-architecture"></a>Architecture de la connectivité du cluster virtuel

Examinons plus en détail l’architecture de connectivité de SQL Managed Instance. Le diagramme suivant montre l’organisation conceptuelle du cluster virtuel.

![Architecture de connectivité du cluster virtuel](./media/connectivity-architecture-overview/connectivityarch003.png)

Les clients se connectent à SQL Managed Instance en utilisant le nom d’hôte qui a la forme suivante : `<mi_name>.<dns_zone>.database.windows.net`. Ce nom d’hôte se résout en une adresse IP privée, bien qu’elle soit inscrite dans une zone DNS (Domain Name System) publique et puisse être résolue publiquement. L’identifiant `zone-id` est généré automatiquement lorsque vous créez le cluster. Si un nouveau cluster héberge une instance gérée secondaire, il partage son ID de zone avec le cluster principal. Pour plus d’informations, consultez [Utiliser des groupes de basculement automatique pour permettre le basculement transparent et coordonné de plusieurs bases de données](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets).

Cette adresse IP privée appartient à l’équilibreur de charge interne de SQL Managed Instance. Il redirige le trafic vers la passerelle de SQL Managed Instance. Comme plusieurs instances managées peuvent s’exécuter à l’intérieur du même cluster, la passerelle utilise le nom d’hôte de SQL Managed Instance pour rediriger le trafic vers le service du moteur SQL approprié.

Les services de gestion et de déploiement se connectent à SQL Managed Instance en utilisant un [point de terminaison de gestion](#management-endpoint) qui est mappé à un équilibreur de charge externe. Le trafic est routé vers les nœuds seulement s’il est reçu sur un ensemble de ports prédéfinis utilisés exclusivement par les composants de gestion de SQL Managed Instance. Un pare-feu intégré sur les nœuds est configuré de manière à autoriser le trafic provenant uniquement des plages d’adresses IP de Microsoft. Tous les certificats authentifient mutuellement les communications entre les composants de gestion et le plan de gestion.

## <a name="management-endpoint"></a>Point de terminaison de gestion

Azure gère SQL Managed Instance à l’aide d’un point de terminaison de gestion. Ce point de terminaison est situé à l’intérieur d’un cluster virtuel de l’instance. Le point de terminaison de gestion est protégé par un pare-feu intégré au niveau du réseau. Au niveau de l’application, il est protégé par la vérification de certificat mutuelle. Pour trouver l’adresse IP du point de terminaison, consultez [Déterminer l’adresse IP du point de terminaison de gestion](management-endpoint-find-ip-address.md).

Quand les connexions sont lancées à l’intérieur de l’instance managée SQL (à l’instar des sauvegardes et des journaux d’audit), le trafic semble démarrer à partir de l’adresse IP publique du point de terminaison de gestion. Vous pouvez limiter l’accès aux services publics à partir de SQL Managed Instance en définissant des règles de pare-feu destinées à autoriser uniquement l’adresse IP de l’instance managée SQL. Pour plus d’informations, consultez [Vérifier le pare-feu intégré de SQL Managed Instance](management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Le trafic acheminé vers les services Azure situés dans la région de l’instance managée SQL est optimisé. C’est pourquoi il ne bénéficie pas d’une traduction d’adresses réseau vers l’adresse IP publique du point de terminaison de gestion. Pour cette même raison, si vous devez utiliser des règles de pare-feu basées sur l’adresse IP, ce qui est souvent nécessaire pour le stockage, le service devra se trouver dans une région différente de celle de l’instance managée SQL.

## <a name="service-aided-subnet-configuration"></a>Configuration de sous-réseau assistée par le service

Pour répondre aux exigences de gestion et de sécurité des clients, SQL Managed Instance passe d’une configuration manuelle à une configuration de sous-réseau assistée par service.

Avec la configuration de sous-réseaux assistée par le service, l’utilisateur contrôle totalement le trafic de données (TDS), tandis que le plan de contrôle SQL Managed Instance doit garantir un flux ininterrompu du trafic de gestion afin de respecter un contrat de niveau de service (SLA).

La configuration de sous-réseau assistée par le service s’appuie sur la fonctionnalité de [délégation de sous-réseau](../../virtual-network/subnet-delegation-overview.md) de réseau virtuel pour fournir une gestion automatique de la configuration du réseau et activer les points de terminaison de service. 

Des points de terminaison de service pourraient être utilisés pour configurer des règles de pare-feu de réseau virtuel sur des comptes de stockage qui conservent des sauvegardes et journaux d’audit. Même avec les points de terminaison de service activés, les clients sont encouragés à utiliser la [liaison privée](../../private-link/private-link-overview.md) qui renforce la sécurité des points de terminaison de service.

> [!IMPORTANT]
> En raison des spécificités de la configuration du plan de contrôle, la configuration de sous-réseau assistée par le service n’active pas les points de terminaison de service dans les clouds nationaux. 

### <a name="network-requirements"></a>Configuration requise pour le réseau

Déployer SQL Managed Instance sur un sous-réseau dédié à l’intérieur du réseau virtuel. Le sous-réseau doit disposer des caractéristiques suivantes :

- **Sous-réseau dédié :** le sous-réseau SQL Managed Instance ne doit contenir aucun autre service cloud qui lui est associé, mais d’autres instances managées sont autorisées. De plus, cela ne peut pas être un sous-réseau de passerelle. Le sous-réseau ne peut contenir aucune ressource en dehors de(s) instance(s) gérée(s) et vous ne pouvez pas ajouter d’autres types de ressource au sous-réseau ultérieurement.
- **Délégation de sous-réseau :** le sous-réseau de SQL Managed Instance doit être délégué à un fournisseur de ressources `Microsoft.Sql/managedInstances`.
- **Groupe de sécurité réseau :** Un groupe de sécurité réseau (NSG) doit être associé au sous-réseau de SQL Managed Instance. Vous pouvez utiliser un groupe de sécurité réseau pour contrôler l’accès au point de terminaison de données de l’instance managée SQL en filtrant le trafic sur les ports 1433 et 11000 à 11999 quand SQL Managed Instance est configuré pour rediriger les connexions. Le service approvisionne et conserve automatiquement les [règles](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) actuelles requises pour permettre un flux ininterrompu du trafic de gestion.
- **Table d’itinéraire défini par l’utilisateur (UDR) :** Une table UDR doit être associée au sous-réseau de SQL Managed Instance. Vous pouvez utiliser la passerelle de réseau virtuel ou une appliance de réseau virtuel (NVA) pour ajouter des entrées à la table d’itinéraires pour acheminer le trafic disposant de plages d’adresses IP privées locales en tant que destination. Le service approvisionne et conserve automatiquement les [entrées](#mandatory-user-defined-routes-with-service-aided-subnet-configuration) actuelles requises pour permettre un flux ininterrompu du trafic de gestion.
- **Nombre d’adresses IP suffisant :** le sous-réseau SQL Managed Instance doit disposer d’au moins 32 adresses IP. Pour plus d’informations, consultez [Déterminer la taille du sous-réseau pour SQL Managed Instance](vnet-subnet-determine-size.md). Vous pouvez déployer des instances gérées dans [le réseau existant](vnet-existing-add-subnet.md) une fois que vous l’avez configuré de manière à satisfaire les [exigences réseau pour SQL Managed Instance](#network-requirements). Sinon, créez un [nouveau réseau et sous-réseau](virtual-network-subnet-create-arm-template.md).
- **Autorisé par les stratégies Azure :** si vous utilisez des stratégies [Azure Policy](../../governance/policy/overview.md) pour refuser la création ou la modification de ressources dans l’étendue qui comprend un sous-réseau/réseau virtuel SQL Managed Instance, ces stratégies ne doivent pas empêcher Managed Instance de gérer ses ressources internes. Les ressources suivantes doivent être exclues des effets de refus pour permettre un fonctionnement normal :
  - Les ressources de type Microsoft.Network/serviceEndpointPolicies, lorsque le nom de la ressource commence par \_e41f87a2\_
  - Toutes les ressources de type Microsoft.Network/networkIntentPolicies
  - Toutes les ressources de type Microsoft.Network/virtualNetworks/subnets/contextualServiceEndpointPolicies
- **Verrous sur le réseau virtuel :** les [verrous](../../azure-resource-manager/management/lock-resources.md) sur le réseau virtuel du sous-réseau dédié, son groupe de ressources parent ou son abonnement peuvent parfois interférer avec les opérations de gestion et de maintenance de SQL Managed Instance. Soyez particulièrement vigilant lorsque vous utilisez ces verrous.

> [!IMPORTANT]
> Lorsqu’une instance gérée est créée, une stratégie d’intention réseau est appliquée au sous-réseau afin d’empêcher toute modification non conforme de la configuration réseau. Lorsque la dernière instance restante est supprimée du sous-réseau, la stratégie d’intention réseau est également supprimée. Les règles ci-dessous sont fournies à titre d’information uniquement et ne doivent pas être déployées à l’aide d’un modèle ARM/PowerShell/CLI. Si vous souhaitez utiliser le dernier modèle officiel, vous pouvez toujours [le récupérer à partir du portail](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>Règles de sécurité du trafic entrant obligatoires avec configuration du sous-réseau assistée par le service
Ces règles sont nécessaires pour garantir le flux du trafic de gestion entrant. Pour plus d’informations sur l’architecture de connectivité et le trafic de gestion, consultez le [paragraphe ci-dessus](#high-level-connectivity-architecture).

| Nom       |Port                        |Protocol|Source           |Destination|Action|
|------------|----------------------------|--------|-----------------|-----------|------|
|gestion  |9000, 9003, 1438, 1440, 1452|TCP     |SqlManagement    |SOUS-RÉSEAU MI  |Allow |
|            |9000, 9003                  |TCP     |CorpNetSaw       |SOUS-RÉSEAU MI  |Allow |
|            |9000, 9003                  |TCP     |CorpnetPublic    |SOUS-RÉSEAU MI  |Allow |
|mi_subnet   |Quelconque                         |Quelconque     |SOUS-RÉSEAU MI        |SOUS-RÉSEAU MI  |Allow |
|health_probe|Quelconque                         |Quelconque     |AzureLoadBalancer|SOUS-RÉSEAU MI  |Allow |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>Règles de sécurité du trafic sortant obligatoires avec configuration du sous-réseau assistée par le service
Ces règles sont nécessaires pour garantir le flux du trafic de gestion sortant. Pour plus d’informations sur l’architecture de connectivité et le trafic de gestion, consultez le [paragraphe ci-dessus](#high-level-connectivity-architecture).

| Nom       |Port          |Protocol|Source           |Destination|Action|
|------------|--------------|--------|-----------------|-----------|------|
|gestion  |443, 12000    |TCP     |SOUS-RÉSEAU MI        |AzureCloud |Allow |
|mi_subnet   |Quelconque           |Quelconque     |SOUS-RÉSEAU MI        |SOUS-RÉSEAU MI  |Allow |

### <a name="mandatory-user-defined-routes-with-service-aided-subnet-configuration"></a>Itinéraires définis par l’utilisateur obligatoires avec configuration de sous-réseau assistée par le service
Ces itinéraires sont nécessaires pour garantir que le trafic de gestion est acheminé directement vers une destination. Pour plus d’informations sur l’architecture de connectivité et le trafic de gestion, consultez le [paragraphe ci-dessus](#high-level-connectivity-architecture).

|Nom|Préfixe de l’adresse|Tronçon suivant|
|----|--------------|-------|
|subnet-to-vnetlocal|SOUS-RÉSEAU MI|Réseau virtuel|
|mi-azurecloud-REGION-internet|AzureCloud.REGION|Internet|
|mi-azurecloud-REGION_PAIR-internet|AzureCloud.REGION_PAIR|Internet|
|mi-azuremonitor-internet|AzureMonitor|Internet|
|mi-corpnetpublic-internet|CorpNetPublic|Internet|
|mi-corpnetsaw-internet|CorpNetSaw|Internet|
|mi-eventhub-REGION-internet|EventHub.REGION|Internet|
|mi-eventhub-REGION_PAIR-internet|EventHub.REGION_PAIR|Internet|
|mi-sqlmanagement-internet|SqlManagement|Internet|
|mi-storage-internet|Stockage|Internet|
|mi-storage-REGION-internet|Storage.REGION|Internet|
|mi-storage-REGION_PAIR-internet|Storage.REGION_PAIR|Internet|
|mi-azureactivedirectory-internet|AzureActiveDirectory|Internet|
||||

\* SOUS-RÉSEAU MI fait référence à la plage d’adresses IP du sous-réseau sous la forme x.x.x.x/y. Ces informations sont disponibles sur le portail Azure, dans les propriétés du sous-réseau.

\** Si l’adresse de destination correspond à l’un des services d’Azure, Azure achemine le trafic directement au service via le réseau principal d’Azure plutôt que d’acheminer le trafic vers Internet. Le trafic entre les services Azure ne traverse pas le réseau Internet, quelle que soit la région Azure où le réseau virtuel existe ou la région Azure dans laquelle une instance du service Azure est déployée. Pour plus d’informations, consultez la [page de documentation UDR](../../virtual-network/virtual-networks-udr-overview.md).

En outre, vous pouvez utiliser la passerelle de réseau virtuel ou une appliance de réseau virtuel (NVA) pour ajouter des entrées à la table d’itinéraires pour acheminer le trafic disposant de plages d’adresses IP privées locales en tant que destination.

Si le réseau virtuel comprend un DNS personnalisé, le serveur DNS personnalisé doit pouvoir résoudre les enregistrements DNS publics. Des résolutions de FQDN additionnels peuvent être nécessaires si vous utilisez des fonctionnalités supplémentaires comme Azure AD Authentication. Pour plus d’informations, consultez [Configurer un système DNS personnalisé](custom-dns-configure.md).

### <a name="networking-constraints"></a>Contraintes de mise en réseau

**TLS 1.2 est appliqué aux connexions sortantes** : En janvier 2020, Microsoft a appliqué TLS 1.2 pour le trafic intra-service dans tous les services Azure. Pour Azure SQL Managed Instance, cela a eu pour effet que TLS 1.2 était appliqué aux connexions sortantes utilisées pour la réplication et aux connexions de serveur lié à SQL Server. Si vous utilisez des versions de SQL Server antérieures à 2016 avec SQL Managed Instance, vérifiez que les [mises à jour spécifiques de TLS 1.2](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) ont été appliquées.

Les fonctionnalités de réseau virtuel suivantes *ne sont pas prises en charge* avec SQL Managed Instance :

- **Homologation Microsoft** : l’activation du [peering Microsoft](../../expressroute/expressroute-faqs.md#microsoft-peering) sur des circuits ExpressRoute appairés, directement ou transitivement, avec un réseau virtuel sur lequel SQL Managed Instance réside, affecte le flux de trafic entre les composants SQL Managed Instance au sein du réseau virtuel et les services dont il dépend, ce qui engendre des problèmes de disponibilité. Des déploiements de SQL Managed Instance sur un réseau virtuel avec une homologation Microsoft déjà activée sont supposés échouer.
- **Homologation de réseau virtuel mondial** : La connectivité de [peering de réseau virtuel](../../virtual-network/virtual-network-peering-overview.md) entre régions Azure ne fonctionne pas pour les instances SQL Managed Instance placées dans des sous-réseaux créés avant le 22/9/2020.
- **AzurePlatformDNS** : L’utilisation de l’[étiquette de service](../../virtual-network/service-tags-overview.md) AzurePlatformDNS pour bloquer la résolution DNS de plateforme rendrait SQL Managed Instance indisponible. Même si SQL Managed Instance prend en charge le DNS défini par le client pour la résolution DNS à l’intérieur du moteur, il existe une dépendance envers le système DNS de plateforme pour les opérations de plateforme.
- **Passerelle NAT** : L’utilisation du service [NAT de réseau virtuel Azure](../../virtual-network/nat-gateway/nat-overview.md) pour contrôler la connectivité sortante avec une adresse IP publique spécifique rendrait SQL Managed Instance indisponible. Le service SQL Managed Instance est actuellement limité à l’utilisation d’un équilibreur de charge de base qui ne permet pas la coexistence de flux entrants et sortants avec le service NAT de réseau virtuel.
- **IPv6 pour réseau virtuel Azure** : Le déploiement de SQL Managed Instance sur des [réseaux virtuels IPv4/IPv6 à double pile](../../virtual-network/ip-services/ipv6-overview.md) se solde par un échec. L’association d’un groupe NSG (groupe de sécurité réseau) ou d’une table UDR (table de routage) contenant des préfixes d’adresses IPv6 à un sous-réseau SQL Managed Instance, ou l’ajout de préfixes d’adresses IPv6 à un groupe NSG ou une table UDR déjà associé(e) à un sous-réseau Managed Instance, ne permet pas d’utiliser SQL Managed Instance. Les déploiements de SQL Managed Instance sur un sous-réseau avec un groupe NSG et une table UDR disposant déjà de préfixes IPv6 se soldent par un échec.
- **Zones privées Azure DNS avec un nom réservé aux services Microsoft** : Voici la liste des noms réservés :windows.net, database.windows.net, core.windows.net, blob.core.windows.net, table.core.windows.net, management.core.windows.net, monitoring.core.windows.net, queue.core.windows.net, graph.windows.net, login.microsoftonline.com, login.windows.net, servicebus.windows.net, vault.azure.net. Le déploiement de SQL Managed Instance sur un réseau virtuel auquel est associée une [zone privée Azure DNS](../../dns/private-dns-privatednszone.md) avec un nom réservé aux services Microsoft échoue. L’association d’une zone privée Azure DNS avec un nom réservé à un réseau virtuel contenant Managed Instance rendrait SQL Managed Instance indisponible. Respectez la [configuration DNS du point de terminaison privé Azure](../../private-link/private-endpoint-dns.md) pour une configuration correcte d’Azure Private Link.
- **Stratégies de point de terminaison de service pour Stockage Azure** : Le déploiement de SQL Managed instance sur un sous-réseau auquel sont associées des [stratégies de point de terminaison de service](../../virtual-network/virtual-network-service-endpoint-policies-overview.md) échoue. Les stratégies de point de terminaison de service n’ont pas pu être associées à un sous-réseau qui héberge Managed Instance.

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble, consultez  [Présentation d’Azure SQL Managed Instance](sql-managed-instance-paas-overview.md).
- Découvrez comment [configurer un nouveau réseau virtuel Azure](virtual-network-subnet-create-arm-template.md) ou un [réseau virtuel Azure existant](vnet-existing-add-subnet.md) sur lequel vous pouvez déployer une instance managée SQL.
- [Calculez la taille du sous-réseau](vnet-subnet-determine-size.md) sur lequel vous souhaitez déployer SQL Managed Instance.
- Découvrez comment créer une instance gérée :
  - À partir du [portail Azure](instance-create-quickstart.md).
  - En utilisant [PowerShell](scripts/create-configure-managed-instance-powershell.md).
  - En utilisant [un modèle Azure Resource Manager](https://azure.microsoft.com/resources/templates/sqlmi-new-vnet/).
  - En utilisant [un modèle Azure Resource Manager (à l’aide de JumpBox, avec SSMS inclus)](https://azure.microsoft.com/resources/templates/sqlmi-new-vnet-w-jumpbox/).