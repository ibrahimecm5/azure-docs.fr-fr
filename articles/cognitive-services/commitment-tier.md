---
title: Créer une ressource Azure Cognitive Services avec des tarifs basés sur le niveau d’engagement
description: Découvrez comment bénéficier de tarifs basés sur le niveau d’engagement, qui diffèrent des tarifs du paiement à l’utilisation.
author: aahill
ms.author: aahi
ms.custom: subject-cost-optimization
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 11/04/2021
ms.openlocfilehash: a139996e881d38fb9170242196b2139993f501ae
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028805"
---
# <a name="quickstart-purchase-commitment-tier-pricing"></a>Guide de démarrage rapide : acheter avec des tarifs basés sur le niveau d’engagement

Cognitive Services propose des tarifs basés sur le niveau d’engagement qui permettent de bénéficier de réductions par rapport au modèle de paiement à l’utilisation. Avec les tarifs basés sur le niveau d’engagement, vous vous engagez à utiliser les fonctionnalités de Cognitive Services suivantes à prix fixe, ce qui vous permet d’avoir un coût total prévisible en fonction des besoins de votre charge de travail :

* Reconnaissance vocale (standard)
* Synthèse vocale (neuronale)
* Traduction de texte (standard)
* Language Understanding (standard) (demandes de texte)
* Azure Cognitive Service for Language
    * Analyse des sentiments
    * Extraction d’expressions clés
    * Détection de la langue
* Vision par ordinateur - Lecture

Les tarifs basés sur le niveau d’engagement sont également disponibles pour le service Applied AI suivant :
* Form Recognizer - Personnalisé/Facture

Pour plus d’informations, consultez [Tarifs Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).

## <a name="request-approval-to-purchase-a-commitment-plan"></a>Demande d’achat d’un plan d’engagement

Pour acheter un plan d’engagement, vous devez [en faire la demande en ligne](https://aka.ms/csgatecommitment). Si votre demande est approuvée, vous pouvez acheter un niveau d’engagement dans le portail Azure pour de nouvelles ressources Azure ou des ressources existantes. 

* Dans le formulaire, vous devez utiliser une adresse e-mail d’entreprise associée à un ID d’abonnement Azure.

* Consultez votre boîte de réception (et votre dossier de courrier indésirable) et recherchez un e-mail de `csgate@microsoft.com` indiquant l’état de votre demande.

Une fois votre demande approuvée, vous pouvez créer une ressource pour utiliser un plan d’engagement ou mettre à jour une ressource existante. 

## <a name="create-a-new-resource"></a>Créer une nouvelle ressource

> [!NOTE]
> Pour acheter et utiliser un plan d’engagement, votre ressource doit être associée au niveau tarifaire Standard. Vous ne pouvez pas acheter un plan d’engagement (ni voir l’option correspondante) si votre ressource est associée au niveau gratuit.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) et sélectionnez **Créer une ressource** pour l’un des services Cognitive Services ou Applied AI listés ci-dessus. 

2. Entrez les informations applicables pour créer votre ressource. Veillez à sélectionner le niveau tarifaire Standard.

    :::image type="content" source="media/commitment-tier/create-resource.png" alt-text="Capture d’écran montrant la création d’une ressource dans le portail Azure." lightbox="media/commitment-tier/create-resource.png":::

3. Une fois votre ressource créée, vous pouvez passer d’un paiement à l’utilisation à un plan d’engagement.  

## <a name="purchase-a-commitment-plan-by-updating-your-azure-resource"></a>Acheter un plan d’engagement en mettant à jour votre ressource Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/) avec l’abonnement Azure qui a été approuvé. 
2. Dans votre ressource Azure pour l’une des fonctionnalités applicables listées ci-dessus, sélectionnez **Tarification du niveau d’engagement**.

    > [!NOTE]
    > L’option d’achat d’un plan d’engagement n’est disponible que si :
    > * La ressource utilise le niveau tarifaire Standard.
    > * Votre demande d’achat d’un plan d’engagement a été approuvée. 
 
1. Sélectionnez **Changer** pour voir les engagements disponibles pour l’API hébergée et l’utilisation du conteneur. 

    :::image type="content" source="media/commitment-tier/commitment-tier-pricing.png" alt-text="Capture d’écran montrant la page des tarifs du niveau d’engagement dans le portail Azure." lightbox="media/commitment-tier/commitment-tier-pricing.png":::

4. Dans la fenêtre qui s’affiche, sélectionnez un **Niveau** et l’option **Renouvellement automatique**.

    * **Niveau d’engagement** : niveau d’engagement pour la fonctionnalité. Le niveau d’engagement est activé dès que vous cliquez sur **Acheter**. Le montant de l’engagement vous est alors facturé au prorata.
    
    * **Renouvellement automatique** : choisissez le mode de renouvellement, de modification ou d’annulation du plan d’engagement actuel à compter du prochain cycle de facturation. Si vous décidez d’effectuer un renouvellement automatique, la **Date de renouvellement automatique** correspond à la date (dans votre fuseau horaire) à laquelle vous serez facturé pour le prochain cycle. Cette date coïncide avec le début du mois civil.
    

    > [!CAUTION]
    > Quand vous cliquez sur **Acheter**, le niveau sélectionné vous est facturé. Une fois l’achat réalisé, le plan d’engagement est non remboursable.
    > 
    > Les plans d’engagement sont facturés tous les mois, à l’exception du premier mois correspondant à l’achat, celui-ci étant facturé au prorata (coût et quota) du nombre de jours restants dans ce mois. Pour les mois suivants, les frais sont facturés le premier jour du mois.

    :::image type="content" source="media/commitment-tier/enable-commitment-plan.png" alt-text="Capture d’écran montrant les tarifs du niveau d’engagement et les détails du renouvellement dans le portail Azure." lightbox="media/commitment-tier/enable-commitment-plan-large.png":::


## <a name="overage-pricing"></a>Tarifs de dépassement

Si vous utilisez la ressource au-delà du quota fourni, l’utilisation supplémentaire vous est facturée conformément au montant de dépassement indiqué dans le niveau d’engagement.

## <a name="purchase-a-different-commitment-plan"></a>Acheter un autre plan d’engagement 

Les plans d’engagement ont une période d’engagement correspondant au mois civil. Vous pouvez acheter un plan d’engagement à tout moment pour remplacer le modèle de paiement à l’utilisation par défaut. Quand vous achetez un plan, vous êtes facturé au prorata du mois restant. Pendant la période d’engagement, vous ne pouvez pas modifier le plan d’engagement pour le mois en cours. Toutefois, vous pouvez choisir un autre plan d’engagement pour le mois civil suivant. Le mois suivant vous est facturé le premier jour de ce mois.

Si vous avez besoin d’un plan d’engagement plus important que ceux proposés, contactez `csgate@microsoft.com`.

## <a name="end-a-commitment-plan"></a>Mettre fin à un plan d’engagement

Si vous décidez d’arrêter un plan d’engagement, sélectionnez **Ne pas renouveler automatiquement** pour votre ressource. Votre plan d’engagement expire à la date de fin de l’engagement affichée. Après cette date, le plan d’engagement ne vous est plus facturé. Vous pouvez continuer à utiliser la ressource Azure pour effectuer des appels d’API, mais vous êtes facturé au tarif du paiement à l’utilisation.

## <a name="see-also"></a>Voir aussi

* [Tarifs d’Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).
* [Soumettre une demande pour bénéficier de tarifs basés sur le niveau d’engagement](https://aka.ms/csgatecommitment)
