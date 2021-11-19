---
title: Prise en charge des protocoles d’en-têtes HTTP dans Azure Front Door | Microsoft Docs
description: Cet article décrit les protocoles d’en-têtes HTTP pris en charge par Front Door.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/10/2021
ms.author: duau
ms.openlocfilehash: d102978b12c37033d2d52c7ee749c1e7f7878c7c
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132489054"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Prise en charge des protocoles d’en-têtes HTTP dans Azure Front Door
Cet article décrit le protocole pris en charge par Front Door avec certaines parties du chemin d’appel (voir l’image). Les sections suivantes donnent de plus amples informations sur les en-têtes HTTP pris en charge par Front Door.

:::image type="content" source="./media/front-door-http-headers-protocol/front-door-protocol-summary.png" alt-text="Protocole d’en-têtes HTTP Azure Front Door":::

>[!IMPORTANT]
>Front Door ne certifie pas les en-têtes HTTP qui ne sont pas documentés ici.

## <a name="client-to-front-door"></a>Du client à Front Door

Front Door accepte la plupart des en-têtes pour la requête entrante sans les modifier. Certains en-têtes réservés sont supprimés de la requête entrante en cas d’envoi, y compris les en-têtes avec un préfixe X-FD-*.

L’en-tête de demande de débogage, « X-Azure-DebugInfo », fournit des informations de débogage supplémentaires sur Front Door. Vous devez envoyer l’en-tête de demande « X-Azure-DebugInfo : 1 » du client à Front Door avant pour recevoir des [en-têtes de réponse facultatifs](#optional-debug-response-headers) de Front Door vers le client. 

## <a name="front-door-to-backend"></a>De la porte d’entrée au backend

Front Door inclut les en-têtes pour une requête entrante, sauf s’ils sont supprimés en raison de restrictions. Front Door ajoute également les en-têtes suivants :

| En-tête  | Exemple et description |
| ------------- | ------------- |
| Via |  *Via: 1.1 Azure* </br> Front Door ajoute la version HTTP du client suivie d’*Azure* comme valeur pour l’en-tête Via. Cet en-tête indique la version HTTP du client et signale que Front Door était un destinataire intermédiaire dans le cadre de la requête entre le client et le back-end.  |
| X-Azure-ClientIP | *X-Azure-ClientIP: 127.0.0.1* </br> Représente l’adresse IP du client associée à la requête traitée. Par exemple, une requête en provenance d’un proxy peut ajouter l’en-tête X-Forwarded-For pour indiquer l’adresse IP de l’appelant d’origine. |
| X-Azure-SocketIP |  *X-Azure-SocketIP: 127.0.0.1* </br> Représente l’adresse IP de socket associée à la connexion TCP d’où provient la requête actuelle. L’adresse IP de client d’une requête peut ne pas être identique à son adresse IP de socket, car l’adresse IP de client peut être arbitrairement remplacée par un utilisateur.|
| X-Azure-Ref | *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Chaîne de référence unique qui identifie une requête prise en charge par Front Door. Elle sert à effectuer des recherches dans les journaux d’accès et est essentielle pour la résolution des problèmes.|
| X-Azure-RequestChain | *X-Azure-RequestChain: hops=1* </br> En-tête utilisé par Front Door pour détecter les boucles de requête, et pour lequel les utilisateurs ne doivent pas établir de dépendance. |
| X-Azure-FDID | *X-Azure-FDID : 55ce4ed1-4b06-4bf1-b40e-4638452104da* <br/> Chaîne de référence qui identifie la requête provenant d’une ressource Front Door spécifique. La valeur peut être affichée dans le portail Azure ou récupérée à l’aide de l’API de gestion. Vous pouvez utiliser cet en-tête en association avec des listes de contrôle d’accès IP pour verrouiller votre point de terminaison en vue d’accepter uniquement les requêtes provenant d’une ressource Front Door particulière. Pour plus d’informations, consultez les [Questions fréquentes (FAQ)](front-door-faq.yml#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-). |
| X-Forwarded-For | *X-Forwarded-For: 127.0.0.1* </br> Le champ d’en-tête HTTP X-Forwarded-For (XFF) identifie souvent l’adresse IP d’origine d’un client qui se connecte à un serveur web par le biais d’un équilibreur de charge ou d’un proxy HTTP. S’il existe un en-tête XFF, Front Door y ajoute l’adresse IP de socket du client ou ajoute l’en-tête XFF avec l’adresse IP de socket du client. |
| X-Forwarded-Host | *X-Forwarded-Host: contoso.azurefd.net* </br> Le champ d’en-tête HTTP X-Forwarded-Host est une méthode couramment utilisée pour identifier l’hôte d’origine demandé par le client dans l’en-tête de requête HTTP Host. En effet, le nom d’hôte de Front Door peut être différent pour le serveur back-end gérant la requête. Toute valeur précédente est remplacée par Front Door. |
| X-Forwarded-Proto | *X-Forwarded-Proto: http* </br> Le champ d’en-tête HTTP X-Forwarded-Proto est souvent utilisé pour identifier le protocole d’origine d’une requête HTTP. Une instance Front Door basée sur la configuration peut communiquer avec le serveur principal à l’aide du protocole HTTPS. Cela est vrai même si la requête envoyé au proxy inverse est HTTP. Toute valeur précédente est remplacée par Front Door. |
| X-FD-HealthProbe | Le champ d’en-tête X-FD-HealthProbe est utilisé pour identifier la sonde d’intégrité de Front Door. Si cet en-tête a la valeur 1, la requête provient de la sonde d’intégrité. Il peut être utilisé pour limiter l’accès à partir de Front Door avec une valeur particulière pour le champ d’en-tête X-Forwarded-Host. |
| X-Azure-FDID | *En-tête X-Azure-FDID : 437c82cd-360a-4a54-94c3-5ff707647783* </br> Ce champ contient des ID Frontdoor qui peuvent être utilisés pour identifier la porte d’entrée de la requête entrante. Ce champ est rempli par le service Front Door. | 

## <a name="front-door-to-client"></a>De la porte d’entrée au client

Les en-têtes envoyés à Front Door à partir du back-end sont également transmis au client. Les en-têtes ci-dessous sont ceux envoyés de Front Door aux clients.

| En-tête  | Exemple et description |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Il s’agit d’une chaîne de référence unique qui identifie une requête traitée par Front Door, ce qui est capital pour la résolution des problèmes, car elle est utilisée dans la recherche des journaux d’accès.|
| Cache X | *X-Cache :* cet en-tête décrit l’état de mise en cache de la requête <br/> - *X-Cache : TCP_HIT* : le premier octet de la requête est une correspondance dans le cache à la périphérie Front Door. <br/> - *X-Cache : TCP_REMOTE_HIT* : le premier octet de la requête est une correspondance dans le cache dans le cache régional (couche de protection d’origine), mais un échec dans le cache de périphérie. <br/> - *X-Cache : TCP_MISS* : le premier octet de la requête est une absence dans le cache, et le contenu est traité à partir de l’origine. <br/> - *X-Cache : PRIVATE_NOSTORE* : la requête ne peut pas être mise en cache car l’en-tête de réponse Cache-Control est défini sur private (privé) ou sur no-store (sans stockage). <br/> - *X-Cache : CONFIG_NOCACHE* : la requête est configurée pour ne pas effectuer de mise en cache dans le profil Front Door. |

### <a name="optional-debug-response-headers"></a>En-têtes de réponse de débogage facultatifs

Vous devez envoyer un en-tête de demande « X-Azure-DebugInfo: 1 » pour activer les en-têtes de réponse facultatifs suivants.

| En-tête  | Exemple et description |
| ------------- | ------------- |
| X-Azure-OriginStatusCode |  *X-Azure-OriginStatusCode : 503* </br> Cet en-tête contient le code d’état HTTP retourné par le serveur principal. À l’aide de cet en-tête, vous pouvez identifier le code d’état HTTP renvoyé par l’application en cours d’exécution sur votre serveur principal sans passer par les journaux du serveur principal. Ce code d’état peut être différent du code d’état HTTP dans la réponse envoyée au client par Front Door. Cet en-tête vous permet de déterminer si le serveur principal est incorrect ou si le problème provient du service Front Door. |
| X-Azure-InternalError | Cet en-tête contient le code d’erreur que Front Door rencontre lors du traitement de la demande. Cette erreur indique que le problème est interne à l’infrastructure ou au service Front Door. Signalez le problème.  |
| X-Azure-ExternalError | *X-Azure-ExternalError : 0x830c1011, L’autorité de certification n’est pas familière.* </br> Cet en-tête indique le code d’erreur que les serveurs Front Door sont répartis lors de l’établissement d’une connexion au serveur principal pour traiter une requête. Cet en-tête permet d’identifier les problèmes de connexion entre Front Door et l’application principale. Cet en-tête inclura un message d’erreur détaillé pour vous aider à identifier les problèmes de connectivité à votre serveur principal (par exemple, résolution DNS, certificat non valide, etc.). |

## <a name="next-steps"></a>Étapes suivantes

- [Créer une Front Door](quickstart-create-front-door.md)
- [Fonctionnement de Front Door](front-door-routing-architecture.md)
