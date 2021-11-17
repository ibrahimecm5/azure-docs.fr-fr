---
title: 'Tutoriel : Configurer un réseau dans un cluster Azure FXT Edge Filer'
description: Découvrez comment personnaliser les paramètres réseau après la création du cluster Microsoft Azure FXT Edge Filer.
author: femila
ms.author: femila
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 10/07/2021
ms.openlocfilehash: a51f6654c8e82a46c1a31f2dd030cd2d5af754cd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347301"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>Tutoriel : Configurer les paramètres réseau du cluster

Avant d’utiliser un nouveau cluster Azure FXT Edge Filer, vous devez vérifier et personnaliser plusieurs paramètres réseau pour votre flux de travail.

Ce tutoriel décrit les paramètres réseau que vous devrez peut-être ajuster pour un nouveau cluster.

Vous apprendrez à effectuer les opérations suivantes :

> [!div class="checklist"]
>
> * Quels paramètres réseau devront être mis à jour après la création d’un cluster.
> * Quels cas d’usage Azure FXT Edge Filer nécessitent un serveur Active Directory ou un serveur DNS.
> * Comment configurer le tourniquet DNS (RRDNS) pour équilibrer automatiquement la charge des requêtes des clients sur le cluster FXT.

La durée nécessaire pour effectuer ces étapes dépend du nombre de modifications de configuration nécessaires dans votre système :

* Si vous avez uniquement besoin de lire le tutoriel et de vérifier quelques paramètres, il vous faudra de 10 à 15 minutes.
* Si vous avez besoin de configurer le tourniquet DNS, cette tâche peut prendre une heure ou plus.

## <a name="adjust-network-settings"></a>Ajuster les paramètres réseau

La configuration d’un nouveau cluster Azure FXT Edge Filer implique plusieurs tâches liées au réseau. Vérifiez cette liste et choisissez celles qui s’appliquent à votre système.

Pour en savoir plus sur les paramètres réseau pour le cluster, consultez la section sur la [configuration des services réseau](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html) dans le Guide de configuration de cluster.

* Configurer le tourniquet DNS pour le réseau utilisé par le client (facultatif)

  Équilibrez la charge du trafic de cluster en configurant le système DNS comme décrit dans [Configurer le système DNS pour le cluster FXT Edge Filer](#configure-dns-for-load-balancing).

* Vérifier les paramètres NTP

* Configurer Active Directory et les téléchargements de noms d’utilisateurs/groupes (si nécessaire)

  Si vos hôtes réseau utilisent Active Directory ou un autre type de service d’annuaire externe, vous devez modifier la configuration des services d’annuaire du cluster pour configurer comment le cluster télécharge les informations sur les noms d’utilisateurs et les groupes. Pour plus d’informations, consultez **Cluster** > **Directory Services** dans le Guide de configuration de cluster.

  Un serveur AD est obligatoire si vous souhaitez prendre en charge SMB. Configurez AD avant de commencer à configurer SMB.

* Définir des réseaux locaux virtuels (facultatif)
  
  Configurez les réseaux locaux virtuels supplémentaires nécessaires avant de définir les serveurs virtuels et l’espace de noms global de votre cluster. Pour plus d’informations, consultez [Working with VLANs](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) dans le Guide de configuration de cluster.

* Configurer les serveurs proxy (si nécessaire)

  Si votre cluster utilise un serveur proxy pour atteindre des adresses externes, effectuez ces étapes pour le configurer :

  1. Définissez le serveur proxy dans la page de paramètres **Configuration du proxy**.
  1. Appliquez la configuration du serveur proxy avec la page **Cluster** > **General Setup** ou **Core Filer Details**.
  
  Pour plus d’informations, consultez [Using web proxies](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html) dans le Guide de configuration de cluster.

* Charger les [certificats de chiffrement](#encryption-certificates) pour le cluster (facultatif)

### <a name="encryption-certificates"></a>Certificats de chiffrement

Le cluster FXT Edge Filer utilise des certificats X.509 pour ces fonctions :

* Pour chiffrer le trafic d’administration de cluster

* Pour authentifier un client auprès des serveurs KMIP tiers

* Pour vérifier les certificats de serveur des fournisseurs de cloud

Si vous avez besoin de charger des certificats sur le cluster, utilisez la page de paramètres **Cluster** > **Certificates**. Pour plus d’informations, consultez la page [Cluster > Certificates](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html) dans le Guide de configuration de cluster.

Pour chiffrer la communication de gestion de cluster, utilisez la page de paramètres **Cluster** > **General Setup** (Configuration générale) afin de sélectionner le certificat à utiliser pour le protocole TLS d’administration.

Vérifiez que vos machines d’administration respectent les [standards de chiffrement](supported-ciphers.md)du cluster.

> [!Note]
> Les clés d’accès au service cloud sont stockés à l’aide de la page de configuration **Cloud Credentials**. La section [Ajouter un Core Filer](add-storage.md#add-a-core-filer) montre un exemple. Pour plus d’informations, consultez la section [Cloud Credentials](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html) du Guide de configuration de cluster.

## <a name="configure-dns-for-load-balancing"></a>Configurer le système DNS pour l’équilibrage de charge

Cette section explique les principes fondamentaux de la configuration d’un tourniquet DNS (RRDNS) pour répartir la charge client parmi toutes les adresses IP exposées aux clients dans votre cluster FXT Edge Filer.

### <a name="decide-whether-or-not-to-use-dns"></a>Décider s’il faut utiliser DNS ou non

L’équilibrage de charge est toujours recommandée, mais vous n’êtes pas obligé de toujours utiliser le système DNS. Par exemple, avec certains types de flux de travail client, il peut être plus judicieux d’utiliser un script pour affecter les adresses IP de cluster uniformément parmi les clients quand ils montent le cluster. Certaines méthodes sont décrites dans [Monter le cluster](mount-clients.md).

Pour déterminer si vous devez ou non utiliser un serveur DNS, prenez en compte les considérations suivantes :

* Si votre système est sollicité uniquement par des clients NFS, le système DNS n’est pas nécessaire. Il est possible de spécifier toutes les adresses réseau à l’aide d’adresses IP numériques.

* Si votre système prend en charge l’accès SMB (CIFS), un DNS est nécessaire, car vous devez alors spécifier un domaine DNS pour le serveur Active Directory.

* Un DNS est nécessaire pour utiliser l’authentification Kerberos.

### <a name="round-robin-dns-configuration-details"></a>Détails de configuration du tourniquet DNS

Un système DNS à tourniquet (Round Robin) (RRDNS) achemine automatiquement les requêtes des clients entre plusieurs adresses.

Pour configurer ce système, vous devez personnaliser le fichier config du serveur DNS de façon à ce qu’il attribue le trafic parmi tous les points de montage du système HPC Cache lorsqu’il reçoit des requêtes de montage vers l’adresse de domaine principale HPC Cache. Les clients montent le cluster en utilisant son nom de domaine comme argument de serveur. Ils sont ensuite routés automatiquement vers l’adresse IP de montage suivante.

Il existe deux étapes principales pour configurer RRDNS :

1. Modifiez le fichier ``named.conf`` de votre serveur DNS pour définir l’ordre cyclique des requêtes envoyées à votre cluster FXT. Cette option amène le serveur à parcourir toutes les valeurs d’adresse IP disponibles. Ajoutez une instruction similaire à celle-ci :

   ```bash
   options {
       rrset-order {
           class IN A name "fxt.contoso.com" order cyclic;
       };
   };
   ```

1. Configurez des enregistrements A et des enregistrements de pointeur (PTR) pour chaque adresse IP disponible, comme dans l’exemple suivant.

   Ces commandes ``nsupdate`` fournissent un exemple de configuration correcte du DNS pour un cluster Azure FXT Edge Filer avec le nom de domaine fxt.contoso.com et trois adresses de montage (10.0.0.10, 10.0.0.11 et 10.0.0.12) :

   ```bash
   update add fxt.contoso.com. 86400 A 10.0.0.10
   update add fxt.contoso.com. 86400 A 10.0.0.11
   update add fxt.contoso.com. 86400 A 10.0.0.12
   update add client-IP-10.contoso.com. 86400 A 10.0.0.10
   update add client-IP-11.contoso.com. 86400 A 10.0.0.11
   update add client-IP-12.contoso.com. 86400 A 10.0.0.12
   update add 10.0.0.10.in-addr.arpa. 86400 PTR client-IP-10.contoso.com
   update add 11.0.0.10.in-addr.arpa. 86400 PTR client-IP-11.contoso.com
   update add 12.0.0.10.in-addr.arpa. 86400 PTR client-IP-12.contoso.com
   ```

   Ces commandes créent un enregistrement A pour chacune des adresses de montage du cluster et configurent également des enregistrements de pointeur pour prendre en charge les vérifications de DNS inversé de manière appropriée.

   Le diagramme ci-dessous illustre la structure de base de cette configuration.

   :::image type="complex" source="media/round-robin-dns-diagram-fxt.png" alt-text="Diagramme montrant la configuration DNS du point de montage client.":::
   <Le diagramme montre les connexions entre trois catégories d’éléments : le nom de domaine unique du cluster FXT Edge Filer (à gauche), trois adresses IP (colonne du milieu) et trois interfaces clientes de DNS inversé d’usage interne (colonne de droite). Sur la gauche, un ovale intitulé « fxt.contoso.com » est connecté par des flèches pointant vers trois ovales étiquetés avec des adresses IP : 10.0.0.10, 10.0.0.11 et 10.0.0.12. Les flèches entre l’ovale fxt.contoso.com et les trois ovales IP sont étiquetées « A ». Chacun des ovales de l’adresse IP est connecté par deux flèches à un ovale étiqueté comme une interface client : l’ovale avec l’adresse IP 10.0.0.10 est connecté à « client-IP-10.contoso.com », l’ovale avec l’adresse IP 10.0.0.11 est connecté à « client-IP-11.contoso.com » et l’ovale avec l’adresse IP 10.0.0.12 est connecté à « client-IP-11.contoso.com ». Les connexions entre les ovales de l’adresse IP et les ovales de l’interface client sont deux flèches : une flèche intitulée « PTR » qui pointe de l’ovale de l’adresse IP vers l’ovale de l’interface client et une flèche intitulée « A » qui pointe de l’ovale de l’interface client vers l’ovale de l’adresse IP.> :::image-end:::

Une fois le système RRDNS configuré, demandez à vos ordinateurs clients de l’utiliser pour résoudre l’adresse du cluster FXT dans leurs commandes de montage.

### <a name="enable-dns-in-the-cluster"></a>Activer le système DNS dans le cluster

Spécifiez le serveur DNS utilisé par le cluster dans la page de paramètres **Cluster** > **Administrative Network**. Cette page contient les paramètres suivants :

* Adresse du serveur DNS
* Nom du domaine DNS
* Domaines de recherche DNS

Pour plus d’informations, consultez [DNS Settings](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns) dans le Guide de configuration de cluster.

## <a name="next-steps"></a>Étapes suivantes

Il s’agit de la dernière étape de configuration de base pour le cluster Azure FXT Edge Filer.

* Apprenez-en davantage sur les voyants et autres indicateurs du système dans [Superviser l’état du matériel](monitor.md).
* Apprenez-en davantage sur la façon dont les clients doivent monter le cluster FXT Edge Filer dans [Monter le cluster](mount-clients.md).
* Pour plus d’informations sur l’exploitation et la gestion d’un cluster FXT Edge Filer, consultez le [Guide de configuration de cluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html).
