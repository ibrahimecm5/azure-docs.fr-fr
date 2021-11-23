---
title: Résoudre les problèmes courants d’Azure Front Door
description: Dans ce tutoriel, vous allez apprendre à résoudre certains des problèmes courants que vous pouvez rencontrer concernant votre instance Azure Front Door.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 11/12/2021
ms.author: duau
ms.openlocfilehash: 681754bff3aead78e2feadfae4bf899ab344c49a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495385"
---
# <a name="troubleshoot-azure-front-door-common-issues"></a>Résoudre les problèmes courants d’Azure Front Door

Cet article explique comment résoudre les problèmes de routage courants que vous pouvez rencontrer concernant votre configuration Azure Front Door.

## <a name="additional-debugging-http-headers"></a>En-têtes HTTP de débogage supplémentaires

Vous pouvez demander à Front Door de retourner des en-têtes de réponse HTTP de débogage supplémentaires. Pour plus d’informations, consultez [En-têtes de réponse facultatifs](front-door-http-headers-protocol.md#optional-debug-response-headers).

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>Réponse 503 d’Azure Front Door après quelques secondes

### <a name="symptom"></a>Symptôme

* Les requêtes régulières envoyées à votre back-end sans passer par Azure Front Door s’effectuent correctement. Le transit via Azure Front Door génère des réponses d’erreur 503.
* L’échec avec Azure Front Door survient généralement après environ 30 secondes.
* Erreurs 503 intermittentes avec le journal `ErrorInfo: OriginInvalidResponse`.

### <a name="cause"></a>Cause

La cause de ce problème peut être lié à l’une des trois possibilités suivantes :
 
* Votre origine prend plus de temps que le délai d’expiration configuré (la valeur par défaut est de 30 secondes) pour recevoir la requête de l’instance Azure Front Door.
* Le temps nécessaire pour envoyer une réponse à la requête provenant d’Azure Front Door prend plus de temps que la valeur du délai d’attente.
* Le client a envoyé une demande de plage d’octets avec `Accept-Encoding header` (compression activée).

### <a name="troubleshooting-steps"></a>Étapes de dépannage

* Envoyez la requête directement à votre back-end (sans passer par Azure Front Door). Découvrez combien de temps il faut généralement à votre back-end pour répondre.
* Envoyez la requête via Azure Front Door et vérifiez si vous obtenez des réponses 503. Si ce n’est pas le cas, il est possible qu’il ne s’agisse pas d’un problème de délai d’expiration. Contactez le support technique.
* Si les demandes transitant par Azure Front Door génèrent un code de réponse d’erreur 503, configurez le **Délai de réponse d’origine (en secondes)** pour le point de terminaison. Vous pouvez étendre le délai d’expiration par défaut jusqu’à 4 minutes (240 secondes). Pour configurer le paramètre, accédez à *Endpoint Manager* et sélectionnez **Modifier un point de terminaison**.

    :::image type="content" source="./media/troubleshoot-issues/origin-response-timeout-1.png" alt-text="Capture d’écran montrant la sélection de l’option Modifier un point de terminaison depuis Endpoint Manager.":::

    Sélectionnez ensuite les **Propriétés du point de terminaison** pour configurer le **Délai de réponse d’origine** :

    :::image type="content" source="./media/troubleshoot-issues/origin-response-timeout-2.png" alt-text="Capture d’écran de la sélection des propriétés du point de terminaison et du champ Délai de réponse d’origine." lightbox="./media/troubleshoot-issues/origin-response-timeout-2-expanded.png":::

* Si le délai d’expiration ne résout pas le problème, utilisez un outil tel que Fiddler ou l’outil de développement de votre navigateur pour vérifier si le client envoie des demandes de plages d’octets avec des en-têtes Accept-Encoding, ce qui conduit à l’origine répondant avec des longueurs de contenu différentes. Si c’est le cas, vous pouvez désactiver la compression sur l’origine/Azure Front Door ou créer une règle de jeu de règles pour supprimer `accept-encoding` de la requête pour les demandes de plages d’octets.

    :::image type="content" source="./media/troubleshoot-issues/remove-encoding-rule.png" alt-text="Capture d’écran de la règle d’acceptation d’encodage dans un jeu de règles.":::

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>Les requêtes envoyées au domaine personnalisé retournent un code d’état 400

### <a name="symptom"></a>Symptôme

* Vous avez créé une instance Azure Front Door, mais une requête auprès du domaine ou de l’hôte front-end retourne un code d’état HTTP 400.
* Vous avez créé un mappage DNS pour un domaine personnalisé vers l’hôte front-end que vous avez configuré. Toutefois, l’envoi d’une requête auprès du nom d’hôte du domaine personnalisé retourne un code d’état HTTP 400. L’opération ne semble pas router vers le back-end que vous avez configuré.

### <a name="cause"></a>Cause

Le problème se produit si vous n’avez pas configuré de règle de routage pour le domaine personnalisé qui a été ajouté en tant qu’hôte front-end. Une règle de routage doit être explicitement ajoutée pour cet hôte front-end. Ceci est vrai même si une de règle de routage a déjà été configurée pour l’hôte front-end sous le sous-domaine Azure Front Door (*.azurefd.net).

### <a name="troubleshooting-steps"></a>Étapes de dépannage

Ajoutez une règle de routage pour le domaine personnalisé, afin de diriger le trafic vers le groupe d’origines sélectionné.

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Azure Front Door ne redirige pas HTTP vers HTTPS

### <a name="symptom"></a>Symptôme

Azure Front Door comporte une règle de routage qui redirige le trafic HTTP vers HTTPS, mais l’accès au domaine conserve toujours HTTP comme protocole.

### <a name="cause"></a>Cause

Ce comportement peut se produire si vous n’avez pas configuré les règles de routage correctement pour Azure Front Door. En gros, votre configuration actuelle n’est pas spécifique et a peut-être des règles en conflit.

### <a name="troubleshooting-steps"></a>Étapes de dépannage


## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>Une requête au nom d’hôte front-end retourne un code d’état 411

### <a name="symptom"></a>Symptôme

Vous avez créé une instance Azure Front Door Standard/Premium et configuré un hôte front-end, un groupe d’origines contenant au moins une origine ainsi qu’une règle de routage qui connecte l’hôte front-end au groupe d’origines. Votre contenu ne semble pas être disponible quand une requête est envoyée à l’hôte front-end configuré, car un code d’état HTTP 411 est retourné.

Les réponses à ces demandes peuvent également contenir une page d’erreur HTML dans le corps de la réponse, qui inclut une déclaration d’explication. Par exemple : `HTTP Error 411. The request must be chunked or have a content length`.

### <a name="cause"></a>Cause

Ce symptôme peut avoir plusieurs causes. La raison générale est que votre requête HTTP n’est pas entièrement conforme à la norme RFC. 

Un exemple de non-conformité est une requête `POST` envoyée sans en-tête `Content-Length` ou `Transfer-Encoding` (par exemple, à l’aide de `curl -X POST https://example-front-door.domain.com`). Cette requête ne répond pas aux exigences définies dans [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2). Azure Front Door la bloque avec une réponse HTTP 411.

Ce comportement est indépendant de la fonctionnalité Web Application Firewall (WAF) d’Azure Front Door. Actuellement, il n’existe aucun moyen de désactiver ce comportement. Toutes les requêtes HTTP doivent répondre aux exigences, même si la fonctionnalité WAF n’est pas utilisée.

### <a name="troubleshooting-steps"></a>Étapes de dépannage

- Vérifiez que vos demandes sont conformes aux exigences des RFC applicables.

- Prenez note du corps du message HTML renvoyé en réponse à votre requête. Un corps de message explique souvent *la raison* pour laquelle votre requête n’est pas conforme.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer une porte d’entrée](quickstart-create-front-door.md).
* Découvrez comment [créer une instance Front Door Standard/Premium](standard-premium/create-front-door-portal.md).
