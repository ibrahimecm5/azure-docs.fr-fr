---
title: TLS de bout en bout avec Azure Front Door
description: En savoir plus sur le chiffrement TLS de bout en bout lors de l’utilisation d’Azure Front Door.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 11/02/2021
ms.author: duau
ms.openlocfilehash: 5e0cac6f9ba6a245ec201666adb2c5cfeed79c38
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343094"
---
# <a name="end-to-end-tls-with-azure-front-door"></a>TLS de bout en bout avec Azure Front Door

Transport Layer Security (TLS), anciennement appelé Secure Sockets Layer (SSL), est la technologie de sécurité standard pour établir une liaison chiffrée entre un serveur web et un navigateur. Cette liaison garantit que toutes les données transmises entre le serveur web et le navigateur web restent privées et chiffrées.

Pour répondre à vos exigences en matière de sécurité ou de conformité, Azure Front Door (AFD) prend en charge le chiffrement TLS de bout en bout. Le déchargement TLS/SSL de Front Door met fin à la connexion TLS, déchiffre le trafic sur Azure Front Door, puis chiffre à nouveau le trafic avant de le transférer au serveur principal. Étant donné que les connexions au serveur principal se produisent sur l’adresse IP publique. Il est fortement recommandé de configurer HTTPS comme protocole de transfert sur Azure Front Door pour appliquer le chiffrement TLS de bout en bout entre le client et le serveur principal.

## <a name="end-to-end-tls-encryption"></a>Chiffrement TSL de bout en bout

Le protocole TLS de bout en bout vous permet de sécuriser les données sensibles en transit vers le back-end tout en bénéficiant des fonctionnalités Azure Front Door, telles que l’équilibrage de charge et la mise en cache globaux. Certaines des fonctionnalités incluent également le routage basé sur l’URL, le fractionnement TCP, la mise en cache sur l’emplacement de périphérie le plus proche des clients et la personnalisation des requêtes HTTP à la périphérie.

Azure Front Door décharge les sessions TLS à la périphérie et déchiffre les requêtes des clients. Il applique ensuite les règles d’acheminement configurées pour acheminer les requêtes vers le serveur principal approprié dans le pool principal. Azure Front Door démarre ensuite une nouvelle connexion TLS au serveur principal et rechiffre toutes les données à l’aide du certificat du serveur principal avant de transmettre la requête au serveur principal. Toute réponse du serveur principal passe par le même processus vers l’utilisateur final. Vous pouvez configurer Azure Front Door pour utiliser le protocole HTTPS comme protocole de transfert pour activer le protocole TLS de bout en bout.

## <a name="supported-tls-versions"></a>Versions TLS prises en charge

Azure Front Door prend en charge trois versions du protocole TLS : les versions TLS 1.0, 1.1 et 1.2. Tous les profils Azure Front Door créés après septembre 2019 utilisent TLS 1.2 comme version minimale par défaut, mais TLS 1.0 et TLS 1.1 sont toujours pris en charge pour la compatibilité descendante.

Bien qu’Azure Front Door prenne en charge TLS 1.2, qui a introduit l’authentification client/mutuelle dans RFC 5246, actuellement, Azure Front Door ne prend pas en charge l’authentification client/mutuelle.

Vous pouvez configurer une version TLS minimale dans Azure Front Door dans les paramètres HTTPS de domaine personnalisé par le biais du Portail Azure ou de l’ [API REST Azure](/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion). Actuellement, vous pouvez choisir entre 1.0 et 1.2. Par conséquent, si vous spécifiez TLS 1.2 comme version minimale, vous pouvez contrôler la version TLS minimale acceptable par Azure Front Door. Lorsqu’Azure Front Door initie le trafic TLS sur le serveur principal, il tente de négocier la meilleure version TLS que le serveur principal peut accepter de manière fiable et cohérente.

## <a name="supported-certificates"></a>Certificats pris en charge

Quand vous créez votre certificat TLS/SSL, vous devez créer une chaîne de certificats complète avec une autorité de certification (AC) autorisée figurant dans la  [liste des autorités de certification de confiance Microsoft](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT). Si vous utilisez une autorité de certification non autorisée, votre demande sera rejetée.

Les certificats provenant d’autorités de certification internes ou les certificats auto-signés ne sont pas autorisés.

## <a name="online-certificate-status-protocol-ocsp-stapling"></a>Agrafage du protocole OCSP (Online Certificate Status Protocol)

L’agrafage OCSP est pris en charge par défaut par Azure Front Door et aucune configuration n’est requise.

## <a name="backend-tls-connection-azure-front-door-to-backend"></a>Connexion TLS back-end (Azure Front Door vers le back-end)

Pour les connexions HTTPS, Azure Front Door s’attend à ce que votre back-end présente le certificat d’une autorité de certification valide avec un ou plusieurs noms d’objet correspondant au *nom d’hôte* du back-end. Par exemple, si le nom d’hôte de votre back-end est défini sur `myapp-centralus.contosonews.net` et que le certificat que votre back-end présente pendant la négociation TLS ne comporte ni `myapp-centralus.contosonews.net` ni `*.contosonews.net` dans le nom de l’objet, Azure Front Door refuse la connexion et génère une erreur.

> [!NOTE]
> Le certificat doit avoir une chaîne de certificats complète avec des certificats feuille et intermédiaires. L’autorité de certification racine doit faire partie de la [liste des autorités de certification de confiance Microsoft](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT). Lorsqu’un certificat sans chaîne complète est présenté, le bon fonctionnement des demandes l’impliquant n’est pas garanti.

Du point de vue de la sécurité, Microsoft ne recommande pas la vérification du nom d’objet du certificat. Dans certains cas d’usage, tels que pour les tests, par exemple, votre origine doit utiliser un certificat auto-signé. Pour résoudre les échecs de connexion HTTPS, vous pouvez désactiver la vérification du nom d’objet du certificat pour Azure Front Door. L’option de désactivation est présente sous les paramètres d’Azure Front Door dans le Portail Azure et dans BackendPoolsSettings dans l’API Azure Front Door. 

## <a name="frontend-tls-connection-client-to-front-door"></a>Connexion TLS du serveur frontal (client à Front Door)

Pour permettre au protocole HTTPS de distribuer le contenu de manière sécurisée sur un domaine personnalisé Azure Front Door, vous pouvez choisir d’utiliser un certificat managé par Azure Front Door ou votre propre certificat.  

* Le certificat managé par Azure Front Door fournit un certificat TLS/SSL standard via DigiCert et est stocké dans le Key Vault d’Azure Front Door.   

* Si vous choisissez d’utiliser votre propre certificat, vous pouvez intégrer un certificat délivré par une autorité de certification prise en charge ; il peut s’agir d’un certificat TLS standard, d’un certificat de validation étendue ou même d’un certificat générique.  

* Les certificats auto-signés ne sont pas pris en charge. Découvrez  [comment activer le protocole HTTPS pour un domaine personnalisé](front-door-custom-domain-https.md).

### <a name="certificate-autorotation"></a>Rotation automatique des certificats

En ce qui concerne l’option des certificats managés d’Azure Front Door, les certificats sont gérés par Azure Front Door et font l’objet d’une rotation automatique dans les 90 jours précédant leur date d’expiration. En ce qui concerne l’option des certificats managés d’Azure Front Door Standard/Premium, les certificats sont gérés par Azure Front Door et font l’objet d’une rotation automatique dans les 45 jours précédant leur date d’expiration. Si vous utilisez un certificat managé Azure Front Door et que vous voyez que la date d’expiration du certificat est inférieure à 60 jours (30 jours pour le SKU Standard/Premium), créez un ticket de support. 

Pour votre propre certificat TLS/SSL personnalisé :

1. Choisissez « La plus récente » comme version du secret.pour que le certificat soit permuté automatiquement quand une version plus récente est disponible dans votre coffre de clés. Pour les certificats personnalisés, le certificat est renouvelé automatiquement dans un délai de 1 à 2 jours avec une version plus récente du certificat, quel que soit la date d’expiration du certificat.

1. Si une version spécifique est sélectionnée, la rotation automatique n’est pas prise en charge. Vous devez resélectionner la nouvelle version manuellement pour faire pivoter le certificat. Le déploiement de la nouvelle version du certificat/secret peut prendre jusqu’à 24 heures.

    Assurez-vous que le principal de service pour Front Door a toujours accès au coffre de clés. Reportez-vous à comment accorder l’accès à votre coffre à clés. Cette opération de lancement de certificat mis à jour par Azure Front Door n’interrompt pas la production, à condition que le nom de l’objet ou le nom alternatif du sujet (SAN) pour le certificat ne change pas.

## <a name="supported-cipher-suites"></a>Suites de chiffrement prises en charge

Pour TLS 1.2, les suites de chiffrement suivantes sont prises en charge :

* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

> [!NOTE]
> Pour Windows 10 et versions ultérieures, nous vous recommandons d’activer une, ou les deux, suites de chiffrement ECDHE pour une meilleure sécurité. Windows 8.1, 8 et 7 ne sont pas compatibles avec ces suites de chiffrement ECDHE. Les suites de chiffrement DHE ont été fournies pour la compatibilité avec ces systèmes d’exploitation.

Quand vous utilisez des domaines personnalisés avec TLS 1.0/1.1 activé, les suites de chiffrement suivantes sont prises en charge :

* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
* TLS_RSA_WITH_AES_256_GCM_SHA384
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA
* TLS_RSA_WITH_AES_128_CBC_SHA
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

Azure Front Door ne prend pas en charge la configuration de suites de chiffrement spécifiques. Vous pouvez obtenir votre certificat SSL/TLS personnalisé auprès de votre autorité de certification (par exemple : Verisign, Entrust ou DigiCert). Ensuite, les suites de chiffrement spécifiques sont marquées sur le certificat lors de sa génération. 

## <a name="next-steps"></a>Étapes suivantes

* [Configurer un domaine personnalisé ](front-door-custom-domain.md)pour Azure Front Door.
* [Activer le protocole HTTPS pour un domaine personnalisé](front-door-custom-domain-https.md).
