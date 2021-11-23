---
ms.openlocfilehash: 35394fc33316518dac760363d2b69fdaa0727d6a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132530005"
---

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Une ressource Communication Services active.](../../create-communication-resource.md)

## <a name="get-a-phone-number"></a>Obtenir un numéro de téléphone

Pour commencer le provisionnement de numéros, accédez à votre ressource Communication Services sur le [portail Azure](https://portal.azure.com).

:::image type="content" source="../../media/manage-phone-azure-portal-start.png" alt-text="Capture d’écran montrant la page principale d’une ressource Communication Services.":::

### <a name="search-for-available-phone-numbers"></a>Rechercher les numéros de téléphone disponibles

Accédez au panneau **Numéros de téléphone** dans le menu des ressources.

:::image type="content" source="../../media/manage-phone-azure-portal-phone-page.png" alt-text="Capture d’écran montrant la page des numéros de téléphone d’une ressource Communication Services.":::

Appuyez sur le bouton **Obtenir** pour lancer l’Assistant. L’Assistant sur le panneau **Numéros de téléphone** vous présente une série de questions qui vous aideront à choisir le numéro de téléphone qui correspond le mieux à votre scénario. 

Vous devez d’abord choisir **le pays ou la région** où vous souhaitez provisionner le numéro de téléphone. Après avoir sélectionné le pays ou la région, vous devrez sélectionner le **Cas d’usage** qui répond le mieux à vos besoins. 

:::image type="content" source="../../media/manage-phone-azure-portal-get-numbers.png" alt-text="Capture d’écran montrant la vue Obtenir des numéros de téléphone.":::

### <a name="select-your-phone-number-features"></a>Sélectionner les fonctionnalités de votre numéro de téléphone

La configuration de votre numéro de téléphone comprend deux étapes : 

1. La sélection du [type de numéro](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)
2. La sélection des [fonctionnalités de numéro](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services)

Vous avez le choix entre deux types de numéros de téléphone : **Local** et **Gratuit**. Une fois que vous avez sélectionné un type de numéro, vous pouvez choisir la fonctionnalité.

Dans notre exemple, nous avons sélectionné le type de numéro **Gratuit** avec les fonctionnalités **Passer des appels** et **Envoyer et recevoir des messages SMS**.

:::image type="content" source="../../media/manage-phone-azure-portal-select-plans.png" alt-text="Capture d’écran montrant la vue Sélectionner les fonctionnalités":::

À partir de là, cliquez sur le bouton **Suivant : Numéros** au bas de la page pour personnaliser le ou les numéros de téléphone que vous souhaitez provisionner.

### <a name="customizing-phone-numbers"></a>Personnalisation de numéros de téléphone

Dans la page **Numéros**, vous allez personnaliser le ou les numéros de téléphone que vous souhaitez provisionner.

:::image type="content" source="../../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="Capture d’écran montrant la page de sélection des numéros.":::

> [!NOTE]
> Ce guide de démarrage rapide présente le flux de personnalisation du type de numéro **Gratuit**. L’expérience peut être légèrement différente si vous avez choisi le type de numéro **Local**, mais le résultat final sera le même.

Choisissez l’**indicatif régional** dans la liste des indicatifs régionaux disponibles, entrez la quantité que vous souhaitez provisionner, puis cliquez sur **Rechercher** pour rechercher les numéros qui répondent aux spécifications sélectionnées. Les numéros de téléphone qui répondent à vos besoins s’affichent avec leur coût mensuel.

:::image type="content" source="../../media/manage-phone-azure-portal-found-numbers.png" alt-text="Capture d’écran montrant la page de sélection des numéros avec des numéros réservés.":::

> [!NOTE]
> La disponibilité dépend du type de numéro, de l’emplacement et des fonctionnalités que vous avez sélectionnés.
> Les numéros sont réservés pendant une courte durée avant que la transaction n’expire. Si la transaction expire, vous devez resélectionner les numéros.

Pour afficher le récapitulatif de l’achat et passer votre commande, cliquez sur le bouton **Suivant : Récapitulatif** situé au bas de la page.

### <a name="purchase-phone-numbers"></a>Acheter des numéros de téléphone

La page récapitulative passe en revue le type de numéro, les fonctionnalités, les numéros de téléphone et le coût mensuel total sélectionnés pour provisionner les numéros de téléphone.

> [!NOTE]
> Les tarifs indiqués sont les **frais récurrents mensuels** qui couvrent le coût de location du numéro de téléphone sélectionné pour vous. Cette vue n’inclut pas les **coûts du paiement à l’utilisation** qui sont engendrés quand vous passez ou recevez des appels. Les tarifs sont [disponibles ici](../../../concepts/pricing.md). Ces coûts dépendent du type de numéro et des destinations appelées. Par exemple, un tarif à la minute pour un appel d’un numéro régional de Seattle à un numéro régional à New York et pour un appel du même numéro à un numéro de téléphone mobile britannique peut être différent.

Enfin, cliquez sur **Passer la commande** en bas de la page pour confirmer.

:::image type="content" source="../../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="Capture d’écran montrant la page récapitulative avec le type de numéro, les fonctionnalités, les numéros de téléphone et le coût mensuel total affichés.":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Rechercher vos numéros de téléphone sur le portail Azure

Accédez à votre ressource Azure Communication Services sur le [portail Azure](https://portal.azure.com) :

:::image type="content" source="../../media/manage-phone-azure-portal-start.png" alt-text="Capture d’écran montrant la page principale d’une ressource Communication Services.":::

Sélectionnez le panneau Phone Numbers (Numéros de téléphone) dans le menu pour gérer vos numéros de téléphone.

:::image type="content" source="../../media/manage-phone-azure-portal-phones.png" alt-text="Capture d’écran montrant la page des numéros de téléphone d’une ressource Communication Services.":::

> [!NOTE]
> L’affichage des numéros provisionnés sur cette page peut prendre quelques minutes.


### <a name="update-phone-number-capabilities"></a>Mettre à jour les fonctionnalités de numéro de téléphone

Dans la page **Numéros de téléphone**, vous pouvez sélectionner un numéro de téléphone pour le configurer.

:::image type="content" source="../../media/manage-phone-azure-portal-capability-update.png" alt-text="Capture d’écran montrant la page de mise à jour des fonctionnalités":::

Sélectionnez les fonctionnalités dans les options disponibles, puis cliquez sur **Enregistrer** pour appliquer votre sélection.

### <a name="release-phone-number"></a>Libérer un numéro de téléphone

Sur la page **Numéros**, vous pouvez libérer des numéros de téléphone.

:::image type="content" source="../../media/manage-phone-azure-portal-release-number.png" alt-text="Capture d’écran montrant la page de libération de numéros de téléphone.":::

Sélectionnez le numéro de téléphone que vous souhaitez libérer, puis cliquez sur le bouton **Libérer** .