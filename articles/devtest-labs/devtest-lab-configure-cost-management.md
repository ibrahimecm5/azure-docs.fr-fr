---
title: Afficher les tendances de coût mensuelles estimées du laboratoire
description: Cet article fournit des informations sur le suivi du coût de votre laboratoire (graphique de tendance des coûts mensuels estimés) dans Azure DevTest Labs.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: f0e99ae3da38ef723b211075c9f32dc1bbdfda70
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286466"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Suivre les coûts associés à un laboratoire dans Azure DevTest Labs
Cet article fournit des informations sur la façon de suivre le coût de votre laboratoire. Il explique la procédure d’affichaeg de la tendance des coûts estimés du labo pour le mois en cours. L’article vous montre également comment afficher le coût du mois en cours par ressource du laboratoire.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Afficher les tendances de coût mensuelles estimées du laboratoire 
Dans cette section, découvrez comment utiliser le graphique **Tendance des coûts mensuels estimés** pour afficher le coût estimé jusqu’à la date actuelle ainsi que le coût projeté pour la fin du mois en cours. Vous apprenez également comment gérer les coûts de laboratoire en définissant des seuils et des dépenses cibles qui, une fois atteints, déclenchent la génération de résultats par DevTest Labs.

Pour afficher le graphique Tendance des coûts mensuels estimés, procédez comme suit : 

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services**, puis **DevTest Labs** dans la liste.
3. Dans la liste de laboratoires, sélectionnez votre laboratoire.  
4. Sélectionnez **Configuration et stratégies** dans le menu de gauche.  
4. Sélectionnez **Tendance des coûts** dans la section **Suivi des coûts** du menu de gauche. La capture d'écran suivante montre un exemple de graphique de coûts. 
   
    ![Capture d’écran montrant un graphique de coûts.](./media/devtest-lab-configure-cost-management/graph.png)

    La valeur **Coût estimé** est le coût estimé à ce jour pour le mois calendaire en cours. Le **Coût projeté** est le coût estimé pour le mois calendaire entier, calculé à l’aide du coût du laboratoire lors des cinq derniers jours.

    Les montants des coûts sont arrondis à l’entier supérieur. Par exemple : 

   * 5,01 est arrondi à 6 
   * 5,50 est arrondi à 6
   * 5,99 est arrondi à 6

     Comme indiqué au-dessus du graphique, les coûts que vous voyez par défaut dans le graphique sont les coûts *estimés* avec les tarifs de l’offre [Paiement à l'utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/). Vous pouvez également définir vos propres dépenses cibles à afficher dans les graphiques en [gérant les coûts cibles de votre laboratoire](#managing-cost-targets-for-your-lab).

     Les coûts suivants ne sont *pas* inclus dans le calcul des coûts :

   * Les abonnements CSP et Dreamspark ne sont actuellement pas pris en charge. Azure DevTest Labs utilise les API de facturation Azure pour calculer le coût du labo, qui ne prend pas en charge les abonnements CSP ou Dreamspark.
   * Les tarifs de votre offre. Actuellement, vous ne pouvez pas utiliser les tarifs de l’offre affichés sous votre abonnement que vous avez négociés avec Microsoft ou les partenaires Microsoft. Seuls les tarifs du paiement à l'utilisation sont disponibles.
   * Vos taxes
   * Vos remises
   * Votre devise de facturation. Actuellement, le coût du labo s'affiche uniquement dans la devise USD.

### <a name="managing-cost-targets-for-your-lab"></a>Gestion des coûts cibles de votre laboratoire
DevTest Labs vous aide à gérer vos coûts de labo en fixant un objectif de dépenses que vous pouvez visualiser dans le graphique Tendance du coût estimé par mois. DevTest Labs peut vous envoyer une notification lorsque les dépenses atteignent le seuil cible spécifié. 

1. À la page **Tendance des coûts**, sélectionnez **Gérer la cible**.

    ![Capture d’écran montrant le bouton Gérer la cible.](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. À la page **Gérer la cible**, spécifiez les dépenses cibles et des seuils. Vous pouvez également indiquer si chaque seuil sélectionné doit figurer sur le graphique de tendance du coût ou être communiqué via une notification Webhook.

    ![Capture d’écran montrant le volet Gérer la cible.](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Sélectionnez la période de temps durant laquelle vos coûts cibles doivent être suivis.
      - **Tous les mois** : le suivi des coûts cibles est effectué mensuellement.
      - **Fixe** : le suivi des coûts cibles est effectué dans la plage de dates que vous spécifiez dans les champs des dates de début et de fin. Généralement, ces valeurs représentent la durée d’exécution prévue de votre projet.
   - Spécifiez un **coût cible**. Par exemple, le montant que vous prévoyez de dépenser pour ce laboratoire dans la période de temps que vous avez définie.
   - Sélectionnez l’option appropriée pour activer ou désactiver un seuil à rapporter (par incréments de 25 %), jusqu’à 125 % du **coût cible** spécifié.
      - **Notifier** : lorsque les résultats atteignent ce seuil, l’URL webhook que vous spécifiez vous en informe.
      - **Tracer sur le graphique** : quand les résultats atteignent ce seuil, les résultats sont tracés sur un graphique de tendance de coût que vous pouvez afficher.
   - Si vous choisissez l’option **Notifier** pour l’envoi d’une notification quand le seuil est atteint, vous devez spécifier une URL Webhook. Dans la zone Intégrations des coûts, sélectionnez **Cliquez ici pour ajouter une intégration**. Entrez une **URL Webhook** dans le volet Configurer la notification, puis sélectionnez **OK**.

       ![Capture d’écran montrant le volet configurer la notification.](./media/devtest-lab-configure-cost-management/configure-notification-new.png)

     - Si vous sélectionnez l’option **Notifier**, vous devez définir une URL Webhook.
     - De même, si vous définissez une URL Webhook, vous devez définir le paramètre **Notification** sur **Activé** dans le volet Seuil de coût.
     - Créez le webhook avant de l’entrer ici.  

       Pour plus d’informations sur les webhooks, consultez [Créer une fonction Azure d’API ou de webhook](../azure-functions/functions-bindings-http-webhook.md). 

## <a name="view-cost-by-resource"></a>Afficher le coût par ressource 
La fonctionnalité de tendance de coût mensuel des laboratoires permet de voir combien vous avez dépensé pendant le mois en cours. La fonctionnalité montre également la projection de vos dépenses jusqu'à la fin du mois sur la base de vos dépenses au cours des sept derniers jours. Pour vous aider à comprendre dès le départ pourquoi les dépenses du laboratoire atteignent des seuils, vous pouvez utiliser la fonctionnalité **coût par ressource**, qui vous montre le coût mensuel jusqu’à la date actuelle **par ressource** dans une table.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services**, puis **DevTest Labs** dans la liste.
3. Dans la liste des laboratoires, sélectionnez le laboratoire souhaité.  
4. Sélectionnez **Configuration et stratégies** dans le menu de gauche.
5. Sélectionnez **Coût par ressource** dans la section **Suivi des coûts** du menu de gauche. Vous voyez les coûts associés à chaque ressource liée à un laboratoire. 

    ![Capture d’écran montrant le coût par ressource.](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Cette fonctionnalité aide à identifier facilement les ressources les plus onéreuses, donc à prendre des mesures pour réduire les dépenses de laboratoire. Par exemple, le coût d’une machine virtuelle est basé sur sa taille. Le prix varie selon la taille de la machine virtuelle. Vous pouvez afficher la taille d’une machine virtuelle et son propriétaire, et discuter avec le propriétaire pour savoir pourquoi il a besoin de cette taille et s’il peut la réduire.

Une [stratégie d’arrêt automatique](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy) aide à réduire le coût en arrêtant les machines virtuelles du laboratoire à une heure précise de la journée. Toutefois, un utilisateur de laboratoire peut refuser de la stratégie d’arrêt, ce qui augmente le coût d’exécution de la machine virtuelle. Sélectionnez une machine virtuelle dans le tableau pour voir si elle a été exclue de la stratégie d’arrêt automatique. Contactez le propriétaire de la machine virtuelle pour savoir pourquoi il s’est désinscrit et voir s’il peut se réinscrire.
 
## <a name="next-steps"></a>Étapes suivantes
Voici quelques possibilités d’opérations pour la suite :

* [Définir des stratégies de laboratoire](devtest-lab-set-lab-policy.md). Apprenez à définir les différentes stratégies utilisées pour gérer l’utilisation de votre laboratoire et de ses machines virtuelles. 
* [Créer une image personnalisée](devtest-lab-create-template.md). Lorsque vous créez une machine virtuelle, vous spécifiez une base. La base peut être une image personnalisée ou une image de la Place de marché. Cet article explique comment créer une image personnalisée à partir d’un fichier VHD.
* [Configurer des images de la Place de marché](devtest-lab-configure-marketplace-images.md). DevTest Labs prend en charge la création de machines virtuelles basées sur des images de la Place de marché Azure. Cet article explique comment spécifier les images de la Place de marché Azure pouvant être utilisées lors de la création de machines virtuelles dans un labo.
* [Créer une machine virtuelle dans un labo](devtest-lab-add-vm.md). Cet article explique comment créer une machine virtuelle à partir d’une image de base personnalisée ou de la Place de marché, et comment utiliser des artefacts dans la machine virtuelle.
