---
title: Recommandations de sécurité
description: Mettez en œuvre les recommandations de sécurité pour répondre à vos obligations de sécurité comme indiqué dans notre modèle de responsabilité partagée. Améliorez la sécurité de votre application.
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/02/2021
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 42eaec619097d673c77b6b233a2f2316605971b6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132348423"
---
# <a name="security-recommendations-for-app-service"></a>Suggestions en matière de sécurité pour App Service

Cet article contient des recommandations de sécurité pour Azure App Service. Mettez en œuvre ces recommandations pour répondre à vos obligations de sécurité comme décrit dans notre modèle de responsabilité partagée et améliorer la sécurité globale de vos solutions d’application web. Pour plus d’informations sur les mesures prises par Microsoft pour répondre à ses responsabilités de fournisseur de services, lisez [Sécurité de l’infrastructure Azure](../security/fundamentals/infrastructure.md).

## <a name="general"></a>Général

| Recommandation | Commentaires |
|-|-|----|
| Rester à jour | Utilisez les dernières versions des plateformes, langages de programmation, protocoles et infrastructures pris en charge. |

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Recommandation | Commentaires |
|-|----|
| Désactiver l’accès anonyme | À moins que vous ayez besoin de prendre en charge les requêtes anonymes, désactivez l’accès anonyme. Pour plus d’informations sur les options d’authentification Azure App Service, consultez [Authentification et autorisation dans Azure App Service](overview-authentication-authorization.md).|
| Exiger l’authentification | Si possible, utilisez le module d’authentification d’App Service au lieu d’écrire du code pour gérer l’authentification et l’autorisation. Consultez [Authentification et autorisation dans Azure App Service](overview-authentication-authorization.md). |
| Protéger les ressources back-end avec un accès authentifié | Vous pouvez utiliser l’identité de l’utilisateur ou une identité d’application pour vous authentifier auprès d’une ressource principale. Lorsque vous choisissez d’utiliser une identité d’application, utilisez une [identité managée](overview-managed-identity.md).
| Nécessiter l’authentification par certificat client | L’authentification par certificat client améliore la sécurité en autorisant uniquement les connexions à partir de clients qui peuvent s’authentifier à l’aide de certificats que vous fournissez. |

## <a name="data-protection"></a>Protection de données

| Recommandation | Commentaires |
|-|-|
| Rediriger le trafic HTTP vers HTTPS | Par défaut, les clients peuvent se connecter à des applications web à l’aide de HTTP ou HTTPS. Nous vous recommandons de rediriger HTTP vers HTTPS, car HTTPS utilise le protocole SSL/TLS pour fournir une connexion sécurisée, à la fois chiffrée et authentifiée. |
| Chiffrer la communication avec les ressources Azure | Quand votre application se connecte à des ressources Azure, telles que [SQL Database](https://azure.microsoft.com/services/sql-database/) ou [Stockage Azure](../storage/index.yml), la connexion reste dans Azure. Étant donné que la connexion passe par la mise en réseau partagée dans Azure, vous devez toujours chiffrer toutes les communications. |
| Exiger la dernière version de TLS possible | Depuis 2018, les applications Azure App Service utilisent TLS 1.2. Les versions plus récentes de TLS comprennent des améliorations de sécurité par rapport aux anciennes versions du protocole. |
| Utiliser FTPS | App Service prend en charge FTP et FTPS pour le déploiement de vos fichiers. Utilisez FTPS au lieu de FTP lorsque cela est possible. Quand un de ces protocoles, ou les deux, ne sont pas utilisés, vous devez [les désactiver](deploy-ftp.md#enforce-ftps). |
| Sécuriser les données d’application | Ne stockez pas les secrets de l’application, tels que les informations d’identification de la base de données, les jetons d’API ou les clés privées, dans vos fichiers de code ou de configuration. L’approche couramment acceptée consiste à y accéder sous forme de [variables d’environnement](https://wikipedia.org/wiki/Environment_variable) à l’aide du modèle standard dans la langue de votre choix. Dans Azure App Service, vous pouvez définir des variables d’environnement via les [paramètres de l’application](./configure-common.md) et les [chaînes de connexion](./configure-common.md). Les paramètres de l’application et les chaînes de connexion sont stockés dans Azure. Les paramètres d’application sont déchiffrés uniquement avant d’être injectés dans la mémoire de processus de votre application au démarrage de celle-ci. Les clés de chiffrement sont régulièrement permutées. Une autre approche consiste à intégrer votre application Azure App Service à [Azure Key Vault](../key-vault/index.yml) pour bénéficier d’une gestion avancée des secrets. En [accédant à Key Vault avec une identité managée](../key-vault/general/tutorial-net-create-vault-azure-web-app.md), votre application App Service peut accéder de manière sécurisée aux secrets dont vous avez besoin. |

## <a name="networking"></a>Mise en réseau

| Recommandation | Commentaires |
|-|-|
| Utiliser les restrictions d’adresses IP statiques | Azure App Service sur Windows permet de définir une liste d’adresses IP pouvant accéder à votre application. La liste autorisée peut inclure des adresses IP individuelles ou une plage d’adresses IP définie par un masque de sous-réseau. Pour plus d’informations, consultez [Restrictions d’adresse IP statique avec Azure App Service](app-service-ip-restrictions.md).  |
| Utiliser le niveau tarifaire isolé | À la différence du niveau tarifaire Isolé, tous les niveaux exécutent vos applications sur l’infrastructure réseau partagée dans Azure App Service. Le niveau Isolé vous procure un isolement réseau complet en exécutant vos applications à l’intérieur d’un [environnement App Service dédié](environment/intro.md). Un environnement App Service s’exécute dans votre propre instance de [Réseau virtuel Azure](../virtual-network/index.yml).|
| Utiliser des connexions sécurisées lors de l’accès aux ressources locales | Vous pouvez utiliser des [connexions hybrides](app-service-hybrid-connections.md), [l’intégration au réseau virtuel](./overview-vnet-integration.md) ou [l’environnement App Service](environment/intro.md) pour vous connecter à des ressources locales. |
| Limiter l’exposition au trafic réseau entrant | Les groupes de sécurité réseau vous permettent de restreindre l’accès réseau et de contrôler le nombre de points de terminaison exposés. Pour plus d’informations, consultez [Contrôle du trafic entrant vers un environnement App Service](environment/app-service-app-service-environment-control-inbound-traffic.md). |

## <a name="monitoring"></a>Surveillance

| Recommandation | Commentaires |
|-|-|
|Utilisez Microsoft Defender pour Microsoft Defender pour App Service dans le Cloud | [Microsoft Defender pour App Service](../security-center/defender-for-app-service-introduction.md) est intégré en mode natif Azure App Service. Defender pour le Cloud évalue les ressources couvertes par votre App Service et génère des suggestions de sécurité en fonction de ses résultats. Utilisez les instructions détaillées dans [ces recommandations](). ../security-center/recommendations-reference.md#appservices-recommendations) pour renforcer les ressources de votre Service App. Microsoft Defender pour le Cloud fournit également une protection contre les menaces et peut détecter une multitude de menaces couvrant presque la liste complète des tactiques MITRE ATT&CK, de la pré-attaque à la commande en passant par le contrôle. Pour obtenir la liste complète des alertes d’Azure App Service, consultez [Azure Defender pour les alertes App Service](../security-center/alerts-reference.md#alerts-azureappserv).|

## <a name="next-steps"></a>Étapes suivantes

Vérifiez auprès de votre fournisseur d’application pour voir s’il existe des exigences de sécurité supplémentaires. Pour plus d’informations sur le développement d’applications sécurisées, consultez [Documentation sur le développement sécurisé](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/).
