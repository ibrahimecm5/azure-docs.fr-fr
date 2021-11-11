---
title: Résoudre les problèmes de configuration d’Azure Front Door
description: Dans ce tutoriel, vous allez apprendre à résoudre vous-même certains des problèmes courants que vous pouvez rencontrer concernant votre instance Azure Front Door.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2021
ms.author: duau
ms.openlocfilehash: 3bae55a4a5b2a2b6ec5ae8ce7c63fb52b96fbd9f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131464183"
---
# <a name="troubleshooting-common-routing-problems"></a>Résolution des problèmes de routage courants

Cet article explique comment résoudre les problèmes de routage courants que vous pouvez rencontrer concernant votre configuration Azure Front Door.

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>Réponse 503 d’Azure Front Door après quelques secondes

### <a name="symptom"></a>Symptôme

* Les requêtes régulières envoyées à votre back-end sans passer par Azure Front Door s’effectuent correctement. Le transit via Azure Front Door génère des réponses d’erreur 503.
* L’échec avec Azure Front Door survient généralement après environ 30 secondes.
* Erreurs 503 intermittentes avec le journal `ErrorInfo: OriginInvalidResponse`.

### <a name="cause"></a>Cause

La cause de ce problème peut être liée à l’une des trois possibilités suivantes :
 
* Votre back-end prend plus de temps que le délai d’expiration configuré (la valeur par défaut est de 30 secondes) pour recevoir la requête de l’instance Azure Front Door.
* Le temps nécessaire pour envoyer une réponse à la requête provenant d’Azure Front Door prend plus de temps que la valeur du délai d’attente.
* Le client a envoyé une demande de plage d’octets avec `Accept-Encoding header` (compression activée).

### <a name="troubleshooting-steps"></a>Étapes de dépannage

* Envoyez la requête directement à votre back-end (sans passer par Azure Front Door). Découvrez combien de temps il faut généralement à votre back-end pour répondre.
* Envoyez la requête via Azure Front Door et vérifiez si vous obtenez des réponses 503. Si ce n’est pas le cas, il est possible qu’il ne s’agisse pas d’un problème de délai d’expiration. Contactez le support technique.
* Si les requêtes qui passent par Azure Front Door génèrent un code de réponse d’erreur 503, configurez le paramètre **Délai d’attente d’envoi/de réception (en secondes)** pour Azure Front Door. Vous pouvez étendre le délai d’expiration par défaut jusqu’à 4 minutes (240 secondes). Le paramètre peut être configuré en accédant au *concepteur Front Door* et en sélectionnant **Paramètres**.

    :::image type="content" source=".\media\troubleshoot-route-issues\send-receive-timeout.png" alt-text="Capture d’écran du champ délai d’expiration d’envoi/réception dans le concepteur Front Door.":::

* Si le délai d’expiration ne résout pas le problème, utilisez un outil, tel que Fiddler ou l’outil de développement de votre navigateur, pour vérifier si le client envoie des demandes de plages d’octets avec des en-têtes Accept-Encoding, ce qui conduit à l’origine qui répond avec des longueurs de contenu différentes. Si c’est le cas, vous pouvez soit désactiver la compression sur l’origine/Azure Front Door, soit créer une règle de jeu de règles pour supprimer `accept-encoding` de la requête pour les demandes de plages d’octets.

    :::image type="content" source=".\media\troubleshoot-route-issues\remove-encoding-rule.png" alt-text="Capture d’écran de la règle d’acceptation d’encodage dans le moteur de règles.":::

## <a name="https-traffic-to-backend-fails"></a>Échec du trafic HTTPS vers le serveur principal

### <a name="symptom"></a>Symptôme

Échec du trafic HTTPS vers le serveur principal.

### <a name="cause"></a>Cause

* Le certificat avec le ou les noms d’objet ne correspond pas au nom d’hôte du serveur principal pendant l’établissement d'une liaison TLS.
* Le certificat d’hébergement du serveur principal ne provient pas d’une autorité de certification valide.

### <a name="troubleshooting-steps"></a>Étapes de dépannage

* Bien que ce ne soit pas recommandé du point de vue de la conformité, vous pouvez contourner cette erreur en désactivant la vérification du nom d’objet du certificat pour votre porte d’entrée. Cette option est présente sous Paramètres dans le portail Azure et sous BackendPoolsSettings dans l’API.
* Seuls des certificats provenant d’[autorités de certification valides](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT) peuvent être utilisés sur le serveur principal avec Front Door. Les certificats provenant d’autorités de certification internes ou les certificats auto-signés ne sont pas autorisés. Le certificat doit avoir une chaîne de certificats complète avec des certificats feuille et intermédiaires, et l’autorité de certification racine doit faire partie de la [liste des autorités de certification de confiance Microsoft](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT).

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>Les requêtes envoyées au domaine personnalisé retournent un code d’état 400

### <a name="symptom"></a>Symptôme

* Vous avez créé une instance Azure Front Door, mais une requête auprès du domaine ou de l’hôte front-end retourne un code d’état HTTP 400.
* Vous avez créé un mappage DNS pour un domaine personnalisé vers l’hôte front-end que vous avez configuré. Toutefois, l’envoi d’une requête auprès du nom d’hôte du domaine personnalisé retourne un code d’état HTTP 400. L’opération ne semble pas router vers le back-end que vous avez configuré.

### <a name="cause"></a>Cause

Le problème se produit si vous n’avez pas configuré de règle de routage pour le domaine personnalisé qui a été ajouté en tant qu’hôte front-end. Une règle de routage doit être explicitement ajoutée pour cet hôte front-end. Ceci est vrai même si une de règle de routage a déjà été configurée pour l’hôte front-end sous le sous-domaine Azure Front Door (*.azurefd.net).

### <a name="troubleshooting-steps"></a>Étapes de dépannage

Ajoutez une règle de routage pour le domaine personnalisé, afin de diriger le trafic vers le pool de back-ends sélectionné.

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Azure Front Door ne redirige pas HTTP vers HTTPS

### <a name="symptom"></a>Symptôme

Azure Front Door comporte une règle de routage qui redirige le trafic HTTP vers HTTPS, mais l’accès au domaine conserve toujours HTTP comme protocole.

### <a name="cause"></a>Cause

Ce comportement peut se produire si vous n’avez pas configuré les règles de routage correctement pour Azure Front Door. En gros, votre configuration actuelle n’est pas spécifique et a peut-être des règles en conflit.

### <a name="troubleshooting-steps"></a>Étapes de dépannage

## <a name="request-to-a-frontend-host-name-returns-a-404-status-code"></a>Une requête à un nom d’hôte front-end retourne un code d’état 404

### <a name="symptom"></a>Symptôme

Vous avez créé une instance Azure Front Door en configurant un hôte front-end, un pool de back-ends contenant au moins un back-end, ainsi qu’une règle de routage qui connecte l’hôte front-end au pool de back-ends. Votre contenu n’est pas disponible quand vous effectuez une requête auprès de l’hôte front-end configuré. En conséquence, un code d’état HTTP 404 est retourné.

### <a name="cause"></a>Cause

Ce symptôme peut avoir plusieurs causes :

* Le back-end n’est pas un back-end destiné au public et n’est pas visible pour Azure Front Door.
* Le back-end n’est pas correctement configuré, ce qui amène Azure Front Door à envoyer une requête incorrecte. En d’autres termes, votre back-end accepte uniquement le protocole HTTP et vous n’avez pas interdit le protocole HTTPS. Azure Front Door tente donc de transférer des requêtes HTTPS.
* Le backend rejette l’en-tête d’hôte qui a été transféré avec la requête au backend.
* La configuration du back-end n’a pas encore été entièrement déployée.

### <a name="troubleshooting-steps"></a>Étapes de dépannage

* Vérifiez le moment du déploiement :
   * Veillez à attendre au moins 10 minutes pour que la configuration soit déployée.

* Vérifiez les paramètres du back-end :
    * Accédez au pool back-end vers lequel la requête doit être routée. (Cela dépend de la façon dont vous avez configuré la règle de routage.) Vérifiez que le type de l’hôte back-end et le nom de l’hôte back-end sont corrects. Si le back-end est un hôte personnalisé, vérifiez que vous l’avez orthographié correctement. 

    * Vérifiez vos ports HTTP et HTTPS. Dans la plupart des cas, 80 et 443 (respectivement) sont appropriés et aucun changement n’est nécessaire. Toutefois, il est possible que votre back-end ne soit pas configuré de cette façon et qu’il écoute sur un autre port.

    * Vérifiez l’en-tête de l’hôte back-end configuré pour les back-ends vers lesquels l’hôte frontend doit router. Dans la plupart des cas, cet en-tête doit être identique au nom de l’hôte back-end. Toutefois, une valeur incorrecte peut provoquer différents codes d’état HTTP 4xx si le backend attend autre chose. Si vous entrez l’adresse IP de votre back-end, vous devrez peut-être définir le nom d’hôte du back-end comme en-tête de l’hôte back-end.

* Vérifier les paramètres de la règle de routage :
    * Accédez à la règle de routage qui doit router du nom d’hôte front-end en question vers un pool back-end. Assurez-vous que les protocoles acceptés sont configurés correctement lors du transfert de la requête. Le champ des **protocoles acceptés** détermine quelles requêtes Azure Front Door doit accepter. Le protocole de transfert détermine quel protocole Azure Front Door doit utiliser pour transférer la requête au back-end.
      
      Par exemple, si le back-end accepte uniquement les requêtes HTTP, les configurations suivantes sont valides :
            
      * Les Protocoles acceptés sont HTTP et HTTPS. Le prrotocole de transfert est HTTP. Une requête de correspondance ne fonctionnera pas car le protocole HTTPS est autorisé. Si une requête a été envoyée avec HTTPS, Azure Front Door tenterait de la transférer à l’aide de HTTPS.

      * Le protocole accepté est HTTP. Le protocole de transfert est soit la correspondance de la requête, soit HTTP.
    - Le champ **Réécriture d’URL** est désactivé par défaut. Ce champ n’est utilisé que si vous voulez limiter l’étendue des ressources que vous souhaitez rendre disponibles et qui sont hébergées par le back-end. Quand ce champ est désactivé, Azure Front Door transfère le même chemin de requête que celui qu’il reçoit. Il est possible de configurer ce champ de façon incorrecte. Ainsi, quand Azure Front Door requête une ressource du back-end qui n’est pas disponible, un code d’état HTTP 404 est retourné.

## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>Une requête au nom d’hôte front-end retourne un code d’état 411

### <a name="symptom"></a>Symptôme

Vous avez créé une porte d’entrée et configuré un hôte frontend, un pool de back-ends contenant au moins un back-end, ainsi qu’une règle de routage qui connecte l’hôte frontend au pool de back-ends. Votre contenu ne semble pas être disponible quand une requête est envoyée à l’hôte front-end configuré, car un code d’état HTTP 411 est retourné.

Les réponses à ces demandes peuvent également contenir une page d’erreur HTML dans le corps de la réponse, qui inclut une déclaration d’explication. Par exemple : `HTTP Error 411. The request must be chunked or have a content length`.

### <a name="cause"></a>Cause

Ce symptôme peut avoir plusieurs causes. La raison générale est que votre requête HTTP n’est pas entièrement conforme à la norme RFC. 

Un exemple de non-conformité est une requête `POST` envoyée sans en-tête `Content-Length` ou `Transfer-Encoding` (par exemple, à l’aide de `curl -X POST https://example-front-door.domain.com`). Cette requête ne répond pas aux exigences définies dans [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2). Azure Front Door la bloque avec une réponse HTTP 411.

Ce comportement est indépendant de la fonctionnalité Web Application Firewall (WAF) d’Azure Front Door. Actuellement, il n’existe aucun moyen de désactiver ce comportement. Toutes les requêtes HTTP doivent répondre aux exigences, même si la fonctionnalité WAF n’est pas utilisée.

### <a name="troubleshooting-steps"></a>Étapes de dépannage

- Vérifiez que vos demandes sont conformes aux exigences des RFC applicables.

- Prenez note du corps du message HTML renvoyé en réponse à votre requête. Un corps de message explique souvent *la raison* pour laquelle votre requête n’est pas conforme.
