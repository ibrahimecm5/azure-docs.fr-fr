---
title: Gérer les abonnements
description: Les abonnements sont constitués d’appareils dédiés gérés et peuvent être intégrés ou annulés en fonction des besoins.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: dbe749f5189b568e9dee288c484f680cb2a4f426
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339760"
---
# <a name="manage-defender-for-iot-subscriptions"></a>Gérer les abonnements Defender pour IoT

## <a name="about-subscriptions"></a>À propos des abonnements

Votre déploiement Defender pour IoT est géré par le biais de votre abonnement Microsoft Defender pour les abonnements de compte IoT. Vous pouvez intégrer, modifier et annuler vos abonnements Defender pour IoT à partir du [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

Pour chaque abonnement, vous êtes invité à définir un certain nombre d’*appareils validés*. Les appareils validés représentent le nombre approximatif d’appareils qui seront analysés dans votre entreprise. 

### <a name="subscription-billing"></a>Facturation de l’abonnement

Vous êtes facturé en fonction du nombre d’appareils validés associés à chaque abonnement.

Le cycle de facturation de Microsoft Defender pour IoT suit un mois civil. Les modifications que vous apportez aux appareils validés pendant le mois sont mises en œuvre une heure après la confirmation de la mise à jour et sont reflétées dans votre facture mensuelle. L’*annulation* de l’abonnement prend elle aussi effet une heure après confirmation de l’annulation.

Votre entreprise peut avoir plusieurs entités de paiement. Si c’est le cas, vous pouvez intégrer plus d’un abonnement.

Avant de vous abonner, vous devez avoir une idée du nombre d’appareils que vous souhaitez que vos abonnements couvrent.

Les utilisateurs peuvent également travailler avec un abonnement d’évaluation, qui permet la surveillance d’un nombre limité d’appareils pendant 30 jours. Consultez les informations de [tarification de Microsoft Defender pour le cloud](https://azure.microsoft.com/pricing/details/azure-defender/) sur les appareils engagés.

## <a name="requirements"></a>Configuration requise

Avant d’intégrer un abonnement, vérifiez les éléments suivants :

- Votre compte Azure est configuré.
- Vous disposez des autorisations d’utilisateur Azure requises.

### <a name="azure-account-subscription-requirements"></a>Exigences en matière d’abonnement et de compte Azure

Pour commencer à utiliser Microsoft Defender pour IoT, vous devez disposer d’un abonnement Microsoft Azure.

Si vous n’avez pas d’abonnement, vous pouvez vous inscrire pour avoir un compte gratuit. Pour plus d’informations, consultez https://azure.microsoft.com/free/.

Si vous avez déjà accès à un abonnement Azure, mais qu’il ne figure pas dans la liste lorsque vous vous abonnez à Defender pour IoT, vérifiez les détails de votre compte et confirmez vos autorisations avec le propriétaire de l’abonnement. Consultez https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade.

### <a name="user-permission-requirements"></a>Spécifications relatives aux autorisations utilisateur

Les **détenteurs d’abonnements** et les **contributeurs aux abonnements** Azure peuvent intégrer, mettre à jour et annuler les abonnements Microsoft Defender pour IoT.

## <a name="onboard-a-trial-subscription"></a>Intégrer un abonnement d’évaluation

Si vous souhaitez évaluer Defender pour IoT, vous pouvez utiliser un abonnement d’évaluation. La version d’évaluation est valable pendant 30 jours et prend en charge 1000 appareils validés. L’utilisation de la version d’évaluation vous permet de déployer plusieurs capteurs Defender pour IoT sur votre réseau. Utilisez les capteurs pour surveiller le trafic, analyser des données, générer des alertes, en savoir plus sur les risques et les vulnérabilités du réseau, et bien plus encore. La version d’évaluation vous permet également de télécharger une console de gestion locale pour afficher les informations agrégées générées par les capteurs.

Cette section décrit comment créer un abonnement d’essai pour un capteur.

**Pour créer un abonnement d’essai :**

1. Accédez à [Defender pour IoT : Prise en main](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) dans le portail Azure.
1. Sélectionnez **Intégrer un abonnement**.
1. Dans la page Tarifs, sélectionnez **Commencer par un essai gratuit**.
1. Sélectionnez un abonnement dans le volet abonnement d’essai intégré, puis sélectionnez **Évaluer**.
1. Confirmez votre évaluation.
1. Intégrez ou configurez un capteur, si nécessaire.

## <a name="onboard-a-subscription"></a>Intégrer un abonnement

Cette section décrit la procédure d’intégration d’un abonnement.

**Pour intégrer un abonnement :**

1. Accédez à [Defender pour IoT : Prise en main](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) dans le portail Azure.
1. Sélectionnez **Intégrer un abonnement**.
1. Dans la page Tarifs, sélectionnez **S’abonner**.
1. Dans la fenêtre **Intégrer un abonnement**, sélectionnez un abonnement et le nombre d’appareils validés dans le menu déroulant.

   :::image type="content" source="media/how-to-manage-subscriptions/onboard-subscription.png" alt-text="Sélectionnez votre abonnement et le nombre d’appareils validés.":::

1. Sélectionnez **S’abonner**.
1. Confirmez votre abonnement.
1. Si vous ne l’avez pas déjà fait, intégrez ou configurez un capteur.

## <a name="update-committed-devices-in-a-subscription"></a>Mettez à jour les appareils validés dans un abonnement

Vous devrez peut-être mettre à jour votre abonnement avec davantage d’appareils validés moins d’appareils validés. Plus d’appareils peuvent nécessiter une surveillance par exemple si vous améliorez la couverture d’un site existant, que vous avez découvert plus d’appareils que prévu ou s’il y a des modifications réseau comme l’ajout de commutateurs.

**Pour mettre à jour un abonnement :**
1. Accédez à [Defender pour IoT : Prise en main](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) dans le portail Azure.
1. Sélectionnez **Intégrer un abonnement**.
1. Sélectionnez l’abonnement, puis sélectionnez les trois points. (...).
1. Sélectionnez **Modifier**.
1. Mettez à jour l’appareil validé, puis sélectionnez **Enregistrer**.
2. Dans la boîte de dialogue de confirmation qui s’affiche, sélectionnez **Confirmer**.

Les modifications apportées à la validation des appareils prennent effet une heure après confirmation de la modification. La facturation de ces modifications sera reflétée au début du mois suivant la confirmation de la modification.

Vous devrez charger un nouveau fichier d’activation dans votre console de gestion locale. Le fichier d’activation reflète le nouveau nombre d’appareils validés. Consultez [Charger un fichier d’activation](how-to-manage-the-on-premises-management-console.md#upload-an-activation-file).
## <a name="offboard-a-subscription"></a>Retirer un abonnement

Vous devrez peut-être annuler un abonnement, par exemple si vous avez besoin d’utiliser une nouvelle entité de paiement. L’annulation de l’abonnement prend effet une heure après confirmation de l’annulation. Votre facture mensuelle à venir reflétera ce changement.

Supprimez tous les capteurs associés à l’abonnement avant de retirer l’abonnement. Pour plus d’informations sur la suppression d’un capteur, consultez [Supprimer un capteur](how-to-manage-sensors-on-the-cloud.md#delete-a-sensor).

**Pour retirer un abonnement :**

1. Accédez à [Defender pour IoT : Prise en main](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) dans le portail Azure.
1. Sélectionnez l’abonnement, puis sélectionnez les trois points. (...).

1. Sélectionnez **Retirer un abonnement**.

1. Dans la fenêtre contextuelle de confirmation, activez la case à cocher pour confirmer que vous avez supprimé tous les capteurs associés à l’abonnement.

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/offboard-popup.png" alt-text="Activez la case à cocher et sélectionnez Annuler pour annuler votre capteur.":::

1. Sélectionnez **Annuler l’intégration**.

## <a name="apply-a-new-subscription"></a>Appliquer un nouvel abonnement

Les considérations commerciales peuvent nécessiter l’application d’un autre abonnement à votre déploiement que celui en cours d’utilisation. Si vous modifiez l’abonnement, vous devez charger un nouveau fichier d’activation du capteur. Le fichier contient des informations sur les dates d’expiration des abonnements.

**Pour appliquer un nouvel abonnement :**

1. Supprimez l’abonnement en cours d’utilisation.
1. Sélectionnez un nouvel abonnement.
1. Téléchargez un fichier d’activation pour le capteur associé à l’abonnement.
1. Chargez le fichier d’activation sur le capteur.

## <a name="next-steps"></a>Étapes suivantes

- [Gérer les capteurs avec Defender pour IoT dans le portail Azure](how-to-manage-sensors-on-the-cloud.md)

- [Activer et configurer votre console de gestion locale](how-to-activate-and-set-up-your-on-premises-management-console.md)
