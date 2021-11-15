---
title: Qu’est-ce qu’Azure Private DNS ?
description: Dans cet article, vous allez découvrir le service d’hébergement DNS privé de Microsoft Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 04/09/2021
ms.author: rohink
ms.openlocfilehash: 819e3f911a769f870b792ed92b699f3356139565
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131457694"
---
# <a name="what-is-azure-private-dns"></a>Qu’est-ce qu’Azure Private DNS ?

Le DNS (Domain Name System) se charge de traduire (ou résoudre) un nom de service en adresse IP.  Azure DNS est un service d’hébergement pour les domaines qui offre une résolution de nommage à l’aide de l’infrastructure Microsoft Azure. Non seulement Azure DNS prend charge les domaines DNS accessibles sur Internet, mais il gère également les zones DNS privées.

Azure Private DNS fournit un service DNS fiable et sécurisé pour votre réseau virtuel. Azure Private DNS gère et résout les noms de domaine dans le réseau virtuel sans nécessiter la configuration d’une solution DNS personnalisée. Grâce aux zones DNS privées, vous pouvez utiliser votre propre nom de domaine personnalisé à la place des noms fournis par Azure pendant le déploiement. L’utilisation de noms de domaine personnalisés vous permet d’adapter votre architecture de réseau virtuel au plus près des besoins de votre organisation. Elle offre une résolution de nommage pour les machines virtuelles au sein d’un réseau virtuel et de réseaux virtuels connectés. De plus, vous pouvez configurer des noms de zones avec une vue divisée qui permet à des zones DNS publique et privée de partager le nom.

Pour résoudre les enregistrements d’une zone DNS privée de votre réseau virtuel, vous devez lier le réseau virtuel à la zone. Les réseaux virtuels liés disposent d’un accès complet et peuvent résoudre tous les enregistrements DNS publiés dans la zone privée. Vous pouvez aussi activer l’inscription automatique sur un lien de réseau virtuel. Lorsque vous activez l’inscription automatique sur un lien de réseau virtuel, les enregistrements DNS des machines virtuelles dans ce réseau virtuel sont inscrits dans la zone privée. Quand l’inscription automatique est activée, Azure DNS met à jour les enregistrements de zone chaque fois qu’une machine virtuelle est créée, qu’elle change d’adresse IP ou qu’elle est supprimée.

![Vue d’ensemble de DNS](./media/private-dns-overview/scenario.png)

> [!NOTE]
> La meilleure pratique consiste à ne pas utiliser de domaine *.local* pour votre zone DNS privée. Certains systèmes d’exploitation ne le permettent pas.

## <a name="benefits"></a>Avantages

Un DNS privé Azure offre les avantages suivants :

* **Évite d’avoir à utiliser des solutions DNS personnalisées**. Auparavant, un grand nombre de clients devaient créer des solutions DNS personnalisées pour gérer les zones DNS dans leur réseau virtuel. Vous pouvez désormais gérer les zones DNS à l’aide de l’infrastructure native d’Azure, ce qui supprime la lourde tâche de créer et gérer des solutions DNS personnalisées.

* **Prise en charge de tous les types d’enregistrements DNS courants**. Azure DNS prend en charge les enregistrements A, AAAA, CNAME, MX, PTR, SOA, SRV et TXT.

* **Gestion automatique des enregistrements de noms d’hôte**. En plus d’héberger vos enregistrements DNS personnalisés, Azure gère automatiquement les enregistrements de noms d’hôte pour les machines virtuelles dans les réseaux virtuels spécifiés. Dans ce scénario, vous pouvez optimiser les noms de domaine que vous utilisez sans avoir à créer de solutions DNS personnalisées ni modifier les applications.

* **Résolution de noms d’hôte entre réseaux virtuels**. Contrairement aux noms d’hôte fournis par Azure, les zones DNS privé peuvent être partagées entre des réseaux virtuels. Cette fonctionnalité simplifie les scénarios de détection de services et réseaux croisés, tels que le peering de réseaux virtuels.

* **Outils familiers et expérience utilisateur**. Pour réduire la courbe d’apprentissage, ce service offre utilise des outils Azure DNS bien établis (portail Azure, Azure PowerShell, Azure CLI, modèles Azure Resource Manager et API REST).

* **Prise en charge DNS avec découpage d’horizon**. Azure DNS vous permet de créer des zones portant le même nom capables de résoudre des résultats différents au sein d’un réseau virtuel et à partir de l’Internet public. Un scénario classique de DNS avec découpage d’horizon consiste à fournir une version dédiée d’un service pour une utilisation au sein du réseau virtuel.

* **Disponible dans toutes les régions Azure**. Les zones privées Azure DNS sont disponibles dans toutes les régions Azure dans le cloud public Azure.

## <a name="capabilities"></a>Fonctionnalités

Azure Private DNS offre les capacités suivantes :

* **Inscription automatique de machines virtuelles à partir d’un réseau virtuel lié à une zone privée avec l’inscription automatique activée**. Les machines virtuelles sont inscrites dans la zone privée en tant qu’enregistrements A pointant vers leurs adresses IP privées. Lors de la suppression d’une machine virtuelle figurant dans un lien de réseau virtuel pour lequel l’inscription automatique est activée, Azure DNS supprime aussi automatiquement l’enregistrement DNS correspondant de la zone privée liée.

* **Prise en charge de la résolution DNS sur plusieurs réseaux virtuels liés à la zone privée**. Pour la résolution DNS entre réseaux virtuels, il n’existe aucune dépendance explicite ayant pour effet d’appairer ces réseaux. En revanche, vous pouvez appairer des réseaux virtuels à d’autres fins (par exemple, pour le trafic HTTP).

* **Prise en charge de la recherche DNS inversée dans l’étendue du réseau virtuel**. La recherche DNS inversée d’une IP privée associée à une zone privée retournera un nom de domaine complet qui inclut le nom de l’enregistrement/hôte, ainsi que le nom de la zone comme suffixe.

## <a name="other-considerations"></a>Autres considérations

Azure Private DNS présente les limites suivantes :

* Un réseau virtuel ne peut être lié qu’à une seule zone privée si l’inscription automatique des enregistrements DNS de machine virtuelle est activée. Vous pouvez toutefois lier plusieurs réseaux virtuels à une seule zone DNS.
* Un DNS inversé ne fonctionne que pour un espace d’adressage IP privé sur le réseau virtuel lié
* Un DNS inversé pour une adresse IP privée dans un réseau virtuel lié retournera `internal.cloudapp.net` comme suffixe par défaut pour la machine virtuelle. Pour les réseaux virtuels liés à une zone privée avec l’inscription automatique activée, un DNS inversé pour une adresse IP privée retourne deux noms de domaine complets : l’un avec le suffixe par défaut `internal.cloudapp.net` et l’autre avec le suffixe de la zone privée.
* Le transfert conditionnel n’est actuellement pas pris en charge en mode natif. Pour activer la résolution entre des réseaux Azure et locaux, consultez [Résolution de noms pour les machines virtuelles et les instances de rôle](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
 
## <a name="pricing"></a>Tarifs

Pour obtenir des informations sur les prix, voir la [tarification Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment créer une zone privée dans Azure DNS à l’aide [d’Azure PowerShell](./private-dns-getstarted-powershell.md) ou [d’Azure CLI](./private-dns-getstarted-cli.md).

* Passez en revue certains [scénarios de zones privées](./private-dns-scenarios.md) courants qui peuvent être réalisés avec des zones privées dans Azure DNS.

* Pour les questions et réponses courantes sur les zones privées dans Azure DNS, consultez [Questions fréquentes (FAQ) sur le DNS privé](./dns-faq-private.yml).

* Pour plus d’informations sur les zones et enregistrements DNS, consultez [Vue d’ensemble des enregistrements et zones DNS](dns-zones-records.md).

* Découvrez certaines des autres [fonctionnalités de réseau](../networking/fundamentals/networking-overview.md) clés d’Azure.
