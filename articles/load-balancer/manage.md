---
title: Paramètres du portail Azure Load Balancer
description: Commencez à vous renseigner sur les paramètres du portail Azure Load Balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 08/16/2021
ms.author: allensu
ms.openlocfilehash: 1911c3a97892a440d0819025c6f040732d03e6e7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131079545"
---
# <a name="azure-load-balancer-portal-settings"></a>Paramètres du portail Azure Load Balancer

À mesure que vous créez une instance Azure Load Balancer, les informations contenues dans cet article vous aideront à en savoir plus sur les paramètres individuels et sur la configuration qui vous convient le mieux.

## <a name="create-load-balancer"></a>Créer un équilibreur de charge

Azure Load Balancer est un équilibreur de charge réseau qui répartit le trafic entre les instances de machine virtuelle dans le pool principal. Pour créer un équilibreur de charge dans le portail, dans la partie supérieure de la page, sélectionnez la zone de recherche. Entrez **Équilibrage de charge**. Sélectionnez **Équilibreurs de charge** dans les résultats de la recherche. Dans la page **Équilibreurs de charge**, sélectionnez **Créer**.

### <a name="basics"></a>Concepts de base

Dans l’onglet **Fonctions de base** de la page Créer un portail d’équilibrage de charge, entrez les informations suivantes :

| Paramètre |  Détails |
| ---------- | ---------- |
| Abonnement  | Sélectionnez votre abonnement. Cette sélection correspond à l’abonnement dans lequel vous souhaitez déployer votre équilibreur de charge. |
| Resource group | Sélectionnez **Créer nouveau** et saisissez le nom de votre groupe de ressources dans la zone de texte. Si vous avez déjà un groupe de ressources, sélectionnez-le. |
| Nom | Ce paramètre est le nom de votre instance Azure Load Balancer. |
| Région | Sélectionnez la région Azure dans laquelle vous souhaitez déployer votre équilibreur de charge. |
| Type | L’équilibreur de charge a deux types : </br> **Interne (privé)** </br> **Public (externe)**</br> Un équilibreur de charge interne (ILB) achemine le trafic vers les membres du pool principal via une adresse IP privée.</br> Un équilibreur de charge public dirige les requêtes des clients via Internet vers le pool principal.</br> En savoir plus sur les [types d’équilibreur de charge](components.md#frontend-ip-configuration-).|
| SKU  | Sélectionnez **Standard**. </br> L’équilibreur de charge a deux niveaux tarifaires : **De base** et **Standard**. </br> Le niveau De base offre des fonctionnalités limitées. </br> Le niveau **Standard** est recommandé pour les charges de travail de production. </br> En savoir plus sur les [niveaux tarifaires](skus.md). |
| Niveau | L’équilibreur de charge a deux niveaux : </br> **Regional** </br> **Global** </br> Un équilibreur de charge régional est restreint pour l’équilibrage de charge dans une région. Global désigne à un équilibreur de charge inter-régions, qui équilibre la charge entre différentes régions. </br> Pour en savoir plus sur le niveau **Global**, consultez la page [Équilibreur de charge inter-régions (préversion)](cross-region-overview.md).

:::image type="content" source="./media/manage/create-public-load-balancer-basics.png" alt-text="Capture d’écran de la création d’un équilibreur de charge public." border="true":::

### <a name="frontend-ip-configuration"></a>Configuration d’adresses IP frontales

Sous l’onglet **Configuration d’adresse IP frontale** de la page Créer un équilibreur de charge, sélectionnez **Ajouter une adresse IP frontale** pour ouvrir la page de création.

:::image type="content" source="./media/manage/create-frontend.png" alt-text="Capture d’écran de la page de création d’adresse IP frontale." border="true":::

#### <a name="-add-a-frontend-ip"></a>**Ajouter un serveur frontal**
##### <a name="public"></a>Publiques

Si vous sélectionnez le type **Public**, les informations suivantes s’affichent :

| Paramètre | Détails |
| ------- | ------- |
| Nom | Nom du serveur frontal qui sera ajouté à l’équilibreur de charge. |
| Version de l’adresse IP | **IPv4** </br> **IPv6** </br> L’équilibreur de charge prend en charge les configurations d’adresses IP frontales IPv4 et IPv6. </br> Pour en savoir plus, consultez la page [Vue d’ensemble du protocole IPv6 pour Azure Load Balancer](load-balancer-ipv6-overview.md). |
| Type IP | **Adresse IP** </br> **Préfixe IP** </br> L’équilibreur de charge prend en charge une adresse IP ou un préfixe IP pour l’adresse IP frontale. Pour en savoir plus, consultez la page [Préfixe d’adresse IP publique](../virtual-network/ip-services/public-ip-address-prefix.md). |

:::image type="content" source="./media/manage/add-frontend-public.png" alt-text="Capture d’écran de l’ajout d’une configuration d’adresse IP frontale." border="true":::

Si vous sélectionnez **Adresse IP** pour **Type IP**, les informations suivantes s’affichent :

| Paramètre | Détails |
| ------- | ------- |
| Adresse IP publique | Sélectionnez **Créer nouveau** pour créer une IP publique pour votre équilibreur de charge public. </br> Si vous avez une adresse IP publique, sélectionnez-la dans la liste déroulante. |
| Name | Nom de la ressource de l’adresse IP publique. |
| Référence SKU | Les IP publiques ont deux niveaux tarifaires : **De base** et **Standard**. </br> Le niveau De base ne prend pas en charge la résilience des zones ni les attributs de zone. </br> Le niveau **Standard** est recommandé pour les charges de travail de production. </br> Les niveaux tarifaires de l’équilibreur de charge et de l’IP publique **doivent correspondre**. |
| Niveau | **Regional** </br> **Global** </br> Les informations affichées varient selon le type de niveau d’équilibrage de charge. Régional pour l’équilibreur de charge traditionnel, Global pour l’inter-régions. |
| Affectation | **Statique** est sélectionné automatiquement pour le niveau Standard. </br> Les IP publiques du niveau De base ont deux types : **dynamique** et **statique**. </br> Les IP publiques dynamiques ne sont pas attribuées avant la création. </br> Les adresses IP peuvent être perdues si la ressource est supprimée. </br> L’utilisation d’adresses IP statiques est recommandée. |
| Zone de disponibilité | Sélectionnez **Redondant interzone** pour créer un équilibreur de charge résilient. </br> Pour créer un équilibreur de charge zonal, sélectionnez une zone spécifique parmi **1**, **2** ou **3**. </br> L’équilibreur de charge standard et les IP publiques prennent en charge les zones. </br> En savoir plus sur les [équilibreurs de charge et les zones de disponibilité](load-balancer-standard-availability-zones.md). </br> Aucune sélection de zone n’est disponible pour le niveau De base. L’équilibreur de charge de base ne prend pas en charge les zones. |
| Préférence de routage | Sélectionnez **Réseau Microsoft**. </br> Réseau Microsoft signifie que le trafic est acheminé via le réseau mondial de Microsoft. </br> Internet signifie que le trafic est acheminé via le réseau du fournisseur de services Internet. </br> En savoir plus sur les [préférences de routage](../virtual-network/ip-services/routing-preference-overview.md).|

:::image type="content" source="./media/manage/create-public-ip.png" alt-text="Capture d’écran de la création d’une adresse IP publique." border="true":::

Si vous sélectionnez **Préfixe IP** pour **Type IP**, les informations suivantes s’affichent :

| Paramètre | Détails |
| ------- | ------- |
| Préfixe d’adresse IP publique | Sélectionnez **Créer** pour créer un préfixe IP public pour votre équilibreur de charge public. </br> Si vous avez un préfixe public, sélectionnez-le dans la liste déroulante. |
| Name | Nom de la ressource de préfixe IP public. |
| Référence SKU | Les préfixes IP publics possèdent une référence SKU **Standard**. |
| Version de l’adresse IP | Il peut s’agir d’**IPv4** ou d’**IPv6**. </br> La version affichée correspond à la version choisie ci-dessus. |
| Taille de préfixe | Les préfixes IPv4 ou IPv6 sont affichés en fonction de la sélection ci-dessus. </br> **IPv4** </br> /24 (256 adresses) </br> /25 (128 adresses) </br> /26 (64 adresses) </br> /27 (32 adresses) </br> /28 (16 adresses) </br> /29 (8 adresses) </br> /30 (4 adresses) </br> /31 (2 adresses) </br> **IPv6** </br> /124 (16 adresses) </br> /125 (8 adresses) </br> /126 (4 adresses) </br> /127 (2 adresses) |
| Zone de disponibilité | Sélectionnez **Redondant interzone** pour créer un équilibreur de charge résilient. </br> Pour créer un équilibreur de charge zonal, sélectionnez une zone spécifique parmi **1**, **2** ou **3**. </br> L’équilibreur de charge Standard et les préfixes IP publics prennent en charge les zones. </br> En savoir plus sur les [équilibreurs de charge et les zones de disponibilité](load-balancer-standard-availability-zones.md).

:::image type="content" source="./media/manage/create-public-ip-prefix.png" alt-text="Capture d’écran de la création d’un préfixe IP public." border="true":::

##### <a name="internal"></a>Interne

Si vous sélectionnez le type **Interne** sous l’onglet **De base**, les informations suivantes s’affichent :

| Paramètre |  Détails |
| ---------- | ---------- |
| Réseau virtuel | Réseau virtuel dont vous souhaitez que votre équilibreur de charge interne fasse partie. </br> L’adresse IP privée du serveur frontal que vous sélectionnez pour votre équilibreur de charge interne sera celle de ce réseau virtuel. |
| Subnet | Les sous-réseaux disponibles pour l’adresse IP de l’adresse IP frontale s’affichent ici. |
| Affectation | Les options sont **Statique** ou **Dynamique**. </br> Statique garantit que l’adresse IP ne change pas. Une adresse IP dynamique peut changer. |
| Zone de disponibilité | Les options disponibles sont : </br> **Redondant dans une zone** </br> **Zone 1** </br> **Zone 2** </br> **Zone 3** </br> Pour créer un équilibreur de charge hautement disponible et résilient aux défaillances de zones de disponibilité, sélectionnez une IP **redondante interzone**. |

:::image type="content" source="./media/manage/add-frontend-internal.png" alt-text="Capture d’écran de l’ajout d’une adresse IP frontale." border="true":::
### <a name="backend-pools"></a>Pools de back-ends

Sous l’onglet **Pools principaux** de la page Créer un équilibreur de charge, sélectionnez **Ajouter un pool de backends** pour ouvrir la page de création.

:::image type="content" source="./media/manage/create-backend-pool.png" alt-text="Capture d’écran de l’onglet Pools principaux." border="true":::

#### <a name="-add-a-backend-pool"></a>**Ajouter un pool de back-ends**

Les éléments suivants s’affichent dans la page de création **Ajouter un pool de backends** :

| Paramètre | Détails |
| ---------- |  ---------- |
| Nom | Nom de votre pool principal. |
| Réseau virtuel | Réseau virtuel de vos instances de serveur principal. |
| Configuration du pool de back-ends | Les options disponibles sont : </br> **Carte d’interface réseau** </br> **Adresse IP** </br> L’option Carte réseau configure le pool principal (ou « de back-ends ») pour qu’il utilise la carte d’interface réseau des machines virtuelles. </br> L’option Adresse IP configure le pool principal pour qu’il utilise l’adresse IP des machines virtuelles. </br> Pour en savoir plus sur la configuration du pool principal, consultez la page [Gestion du pool back-end](backend-pool-management.md).
| Version de l’adresse IP | Les options sont **IPv4** ou **IPv6**. |

Vous pouvez ajouter des machines virtuelles ou des groupes de machines virtuelles identiques dans le pool principal de votre instance Azure Load Balancer. Créez d’abord les machines virtuelles ou les groupes de machines virtuelles identiques. 

:::image type="content" source="./media/manage/add-backend-pool.png" alt-text="Capture d’écran de la page Ajouter un pool principal." border="true":::

### <a name="inbound-rules"></a>Règles de trafic entrant

Sous l’onglet **Règles de trafic entrant**, on trouve deux sections : **Règle d'équilibrage de charge** et **Règle NAT de trafic entrant**.

Sous l’onglet **Règles de trafic entrant** de la page Créer un équilibreur de charge, sélectionnez d’équilibrage de charge, sélectionnez **Ajouter une règle d'équilibrage de charge** pour ouvrir la page de création.

:::image type="content" source="./media/manage/inbound-rules.png" alt-text="Capture d’écran de l’ajout d’une règle de trafic entrant." border="true":::

#### <a name="-add-a-load-balancing-rule"></a>**Ajouter une règle d'équilibrage de charge**

Les éléments suivants s’affichent dans la page de création **Ajouter une règle d'équilibrage de charge** :

| Paramètre | Détails |
| ---------- | ---------- |
| Nom | Nom de la règle d’équilibrage de charge. |
| Version de l’adresse IP | Les options sont **IPv4** ou **IPv6**.  |
| Adresse IP du serveur frontal | Sélectionnez l’adresse IP frontale. </br> Adresse IP frontale de votre équilibreur de charge auquel vous souhaitez associer la règle d’équilibrage de charge.|
| Protocol | Azure Load Balancer est un équilibreur de charge réseau de couche 4. </br> Vos options sont : **TCP** ou **UDP**. |
| Port | Ce paramètre est le port associé à l’adresse IP frontale à laquelle vous souhaitez que le trafic soit distribué en fonction de cette règle d’équilibrage de charge. |
| Port principal | Ce paramètre est le port sur les instances du pool principal vers lequel vous souhaitez que l’équilibreur de charge envoie le trafic. Ce paramètre peut être le même que le port frontal ou différent si vous avez besoin de flexibilité pour votre application. |
| Pool principal | Pool principal sur lequel vous souhaitez appliquer cette règle d’équilibrage de charge. |
| Sonde d’intégrité | Sélectionnez **Créer** pour créer une sonde.  </br> Seules les instances saines recevront un nouveau trafic. |
| Persistance de session |  Les options disponibles sont : </br> **Aucun** </br> **Adresse IP du client** </br> **Adresse IP du client et protocole**</br> </br> Maintenez le trafic d’un client vers la même machine virtuelle dans le pool principal. Ce trafic sera conservé pendant toute la durée de la session. </br> **Aucun** spécifie que les requêtes successives provenant du même client peuvent être gérées par n’importe quelle machine virtuelle. </br> **Adresse IP du client** spécifie que les requêtes successives provenant de la même adresse IP du client seront gérées par la même machine virtuelle. </br> **Adresse IP du client et protocole** spécifie que les requêtes successives provenant de la même adresse IP du client et via le même protocole seront gérées par la même machine virtuelle. </br> En savoir plus sur les [modes de distribution](load-balancer-distribution-mode.md). |
| Délai d’inactivité (minutes) | Maintenez une connexion **TCP** ou **HTTP** ouverte sans compter sur des clients pour l’envoi de messages de persistance de connexion. |  
| Réinitialisation du protocole TCP | L’équilibreur de charge peut envoyer des **réinitialisations du protocole TCP** pour aider à créer un comportement d’application plus prévisible lorsque la connexion est inactive. </br> En savoir plus sur la [réinitialisation du protocole TCP](load-balancer-tcp-reset.md).|
| IP flottante | « IP flottante » est le terme Azure désignant une partie de ce que l’on appelle le **retour direct du serveur (DSR)** . </br> La configuration DSR comprend deux parties : <br> 1. une topologie de flux ; </br> 2. Schéma de mappage d’adresses IP au niveau de la plateforme. </br></br> Azure Load Balancer fonctionne toujours dans une topologie de flux DSR, que l’IP flottante soit activée ou non. </br> Ce fonctionnement signifie que la partie sortante d’un flux est toujours correctement réécrite pour retourner directement à l’origine. </br> Sans adresse IP flottante, Azure expose un schéma traditionnel de mappage d’adresses IP d’équilibrage de charge : l’adresse IP des instances de machine virtuelle. </br> L’activation de l’IP flottante modifie le mappage des adresses IP à l’adresse IP frontale de l’équilibreur de charge afin de permettre davantage de flexibilité. </br> Pour plus d’informations, consultez [Serveurs frontaux multiples dans Azure Load Balancer](load-balancer-multivip-overview.md).|
| Traduction d’adresses réseau (SNAT) sources sortante | Les options disponibles sont : </br> **(Recommandé) Utilisez des règles de trafic sortant pour permettre aux membres du pool de back-ends d'accéder à Internet.** </br> **Utilisez une règle de trafic sortant implicite. Non recommandé, car peut entraîner l'épuisement du port SNAT.** </br> Sélectionnez l’option commençant par **(Recommandé)** pour éviter l’épuisement du port SNAT. Une **passerelle NAT** ou des **règles de trafic sortant** sont requises pour fournir une transition SNAT aux membres du pool de back-ends. Pour en savoir plus sur les **passerelles NAT**, consultez la page [Qu’est-ce que le service NAT de réseau virtuel ?](../virtual-network/nat-gateway/nat-overview.md). </br> Pour plus d’informations sur les connexions sortantes dans Azure, consultez [Utilisation de SNAT (Source Network Address Translation) pour les connexions sortantes](load-balancer-outbound-connections.md). |

:::image type="content" source="./media/manage/add-load-balancing-rule.png" alt-text="Capture d’écran de l’ajout d’une règle d’équilibrage de charge." border="true":::

#### <a name="create-health-probe"></a>Créer une sonde d’intégrité

Si vous avez sélectionné **Créer** lors de la configuration de la sonde d’intégrité de la règle d’équilibrage de charge ci-dessus, les options suivantes s’affichent :

| Paramètre | Détails |
| ---------- | ---------- |
| Nom | Nom de votre sonde d’intégrité. |
| Protocol | Le protocole que vous sélectionnez définit le type de vérification utilisé pour déterminer si la ou les instances principales sont saines. </br> Les options disponibles sont : </br> **TCP** </br> **HTTPS** </br> **HTTP** </br> Vérifiez que vous utilisez le protocole approprié. Cette sélection dépend de la nature de votre application. </br> La configuration de la sonde d’intégrité et les réponses de la sonde déterminent quelles instances de pool principal recevront de nouveaux flux. </br> Vous pouvez utiliser des sondes d’intégrité pour détecter la défaillance d’une application sur un point de terminaison back-end. </br> En savoir plus sur les [sondes d’intégrité](load-balancer-custom-probe-overview.md). |
| Port | Port de destination de la sonde d’intégrité. </br> Ce paramètre désigne le port sur l’instance principale que la sonde d’intégrité utilisera pour déterminer l’intégrité de l’instance. |
| Intervalle | Nombre de secondes entre les tentatives de la sonde. </br> L’intervalle détermine la fréquence à laquelle la sonde d’intégrité tentera d’atteindre l’instance principale. </br> Si vous sélectionnez 5, la sonde effectuera une deuxième tentative après 5 secondes, et ainsi de suite. |
| Seuil de défaillance sur le plan de l’intégrité | Nombre d’échecs de sonde consécutifs qui se produisent avant qu’une machine virtuelle soit considérée comme non saine.</br> Si vous sélectionnez 2, aucun flux n’est défini sur cette instance principale après deux échecs consécutifs. |

:::image type="content" source="./media/manage/add-health-probe.png" alt-text="Capture d’écran de l’ajout d’une sonde d’intégrité." border="true":::

Sous l’onglet **Règles de trafic entrant** de la page Créer un équilibreur de charge, sélectionnez **+ Ajouter une règle NAT de trafic entrant** pour ouvrir la page de création.

#### <a name="-add-an-inbound-nat-rule"></a>**+ Ajouter une règle NAT de trafic entrant**

La page de création **+ Ajouter une règle NAT de trafic entrant** contient les éléments suivants :

| Paramètre | Détails |
| ---------- | ---------- |
| Nom | Nom de votre règle NAT de trafic entrant. |
| Adresse IP du serveur frontal | Sélectionnez l’adresse IP frontale. </br> Adresse IP frontale de votre équilibreur de charge à laquelle vous souhaitez associer la règle NAT de trafic entrant. |
| Version de l’adresse IP | Les options sont IPv4 ou IPv6. |
| Service | Type de service que vous exécuterez sur Azure Load Balancer. </br> Une sélection permet de mettre à jour les informations de port de manière appropriée. |
| Protocol | Azure Load Balancer est un équilibreur de charge réseau de couche 4. </br> Vos options sont : TCP ou UDP. |
| Délai d’inactivité (minutes) | Maintenez une connexion TCP ou HTTP ouverte sans compter sur des clients pour l’envoi de messages de persistance de connexion. |
| Réinitialisation du protocole TCP | L’équilibreur de charge peut envoyer des réinitialisations du protocole TCP pour aider à créer un comportement d’application plus prévisible lorsque la connexion est inactive. </br> En savoir plus sur la [réinitialisation du protocole TCP](load-balancer-tcp-reset.md). |
| Port | Ce paramètre est le port associé à l’adresse IP frontale à laquelle vous souhaitez que le trafic soit distribué en fonction de cette règle NAT de trafic entrant. |
| Machine virtuelle cible | Partie de la machine virtuelle du pool principal à laquelle vous souhaitez associer cette règle. |
| Mappage de ports | Ce paramètre peut être défini par défaut ou personnalisé selon les préférences de votre application. |

:::image type="content" source="./media/manage/add-inbound-nat-rule.png" alt-text="Capture d’écran de l’ajout d’une règle NAT de trafic entrant." border="true":::

### <a name="outbound-rules"></a>Règles de trafic sortant

Sous l’onglet **Règles de trafic sortant** de la page Créer un équilibreur de charge, sélectionnez **Ajouter une règle de trafic sortant** pour ouvrir la page de création.

> [!NOTE]
> L’onglet Règles de trafic sortant est uniquement valide pour un équilibreur de charge Standard public. Les règles de trafic sortant ne sont pas prises en charge sur un équilibreur de charge interne ou De base. Le service NAT de réseau virtuel Azure constitue la méthode recommandée pour fournir un accès Internet sortant au pool de back-ends. Pour en savoir plus sur le service **NAT de réseau virtuel Azure** et la ressource de passerelle NAT, consultez la page **[Qu’est-ce que le service NAT de réseau virtuel ?](../virtual-network/nat-gateway/nat-overview.md)** .

:::image type="content" source="./media/manage/create-outbound-rule.png" alt-text="Capture d’écran de la création d’une règle de trafic sortant." border="true":::

#### <a name="-add-an-outbound-rule"></a>**Ajouter une règle de trafic sortant**

Les éléments suivants s’affichent dans la page de création **Ajouter une règle de trafic sortant** :

| Paramètre | Détails |
| ------- | ------ |
| Nom | Nom de votre règle de trafic sortant. |
| Adresse IP du serveur frontal | Sélectionnez l’adresse IP frontale. </br> Adresse IP frontale de votre équilibreur de charge à laquelle vous souhaitez associer la règle de trafic sortant. |
| Protocol | Azure Load Balancer est un équilibreur de charge réseau de couche 4. </br> Vos options sont : **Tout**, **TCP** ou **UDP**. |
| Délai d’inactivité (minutes) | Maintenez une connexion **TCP** ou **HTTP** ouverte sans compter sur des clients pour l’envoi de messages de persistance de connexion. |
| Réinitialisation du protocole TCP | L’équilibreur de charge peut envoyer des **réinitialisations du protocole TCP** pour aider à créer un comportement d’application plus prévisible lorsque la connexion est inactive. </br> En savoir plus sur la [réinitialisation du protocole TCP](load-balancer-tcp-reset.md). |
| Pool principal | Pool principal sur lequel vous souhaitez appliquer cette règle de trafic sortant. |
| **Allocation de ports** |   |
| Allocation de ports | Les choix sont les suivants : </br> **Choisir manuellement le nombre de ports de sortie** </br> **Utiliser le nombre de ports de sortie par défaut** </br> Nous vous recommandons de sélectionner la valeur par défaut **Choisir manuellement le nombre de ports de sortie pour empêcher l’épuisement du port SNAT**. Si vous sélectionnez **Utiliser le nombre de ports de sortie par défaut**, vous ne pourrez pas sélectionner de valeur pour **Ports de sortie**. |
| Ports sortants | Les choix sont les suivants : </br> **Ports par instance** </br> **Nombre maximal d'instances backend**. </br> Nous vous recommandons de sélectionner **Ports par instance** et de saisir la valeur **10 000**. |

:::image type="content" source="./media/manage/add-outbound-rule.png" alt-text="Capture d’écran de l’ajout d’une règle de trafic sortant." border="true":::

## <a name="portal-settings"></a>Paramètres du portail
### <a name="frontend-ip-configuration"></a>Configuration d’adresses IP frontales

Adresse IP de l’équilibreur de charge Azure Load Balancer. Il s’agit du point de contact pour les clients. 

Vous pouvez avoir une ou plusieurs configurations d’adresses IP frontales. Si vous avez parcouru la section de création ci-dessus, vous devriez avoir déjà créé un serveur front-end (ou « frontal ») pour votre équilibreur de charge. 

Si vous souhaitez ajouter une configuration d’adresse IP frontale à votre équilibreur de charge, accédez à votre équilibreur de charge dans le portail Azure, sélectionnez **Configuration d’adresse IP frontale**, puis sélectionnez **+Ajouter**.

| Paramètre |  Détails |
| ---------- | ---------- |
| Nom | Nom de votre configuration d’adresse IP frontale. |
| Version de l’adresse IP | Version de l’adresse IP que vous souhaitez avoir pour votre serveur frontal. </br> L’équilibreur de charge prend en charge les configurations d’adresses IP frontales IPv4 et IPv6. |
| Type IP | Le type d’adresse IP détermine si une adresse IP unique est associée à votre serveur frontal ou s’il s’agit d’une plage d’adresses IP utilisant un préfixe IP. </br> Un [préfixe d’IP publique](../virtual-network/ip-services/public-ip-address-prefix.md) vous aide quand vous devez vous connecter au même point de terminaison à plusieurs reprises. Le préfixe garantit qu’un nombre suffisant de ports sont fournis pour faciliter la résolution des problèmes de port SNAT. |
| IP publique (ou Préfixe si vous avez sélectionné Préfixe ci-dessus) | Sélectionnez ou créez une nouvelle IP publique (ou un nouveau préfixe) pour le serveur frontal de votre équilibreur de charge. |

:::image type="content" source="./media/manage/frontend.png" alt-text="Page Créer une configuration d’adresse IP frontale." border="true":::

### <a name="backend-pools"></a>Pools de back-ends

Le pool d’adresses principal contient les adresses IP des interfaces réseau virtuelles dans le pool principal. 

Si vous souhaitez ajouter un pool principal à votre équilibreur de charge, accédez à votre équilibreur de charge dans le portail Azure, sélectionnez **Pools principaux**, puis sélectionnez **+Ajouter**.

| Paramètre | Détails |
| ---------- |  ---------- |
| Nom | Nom de votre pool principal. |
| Réseau virtuel | Réseau virtuel de vos instances de serveur principal. |
| Configuration d’un pool de back-ends | Les options disponibles sont : </br> **Carte d’interface réseau** </br> **Adresse IP** </br> L’option Carte réseau configure le pool principal (ou « de back-ends ») pour qu’il utilise la carte d’interface réseau des machines virtuelles. </br> L’option Adresse IP configure le pool principal pour qu’il utilise l’adresse IP des machines virtuelles. </br> Pour en savoir plus sur la configuration du pool principal, consultez la page [Gestion du pool back-end](backend-pool-management.md). |
| Version de l’adresse IP | Les options sont **IPv4** ou **IPv6**. |

Vous pouvez ajouter des machines virtuelles ou des groupes de machines virtuelles identiques dans le pool principal de votre instance Azure Load Balancer. Créez d’abord les machines virtuelles ou les groupes de machines virtuelles identiques. Ensuite, ajoutez-les à l’équilibreur de charge dans le portail.

:::image type="content" source="./media/manage/backend.png" alt-text="Page Créer un pool principal." border="true":::

### <a name="health-probes"></a>Sondes d’intégrité

Une sonde d’intégrité est utilisée pour surveiller l’état de vos instances ou machines virtuelles principales. L’état de la sonde d’intégrité détermine à quel moment les nouvelles connexions sont envoyées à une instance en fonction des contrôles d’intégrité. 

Si vous souhaitez ajouter une sonde d’intégrité à votre équilibreur de charge, accédez à votre équilibreur de charge dans le portail Azure, sélectionnez **Sondes d’intégrité**, puis sélectionnez **+Ajouter**.

| Paramètre | Détails |
| ---------- | ---------- |
| Nom | Nom de votre sonde d’intégrité. |
| Protocol | Le protocole que vous sélectionnez définit le type de vérification utilisé pour déterminer si la ou les instances principales sont saines. </br> Les options disponibles sont : </br> **TCP** </br> **HTTPS** </br> **HTTP** </br> Vérifiez que vous utilisez le protocole approprié. Cette sélection dépend de la nature de votre application. </br> La configuration de la sonde d’intégrité et les réponses de la sonde déterminent quelles instances de pool principal recevront de nouveaux flux. </br> Vous pouvez utiliser des sondes d’intégrité pour détecter la défaillance d’une application sur un point de terminaison back-end. </br> En savoir plus sur les [sondes d’intégrité](load-balancer-custom-probe-overview.md). |
| Port | Port de destination de la sonde d’intégrité. </br> Ce paramètre désigne le port sur l’instance principale que la sonde d’intégrité utilisera pour déterminer l’intégrité de l’instance. |
| Intervalle | Nombre de secondes entre les tentatives de la sonde. </br> L’intervalle détermine la fréquence à laquelle la sonde d’intégrité tentera d’atteindre l’instance principale. </br> Si vous sélectionnez 5, la sonde effectuera une deuxième tentative après 5 secondes, et ainsi de suite. |
| Seuil de défaillance sur le plan de l’intégrité | Nombre d’échecs de sonde consécutifs qui se produisent avant qu’une machine virtuelle soit considérée comme non saine.</br> Si vous sélectionnez 2, aucun flux n’est défini sur cette instance principale après deux échecs consécutifs. |

:::image type="content" source="./media/manage/health-probe.png" alt-text="Capture d’écran de la création/ajout d’une sonde d’intégrité." border="true":::

### <a name="load-balancing-rules"></a>Règles d’équilibrage de charge

Définit la manière dont le trafic entrant est distribué à toutes les instances du pool principal. Une règle d’équilibrage de charge fait correspondre une configuration d’adresse IP frontale et un port donnés à plusieurs adresses IP frontales et ports.

Si vous souhaitez ajouter une règle d’équilibrage de charge à votre équilibreur de charge, accédez à votre équilibreur de charge dans le portail Azure, sélectionnez **Règles d’équilibrage de charge**, puis sélectionnez **+Ajouter**.
    
| Paramètre | Détails |
| ---------- | ---------- |
| Nom | Nom de la règle d’équilibrage de charge. |
| Version de l’adresse IP | Les options sont **IPv4** ou **IPv6**.  |
| Adresse IP du serveur frontal | Sélectionnez l’adresse IP frontale. </br> Adresse IP frontale de votre équilibreur de charge auquel vous souhaitez associer la règle d’équilibrage de charge.|
| Protocol | Azure Load Balancer est un équilibreur de charge réseau de couche 4. </br> Vos options sont : **TCP** ou **UDP**. |
| Port | Ce paramètre est le port associé à l’adresse IP frontale à laquelle vous souhaitez que le trafic soit distribué en fonction de cette règle d’équilibrage de charge. |
| Port principal | Ce paramètre est le port sur les instances du pool principal vers lequel vous souhaitez que l’équilibreur de charge envoie le trafic. Ce paramètre peut être le même que le port frontal ou différent si vous avez besoin de flexibilité pour votre application. |
| Pool principal | Pool principal sur lequel vous souhaitez appliquer cette règle d’équilibrage de charge. |
| Sonde d’intégrité | Sonde d’intégrité que vous avez créée pour vérifier l’état des instances dans le pool principal. </br> Seules les instances saines recevront un nouveau trafic. |
| Persistance de session |  Les options disponibles sont : </br> **Aucun** </br> **Adresse IP du client** </br> **Adresse IP du client et protocole**</br> </br> Maintenez le trafic d’un client vers la même machine virtuelle dans le pool principal. Ce trafic sera conservé pendant toute la durée de la session. </br> **Aucun** spécifie que les requêtes successives provenant du même client peuvent être gérées par n’importe quelle machine virtuelle. </br> **Adresse IP du client** spécifie que les requêtes successives provenant de la même adresse IP du client seront gérées par la même machine virtuelle. </br> **Adresse IP du client et protocole** spécifie que les requêtes successives provenant de la même adresse IP du client et via le même protocole seront gérées par la même machine virtuelle. </br> En savoir plus sur les [modes de distribution](load-balancer-distribution-mode.md). |
| Délai d’inactivité (minutes) | Maintenez une connexion **TCP** ou **HTTP** ouverte sans compter sur des clients pour l’envoi de messages de persistance de connexion. |  
| Réinitialisation du protocole TCP | L’équilibreur de charge peut envoyer des **réinitialisations du protocole TCP** pour aider à créer un comportement d’application plus prévisible lorsque la connexion est inactive. </br> En savoir plus sur la [réinitialisation du protocole TCP](load-balancer-tcp-reset.md).|
| IP flottante | « IP flottante » est le terme Azure désignant une partie de ce que l’on appelle le **retour direct du serveur (DSR)** . </br> La configuration DSR comprend deux parties : <br> 1. une topologie de flux ; </br> 2. Schéma de mappage d’adresses IP au niveau de la plateforme. </br></br> Azure Load Balancer fonctionne toujours dans une topologie de flux DSR, que l’IP flottante soit activée ou non. </br> Ce fonctionnement signifie que la partie sortante d’un flux est toujours correctement réécrite pour retourner directement à l’origine. </br> Sans adresse IP flottante, Azure expose un schéma traditionnel de mappage d’adresses IP d’équilibrage de charge : l’adresse IP des instances de machine virtuelle. </br> L’activation de l’IP flottante modifie le mappage des adresses IP à l’adresse IP frontale de l’équilibreur de charge afin de permettre davantage de flexibilité. </br> Pour plus d’informations, consultez [Serveurs frontaux multiples dans Azure Load Balancer](load-balancer-multivip-overview.md).|
| Traduction d’adresses réseau (SNAT) sources sortante | Les options disponibles sont : </br> **(Recommandé) Utilisez des règles de trafic sortant pour permettre aux membres du pool de back-ends d'accéder à Internet.** </br> **Utilisez une règle de trafic sortant implicite. Non recommandé, car peut entraîner l'épuisement du port SNAT.** </br> Sélectionnez l’option commençant par **(Recommandé)** pour éviter l’épuisement du port SNAT. Une **passerelle NAT** ou des **règles de trafic sortant** sont requises pour fournir une transition SNAT aux membres du pool de back-ends. Pour en savoir plus sur les **passerelles NAT**, consultez la page [Qu’est-ce que le service NAT de réseau virtuel ?](../virtual-network/nat-gateway/nat-overview.md). </br> Pour plus d’informations sur les connexions sortantes dans Azure, consultez [Utilisation de SNAT (Source Network Address Translation) pour les connexions sortantes](load-balancer-outbound-connections.md). |

:::image type="content" source="./media/manage/load-balancing-rule.png" alt-text="Capture d’écran de l’ajout d’une règle d’équilibrage de charge." border="true":::

### <a name="inbound-nat-rules"></a>Règles NAT entrantes

Une règle NAT de trafic entrant transfère le trafic entrant envoyé à la combinaison de l’adresse IP frontale et du port. 

Le trafic est envoyé à une machine virtuelle ou instance spécifique dans le pool back-end. Ce réacheminement de port est accompli à l’aide de la même distribution basée sur le hachage que l’équilibrage de charge.

Si votre scénario nécessite que des sessions RDP (Remote Desktop Protocol) ou SSH (Secure Shell) séparent des instances de machine virtuelle d’un pool principal, plusieurs points de terminaison internes peuvent être mappés à des ports sur la même adresse IP frontale. 

Les adresses IP frontales peuvent être utilisées pour administrer à distance vos machines virtuelles sans serveur de rebond supplémentaire.

Si vous souhaitez ajouter une règle NAT de trafic entrant à votre équilibreur de charge, accédez à votre équilibreur de charge dans le portail Azure, sélectionnez **Règles NAT de trafic entrant**, puis sélectionnez **+Ajouter**.

| Paramètre | Détails |
| ---------- | ---------- |
| Nom | Nom de votre règle NAT de trafic entrant. |
| Adresse IP du serveur frontal | Sélectionnez l’adresse IP frontale. </br> Adresse IP frontale de votre équilibreur de charge à laquelle vous souhaitez associer la règle NAT de trafic entrant. |
| Version de l’adresse IP | Les options sont IPv4 ou IPv6. |
| Service | Type de service que vous exécuterez sur Azure Load Balancer. </br> Une sélection permet de mettre à jour les informations de port de manière appropriée. |
| Protocol | Azure Load Balancer est un équilibreur de charge réseau de couche 4. </br> Vos options sont : TCP ou UDP. |
| Délai d’inactivité (minutes) | Maintenez une connexion TCP ou HTTP ouverte sans compter sur des clients pour l’envoi de messages de persistance de connexion. |
| Réinitialisation du protocole TCP | L’équilibreur de charge peut envoyer des réinitialisations du protocole TCP pour aider à créer un comportement d’application plus prévisible lorsque la connexion est inactive. </br> En savoir plus sur la [réinitialisation du protocole TCP](load-balancer-tcp-reset.md). |
| Port | Ce paramètre est le port associé à l’adresse IP frontale à laquelle vous souhaitez que le trafic soit distribué en fonction de cette règle NAT de trafic entrant. |
| Machine virtuelle cible | Partie de la machine virtuelle du pool principal à laquelle vous souhaitez associer cette règle. |
| Mappage de ports | Ce paramètre peut être défini par défaut ou personnalisé selon les préférences de votre application. |

:::image type="content" source="./media/manage/inbound-nat-rule.png" alt-text="Capture d’écran de l’ajout d’une règle NAT de trafic entrant." border="true":::

### <a name="outbound-rules"></a>Règles de trafic sortant

Les règles de trafic sortant de l’équilibreur de charge configurent la SNAT sortante pour les machines virtuelles dans le pool principal.

Si vous souhaitez ajouter une règle de trafic sortant à votre équilibreur de charge, accédez à votre équilibreur de charge dans le portail Azure, sélectionnez **Règles de trafic sortant**, puis sélectionnez **+Ajouter**.

| Paramètre | Détails |
| ------- | ------ |
| Nom | Nom de votre règle de trafic sortant. |
| Adresse IP du serveur frontal | Sélectionnez l’adresse IP frontale. </br> Adresse IP frontale de votre équilibreur de charge à laquelle vous souhaitez associer la règle de trafic sortant. |
| Protocol | Azure Load Balancer est un équilibreur de charge réseau de couche 4. </br> Vos options sont : **Tout**, **TCP** ou **UDP**. |
| Délai d’inactivité (minutes) | Maintenez une connexion **TCP** ou **HTTP** ouverte sans compter sur des clients pour l’envoi de messages de persistance de connexion. |
| Réinitialisation du protocole TCP | L’équilibreur de charge peut envoyer des **réinitialisations du protocole TCP** pour aider à créer un comportement d’application plus prévisible lorsque la connexion est inactive. </br> En savoir plus sur la [réinitialisation du protocole TCP](load-balancer-tcp-reset.md). |
| Pool principal | Pool principal sur lequel vous souhaitez appliquer cette règle de trafic sortant. |

:::image type="content" source="./media/manage/outbound-rule.png" alt-text="Capture d’écran de l’ajout d’une règle de trafic sortant." border="true":::

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez pris connaissance des différents termes et paramètres du portail Azure pour Azure Load Balancer.

* [En savoir](./load-balancer-overview.md) plus sur Azure Load Balancer.
* [FAQ](./load-balancer-faqs.yml) relatives à Azure Load Balancer.