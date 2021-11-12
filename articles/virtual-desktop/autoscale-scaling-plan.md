---
title: Préversion de la mise à l’échelle automatique de l’hôte de session Azure Virtual Desktop
description: Comment utiliser la fonctionnalité de mise à l’échelle automatique pour allouer des ressources dans votre déploiement.
author: Heidilohr
ms.topic: how-to
ms.date: 10/19/2021
ms.author: helohr
manager: femila
ms.custom: references_regions
ms.openlocfilehash: 2636638bcab5577bee2f4c07bae3653cc4151248
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131421687"
---
# <a name="autoscale-preview-for-azure-virtual-desktop-host-pools"></a>Mise à l’échelle automatique (préversion) pour les pools d’hôtes Azure Virtual Desktop

> [!IMPORTANT]
> La fonctionnalité de mise à l'échelle automatique est actuellement disponible en version préliminaire.
> Pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou plus généralement non encore en disponibilité générale, consultez [l’Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La fonctionnalité de mise à l’échelle automatique (préversion) vous permet d’effectuer un scale-up ou un scale-down des machines virtuelles de votre déploiement Azure Virtual Desktop pour optimiser les coûts de déploiement. En fonction de vos besoins, vous pouvez créer un plan de mise à l’échelle en fonction des éléments suivants :

- Heure de la journée
- Jours spécifiques de la semaine
- Limites de session par hôte de session

>[!NOTE]
> - Azure Virtual Desktop (classique) ne prend pas en charge la fonctionnalité de mise à l’échelle automatique. 
> - Autsoscale ne prend pas en charge la mise à l’échelle des disques éphémères.

Pour de meilleurs résultats, nous vous recommandons d’utiliser la mise à l’échelle automatique avec les machines virtuelles que vous avez déployées avec des modèles Azure Resource Manager Azure Virtual Desktop ou des outils internes de Microsoft.

>[!IMPORTANT]
>La préversion de cette fonctionnalité a actuellement les limitations suivantes :
>
> - Vous pouvez utiliser la mise à l’échelle automatique seulement dans le cloud public Azure.
> - Vous pouvez configurer la mise à l’échelle automatique seulement avec le portail Azure.
> - Vous pouvez déployer le plan de mise à l’échelle seulement dans les régions européennes et aux États-Unis.

## <a name="requirements"></a>Spécifications

Avant de créer votre premier plan de mise à l’échelle, veillez à suivre ces instructions :

- Actuellement, vous pouvez configurer la mise à l’échelle automatique seulement avec des pools hôtes existants regroupés.
- Tous les pools d’hôtes que vous mettez à l’échelle automatiquement doivent avoir un paramètre MaxSessionLimit configuré. N’utilisez pas la valeur par défaut. Vous pouvez configurer cette valeur dans les paramètres du pool d’hôtes dans le portail Azure ou exécuter les applets de commande [New-AZWvdHostPool](/powershell/module/az.desktopvirtualization/new-azwvdhostpool?view=azps-5.7.0&preserve-view=true) ou [Update-AZWvdHostPool](/powershell/module/az.desktopvirtualization/update-azwvdhostpool?view=azps-5.7.0&preserve-view=true) dans PowerShell.
- Vous devez accorder l’accès à Azure Virtual Desktop pour gérer la puissance sur vos ressources de calcul de machine virtuelle.

## <a name="create-a-custom-rbac-role"></a>Créer un rôle RBAC personnalisé

Pour commencer à créer un plan de mise à l’échelle, vous devez d’abord créer un rôle RBAC personnalisé dans votre abonnement. Ce rôle va permettre à Windows Virtual Desktop de gérer la puissance de toutes les machines virtuelles de votre abonnement. Il permet également au service d’appliquer des actions sur les pools d’hôtes et sur les machines virtuelles quand il n’y a aucune session utilisateur active.

Pour créer le rôle personnalisé, suivez les instructions fournies dans [Rôles personnalisés d’Azure](../role-based-access-control/custom-roles.md) en utilisant le modèle JSON suivant. Ce modèle comprend déjà les autorisations dont vous avez besoin. Pour obtenir des instructions plus détaillées, consultez [Assigner des rôles personnalisés avec le Portail Azure](#assign-custom-roles-with-the-azure-portal).
```json
 {
 "properties": {
 "roleName": "Autoscale",
 "description": "Friendly description.",
 "assignableScopes": [
 "/subscriptions/<SubscriptionID>"
 ],
  "permissions": [
   {
   "actions": [
                 "Microsoft.Insights/eventtypes/values/read",
                 "Microsoft.Compute/virtualMachines/deallocate/action",
                 "Microsoft.Compute/virtualMachines/restart/action",
                 "Microsoft.Compute/virtualMachines/powerOff/action",
                 "Microsoft.Compute/virtualMachines/start/action",
                 "Microsoft.Compute/virtualMachines/read",
                 "Microsoft.DesktopVirtualization/hostpools/read",
                 "Microsoft.DesktopVirtualization/hostpools/write",
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/write",
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/delete",
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/read",
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/sendMessage/action",
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/read"
],
  "notActions": [],
  "dataActions": [],
  "notDataActions": []
  }
 ]
}
}
```

## <a name="assign-custom-roles-with-the-azure-portal"></a>Attribuer des rôles personnalisés avec le Portail Azure

Pour créer et attribuer le rôle personnalisé à votre abonnement avec le Portail Azure :

1. Ouvrez le portail Azure et accédez à **Abonnements**.

2. Sélectionnez le bouton **+** dans le coin supérieur gauche de l’écran, puis sélectionnez **Ajouter un rôle personnalisé** dans le menu déroulant, comme illustré dans la capture d’écran suivante.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran montrant le menu déroulant qui s’affiche lorsque vous sélectionnez le bouton avec le signe Plus dans le panneau de configuration du portail Azure. L’option Ajouter un rôle personnalisé est sélectionnée et mise en évidence avec une bordure rouge.](media/add-custom-role.png)

3. Ensuite, nommez le rôle personnalisé et ajoutez une description. Nous vous recommandons de nommer le rôle « Autoscale » (Mise à l’échelle automatique).

4. Sous l’onglet **Autorisations**, ajoutez les autorisations suivantes à l’abonnement auquel vous attribuez le rôle :

    ```azcopy
        "Microsoft.Insights/eventtypes/values/read"
                 "Microsoft.Compute/virtualMachines/deallocate/action"
                 "Microsoft.Compute/virtualMachines/restart/action"
                 "Microsoft.Compute/virtualMachines/powerOff/action"
                 "Microsoft.Compute/virtualMachines/start/action"
                 "Microsoft.Compute/virtualMachines/read"
                 "Microsoft.DesktopVirtualization/hostpools/read"
                 "Microsoft.DesktopVirtualization/hostpools/write"
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read"
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/write"
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/delete"
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/read"
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/sendMessage/action"
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/read"
    ```

5. Lorsque vous avez terminé, sélectionnez **OK**.

Ensuite, vous devez attribuer le rôle pour accorder l’accès à Azure Virtual Desktop.

Pour attribuer le rôle personnalisé afin d’accorder l’accès :

1. Sous l’**onglet Contrôle d’accès (IAM)** , sélectionnez **Ajouter des attributions de rôle**.

2. Sélectionnez le rôle que vous venez de créer et passez à l’écran suivant.

3. Cliquez sur **+ Sélectionner des membres**. Dans la barre de recherche, entrez et sélectionnez **Windows Virtual Desktop**, comme illustré dans la capture d’écran suivante. Si vous disposez d’un déploiement Azure Virtual Desktop (classique) et d’un environnement Azure Virtual Desktop avec des objets Azure Resource Manager, vous verrez deux applications portant le même nom. Sélectionnez-les tous les deux.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran du menu Ajouter une attribution de rôle. Le champ de sélection est mis en évidence en rouge, avec l’utilisateur entrant « Windows Virtual Desktop » dans le champ de recherche.](media/search-for-role.png)

4. Sélectionnez **Vérifier + attribuer** pour terminer l’attribution.

## <a name="how-creating-a-scaling-plan-works"></a>Fonctionnement de la création d’un plan de mise à l’échelle

Avant de créer votre plan, gardez à l’esprit les points suivants :

- Vous pouvez affecter un même plan de mise à l’échelle à un ou plusieurs pools d’hôtes du même type de pool d’hôtes. La planification du plan de mise à l’échelle sera également appliqué à tous les pools d’hôtes affectés.

- Vous ne pouvez associer qu’un seul plan de mise à l’échelle par pool d’hôtes. Si vous affectez un même plan de mise à l’échelle à plusieurs pools d’hôtes, vous ne pouvez pas affecter un autre plan de mise à l’échelle à ces pools d’hôtes.

- Un plan de mise à l’échelle peut fonctionner seulement dans le fuseau horaire configuré pour ce plan.

- Un plan de mise à l’échelle peut avoir une ou plusieurs planifications. Par exemple, des planifications différentes pour les jours de la semaine et pour le week-end.

- Veillez à bien comprendre les modèles d’utilisation avant de définir votre planification. Vous allez devoir planifier pour les moments de la journée suivants :

    - Augmentation : le début de la journée, quand l’utilisation reprend.
    - Heures de pointe : le moment de la journée où l’utilisation est la plus élevée.
    - Diminution : quand l’utilisation diminue. C’est généralement le moment où vous arrêtez vos machines virtuelles pour réduire les coûts.
    - Heures creuses : le moment où l’utilisation est la plus faible possible. Vous pouvez définir le nombre maximal de machines virtuelles qui peuvent être actives pendant cette période.

- Le plan de mise à l’échelle prend effet dès que vous l’activez.

Gardez aussi ces limitations à l’esprit :

- N’utilisez pas la mise à l’échelle automatique conjointement avec d’autres outils de mise à l’échelle de Microsoft ou de tiers. Veillez à désactiver ceux-ci pour les pools d’hôtes auxquels vous appliquez les plans de mise à l’échelle.

- La mise à l’échelle automatique remplace le mode maintenance : veillez donc à utiliser des étiquettes d’exclusion lors de la mise à jour des machines virtuelles dans les pools d’hôtes.

- La mise à l’échelle automatique ignore les algorithmes d’équilibrage de charge existants dans les paramètres de votre pool d’hôtes et applique à la place un équilibrage de charge en fonction de la configuration de votre planification.

## <a name="create-a-scaling-plan"></a>Créer un plan de mise à l’échelle

Pour créer un plan de mise à l’échelle :

1. Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com/).

2. Accédez à **Azure Virtual Desktop** > **Plans de mise à l’échelle**, puis sélectionnez **Créer**.

3. Sous l’onglet **Concepts de base**, regardez sous **Détails du projet**, puis et sélectionnez le nom de l’abonnement auquel vous allez affecter le plan de mise à l’échelle.

4. Si vous souhaitez créer un nouveau groupe de ressources, sélectionnez **Créer**. Si vous souhaitez utiliser un groupe de ressources existant, sélectionnez son nom dans le menu déroulant.

5. Entrez un nom pour le plan de mise à l’échelle dans le champ **Nom**.

6. Si vous le souhaitez, vous pouvez également ajouter un nom « convivial » qui sera affiché pour vos utilisateurs et une description pour votre plan.

7. Pour **Région**, sélectionnez une région pour votre plan de mise à l’échelle. Les métadonnées pour l’objet seront stockées dans la zone géographique associée à la région. Pour en savoir plus sur les régions, consultez [Emplacements des données](data-locations.md).

8. Pour **Fuseau horaire**, sélectionnez le fuseau horaire à utiliser avec votre plan.

9. Dans **Étiquettes d’exclusion**, entrez des étiquettes pour les machines virtuelles que vous ne souhaitez pas inclure dans les opérations de mise à l’échelle. Par exemple, vous voulez utiliser cette fonctionnalité pour la maintenance. Quand vous avez défini des machines virtuelles sur le mode maintenance, utilisez l’étiquette pour que la mise à l’échelle automatique ne remplace pas le mode maintenance.

10. Sélectionnez **Suivant** pour accéder à l’onglet **Planifications**.

## <a name="configure-a-schedule"></a>Configurer une planification

Les planifications vous permettent de définir le moment où la mise à l’échelle automatique active les modes Augmentation et Diminution au cours de la journée. Dans chaque phase de la planification, la mise à l’échelle automatique désactive des machines virtuelles seulement quand un hôte de session n’a aucune session active. Les valeurs par défaut que vous voyez quand vous essayez de créer une planification sont les valeurs suggérées pour les jours de la semaine, mais vous pouvez les modifier si nécessaire. 

Pour créer ou modifier une planification :

1. Sous l’onglet **Planifications**, sélectionnez **Ajouter une planification**.

2. Entrez un nom pour votre planification dans le champ **Nom de la planification**.

3. Dans le champ **Répéter**, sélectionnez les jours où votre planification va se répéter.

4. Sous l’onglet **Augmentation**, renseignez les champs suivants :

    - Pour **Heure de début**, sélectionnez une heure dans le menu déroulant pour commencer à préparer les machines virtuelles pour les heures de pointe de l’activité.

    - Pour **Algorithme d’équilibrage de charge**, nous recommandons de sélectionner **Algorithme de parcours en largeur**. L’équilibrage de charge en largeur d’abord va distribuer les utilisateurs sur les machines virtuelles existantes pour conserver des temps d’accès rapides.
        
        >[!NOTE]
        >La préférence d’équilibrage de charge que vous sélectionnez ici va remplacer celle que vous avez sélectionnée pour les paramètres de votre pool d’hôtes d’origine.

    - Pour **Pourcentage minimal de machines virtuelles de l’hôte de session**, entrez la quantité de ressources de l’hôte de session que vous souhaitez utiliser pendant les heures d’augmentation et les heures de pointe. Par exemple, si vous choisissez **10 %** et que votre pool d’hôtes a 10 hôtes de session, la mise à l’échelle automatique va conserver un hôte de session disponible pour les connexions utilisateur à tout moment pendant les heures d’augmentation et les heures de pointe.
    
    - Pour **Seuil de capacité**, entrez le pourcentage d’utilisation du pool d’hôtes qui va déclencher le début des phases d’augmentation et de pointe. Par exemple, si vous choisissez **60 %** pour un pool d’hôtes qui peut gérer 100 sessions, la mise à l’échelle automatique va activer des hôtes supplémentaires seulement une fois que le pool d’hôtes dépasse 60 sessions.

5. Sous l’onglet **Heures de pointe**, renseignez les champs suivants :

    - Pour le champ **Heures de début**, entrez une heure de début pour la période où le taux d’utilisation est le plus élevé au cours de la journée. Veillez à ce que l’heure soit dans le même fuseau horaire que celui que vous avez spécifié pour votre plan de mise à l’échelle. Cette heure correspond également à l’heure de fin de votre phase d’augmentation.

    - Pour **Équilibrage de charge**, vous pouvez sélectionner l’équilibrage de charge en largeur d’abord ou l’équilibrage de charge en profondeur d’abord. L’équilibrage de charge en largeur d’abord répartit les nouvelles sessions utilisateur entre toutes les sessions disponibles dans le pool d’hôtes. L’équilibrage de charge en profondeur d’abord répartit les nouvelles sessions utilisateur sur un hôte de session disponible qui a le plus grand nombre de connexions sans avoir encore atteint sa limite maximale de sessions. Pour plus d’informations sur les types d’équilibrage de charge, consultez [Configurer la méthode d’équilibrage de charge d’Azure Virtual Desktop](configure-host-pool-load-balancing.md).

    >[!NOTE]
    >Vous ne pouvez pas modifier le seuil de capacité ici. Au lieu de cela, la valeur que vous avez entrée dans **Augmentation** va être utilisée pour ce paramètre.

    - Pour **Diminution**, vous entrez des valeurs dans des champs similaires à **Augmentation**, mais cette fois-ci, c’est pour le moment où l’utilisation du pool d’hôtes diminue. Ceci comprend les champs suivants :

      - Heure de début
      - Algorithme d’équilibrage de charge
      - Pourcentage minimal d’hôtes (%)
      - Seuil de capacité (%)
      - Forcer la déconnexion des utilisateurs

    - De même, les **Heures creuses** fonctionnent comme **Heures de pointe** :

      - Heure de début, qui est aussi la fin de la période de diminution.
      - Algorithme d’équilibrage de charge. Nous recommandons de choisir **en profondeur d’abord** pour réduire progressivement le nombre d’hôtes de session en fonction des sessions sur chaque machine virtuelle.
      - Tout comme pour les heures de pointe, vous ne pouvez pas configurer le seuil de capacité ici. Au lieu de cela, la valeur que vous avez entrée dans **Diminution** va être utilisée pour ce paramètre.

## <a name="assign-host-pools"></a>Affecter des pools d’hôtes

Maintenant que vous avez configuré votre plan de mise à l’échelle, il est temps d’affecter le plan à vos pools d’hôtes. Cochez la case en regard de chaque pool d’hôtes que vous voulez inclure. Si vous ne souhaitez pas activer la mise à l’échelle automatique, décochez toutes les cases. Vous pouvez toujours revenir à ce paramètre ultérieurement et le modifier.

>[!NOTE]
>Quand vous créez ou que vous mettez à jour un plan de mise à l’échelle qui est déjà affecté à des pools d’hôtes, ses modifications sont appliquées immédiatement.

## <a name="add-tags"></a>Ajouter des étiquettes 

Après cela, vous devez entrer des étiquettes. Les étiquettes sont constituées de paires nom-valeur qui vous permettent de catégoriser des ressources pour une facturation consolidée. Vous pouvez appliquer la même étiquette à plusieurs ressources et groupes de ressources. Pour en savoir plus sur l’étiquetage des ressources, consultez [Utiliser des étiquettes pour organiser vos ressources Azure](../azure-resource-manager/management/tag-resources.md).

>[!NOTE] 
>Si vous changez les paramètres des ressources sous d’autres onglets après avoir créé des étiquettes, vos étiquettes sont mises à jour automatiquement.

Une fois que vous avez terminé, accédez à l’onglet **Vérifier + créer**, puis sélectionnez **Créer** pour déployer votre pool d’hôtes.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé votre plan de mise à l’échelle, voici quelques opérations que vous pouvez effectuer :

- [Affecter votre plan de mise à l’échelle à des pool d’hôtes nouveaux ou existants](autoscale-new-existing-host-pool.md)
- [Activer les diagnostics pour votre plan de mise à l’échelle](autoscale-diagnostics.md)
