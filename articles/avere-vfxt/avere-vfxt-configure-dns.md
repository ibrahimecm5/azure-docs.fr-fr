---
title: Configuration d’un DNS pour Avere vFXT - Azure
description: Découvrez comment configurer un serveur DNS pour l’équilibrage de charge en tourniquet (round-robin) avec Avere vFXT pour Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: rohogue
ms.openlocfilehash: 26f85b4d3a2cc49e55d228fbd73ae7cb3b66bf5d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337867"
---
# <a name="avere-cluster-dns-configuration"></a>Configuration d’un DNS pour le cluster Avere

Cette section explique les principes de base de la configuration d’un système DNS pour l’équilibrage de charge de votre cluster Avere vFXT.

Ce document *n’inclut pas* d’instructions pour la configuration et la gestion d’un serveur DNS dans l’environnement Azure.

Au lieu d’utiliser le DNS en tourniquet pour équilibrer la charge d’un cluster vFXT dans Azure, assignez manuellement les adresses IP en les répartissant entre les différents clients montés. Plusieurs méthodes d’assignation sont décrites dans [Monter le cluster Avere](avere-vfxt-mount-clients.md).

Pour déterminer si vous devez ou non utiliser un serveur DNS, prenez en compte les considérations suivantes :

* Si votre système est accessible par des clients NFS uniquement, l’utilisation d’un DNS n’est pas nécessaire, car il est possible de spécifier toutes les adresses réseau à l’aide d’adresses IP numériques.

* Si votre système prend en charge l’accès SMB (CIFS), un DNS est nécessaire, car vous devez alors spécifier un domaine DNS pour le serveur Active Directory.

* Un DNS est nécessaire pour utiliser l’authentification Kerberos.

## <a name="load-balancing"></a>Équilibrage de la charge

Pour répartir la charge globale, configurez votre domaine DNS afin qu’il utilise l’équilibrage de charge en tourniquet (round-robin) pour répartir les adresses IP accessibles par les clients.

## <a name="configuration-details"></a>Détails de configuration

Lorsque les clients accèdent au cluster, RRDNS (DNS tourniquet) répartit automatiquement leurs requêtes entre toutes les interfaces disponibles.

Pour configurer ce système, vous devez personnaliser le fichier config du serveur DNS de façon à ce qu’il attribue le trafic parmi tous les points de montage du cluster vFXT lorsqu’il reçoit des requêtes de montage vers l’adresse de domaine principale du cluster. Les clients montent le cluster vFXT en utilisant son nom de domaine comme argument de serveur. Ils sont ensuite routés automatiquement vers l’adresse IP de montage suivante.

Il existe deux étapes principales pour configurer RRDNS :

1. Modifiez le fichier ``named.conf`` de votre serveur DNS pour définir l’ordre cyclique des requêtes envoyées à votre cluster vFXT. Cette option amène le serveur à parcourir toutes les valeurs d’adresse IP disponibles. Ajoutez une instruction similaire à celle-ci :

   ```bash
   options {
       rrset-order {
           class IN A name "vfxt.contoso.com" order cyclic;
       };
   };
   ```

1. Configurez des enregistrements A et des enregistrements de pointeur (PTR) pour chaque adresse IP disponible, comme dans l’exemple suivant.

   Ces commandes ``nsupdate`` fournissent un exemple de configuration correcte du DNS pour un cluster vFXT avec le nom de domaine vfxt.contoso.com et trois adresses de montage (10.0.0.10, 10.0.0.11 et 10.0.0.12) :

   ```bash
   update add vfxt.contoso.com. 86400 A 10.0.0.10
   update add vfxt.contoso.com. 86400 A 10.0.0.11
   update add vfxt.contoso.com. 86400 A 10.0.0.12
   update add client-IP-10.contoso.com. 86400 A 10.0.0.10
   update add client-IP-11.contoso.com. 86400 A 10.0.0.11
   update add client-IP-12.contoso.com. 86400 A 10.0.0.12
   update add 10.0.0.10.in-addr.arpa. 86400 PTR client-IP-10.contoso.com
   update add 11.0.0.10.in-addr.arpa. 86400 PTR client-IP-11.contoso.com
   update add 12.0.0.10.in-addr.arpa. 86400 PTR client-IP-12.contoso.com
   ```

   Ces commandes créent un enregistrement A pour chacune des adresses de montage du cluster et configurent également des enregistrements de pointeur pour prendre en charge les vérifications de DNS inversé de manière appropriée.

   Le diagramme ci-dessous illustre la structure de base de cette configuration.

   :::image type="complex" source="media/round-robin-dns-diagram-vfxt.png" alt-text="Diagramme montrant la configuration DNS du point de montage client.":::
   <Le diagramme montre les connexions entre trois catégories d’éléments : le nom de domaine unique du cluster vFXT (à gauche), trois adresses IP (colonne du milieu) et trois interfaces clientes inversées d’usage interne (colonne de droite). Sur la gauche, un ovale intitulé « vfxt.contoso.com » est connecté par des flèches pointant vers trois ovales étiquetés avec des adresses IP : 10.0.0.10, 10.0.0.11 et 10.0.0.12. Les flèches entre l’ovale vfxt.contoso.com et les trois ovales IP sont étiquetées « A ». Chacun des ovales de l’adresse IP est connecté par deux flèches à un ovale étiqueté comme une interface client : l’ovale avec l’adresse IP 10.0.0.10 est connecté à « client-IP-10.contoso.com », l’ovale avec l’adresse IP 10.0.0.11 est connecté à « client-IP-11.contoso.com » et l’ovale avec l’adresse IP 10.0.0.12 est connecté à « client-IP-11.contoso.com ». Les connexions entre les ovales de l’adresse IP et les ovales de l’interface client sont deux flèches : une flèche intitulée « PTR » qui pointe de l’ovale de l’adresse IP vers l’ovale de l’interface client et une flèche intitulée « A » qui pointe de l’ovale de l’interface client vers l’ovale de l’adresse IP.> :::image-end:::

Une fois le système RRDNS configuré, demandez à vos ordinateurs clients de l’utiliser pour résoudre l’adresse du cluster dans leurs commandes de montage.

## <a name="cluster-dns-settings"></a>Paramètres du DNS du cluster

Spécifiez le serveur DNS utilisé par le cluster vFXT dans la page de paramètres **Cluster** > **Administrative Network** (Cluster > Réseau d’administration). Cette page contient les paramètres suivants :

* Adresse du serveur DNS
* Nom du domaine DNS
* Domaines de recherche DNS

Pour plus d’informations sur l’utilisation de cette page, consultez [DNS Settings](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns) dans le guide de configuration du cluster Avere.
