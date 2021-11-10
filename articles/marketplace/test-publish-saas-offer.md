---
title: Comment tester et publier une offre SaaS sur la place de marché commerciale de Microsoft
description: Utilisez l’Espace Partenaires pour soumettre votre offre SaaS en préversion, afficher un aperçu de votre offre, la tester, puis la publier sur la place de marché commerciale Microsoft.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 09/27/2021
ms.openlocfilehash: 21bb6df47c255da5ffc463d3d7f42e61da6de1cc
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063134"
---
# <a name="how-to-test-and-publish-a-saas-offer-to-the-commercial-marketplace"></a>Comment tester et publier une offre SaaS sur la place de marché commerciale

Cet article explique comment utiliser l’Espace partenaires afin de soumettre votre offre SaaS pour publication, afficher un aperçu de votre offre, la tester, puis la publier en direct sur la place de marché commerciale. Vous devez avoir déjà créé une offre que vous souhaitez publier.

> [!NOTE]
> Nous vous recommandons de créer une offre de test et de développement (DEV) distincte comme un moyen à faible risque de test avant de publier votre offre de production (PROD). Procédez comme suit pour créer et tester l’offre DEV avant de publier votre offre (PROD).

## <a name="submit-your-offer-for-publishing"></a>Soumettre votre offre pour publication

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[Vue des espaces de travail](#tab/workspaces-view)

1. Connectez-vous à l’[Espace partenaires](https://go.microsoft.com/fwlink/?linkid=2166002).

1. Dans la page d’accueil, sélectionnez la vignette **Offres de la Place de marché**.

    [ ![Illustre la vignette Offres de la Place de marché sur la page d’accueil de l’Espace partenaires.](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. Sur la page d’offres de la Place de marché, sélectionnez l’offre que vous souhaitez publier.
1. Dans l’angle supérieur droit du portail, sélectionnez **Vérifier et publier**.
1. Assurez-vous que la colonne **État** de chaque page indique **Complète**. Les états possibles sont les suivants :

   - **Non démarré** : la page n’est pas démarrée.
   - **Incomplète** : des informations requises sont manquantes dans la page ou des erreurs doivent être corrigées. Vous devez revenir à la page et la mettre à jour.
   - **Complète** : la page est complète. Toutes les données requises ont été fournies et il n’existe aucune erreur.

1. Si l’un des pages présente un État autre que **Complète**, sélectionnez le nom de la page, corrigez le problème, enregistrez la page, puis sélectionnez **Vérifier et publier** à nouveau pour revenir à cette page.
1. Après avoir compléter toutes les pages, dans la boîte **Notes pour la certification**, fournissez des instructions de test à l’équipe de certification pour vous assurer que votre application est correctement testée. Fournissez toutes les notes supplémentaires utiles pour comprendre votre demande.
1. Pour démarrer le processus de publication de votre offre, sélectionnez **Publier**. La page **Vue d’ensemble de l’offre** s’affiche et indique l’**État de publication** de l’offre.

L’état de publication de votre offre changera à mesure qu’il progressera dans le processus de publication. Pour plus d’informations sur ce processus, consultez [Étapes de validation et de publication](review-publish-offer.md#validation-and-publishing-steps).

#### <a name="current-view"></a>[Affichage actuel](#tab/current-view)

1. Connectez-vous au tableau de bord de la place de marché commerciale dans l’[Espace partenaires](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).
1. Sur la page **Vue d’ensemble**, sélectionnez l’offre que vous souhaitez publier.
1. Dans l’angle supérieur droit du portail, sélectionnez **Vérifier et publier**.
1. Assurez-vous que la colonne **État** de chaque page indique **Complète**. Les états possibles sont les suivants :

   - **Non démarré** : la page n’est pas démarrée.
   - **Incomplète** : des informations requises sont manquantes dans la page ou des erreurs doivent être corrigées. Vous devez revenir à la page et la mettre à jour.
   - **Complète** : la page est complète. Toutes les données requises ont été fournies et il n’existe aucune erreur.

1. Si l’un des pages présente un État autre que **Complète**, sélectionnez le nom de la page, corrigez le problème, enregistrez la page, puis sélectionnez **Vérifier et publier** à nouveau pour revenir à cette page.
1. Après avoir compléter toutes les pages, dans la boîte **Notes pour la certification**, fournissez des instructions de test à l’équipe de certification pour vous assurer que votre application est correctement testée. Fournissez toutes les notes supplémentaires utiles pour comprendre votre demande.
1. Pour démarrer le processus de publication de votre offre, sélectionnez **Publier**. La page **Vue d’ensemble de l’offre** s’affiche et indique l’**État de publication** de l’offre.

L’état de publication de votre offre changera à mesure qu’il progressera dans le processus de publication. Pour plus d’informations sur ce processus, consultez [Étapes de validation et de publication](review-publish-offer.md#validation-and-publishing-steps).

---

## <a name="preview-and-test-your-offer"></a>Afficher un aperçu de votre offre et la tester

Une fois que l’offre est prête à être approuvée, nous vous enverrons un e-mail pour vous demander de consulter et d’approuver la préversion de votre offre. Vous pouvez également actualiser la page **Vue d’ensemble de l’offre** depuis votre navigateur pour voir si votre offre a atteint la phase d’approbation de publication. Si c’est le cas, le bouton **Go Live** et les liens d’aperçu seront disponibles. Il y aura un lien soit pour l’aperçu Microsoft AppSource, soit pour l’aperçu de la Place de marché Azure, ou les deux, selon les options que vous avez choisies lors de la création de votre offre. Si vous avez choisi de vendre votre offre par le biais de Microsoft, quiconque a été ajouté au public de la préversion peut tester son acquisition et son déploiement pour vérifier qu’elle répond à vos exigences au cours de cette phase.

La capture d’écran suivante montre la page **Vue d’ensemble de l’offre** pour une offre SaaS, avec deux liens d’aperçu sous le bouton **Démarrer**. Les étapes de validation que vous verrez sur cette page varient en fonction du type d’offre et des sélections que vous avez faites lors de la création de l’offre.

[ ![Illustre l’état de publication d’une offre dans l’Espace partenaires. Le bouton Démarrer est affiché. Le lien Afficher le rapport de validation s’affiche également sous validation automatique.](./media/review-publish-offer/publish-status-saas.png) ](./media/review-publish-offer/publish-status-saas.png#lightbox)

Pour afficher un aperçu de votre offre, procédez comme suit.

1. Sur la page **Vue d’ensemble de l’offre**, sélectionnez un lien d’aperçu sous le bouton **Démarrer**.

1. Pour valider le flux d’achat et d’installation de bout en bout, achetez les plans dans votre offre en préversion. Commencez par informer Microsoft avec un [ticket de support](https://aka.ms/marketplacesupport) pour vous assurer que nous ne vous imputions pas de frais.

1. Si votre offre SaaS prend en charge la [facturation contrôlée à l’aide du service de contrôle de la place de marché commerciale](./partner-center-portal/saas-metered-billing.md), examinez et suivez les meilleures pratiques de test détaillées dans les [API de facturation contrôlée de la place de marché](marketplace-metering-service-apis.md#development-and-testing-best-practices).

1. Passez en revue et suivez les instructions de test dans [API de traitement SaaS version 2 dans la place de marché commerciale de Microsoft](./partner-center-portal/pc-saas-fulfillment-webhook.md#development-and-testing) pour vous assurer que votre offre est correctement intégrée avec les API avant de la publier en ligne.

1. Si l’étape de validation de l’offre a généré des avertissements, un lien **Afficher le rapport de validation** s’affiche sur la page **Vue d’ensemble de l’offre**. Veillez à consulter le rapport et à résoudre les problèmes avant de cliquer sur le bouton **Démarrer**. Dans le cas contraire, la certification risque d’échouer et de retarder la mise en ligne de votre offre.

1. Si vous devez apporter des changements à l’offre après en avoir affiché l’aperçu et l’avoir testée, vous pouvez la modifier et soumettre à la publication une nouvelle préversion. Pour plus d’informations, consultez [Mettre à jour une offre existante dans la Place de marché commerciale](update-existing-offer.md).

## <a name="publish-your-offer-live"></a>Publier votre offre en ligne

Après avoir complètement testé votre préversion, sélectionnez **Démarrer** pour publier votre offre sur le place de marché commerciale. Si votre offre est déjà en ligne dans la place de marché commerciale, les mises à jour que vous effectuez ne sont pas mises en ligne tant que vous ne sélectionnez pas **Démarrer**.

> [!IMPORTANT]
> Ne sélectionnez jamais **En ligne** pour une [offre de développement/test](create-saas-dev-test-offer.md).

Maintenant que vous avez choisi de rendre votre offre disponible dans la place de marché commerciale, nous effectuons une série de contrôles de validation finaux pour nous assurer que l’offre en ligne est configurée exactement comme sa préversion. Pour plus d’informations sur ces contrôles de validation, consultez [Phase de publication](review-publish-offer.md#publish-phase).

Une fois ces vérifications terminées, votre offre est publiée sur la place de marché.

## <a name="next-steps"></a>Étapes suivantes

- [Accéder aux rapports analytiques de la Place de marché commerciale dans l’Espace partenaires](analytics.md)
