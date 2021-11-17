---
title: 'Azure Front Door : Questions fréquentes'
description: Cette page fournit des réponses aux questions les plus souvent posées sur Azure Front Door Standard/Premium.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 05/18/2021
ms.author: duau
ms.openlocfilehash: 2be4e0b08ca31cabbd0ffbadf37760d289da7eb9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331259"
---
# <a name="frequently-asked-questions-for-azure-front-door-standardpremium-preview"></a>Questions fréquentes sur Azure Front Door Standard/Premium (préversion)

Cet article répond aux questions courantes sur les fonctionnalités d’Azure Front Door.

## <a name="general"></a>Général

### <a name="what-is-azure-front-door"></a>Qu’est-ce qu’Azure Front Door ?

Azure Front Door est un CDN Cloud moderne, rapide, fiable et sécurisé, avec protection intelligente contre les menaces. Il offre une accélération de contenu statique et dynamique, un équilibrage de charge global et une sécurité renforcée pour vos applications, API, sites Web et services Cloud hyperscale globaux avec protection intelligente contre les menaces.

### <a name="what-features-does-azure-front-door-support"></a>Quelles sont les fonctionnalités prises en charge par Azure Front Door ?

Azure Front Door prend en charge :

* Le contenu statique et l’accélération d’application dynamique
* Le échargement TLS/SSL et TLS de bout en bout
* Pare-feu d’applications web
* Affinité de session basée sur les cookies
* Le routage basé sur le chemin de l’URL
* Les certificats gratuits et gestions de plusieurs domaines

Pour obtenir une liste complète des fonctionnalités prises en charge, consultez [Présentation d’Azure Front Door](overview.md).

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Quelle est la différence entre Azure Front Door et Azure Application Gateway ?

Bien que Front Door et Application Gateway soient tous deux des équilibreurs de charge de couche 7 (HTTP/HTTPS), la principale différence est que Front Door est un service mondial. Application Gateway est un service régional. Alors que Front Door peut équilibrer la charge entre vos différents empreintes/clusters/unités d’échelle dans plusieurs régions, Application Gateway vous permet d’équilibrer la charge entre vos machines virtuelles/conteneurs, qui se trouvent dans l’unité d’échelle.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Quand faut-il déployer Application Gateway derrière Front Door ?

Les principaux scénarios pouvant justifier l’utilisation d’Application Gateway derrière Front Door sont les suivants :

* Front Door peut effectuer un équilibrage de charge basé sur le chemin uniquement au niveau mondial, mais si vous souhaitez équilibrer encore davantage le trafic avec votre réseau virtuel, vous devez utiliser Application Gateway.
* Étant donné que Front Door ne fonctionne pas au niveau de la machine virtuelle ou du conteneur, il ne peut pas effectuer de drainage des connexions. contrairement à Application Gateway. 
* Avec Application Gateway derrière Front Door, vous pouvez atteindre un déchargement TLS/SSL de 100 % et acheminer uniquement les requêtes HTTP au sein du réseau virtuel.
* Front Door et Application Gateway prennent tous deux en charge l’affinité de session. Front Door peut diriger le trafic résultant d'une session utilisateur vers le même back-end à des fins de traitement dans une région donnée. Application Gateway peur directement affiner le trafic vers le même serveur au sein du cluster.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Est-il possible de déployer Azure Load Balancer derrière Front Door ?

Azure Front Door a besoin d’une adresse IP virtuelle publique ou d’un nom DNS disponible publiquement vers lequel router le trafic. Le déploiement d’Azure Load Balancer derrière Front Door est un cas d’usage courant.

### <a name="what-protocols-does-azure-front-door-support"></a>Quels sont les protocoles pris en charge par Azure Front Door ?

Azure Front Door prend en charge HTTP, HTTPS et HTTP/2.

### <a name="how-does-azure-front-door-support-http2"></a>Dans quelle mesure Azure Front Door prend-il en charge HTTP/2 ?

La prise en charge du protocole HTTP/2 est disponible pour les clients se connectant uniquement à Azure Front Door. La communication vers les back-ends dans le pool de back-ends s’effectue sur HTTP/1.1. La prise en charge d’HTTP/2 est activée par défaut.

### <a name="what-resources-are-supported-today-as-part-of-an-origin-group"></a>Quelles sont les ressources actuellement prises en charge dans un groupe d’origine ?

Les groupes d’origine peuvent être composés de deux types d’origines :

- Les origines publiques incluent les comptes de stockage, les applications App Service, les instances Kubernetes ou tout autre nom d’hôte personnalisé disposant d’une connectivité publique. Ces origines doivent être définies à l’aide d’une adresse IP publique ou d’un nom d’hôte DNS pouvant être résolu publiquement. Les membres des groupes d’origine peuvent être déployés dans des zones de disponibilité, des régions ou même en dehors d’Azure tant qu’ils disposent d’une connectivité IP. Les origines publiques sont prises en charge pour les niveaux Front Door Standard et Premium.
- Les [origines Private Link](concept-private-link.md) sont disponibles quand vous utilisez Azure Front Door (Premium).

### <a name="what-regions-is-the-service-available-in"></a>Dans quelles régions le service est-il disponible ?

Azure Front Door est un service mondial ; il n’est pas lié à une région Azure spécifique. Le seul emplacement que vous devez indiquer en créant un service Front Door est pour le groupe de ressources. Cet emplacement précise l’endroit où les métadonnées du groupe de ressources seront stockées. Le ressource Front Door proprement dite est créée en tant que ressource globale, et la configuration est déployée globalement vers tous les emplacements périphériques. 

### <a name="where-are-the-edge-locations-for-azure-front-door"></a>Où sont les emplacements périphériques pour Azure Front Door ?

Pour obtenir la liste complète des emplacements périphériques pour Azure Front Door, consultez [Emplacements périphériques Azure Front Door](edge-locations.md).

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Azure Front Door est-il un déploiement dédié à mon application ou est-il partagé entre les clients ?

Azure Front Door est un service multilocataire distribué à l’échelle mondiale. L’infrastructure pour Front Door est donc partagée entre tous ses clients. En créant une instance Front Door, vous définissez la configuration spécifique requise pour votre application. Les modifications apportées à votre instance Front Door n’affectent pas les autres configurations Front Door.

### <a name="is-http-https-redirection-supported"></a>La redirection HTTP->HTTPS est-elle prise en charge ?

Oui. En fait, Azure Front Door prend en charge la redirection de chaîne de requête, de chemin et d’hôte ainsi qu’une partie de la redirection d’URL. Découvrez-en plus sur la [redirection d’URL](concept-rule-set-url-redirect-and-rewrite.md). 

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Comment faire pour restreindre l’accès à mon back-end uniquement à Azure Front Door ?

La meilleure façon de verrouiller votre application pour accepter le trafic uniquement à partir de votre instance Front Door spécifique consiste à publier votre application via un point de terminaison privé. Le trafic entre Front Door et l’application traverse le réseau virtuel et Private Link sur le réseau principal de Microsoft, ce qui élimine l’exposition sur l’Internet public.

En savoir plus sur l’[origine de la sécurisation de Front Door avec Private Link](concept-private-link.md).  

Autre façon de verrouiller votre application pour accepter le trafic uniquement à partir de votre Front Door spécifique, vous devez configurer des listes de contrôle d’accès (ACL) IP pour votre serveur principal. Limitez ensuite le trafic de votre serveur principal à la valeur spécifique de l’en-tête « X-Azure-FDID » envoyé par Front Door. Voici le détail de ces étapes :

* Configurez les ACL IP pour vos back-ends de manière à accepter le trafic en provenance uniquement de l’espace d’adressage IP back-end d’Azure Front Door et des services d’infrastructure d’Azure. Reportez-vous aux détails d’adresses IP ci-dessous afin de configurer les listes ACL pour votre back-end :
 
    * Consultez la section *AzureFrontDoor.Backend* dans [Plage d'adresses IP Azure et Étiquettes de service](https://www.microsoft.com/download/details.aspx?id=56519) pour la plage d'adresses IP back-end de Front Door. Vous pouvez également utiliser l’étiquette de service *AzureFrontDoor.Backend* dans vos [groupes de sécurité réseau](../../virtual-network/network-security-groups-overview.md#security-rules).
    * [Services d’infrastructure de base](../../virtual-network/network-security-groups-overview.md#azure-platform-considerations) d’Azure par le biais des adresses IP hôte virtualisées : `168.63.129.16` et `169.254.169.254`.

    > [!WARNING]
    > L’espace IP back-end de Front Door pourra changer ultérieurement, mais avant cela nous ferons en sorte d’avoir procédé à l’intégration avec les [balises de service et les plages d’adresses IP Azure](https://www.microsoft.com/download/details.aspx?id=56519). Nous vous recommandons de vous abonner aux [balises de service et aux plages d’adresses IP Azure](https://www.microsoft.com/download/details.aspx?id=56519) pour vous tenir informé des modifications ou mises à jour.

* Effectuez une opération GET sur votre instance Front Door avec la version d’API `2020-01-01` ou une version ultérieure. Dans l’appel d’API, recherchez le champ `frontdoorID`. Filtrez sur l’en-tête entrant « **X-Azure-FDID** » envoyé par Front Door à votre back-end avec la valeur du champ `frontdoorID`. Vous pouvez également trouver la valeur `Front Door ID` sous la section Vue d’ensemble de la page du portail Front Door. 

* Appliquez un filtrage de règles sur votre serveur web principal pour limiter le trafic en fonction de la valeur obtenue de l’en-tête « X-Azure-FDID ».

  Voici un exemple pour [Microsoft Internet Information Services (IIS)](https://www.iis.net/) :

    ``` xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
        <system.webServer>
            <rewrite>
                <rules>
                    <rule name="Filter_X-Azure-FDID" patternSyntax="Wildcard" stopProcessing="true">
                        <match url="*" />
                        <conditions>
                            <add input="{HTTP_X_AZURE_FDID}" pattern="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" negate="true" />
                        </conditions>
                        <action type="AbortRequest" />
                    </rule>
                </rules>
            </rewrite>
        </system.webServer>
    </configuration>
    ```

* Azure Front Door prend également en charge la balise de service *AzureFrontDoor.Frontend*, qui fournit la liste des adresses IP que les clients utilisent lorsqu'ils se connectent à Front Door. Vous pouvez utiliser la balise de service *AzureFrontDoor.Frontend* lorsque vous contrôlez le trafic sortant qui doit être autorisé à se connecter aux services déployés derrière Azure Front Door. Azure Front Door prend également en charge une balise de service supplémentaire, *AzureFrontDoor.FirstParty* , pour une intégration interne avec d'autres services Azure. Voir les [balises de service disponibles](../../virtual-network/service-tags-overview.md#available-service-tags) pour plus de détails sur les cas d'utilisation des balises de service Azure Front Door.

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>L’adresse IP anycast peut-elle changer pendant la durée de vie de ma porte d’entrée ?

L’adresse IP anycast front-end de votre porte d’entrée ne devrait généralement pas changer, et peut rester statique pendant toute la durée de vie de Front Door. Toutefois, il n’existe **aucune garantie** à ce sujet. Veillez à ne créer aucune dépendance directe envers l’adresse IP.

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>Azure Front Door prend-il en charge les adresses IP statiques ou dédiées ?

Non, actuellement Azure Front Door ne prend pas en charge les adresses IP anycast front-end statiques ou dédiées. 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>Azure Front Door prend-il en charge les en-têtes x-forwarded-for ?

Oui, Azure Front Door prend en charge les en-têtes X-Forwarded-For, X-Forwarded-Host et X-Forwarded-Proto. Pour X-Forwarded-For, si l’en-tête était déjà présent, Front Door y ajoute l’adresse IP de socket du client. Sinon, il ajoute l’en-tête avec l’adresse IP de socket du client comme valeur. Pour X-Forwarded-Host et X-Forwarded-Proto, la valeur est remplacée.  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Combien de temps faut-il pour déployer Azure Front Door ? Ma porte d’entrée fonctionne-t-elle toujours pendant sa mise à jour ?

La plupart des mises à jour de la configuration du moteur de règles prennent moins de 20 minutes. Vous pouvez vous attendre à ce que la règle prenne effet dès que la mise à jour est terminée. 

 > [!Note]  
  > Le déploiement global de la plupart des mises à jour de certificats TLS/SSL personnalisés prend généralement de quelques minutes à une heure.

Toutes les mises à jour des routes ou des pools de back-ends étant fluides, elles n’entraînent aucun temps d’arrêt (si la nouvelle configuration est correcte). Les mises à jour de certificat ne provoquent aucune interruption, sauf si vous passez d’« Azure Front Door Géré » à « Utiliser votre propre certificat » ou l’inverse.


## <a name="configuration"></a>Configuration

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Azure Front Door peut-il équilibrer la charge ou router le trafic au sein d’un réseau virtuel ?

Azure Front Door (Standard) nécessite une adresse IP publique ou un nom DNS pouvant être résolu publiquement pour router le trafic. Azure Front Door ne peut pas router directement vers les ressources d’un réseau virtuel. Vous pouvez utiliser Application Gateway ou Azure Load Balancer avec une adresse IP publique pour résoudre ce problème.

Azure Front Door (Premium) prend en charge le routage du trafic vers les [origines Private Link](concept-private-link.md).

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Quels sont les différents délais d’attente et limites applicables à Azure Front Door ?

Pour en savoir plus sur tous les délais d’attente et limites documentés pour Azure Front Door, consultez [cet article](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits).

### <a name="how-long-does-it-take-for-a-rule-to-take-effect-after-being-added-to-the-front-door-rules-engine"></a>Combien de temps faut-il pour qu’une règle prenne effet après avoir été ajoutée au moteur de règles Front Door ?

La plupart des mises à jour de la configuration du moteur de règles prennent moins de 20 minutes. Vous pouvez vous attendre à ce que la règle prenne effet dès que la mise à jour est terminée. 

### <a name="can-i-configure-azure-cdn-behind-my-front-door-profile-or-front-door-behind-my-azure-cdn"></a>Puis-je configurer Azure CDN derrière mon profil Front Door ou Front Door derrière Azure CDN ?

Impossible de configurer ensemble Azure Front Door et Azure CDN, car ces deux services utilisent les mêmes sites de périphérie Azure pour répondre aux requêtes. 

## <a name="performance"></a>Performances

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Comment Azure Front Door prend-il en charge la haute disponibilité et la scalabilité ?

Azure Front Door est une plateforme multilocataire mondialement distribuée avec des volumes de capacité élevés afin de répondre aux besoins de scalabilité de votre application. Délivré à partir de la périphérie du réseau mondial Microsoft, le service Front Door offre des fonctionnalités d’équilibrage de charge mondial qui vous permettent de faire basculer toute votre application ou certains microservices spécifiques entre différentes régions ou différents clouds.

## <a name="tls-configuration"></a>Configuration TLS

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Quelles sont les versions de TLS prises en charge par Azure Front Door ?

Tous les profils Front Door créés après septembre 2019 utilisent TLS 1.2 comme configuration minimale par défaut.

Front Door prend en charge les versions TLS 1.0, 1.1 et 1.2. TLS 1.3 n’est pas encore pris en charge. Pour plus d’informations, reportez-vous à [TLS de bout en bout avec Azure Front Door](../concept-end-to-end-tls.md).

### <a name="why-is-https-traffic-to-my-backend-failing"></a>Pourquoi le trafic HTTPS vers mon back-end échoue ?

Concernant les connexions HTTPS vers votre back-end, qu’il s’agisse de sondes d’intégrité ou de transferts de requêtes, le trafic HTTPS peut échouer pour deux raisons :

* **Incompatibilité du nom d’objet du certificat** : Pour les connexions HTTPS, Front Door s’attend à ce que votre back-end présente le certificat d’une autorité de certification valide avec un ou plusieurs noms d’objet correspondant au nom d’hôte du back-end. Par exemple, si le nom d'hôte de votre back-end est défini sur `myapp-centralus.contosonews.net` et que le certificat que votre back-end présente pendant l’établissement d’une liaison TLS ne comporte ni `myapp-centralus.contosonews.net` ni `*myapp-centralus*.contosonews.net` dans le nom de l'objet. Front Door refuse alors la connexion et provoque une erreur. 
    * **Solution**: bien que ce ne soit pas recommandé du point de vue de la conformité, vous pouvez contourner cette erreur en désactivant la vérification du nom d’objet du certificat pour votre instance Front Door. Cette option est présente sous Paramètres dans le Portail Azure et sous BackendPoolsSettings dans l’API.
* **Certificat d’hébergement back-end issu d’une autorité de certification non valide** : seuls les certificats provenant d’[autorités de certification valides](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT) peuvent être utilisés sur le back-end avec Front Door. Les certificats provenant d’autorités de certification internes ou les certificats auto-signés ne sont pas autorisés.

### <a name="can-i-use-clientmutual-authentication-with-azure-front-door"></a>Puis-je utiliser l’authentification client/mutuelle avec Azure Front Door ?

Non. Bien qu’Azure Front Door prenne en charge TLS 1.2, qui a introduit l’authentification client/mutuelle dans [RFC 5246](https://tools.ietf.org/html/rfc5246), actuellement, Azure Front Door Service ne prend pas en charge l’authentification client/mutuelle.

## <a name="diagnostics-and-logging"></a>Diagnostics et journalisation

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Quels sont les types de métriques et de journaux disponibles avec Azure Front Door ?

Pour plus d’informations sur les journaux et autres fonctionnalités de diagnostic, consultez Supervision des métriques et des journaux pour Front Door.

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Quelle est la stratégie de conservation sur les journaux de diagnostic ?

Les journaux de diagnostic circulent vers le compte de stockage des clients, et les clients peuvent définir la stratégie de rétention en fonction de leurs préférences. Les journaux de diagnostic peuvent également être envoyés à un hub d’événements ou à des journaux d’activité Azure Monitor. Pour plus d’informations, consultez [Journalisation d’Azure Front Door](how-to-logs.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Comment obtenir des journaux d’audit pour Azure Front Door ?

Des journaux d’audit sont disponibles pour Azure Front Door. Dans le portail, cliquez sur **Journal d’activité** dans le panneau du menu de votre instance Front Door pour accéder au journal d’audit. 

### <a name="can-i-set-alerts-with-azure-front-door"></a>Puis-je définir des alertes avec Azure Front Door ?

Oui, Azure Front Door prend en charge les alertes. Les alertes sont configurées d’après les métriques utilisées. 

## <a name="billing"></a>Facturation

### <a name="will-i-be-billed-for-the-azure-front-door-resources-that-are-disabled"></a>Suis-je facturé pour les ressources Azure Front Door qui sont désactivées ?

Des ressources Azure Front Door telles que des profils Front Door ne sont pas facturées si elles sont désactivées.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [créer une instance Front Door Standard/Premium](create-front-door-portal.md).
