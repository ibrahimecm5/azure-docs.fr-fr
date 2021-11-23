---
title: Mise en réseau d’App Service Environment
description: Détails de la mise en réseau d’App Service Environment
author: madsd
ms.topic: overview
ms.date: 11/15/2021
ms.author: madsd
ms.openlocfilehash: cdedc94e64a0646c7e4ffa8c93f082d040e440c7
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524072"
---
# <a name="app-service-environment-networking"></a>Mise en réseau d’App Service Environment

> [!NOTE]
> Cet article concerne la fonctionnalité App Service Environment v3 qui est utilisée avec les plans App Service Isolé v2
> 

L’environnement App Service (ASE) est un déploiement monolocataire d’Azure App Service qui héberge des conteneurs Windows et Linux, des applications web, des applications API, des applications logiques et des applications de fonction. Quand vous installez un ASE, vous choisissez le réseau virtuel Azure dans lequel vous voulez le déployer. Tout le trafic d’application entrant et sortant se fait à l’intérieur du réseau virtuel que vous spécifiez. L’ASE est déployé dans un seul sous-réseau de votre réseau virtuel. Rien d’autre ne peut être déployé dans ce même sous-réseau.

## <a name="subnet-requirements"></a>Configuration requise du sous-réseau

Le sous-réseau doit être vide et délégué à Microsoft.Web/hostingEnvironments.

La taille du sous-réseau peut affecter les limites de mise à l’échelle des instances de plan App Service au sein de l’ASE. Nous vous recommandons d’utiliser un espace d’adressage `/24` (256 adresses) pour votre sous-réseau afin de garantir un nombre suffisant d’adresses pour prendre en charge la mise à l’échelle de production.

Pour utiliser un sous-réseau plus petit, vous devez tenir compte des détails suivants relatifs à l’ASE et à la configuration réseau.

Tout sous-réseau donné possède cinq adresses réservées aux fins de gestion. En plus des adresses de gestion, l’ASE met dynamiquement à l’échelle l’infrastructure de prise en charge et utilise entre 4 et 27 adresses, en fonction de la configuration et de la charge. Les adresses restantes peuvent être utilisées pour les instances du plan App Service. La taille minimale de votre sous-réseau est un espace d’adressage `/27` (32 adresses).

Si vous n’avez pas assez d’adresses dans votre sous-réseau, vous pouvez être limité dans le scale-out de vos plans App Service dans l’ASE ou constater une augmentation de la latence quand la charge du trafic est intense si nous ne pouvons pas mettre à l’échelle l’infrastructure de prise en charge.

## <a name="addresses"></a>Adresses

ASE dispose des informations réseau suivantes lors de sa création :

| Type d’adresse | description |
|--------------|-------------|
| Réseau virtuel ASE | Le réseau virtuel où l’ASE est déployé |
| Sous-réseau de l’ASE | Sous-réseau dans lequel ASE est déployé |
| Suffixe de domaine | Suffixe de domaine utilisé par les applications réalisées dans cet environnement ASE |
| Adresse IP virtuelle | Ce paramètre représente l’adresse IP virtuelle utilisée par l’ASE. Les deux valeurs possibles sont interne et externe |
| Adresse entrante | L’adresse entrante correspond à l’adresse à laquelle vos applications sur cet environnement ASE sont atteintes. Si vous avez une adresse IP virtuelle interne, il s’agit d’une adresse de votre sous-réseau ASE. Si l’adresse est externe, il s’agit d’une adresse publique |
| Adresses sortantes par défaut | Les applications de cet environnement ASE utilisent cette adresse, par défaut, lors de l’établissement d’appels sortants vers Internet |

ASEv3 contient des détails sur les adresses utilisées par l’environnement ASE dans la partie **Adresses IP** du portail ASE.

![Interface utilisateur des adresses ASE](./media/networking/networking-ip-addresses.png)

À mesure que vous mettez à l’échelle vos plans App Service dans votre environnement ASE, vous utiliserez davantage d’adresses de votre sous-réseau ASE. Le nombre d’adresses utilisées varie selon le nombre d’instances du plan App Service que vous avez et du volume de trafic qu’ASE reçoit. Les applications de l’environnement ASE n’ont pas d’adresses dédiées dans le sous-réseau ASE. Les adresses spécifiques utilisées par une application dans le sous-réseau ASE par une application changent au fil du temps.

## <a name="ports-and-network-restrictions"></a>Ports et restrictions réseau

Pour que votre application reçoive du trafic, vous devez vérifier que les règles de groupe de sécurité réseau (NSG) du trafic entrant autorisent le sous-réseau de l’ASE à recevoir le trafic à partir des ports nécessaires. En plus des ports sur lesquels vous voulez recevoir le trafic, vous devez vérifier que l’équilibreur de charge Azure peut se connecter au sous-réseau ASE sur le port 80. Il est utilisé pour les vérifications d’intégrité de machines virtuelles internes. Vous pouvez toujours contrôler le trafic sur le port 80 entre le réseau virtuel et votre sous-réseau ASE.

La recommandation générale est de configurer la règle NSG de trafic entrant suivante :

|Port|Source|Destination|
|-|-|-|
|80,443|VirtualNetwork|Plage de sous-réseau ASE|

Les exigences minimales pour que l’ASE soit opérationnel sont les suivantes :

|Port|Source|Destination|
|-|-|-|
|80|AzureLoadBalancer|Plage de sous-réseau ASE|

Si vous utilisez la règle minimum nécessaire, vous pouvez avoir besoin d’une ou de plusieurs règles pour le trafic de votre application. Si vous utilisez une option de déploiement ou de débogage, vous devez également autoriser ce trafic vers le sous-réseau ASE. La source de ces règles peut être le réseau virtuel, ou une ou plusieurs plages IP ou des IP clientes spécifiques. La destination est toujours la plage du sous-réseau ASE.

Les ports d’accès normaux pour les applications sont les suivants :

|Utilisation|Ports|
|-|-|
|HTTP/HTTPS|80, 443|
|FTP/FTPS|21, 990, 10001-10020|
|Débogage distant de Visual Studio|4022, 4024, 4026|
|Service Web Deploy|8172|

## <a name="network-routing"></a>Routage réseau

Vous pouvez définir des tables de route (UDR) sans restriction. Vous pouvez forcer le tunneling de tout le trafic d’application sortant de votre environnement ASE vers un appareil de pare-feu de sortie, comme le Pare-feu Azure, et ne vous soucier de rien d’autre que des dépendances de vos applications. Vous pouvez placer des périphériques WAF, tels que la passerelle applicative, devant le trafic entrant vers votre environnement ASE pour exposer des applications spécifiques sur cet environnement. Pour personnaliser l’adresse sortante de vos applications sur un ASE, vous pouvez ajouter une passerelle NAT à votre sous-réseau ASE.

## <a name="dns"></a>DNS

Les sections suivantes décrivent les considérations et la configuration DNS pour l’entrée et la sortie dans votre ASE.

### <a name="dns-configuration-to-your-ase"></a>Configuration DNS vers votre environnement ASE

Si votre environnement ASE est créé avec une adresse IP virtuelle externe, vos applications sont automatiquement placées dans un DNS public. Si votre environnement ASE est créé avec une adresse IP virtuelle interne, vous devrez peut-être configurer le service DNS pour celle-ci. Si vous avez choisi de configurer automatiquement les zones privées Azure DNS pendant la création de l’ASE, le DNS est configuré dans votre réseau virtuel ASE. Si vous avez sélectionné l’option de configuration manuelle de DNS, vous devez utiliser votre propre serveur DNS ou configurer les zones privées Azure DNS. Pour trouver l’adresse entrante de votre ASE, accédez à l’interface utilisateur **portail ASE > adresses IP**. 

Si vous souhaitez utiliser votre propre serveur DNS, vous devez ajouter les enregistrements suivants :

1. créez une zone pour `<ASE-name>.appserviceenvironment.net`
1. créez un enregistrement A dans cette zone qui pointe * vers l’adresse IP entrante qu’utilise votre ASE
1. créez un enregistrement A dans cette zone qui pointe @ vers l’adresse IP entrante qu’utilise votre ASE
1. créez une zone dans `<ASE-name>.appserviceenvironment.net` nommée scm
1. Créez un enregistrement A dans la zone scm qui pointe * vers l’adresse IP qu’utilise le point de terminaison privé de votre ASE.

Pour configurer DNS dans les zones privées Azure DNS :

1. créez une zone privée Azure DNS nommée `<ASE-name>.appserviceenvironment.net`
1. créez un enregistrement A dans cette zone qui pointe * vers l’adresse IP entrante
1. créez un enregistrement A dans cette zone qui pointe @ vers l’adresse IP entrante
1. créez un enregistrement A dans cette zone qui pointe *.scm vers l’adresse IP entrante

En plus du domaine par défaut fourni pendant la création d’une application, vous pouvez également ajouter un domaine personnalisé à votre application. Vous pouvez définir un nom de domaine personnalisé sans validation sur vos applications dans un environnement ASE ILB. Si vous utilisez des domaines personnalisés, vous devez vérifier que des enregistrements DNS sont configurés. Vous pouvez suivre les instructions ci-dessus pour configurer des zones et des enregistrements DNS pour un nom de domaine personnalisé en remplaçant le nom de domaine par défaut par le nom de domaine personnalisé. Le nom de domaine personnalisé fonctionne pour les demandes d’application, mais pas pour le site GCL. Le site GCL est disponible uniquement pour *&lt;appname&gt;.scm.&lt;asename&gt;.appserviceenvironment.net*.

### <a name="dns-configuration-from-your-ase"></a>Configuration DNS à partir de votre environnement ASE

Les applications dans votre ASE utilisent le DNS avec lequel votre réseau virtuel est configuré. Si vous voulez que certaines applications utilisent un serveur DNS différent de celui avec lequel votre réseau virtuel est configuré, vous pouvez le définir manuellement pour chaque application avec les paramètres d’application WEBSITE_DNS_SERVER et WEBSITE_DNS_ALT_SERVER. Le paramètre d’application WEBSITE_DNS_ALT_SERVER configure le serveur DNS secondaire. Le serveur DNS secondaire est utilisé uniquement en l’absence de réponse du serveur DNS principal.

## <a name="limitations"></a>Limites

Quand l’ASE est déployé dans le réseau virtuel d’un client, certaines fonctionnalités réseau ne sont pas disponibles avec l’ASE :

* Envoyer du trafic SMTP. Vous pouvez toujours bénéficier des alertes déclenchées par e-mail, mais votre application ne peut pas envoyer de trafic sortant sur le port 25
* Utiliser Network Watcher ou un flux NSG pour surveiller le trafic sortant

## <a name="more-resources"></a>Plus de ressources

- [Informations de référence sur les variables d’environnement et les paramètres d’application](../reference-app-settings.md)