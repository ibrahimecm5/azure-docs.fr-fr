---
title: Fonctionnalités du Pare-feu Azure Premium
description: Le Pare-feu Azure Premium est un service de sécurité réseau informatique managé qui protège vos ressources de réseau virtuel Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 11/04/2021
ms.author: victorh
ms.custom: references_regions
ms.openlocfilehash: addbd908670741cd0ba8662bf73a1a6788487498
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851455"
---
# <a name="azure-firewall-premium-features"></a>Fonctionnalités du Pare-feu Azure Premium

:::image type="content" source="media/premium-features/icsa-cert-firewall-small.png" alt-text="Logo de certification ICSA" border="false"::::::image type="content" source="media/premium-features/pci-logo.png" alt-text="Logo de certification PCI" border="false":::


 Pare-feu Azure Premium est un pare-feu de nouvelle génération dont les capacités sont requises pour les environnements hautement sensibles et réglementés.

:::image type="content" source="media/premium-features/premium-overview.png" alt-text="Diagramme de vue d’ensemble du Pare-feu Azure Premium":::

Le Pare-feu Azure Premium utilise une stratégie de pare-feu, une ressource globale qui peut être utilisée pour gérer de manière centralisée vos pare-feu à l’aide d’Azure Firewall Manager. À partir de cette version, toutes les nouvelles fonctionnalités sont configurables via la stratégie de pare-feu uniquement. Les règles de pare-feu (classiques) continuent à être prises en charge et peuvent être utilisées pour configurer les fonctionnalités existantes du pare-feu standard.  La stratégie de pare-feu peut être gérée indépendamment ou avec Azure Firewall Manager. Une stratégie de pare-feu associée à un seul pare-feu n’est pas facturée.

Le Pare-feu Azure Premium inclut les fonctionnalités suivantes :

- **Inspection TLS** : déchiffre le trafic sortant, traite les données, puis les chiffre et les transmet à la destination.
- **IDPS** : un système IDPS (Intrusion Detection and Prevention System) vous permet de surveiller les activités malveillantes, de consigner des informations sur ces activités, de les signaler, voire de les bloquer.
- **Filtrage d’URL** : étend la fonctionnalité de filtrage de nom de domaine complet du Pare-feu Azure pour prendre en compte une URL entière. Par exemple, `www.contoso.com/a/c` plutôt que `www.contoso.com`.
- **Catégories web** : permettent aux administrateurs d’autoriser ou de refuser aux utilisateurs l’accès aux catégories de sites web telles que les sites web de jeux d’argent, les sites web de réseaux sociaux, etc.


## <a name="tls-inspection"></a>Inspection TLS

Le Pare-feu Azure Premium met fin aux connexions TLS sortantes et Est-Ouest. L’inspection TLS entrante est prise en charge avec [Azure Application Gateway](../web-application-firewall/ag/ag-overview.md), ce qui permet un chiffrement de bout en bout. Le Pare-feu Azure effectue les fonctions de sécurité à valeur ajoutée requises et chiffre à nouveau le trafic envoyé à la destination d’origine.

> [!TIP]
> Les versions 1.0 et 1.1 de TSL sont déconseillées et non prises en charge. Les versions 1.0 et 1.1 de TLS/SSL (Secure Sockets Layer) se sont avérées vulnérables et bien qu’elles fonctionnent encore pour assurer la compatibilité descendante, elles sont déconseillées. Migrez vers TLS 1.2 dès que possible.

Pour en savoir plus sur les conditions requises pour les certificats d’autorité de certification intermédiaires du Pare-feu Azure Premium, consultez [Certificats du Pare-feu Azure Premium](premium-certificates.md).

## <a name="idps"></a>IDPS

Un système IDPS (Intrusion Detection and Prevention System) vous permet de surveiller les activités malveillantes, de consigner des informations sur ces activités, de les signaler, voire de les bloquer. 

Le Pare-feu Azure Premium propose un système IDPS basé sur les signatures pour permettre une détection rapide des attaques en recherchant des modèles spécifiques, tels que des séquences d’octets dans le trafic réseau ou des séquences d’instructions malveillantes connues utilisées par un programme malveillant. Les signatures IDP sont applicables au trafic au niveau de l’application et du réseau (couches 4-7) et sont entièrement gérées et mises à jour en permanence. Les IDP peuvent être appliqués au trafic entrant, au trafic spoke-to-spoke (est-ouest) et au trafic sortant.

Les signatures/ensembles de règles du Pare-feu Azure incluent les éléments suivants :
- Accent mis sur la prise d’empreinte numérique des logiciels malveillants, des centres de commande et de contrôle, des kits de code malveillant exploitant une faille de sécurité et des diverses activités malveillantes manquées par les méthodes de protection traditionnelles
- Plus de 58 000 règles dans plus de 50 catégories.
    - Exemples de catégories : commande et contrôle des logiciels malveillants, hameçonnage, chevaux de Troie, botnets, événements d’information, code malveillant exploitant une faille de sécurité, vulnérabilités, protocoles réseau SCADA, activité des kits de code malveillant, etc.
- Entre 20 et 40 nouvelles règles publiées quotidiennement
- Faible classification de faux positifs grâce à un bac à sable (sandbox) de pointe et à une boucle de rétroaction du réseau de capteurs globaux.

Le système IDPS vous permet de détecter les attaques dans tous les ports et protocoles pour le trafic non chiffré. Cela étant, lorsque le trafic HTTPS doit être inspecté, le Pare-feu Azure peut utiliser sa fonction d’inspection TLS pour déchiffrer le trafic et mieux détecter les activités malveillantes.  

La liste de contournement IDPS vous permet de ne pas filtrer le trafic vers les adresses IP, les plages et les sous-réseaux spécifiés dans cette liste.

Vous pouvez également utiliser des règles de signature lorsque le mode IDPS est défini sur **Alerte**, mais que vous souhaitez bloquer une ou plusieurs signatures spécifiques, avec le trafic associé. Dans ce cas, vous pouvez ajouter de nouvelles règles de signature en définissant le mode d’inspection TLS sur **refuser**.


## <a name="url-filtering"></a>Un filtrage des URL

Le filtrage d’URL étend la fonctionnalité de filtrage de nom de domaine complet du Pare-feu Azure pour prendre en compte une URL entière. Par exemple, `www.contoso.com/a/c` plutôt que `www.contoso.com`.  

Le filtrage d’URL peut être appliqué au trafic HTTP et HTTPS. Lorsque le trafic HTTPS est inspecté, le Pare-feu Azure Premium peut utiliser sa fonctionnalité d’inspection TLS pour déchiffrer le trafic et extraire l’URL cible afin de vérifier si l’accès est autorisé. L’inspection TLS nécessite un consentement au niveau de la règle d’application. Après activation, vous pouvez utiliser des URL pour le filtrage avec HTTPS. 

## <a name="web-categories"></a>Catégories web

Les catégories web permettent aux administrateurs d’autoriser ou de refuser aux utilisateurs l’accès aux catégories de sites web telles que les sites web de jeux d’argent, les sites web de réseaux sociaux, etc. Les catégories web sont également incluses dans le Pare-feu Azure Standard, mais elles sont plus précises dans le Pare-feu Azure Premium. Contrairement à la fonctionnalité de catégories web de la référence SKU Standard qui correspond à la catégorie basée sur un nom de domaine complet, la référence SKU Premium correspond à la catégorie en fonction de l’URL complète pour le trafic HTTP et HTTPS. 

Par exemple, si le Pare-feu Azure intercepte une demande HTTPS pour `www.google.com/news`, la catégorisation suivante est attendue : 

- Pare-feu Standard : seule la partie du nom de domaine complet étant examinée, `www.google.com` est classé en tant que *Moteur de recherche*. 

- Pare-feu Premium : l’URL complète étant examinée, `www.google.com/news` est classée en tant qu’*Actualités*.

Les catégories sont organisées en fonction de leur gravité sous **Responsabilité**, **Bande passante élevée**, **Utilisation métier**, **Perte de productivité**, **Navigation générale** et **Sans catégorie**. Pour obtenir une description détaillée des catégories Web, consultez [Catégories web Azure Firewall](web-categories.md).

### <a name="web-category-logging"></a>Journalisation des catégories web
Vous pouvez afficher le trafic ayant été filtré par le champ **Catégories web** dans les journaux d’application. Le champ **Catégories web** s’affiche uniquement s’il a été explicitement configuré dans vos règles d’application de stratégie de pare-feu. Par exemple, si vous n’avez pas de règle qui refuse explicitement les *moteurs de recherche* et qu’un utilisateur demande à accéder à www.bing.com, seul un message de refus par défaut s’affiche au lieu d’un message de catégorie web. Cela est dû au fait que la catégorie web n’a pas été configurée de manière explicite.

### <a name="category-exceptions"></a>Exceptions de catégorie

Vous pouvez créer des exceptions à vos règles de catégorie web. Créez une collection de règles d’autorisation ou de refus distinct avec une priorité plus élevée au sein du groupe de collections de règles. Par exemple, vous pouvez configurer une collection de règles qui autorise `www.linkedin.com` avec la priorité 100, avec une collection de règles qui refuse **Réseaux sociaux** avec la priorité 200. Cette opération crée l’exception pour la catégorie web **Réseaux sociaux** prédéfinie.

### <a name="web-category-search"></a>Recherche de catégorie web

Vous pouvez identifier la catégorie d’un FQDN ou d’une URL donnés à l’aide de la fonctionnalité de **contrôle de catégorie web**. Pour ce faire, sélectionnez l’onglet **Catégories web** sous **Paramètres de stratégie de pare-feu**. Cela s’avère particulièrement utile lors de la définition de vos règles d’application pour le trafic de destination.

:::image type="content" source="media/premium-features/firewall-category-search.png" alt-text="Boîte de dialogue de recherche de catégorie de pare-feu":::

### <a name="category-change"></a>Changement de catégorie

Sous l’onglet **Catégories web** des **Paramètres de stratégie de pare-feu**, vous pouvez demander un changement de catégorisation si vous : 

- pensez qu’un nom de domaine complet (FQDN) ou une URL doit être dans une catégorie différente 

   or 

- avez une catégorie suggérée pour un FQDN ou une URL sans catégorie 

 Après avoir envoyé un rapport de changement de catégorie, vous recevez un jeton dans les notifications, indiquant que nous avons reçu la demande pour traitement. Vous pouvez vérifier si la demande est en cours, refusée ou approuvée en entrant le jeton dans la barre de recherche.  Pour ce faire, veillez à enregistrer votre ID de jeton.

:::image type="content" source="media/premium-features/firewall-category-change.png" alt-text="Boîte de dialogue rapport de catégorie de pare-feu":::

 ## <a name="supported-regions"></a>Régions prises en charge

Le Pare-feu Azure Premium est pris en charge dans les régions suivantes :

- Australie Centre (public/Australie)
- Australie Centre 2 (public/Australie)
- Australie Est (public/Australie)
- Australie Sud-Est (public/Australie)
- Brésil Sud (public/Brésil)
- Brésil Sud-Est (public/Brésil)
- Canada Centre (public/Canada)
- Canada Est (public/Canada)
- Inde Centre (public/Inde)
- USA Centre (public/États-Unis)
- USA Centre EUAP (public/Canaries (États-Unis))
- Chine Nord 2 (Mooncake / Chine)
- Chine Est 2 (Mooncake / Chine)
- Asie Est (public/Asie-Pacifique)
- USA Est (public/États-Unis)
- USA Est 2 (public/États-Unis)
- France Centre (public/France)
- France Sud (public/France)
- Allemagne Centre-Ouest (public/Allemagne)
- Japon Est (public/Japon)
- Japon Ouest (public/Japon)
- Corée Centre (public/Corée)
- Corée Sud (public/Corée)
- USA Centre Nord (public/États-Unis)
- Europe Nord (public/Europe)
- Norvège Est (public/Norvège)
- Afrique du Sud Nord (public/Afrique du Sud)
- USA Centre Sud (public/États-Unis)
- Inde Sud (public/Inde)
- Asie Sud-Est (public/Asie-Pacifique)
- Suisse Nord (public/Suisse)
- Émirats arabes unis Centre (public/Émirats arabes unis)
- Émirats arabes unis Nord (public/Émirats Arabes Unis)
- Royaume-Uni Sud (public/Royaume-Uni)
- Royaume-Uni Ouest (public/Royaume-Uni)
- Gouvernement américain - Arizona (Fairfax / Gouvernement des États-Unis)
- Gouvernement des États-Unis - Texas (Fairfax / Gouvernement des États-Unis)
- Gouvernement des États-Unis - Virginie (Fairfax / Gouvernement des États-Unis)
- USA Centre-Ouest (public/États-Unis)
- Europe Ouest (public/Europe)
- Inde Ouest (public/Inde)
- USA Ouest (public/États-Unis)
- USA Ouest 2 (public/États-Unis)
- USA Ouest 3 (public/États-Unis)


## <a name="known-issues"></a>Problèmes connus

Les problèmes connus du Pare-feu Azure Premium sont les suivants :


|Problème  |Description  |Limitation des risques  |
|---------|---------|---------|
|Prise en charge ESNI pour la résolution de nom de domaine complet dans HTTPS|Le chiffrement SNI n’est pas pris en charge dans l’établissement d'une liaison HTTPS.|Aujourd’hui, seul Firefox prend en charge ESNI via une configuration personnalisée. La solution de contournement recommandée consiste à désactiver cette fonctionnalité.|
|Certificats clients (TLS)|Les certificats clients sont utilisés pour créer une approbation d’identité mutuelle entre le client et le serveur. Les certificats clients sont utilisés lors d’une négociation TLS. Le Pare-feu Azure renégocie une connexion avec le serveur et n’a pas accès à la clé privée des certificats clients.|Aucun|
|QUIC/HTTP3|QUIC est la nouvelle version majeure de HTTP. Il s’agit d’un protocole basé sur UDP sur 80 (PLAN) et 443 (SSL). L’inspection FQDN/URL/TLS n’est pas prise en charge.|Configurez le passage UDP 80/443 en tant que règles de réseau.|
Certificats signés par le client non approuvés|Les certificats signés par le client ne sont pas approuvés par le pare-feu lorsqu’ils proviennent d’un serveur web intranet.|Un correctif est en cours d’étude.
|Adresse IP source erronée dans les alertes avec système IDPS pour HTTP (sans inspection TLS).|Lorsque le trafic HTTP en texte brut est utilisé, que le système IDPS émet une nouvelle alerte et que la destination est une adresse IP publique, l’adresse IP source affichée est incorrecte (l’adresse IP interne est affichée à la place de l’adresse IP d’origine).|Un correctif est en cours d’étude.|
|Propagation du certificat|Après l’application d’un certificat d’autorité de certification sur le pare-feu, la prise en compte du certificat peut prendre de 5 à 10 minutes.|Un correctif est en cours d’étude.|
|Prise en charge du protocole TLS 1.3|Le protocole TLS 1.3 est partiellement pris en charge. Le tunnel TLS entre le client et le pare-feu est basé sur le protocole TLS 1.2, et celui entre le pare-feu et le serveur web externe est basé sur le protocole TLS 1.3.|Des mises à jour sont à l’étude.|
|Point de terminaison privé Key Vault|Key Vault prend en charge l’accès au point de terminaison privé pour limiter son exposition réseau. Les services Azure approuvés peuvent contourner cette limitation si une exception est configurée comme décrit dans la [documentation de Key Vault](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services). Le Pare-feu Azure n’est pas répertorié actuellement comme un service approuvé et ne peut pas accéder au Key Vault.|Un correctif est en cours d’étude.|

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur les certificats du Pare-feu Azure Premium](premium-certificates.md)
- [Déployer et configurer le Pare-feu Azure Premium](premium-deploy.md)
- [Migrer vers le Pare-feu Azure Premium](premium-migrate.md)
