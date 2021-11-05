---
title: 'Isolement réseau et lien privé : réponses aux questions'
description: Les utilisateurs peuvent restreindre l’accès public aux ressources répondant aux questions.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: e16734ac0ff53f778fad368913d89bcea3869ee3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097722"
---
#  <a name="network-isolation-and-private-endpoints"></a>Isolement réseau et points de terminaison privés

Les étapes ci-dessous décrivent comment restreindre l’accès public aux ressources de réponses aux questions et comment activer le lien privé Azure. Protégez une ressource Cognitive Services contre l’accès public en [configurant le réseau virtuel](../../../cognitive-services-virtual-networks.md?tabs=portal).

## <a name="private-endpoints"></a>Points de terminaison privés

Azure Private Endpoint est une interface réseau qui vous permet de vous connecter de façon privée et sécurisée à un service basé sur Azure Private Link.  Le service Réponses aux questions personnalisées vous aide à créer des points de terminaison privés dans le service Recherche Azure.

Les points de terminaison privés sont fournis par [Azure Private Link](../../../../private-link/private-link-overview.md), en tant que service distinct. Pour plus d’informations sur les coûts, consultez la page [Tarification](https://azure.microsoft.com/pricing/details/private-link/).

## <a name="steps-to-enable-private-endpoint"></a>Étapes pour activer un point de terminaison privé

1. Attribuez le rôle de *contributeur* à la ressource de langue (en fonction du contexte que cela peut afficher en tant que ressource analyse de texte) dans l’instance Azure Search. Cette opération nécessite un accès *Propriétaire* à l’abonnement. Ouvrez l’onglet Identité de la ressource de service pour accéder à l’identité.

> [!div class="mx-imgBorder"]
> ![Identité d’Analyse de texte](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoints-identity.png)

2. Ajoutez l’identité ci-dessus comme *Contributeur* en accédant à l’onglet Azure Search Service IAM.

![IAM pour service géré](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-access-control.png)

3. Sélectionnez *Ajouter des attributions de rôles*, ajoutez l’identité, puis sélectionnez *Enregistrer*.

![Attribution de rôle gérée](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-role-assignment.png)

4. À présent, ouvrez l’onglet *Mise en réseau* de l’instance du service Recherche Azure, puis basculez les données de connectivité du point de terminaison de *Public* vers *Privé*. Cette opération est un long processus qui peut prendre jusqu’à 30 minutes. 

![Mise en réseau Recherche Azure gérée](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking.png)

5. Ouvrez l’onglet *Mise en réseau* du service QnA Maker managé, puis sous *Autoriser l’accès depuis*, sélectionnez l’option *Points de terminaison privés et réseaux sélectionnés* et cliquez sur *Enregistrer*.
 
> [!div class="mx-imgBorder"]
> ![Mise en réseau du service Analyse de texte](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-custom-qna.png)

Cela permet d’établir une connexion de point de terminaison privée entre la ressource de langue et l’instance de service Azure Recherche cognitive. Vous pouvez vérifier la connexion au point de terminaison privé sous l’onglet *Mise en réseau* de l’instance du service Recherche cognitive Azure. Une fois l’intégralité de l’opération terminée, vous pouvez utiliser votre ressource de langue avec la réponse aux questions activée.

![Service de mise en réseau géré](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-3.png)

## <a name="support-details"></a>Détails de la prise en charge
 * Une fois que vous avez activé l’accès privé à votre service Analyse de texte, nous ne prenons plus en charge les modifications apportées au service Azure Recherche cognitive. Si vous modifiez le service Recherche cognitive Azure via l’onglet « Fonctionnalités » après avoir activé l’accès privé, le service Analyse de texte devient inutilisable.

 * Après avoir établi une connexion au point de terminaison privé, si vous basculez la mise en réseau du service Recherche cognitive Azure sur « Publique », vous ne pouvez plus utiliser le service Analyse de texte. La mise en réseau du service Recherche Azure doit être « Privée » pour que la connexion au point de terminaison privé fonctionne.

## <a name="restrict-access-to-cognitive-search-resource"></a>Restreindre l’accès à une ressource Recherche cognitive

Suivez les étapes ci-dessous pour restreindre l’accès public aux ressources de la langue de réponses aux questions. Protégez une ressource Cognitive Services contre l’accès public en [configurant le réseau virtuel](../../../cognitive-services-virtual-networks.md?tabs=portal).

Après avoir restreint l’accès à la ressource Cognitive Services en fonction du sous-réseau, effectuez les étapes suivantes pour parcourir les bases de connaissances sur le portail Language Studio à partir de votre réseau local ou de votre navigateur local.
- Accordez l’accès au [réseau local](../../../cognitive-services-virtual-networks.md?tabs=portal#configuring-access-from-on-premises-networks).
- Accordez l’accès à votre [machine/navigateur local](../../../cognitive-services-virtual-networks.md?tabs=portal#managing-ip-network-rules).
- Ajoutez l’**adresse IP publique de la machine sous la section Pare-feu** de l’onglet **Réseau**. Par défaut, `portal.azure.com` affiche l’IP publique de la machine de navigation actuelle. Sélectionnez cette entrée, puis sélectionnez **Enregistrer**.

     > [!div class="mx-imgBorder"]
     > [ ![Capture d’écran de l’IU de configuration - Pare-feux et réseaux virtuels]( ../../../qnamaker/media/network-isolation/firewall.png) ](  ../../../qnamaker/media/network-isolation/firewall.png#lightbox)
