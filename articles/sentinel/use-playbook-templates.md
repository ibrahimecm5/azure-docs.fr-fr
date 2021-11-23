---
title: Créer et personnaliser des playbooks Microsoft Sentinel à partir de modèles intégrés | Microsoft Docs
description: Cet article explique comment créer des playbooks à partir de modèles de playbooks et comment utiliser ces derniers afin de les personnaliser en fonction de vos besoins.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: f2034ba667e3d96846aa27e58dce2555da39116b
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518828"
---
# <a name="create-and-customize-microsoft-sentinel-playbooks-from-built-in-templates"></a>Créer et personnaliser des playbooks Microsoft Sentinel à partir de modèles intégrés

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> Les **modèles de playbook** sont actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

Un modèle de playbook est un workflow prédéfini, testé et prêt à l’emploi qui peut être personnalisé pour répondre à vos besoins. Les modèles peuvent également servir de référence dans les bonnes pratiques pour le développement de règles à partir de zéro ou d’inspiration pour les nouveaux scénarios d’automatisation.

Les modèles de playbook ne sont pas des playbooks actifs, mais ils vous permettent d’en créer un (une copie modifiable du modèle).

De nombreux modèles de playbooks ont été développés par la communauté Microsoft Sentinel, des éditeurs de logiciels indépendants (ISV) et les experts de Microsoft, sur la base de scénarios d’automatisation populaires utilisés par les centres d’opérations de sécurité du monde entier.

Vous pouvez obtenir des modèles de playbook à partir des sources suivantes :

- L’onglet **Modèles de playbook** (sous **Automatisation**) présente les principaux scénarios fournis par la communauté Microsoft Sentinel. Vous pouvez créer plusieurs playbooks actifs à partir du même modèle.

    Quand une nouvelle version du modèle est publiée, les playbooks actifs créés à partir de ce modèle (sous l’onglet **Playbooks**) sont étiquetés avec une notification indiquant qu’une mise à jour est disponible.

- Les modèles de playbook peuvent également être obtenus dans le cadre d’une [solution Microsoft Sentinel](sentinel-solutions.md) pour un produit spécifique. Le déploiement de la solution produit des playbooks actifs.

- Le [référentiel GitHub Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks) contient de nombreux modèles de playbook. Vous pouvez les déployer dans un abonnement Azure en sélectionnant le bouton **Déployer sur Azure**.

Techniquement, un modèle de playbook est un [modèle Azure Resource Manager (ARM)](../azure-resource-manager/templates/index.yml) composé de plusieurs ressources : un workflow Azure Logic Apps et des connexions d’API pour chaque connexion impliquée.

Cet article porte sur le déploiement d’un modèle de playbook à partir de l’onglet **Modèles de playbook** sous **Automatisation**.

Cet article vous aide à comprendre comment :

> [!div class="checklist"]
> - Explorer les modèles de playbook prêts à l’emploi
> - Déployer un modèle de playbook

## <a name="explore-playbook-templates"></a>Explorer les modèles de playbook

Dans le menu de navigation de Microsoft Sentinel, sélectionnez **Automatisation**, puis l’onglet **Modèles de playbooks**.

Les modèles de playbooks affichés ici présentent les principaux scénarios d’automatisation que les centres des opérations de sécurité ont tendance à utiliser ou dont ils s’inspirent. La plupart de ces playbooks ont été fournis par la communauté de Microsoft Sentinel et se trouvaient à l’origine dans le référentiel GitHub de Microsoft Sentinel. Certains d’entre eux ont été intégrés dans les solutions Microsoft Sentinel.

:::image type="content" source="media/use-playbook-templates/gallery.png" alt-text="Capture d’écran de la galerie de playbooks." lightbox="media/use-playbook-templates/gallery.png":::

Pour trouver un modèle de playbook adapté à vos besoins, vous pouvez filtrer la liste selon les critères suivants :

- **Déclencheur** indique que le playbook est déclenché par la création d’un incident (et peut donc être attaché à une règle d’automatisation), par la création d’une alerte (et peut donc être attaché à une règle d’analyse) ou par autre chose. [En savoir plus](playbook-triggers-actions.md#microsoft-sentinel-triggers-summary)

- **Connecteurs Logic Apps** affiche les services externes avec lesquels ce playbook va interagir. Pendant le processus de déploiement, chaque connecteur devra assumer une identité pour s’authentifier auprès du service externe.

- **Entités** montre les types d’entités explicitement filtrés et analysés par un playbook qui s’attend à trouver ces types d’entités dans l’incident. Par exemple, un playbook qui demande à un pare-feu de bloquer une adresse IP s’attend à opérer sur des incidents créés par des règles d’analyse qui génèrent des alertes contenant des adresses IP, comme une règle de détection d’attaque par force brute.

- **Étiquettes** montre les étiquettes appliquées au playbook pour le relier à un scénario spécifique ou pour indiquer une caractéristique spéciale.

  Exemples :

  - **Enrichissement** : Le playbook récupère des informations d’un autre service pour ajouter des informations à un incident. Ces informations sont généralement ajoutées sous forme de commentaire à l’incident ou envoyées au centre des opérations de sécurité.

  - **Correction** : Le playbook entreprend une action sur les entités concernées pour éliminer une menace potentielle.

  - **Synchronisation** : Le playbook aide à maintenir un service externe, tel qu’un service de gestion des incidents, à jour avec les propriétés de l’incident.

  - **Notification** : Le playbook envoie un e-mail ou un message.

  - **Réponse de Teams** : Le playbook permet aux analystes d’effectuer une action manuelle à partir de Teams à l’aide de cartes interactives.

:::image type="content" source="media/use-playbook-templates/filters.png" alt-text="Filtrer la liste des modèles de playbook":::

## <a name="customize-a-playbook-from-a-template"></a>Personnaliser un playbook à partir d’un modèle

Cette procédure décrit comment déployer des modèles de playbook.

Vous pouvez répéter ce processus pour créer plusieurs playbooks sur le même modèle.

1. Sélectionnez un nom de playbook dans l’onglet **Modèles de playbooks**.

1. Si le playbook a des conditions préalables, veillez à suivre les instructions.

    - Certains playbooks appellent d’autres playbooks en tant qu’actions. Ce deuxième playbook est appelé un **playbook imbriqué**. Dans ce cas, l’une des conditions préalables sera de déployer d’abord le playbook imbriqué.

    - Certains playbooks nécessiteront le déploiement d’un connecteur Logic Apps personnalisé ou d’une fonction Azure. Dans ce cas, un lien **Déployer dans Azure** <!-- WHERE? --> vous conduira au processus général de déploiement du modèle ARM.

1. Sélectionnez **Créer un playbook** pour ouvrir l’Assistant de création de playbook basé sur le modèle sélectionné. L’Assistant comporte quatre onglets :

    - **Informations de base :** Localisez votre nouveau playbook (ressource Logic Apps) et donnez-lui un nom (vous pouvez utiliser le nom par défaut).
        :::image type="content" source="media/use-playbook-templates/basics.png" alt-text="Assistant de création de playbook, onglet Informations de base":::

    - **Paramètres :** Entrez les valeurs spécifiques au client que le playbook utilisera. Par exemple, si ce playbook envoie un e-mail au centre des opérations de sécurité, vous pouvez définir l’adresse e-mail du destinataire ici. Cet onglet s’affiche uniquement si le playbook a des paramètres.

        > [!NOTE]
        > Si un connecteur personnalisé est utilisé dans ce playbook, il doit être déployé dans le même groupe de ressources et vous pourrez insérer son nom dans cet onglet.

        :::image type="content" source="media/use-playbook-templates/parameters.png" alt-text="Assistant de création de playbook, onglet Paramètres":::

    - **Connexions :** Développez chaque action pour voir les connexions existantes que vous avez créées pour les playbooks précédents. En savoir plus sur la [création de connexions pour les playbooks](authenticate-playbooks-to-sentinel.md).

        > [!NOTE]
        > Pour les connecteurs personnalisés, les connexions seront affichées par le nom du connecteur personnalisé entré dans l’onglet **Paramètres**.

        :::image type="content" source="media/use-playbook-templates/connections.png" alt-text="Assistant de création de playbook, onglet Connexions":::

        S’il n’y en a pas, ou si vous voulez en créer de nouvelles, choisissez **Créer une nouvelle connexion après le déploiement**. Vous accéderez ainsi au concepteur Logic Apps une fois le processus de déploiement terminé.

        Pour les connecteurs qui prennent en charge la [connexion avec une identité managée](authenticate-playbooks-to-sentinel.md#authenticate-with-managed-identity), comme **Microsoft Sentinel**, ce sera la méthode de connexion sélectionnée par défaut.

    - **Vérifier et créer :** Affichez un résumé du processus et attendez la validation de vos entrées avant de créer le playbook.

1. Après avoir suivi les étapes de l’Assistant de création de playbook jusqu’au bout, vous accédez à la conception du flux de travail du nouveau playbook dans le concepteur Logic Apps.

    :::image type="content" source="media/use-playbook-templates/designer.png" alt-text="Voir le playbook dans le concepteur Logic Apps":::

1. Pour chaque connecteur pour lequel vous avez choisi de créer une nouvelle connexion après le déploiement :
    1. Dans le menu de navigation, sélectionnez **Connexions d’API**.
    1. Sélectionnez le nom de la connexion.
    1. Sélectionnez **Modifier la connexion d’API** dans le menu de navigation.
    1. Renseignez les paramètres requis et cliquez sur **Enregistrer**.
        :::image type="content" source="media/use-playbook-templates/edit-api-connection.png" alt-text="Capture d’écran montrant comment modifier les connexions d’API.":::

    Vous pouvez également créer une nouvelle connexion à partir des étapes appropriées dans le concepteur Logic Apps :
    1. Pour chaque étape qui s’affiche avec un signe d’erreur, sélectionnez-la pour la développer.
    1. Sélectionnez **Ajouter**.
    1. Authentifiez-vous conformément aux instructions pertinentes.
    1. Si d’autres étapes utilisent ce même connecteur, développez leurs zones. Dans la liste des connexions qui s’affiche, sélectionnez la connexion que vous venez de créer.

1. Si vous avez choisi d’utiliser une connexion d’identité managée pour Microsoft Sentinel (ou pour d’autres connexions prises en charge), accordez des autorisations au nouveau playbook sur l’espace de travail Microsoft Sentinel (ou sur les ressources cibles pertinentes pour les autres connecteurs).

1. Enregistrez le playbook. Vous pouvez maintenant le voir dans l’onglet **Playbooks actifs**.

1. Pour exécuter ce playbook, [définissez une réponse automatique](automate-responses-with-playbooks.md#set-an-automated-response) ou [exécutez-le manuellement](automate-responses-with-playbooks.md#run-a-playbook-manually-on-an-alert) (déclenchement d’alerte uniquement).

1. La plupart des modèles peuvent être utilisés tels quels, mais nous vous recommandons d’effectuer les ajustements nécessaires pour adapter le nouveau playbook aux besoins de votre centre des opérations de sécurité.

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="issue-found-a-bug-in-the-playbook"></a>Problème : Un bogue a été détecté dans le playbook

Pour signaler un bogue ou demander une amélioration pour un playbook, sélectionnez le lien **Pris en charge par** dans le volet d’informations du playbook. S’il s’agit d’un playbook pris en charge par la communauté, le lien vous permettra d’ouvrir un problème GitHub. Sinon, vous serez redirigé vers la page de l’organisme concerné.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à travailler avec des modèles de playbook et à créer et à personnaliser des playbooks en fonction de vos besoins. En savoir plus sur les playbooks et l’automatisation dans Microsoft Sentinel :

- [Orchestration, automatisation et réponse aux incidents de sécurité (SOAR) dans Microsoft Sentinel](automation-in-azure-sentinel.md)
- [Automatiser la réponse aux menaces à l’aide de règles dans Microsoft Sentinel](automate-responses-with-playbooks.md)
- [Didacticiel : utiliser des règles d’automatisation dans Microsoft Sentinel](tutorial-respond-threats-playbook.md)
- [Authentifier des playbooks auprès de Microsoft Sentinel](authenticate-playbooks-to-sentinel.md)
- [Utilisation de déclencheurs et d’actions dans les playbooks Microsoft Sentinel](playbook-triggers-actions.md)
