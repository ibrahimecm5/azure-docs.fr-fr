---
title: Réduire les coûts d’Azure App Service avec une capacité réservée
description: Découvrez comment réduire les coûts des instances réservées Azure App Service Premium v3 et Isolé v2 et des frais de timbre Isolé.
author: bandersmsft
ms.reviewer: sapnakeshari
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 10/27/2021
ms.author: banders
ms.custom: references_regions
ms.openlocfilehash: 2566b72723b4f7254e2509a331ed554f93ad4bc3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131457883"
---
# <a name="save-costs-with-azure-app-service-reserved-instances"></a>Réaliser des économies avec les instances réservées Azure App Service

Cet article explique comment réaliser des économies avec des instances réservées Azure App Service Premium v3 et Isolé v2, ainsi que des frais de timbre Isolé.

## <a name="save-with-premium-v3-reserved-instances"></a>Réaliser des économies avec les instances réservées Premium v3

Vous pouvez réaliser des économies en optant pour une instance réservée Azure App Service Premium v3. La remise de réservation est appliquée automatiquement au nombre d’instances en cours d’exécution qui correspondent à l’étendue et aux attributs de la réservation. Vous n’avez pas besoin d’attribuer une réservation à une instance pour obtenir les remises.

## <a name="determine-the-right-reserved-instance-size-before-you-buy"></a>Déterminer la taille de l’instance réservée appropriée avant l’achat

Avant d’acheter une réservation, vous devez déterminer la taille de l’instance réservée Premium v3 dont vous avez besoin. Les sections suivantes vous aideront à déterminer la taille de l’instance réservée Premium v3 appropriée.

### <a name="use-reservation-recommendations"></a>Utiliser les suggestions de réservation

Vous pouvez utiliser les suggestions de réservation pour déterminer les réservations à acheter.

- Les suggestions d’achat et les quantités recommandées s’affichent lorsque vous achetez une instance réservée Premium v3 dans le portail Azure.
- Azure Advisor fournit des suggestions d’achat pour les divers abonnements.
- Vous pouvez utiliser les API pour obtenir des suggestions d’achat tant pour l’étendue partagée que pour l’étendue d’un abonnement unique. Pour plus d’informations, voir [API de suggestion d’achat d’instance réservée pour les entreprises](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- Pour les clients Accord Entreprise (EA) et Microsoft Customer Agreement (MCA), des suggestions d’achat pour des étendues d’abonnement simples et partagées sont disponibles dans le [pack de contenu Power BI Azure Consumption Insights](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="analyze-your-usage-information"></a>Analyser vos informations d’utilisation

Pour faciliter la détermination des réservations à acheter, vous devez analyser vos informations d’utilisation. Des données d’utilisation sont disponibles dans le fichier d’utilisation et les API. Combinez-les pour déterminer la réservation à acheter. Pour déterminer la quantité de réservations à acheter, identifiez des instances Premium v3 très utilisées quotidiennement.

Votre fichier d’utilisation indique vos frais par période de facturation et l’utilisation quotidienne. Pour plus d’informations sur le téléchargement de votre fichier d’utilisation, consultez [Afficher et télécharger vos informations d’utilisation et vos frais Azure](../understand/download-azure-daily-usage.md). Ensuite, en utilisant les informations du fichier d’utilisation, vous pouvez [déterminer la réservation à acheter](determine-reservation-purchase.md).

## <a name="buy-a-premium-v3-reserved-instance"></a>Acheter une instance réservée Premium v3

Vous pouvez acheter une instance réservée Premium v3 dans le [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). Payez la réservation [à l’avance ou par paiements mensuels](prepare-buy-reservation.md). Ces conditions s’appliquent à l’achat d’une instance réservée Premium v3 :

- Vous devez avoir le rôle Propriétaire pour au moins un abonnement EA ou un abonnement assorti d’un paiement à l’utilisation.
- Pour les abonnements EA, l’option **Ajouter des instances réservées** doit être activée sur le [portail EA](https://ea.azure.com/). Ou, si ce paramètre est désactivé, vous devez être administrateur EA de l’abonnement. Les clients EA directs peuvent désormais mettre à jour les paramètres des **instances réservées** dans le [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/BillingAccounts). Accédez au menu Stratégies pour modifier les paramètres.
- Pour le programme CSP (Cloud Solution Provider), seuls les agents administrateurs ou les agents commerciaux peuvent acheter les réservations.

Pour acheter une instance :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Tous les services** > **Réservations**.
3. Sélectionnez **Ajouter** pour acheter une nouvelle réservation, puis cliquez sur **Instance**.
4. Complétez les champs obligatoires. Les instances réservées Premium v3 en cours d’exécution qui correspondent aux attributs que vous avez sélectionnés peuvent bénéficier de la remise de réservation. Le nombre réel de vos instances réservées Premium v3 qui bénéficient de la remise dépend de l’étendue et de la quantité sélectionnées.

Si vous avez un Accord Entreprise, vous pouvez utiliser l’option **Ajouter** pour ajouter rapidement des instances supplémentaires. L’option n’est pas disponible pour les autres types d’abonnements.

| **Champ** | **Description** |
|---|---|
| Abonnement | Abonnement utilisé pour payer la réservation. Les coûts de la réservation sont facturés au mode de paiement défini sur l’abonnement. Le type d’abonnement doit être Accord Entreprise (numéros de l’offre : MS-AZR-0017P or MS-AZR-0148P) ou Microsoft Customer Agreement (MCA) ou un abonnement individuel avec paiement à l’utilisation (numéros de l’offre : MS-AZR-0003P ou MS-AZR-0023P). Les frais sont déduits du solde de l’engagement financier, si disponibles, ou facturés comme un dépassement. Pour un abonnement payable à l’utilisation, les frais sont facturés sur le mode de paiement par carte de crédit ou facture défini sur l’abonnement. |
| Étendue | L’étendue de la réservation peut couvrir un seul abonnement ou plusieurs abonnements (étendue partagée). Si vous sélectionnez : <ul><li>**Étendue de groupe de ressources unique** : applique la remise de réservation aux ressources correspondantes incluses dans le groupe de ressources sélectionné uniquement. </li><li>**Étendue d’abonnement unique** : applique la remise de réservation aux ressources correspondantes incluses dans l’abonnement sélectionné.</li><li>**Étendue partagée** : applique la remise de réservation aux ressources correspondantes dans les abonnements éligibles inclus dans le contexte de facturation. Pour les clients EA, le contexte de facturation est défini par l’inscription. Pour les abonnements individuels utilisant les tarifs du paiement à l’utilisation, l’étendue de facturation correspond à tous les abonnements éligibles créés par l’administrateur de compte.</li><li>**Groupe d’administration** : applique la remise de réservation à la ressource correspondante dans la liste des abonnements qui font partie du groupe d’administration et de l’étendue de facturation.</li></ul> |
| Région | Région Azure couverte par la réservation. |
| Taille des instances réservées Premium v3 | Taille des instances réservées Premium v3. |
| Terme | Une année ou trois ans. Une durée de 5 ans est également disponible uniquement pour les instances réservées Premium v3 HBv2. |
| Quantité | Nombre d’instances achetées au sein de la réservation. La quantité correspond au nombre d’instances réservées Premium v3 en cours d’exécution pouvant bénéficier de la remise. Par exemple, si vous exécutez 10 instances réservées Premium v3 Standard\_D2 dans la région USA Est, vous devez spécifier 10 comme quantité afin d’optimiser l’avantage pour toutes les instances réservées Premium v3 en cours d’exécution. |

## <a name="buy-an-isolated-v2-reserved-instance"></a>Acheter une instance réservée Isolé v2 isolée

Vous pouvez acheter une instance Réservé v2 dans le [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). Payez la réservation [à l’avance ou par paiements mensuels](prepare-buy-reservation.md). Ces conditions s’appliquent à l’achat d’une instance réservée Isolé v2 :

- Vous devez avoir le rôle Propriétaire pour au moins un abonnement EA ou un abonnement assorti d’un paiement à l’utilisation.
- Pour les abonnements EA, l’option **Ajouter des instances réservées** doit être activée sur le [portail EA](https://ea.azure.com/). Ou, si ce paramètre est désactivé, vous devez être administrateur EA de l’abonnement. Les clients EA directs peuvent désormais mettre à jour les paramètres des **instances réservées** dans le [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/BillingAccounts). Accédez au menu Stratégies pour modifier les paramètres.
- Pour le programme CSP (Cloud Solution Provider), seuls les agents administrateurs ou les agents commerciaux peuvent acheter les réservations.

Pour acheter une instance :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Tous les services** > **Réservations**.
3. Sélectionnez **Ajouter** pour acheter une nouvelle réservation, puis cliquez sur **Instance**.
4. Complétez les champs obligatoires. Les instances réservées Isolé v2 en cours d’exécution qui correspondent aux attributs que vous avez sélectionnés peuvent bénéficier de la remise de réservation. Le nombre réel de vos instances réservées Isolé v2 qui bénéficient de la remise dépend de l’étendue et de la quantité sélectionnées.

Si vous avez un Accord Entreprise, vous pouvez utiliser l’option **Ajouter** pour ajouter rapidement des instances supplémentaires. L’option n’est pas disponible pour les autres types d’abonnements.

| **Champ** | **Description** |
| --- | --- |
| Abonnement | Abonnement utilisé pour payer la réservation. Les coûts de la réservation sont facturés au mode de paiement défini sur l’abonnement. Le type d’abonnement doit être Accord Entreprise (numéros de l’offre : MS-AZR-0017P or MS-AZR-0148P) ou Microsoft Customer Agreement (MCA) ou un abonnement individuel avec paiement à l’utilisation (numéros de l’offre : MS-AZR-0003P ou MS-AZR-0023P). Les frais sont déduits du solde de l’engagement financier, si disponibles, ou facturés comme un dépassement. Pour un abonnement payable à l’utilisation, les frais sont facturés sur le mode de paiement par carte de crédit ou facture défini sur l’abonnement. |
| Étendue | L’étendue de la réservation peut couvrir un seul abonnement ou plusieurs abonnements (étendue partagée). Si vous sélectionnez :<UL><LI>**Étendue de groupe de ressources unique** : applique la remise de réservation aux ressources correspondantes incluses dans le groupe de ressources sélectionné uniquement.</li><li>**Étendue d’abonnement unique** : applique la remise de réservation aux ressources correspondantes incluses dans l’abonnement sélectionné.</li><li>**Étendue partagée** : applique la remise de réservation aux ressources correspondantes dans les abonnements éligibles inclus dans le contexte de facturation. Pour les clients EA, le contexte de facturation est défini par l’inscription. Pour les abonnements individuels utilisant les tarifs du paiement à l’utilisation, l’étendue de facturation correspond à tous les abonnements éligibles créés par l’administrateur de compte.</li><li>**Groupe d’administration** : applique la remise de réservation à la ressource correspondante dans la liste des abonnements qui font partie du groupe d’administration et de l’étendue de facturation.</li></ul> |
| Région | Région Azure couverte par la réservation. |
| Taille d’instance réservée Isolé v2 | Taille des instances réservées Isolé v2. |
| Terme | Une année ou trois ans. Il y a également un terme de 5 ans disponible uniquement pour les instances réservées HBv2 Isolé v2. |
| Quantité | Nombre d’instances achetées au sein de la réservation. La quantité correspond au nombre d’instances réservées Isolé v2 en cours d’exécution pouvant bénéficier de la remise. Par exemple, si vous exécutez 10 instances réservées Isolé v2 Standard\_D2 dans la région USA Est, vous devez spécifier 10 comme quantité afin d’optimiser l’avantage pour toutes les instances réservées Isolé v2 en cours d’exécution. |

## <a name="save-with-isolated-stamp-fees"></a>Réaliser des économies sur les frais de timbre isolé

Vous pouvez réaliser des économies sur les frais de timbre Azure App Service Isolé en optant pour une réservation de votre utilisation de timbre pour une durée de trois ans. Pour acheter une capacité réservée de frais de timbre Isolé, vous devez choisir la région Azure dans laquelle l’horodatage sera déployé et le nombre d’horodatages à acheter.

Lorsque vous achetez une réservation, l’utilisation des frais de timbre Isolé correspondant aux attributs de la réservation n’est plus facturée au tarif de paiement à l’utilisation. La réservation est appliquée automatiquement au nombre d’horodatages Isolé qui correspondent à la région et à l’étendue de la capacité réservée. Vous n’avez pas besoin d’affecter une réservation à un horodatage Isolé. La réservation ne s’applique pas aux workers, si bien que toutes les autres ressources associées à l’horodatage sont facturées séparément.

Lorsque la capacité réservée arrive à expiration, les instances Isolé continuent de s’exécuter, mais elles sont facturées au tarif de paiement à l’utilisation. Les réservations ne sont pas renouvelées automatiquement.

## <a name="determine-the-right-isolated-stamp-reservation-to-purchase"></a>Déterminer la réservation de timbres isolés appropriée à acheter

En achetant une réservation, vous vous engagez à utiliser les quantités réservées au cours des trois prochaines années. Vérifiez vos données d’utilisation pour déterminer le nombre de timbres App Service Isolé que vous utilisez régulièrement et que vous êtes susceptible d’utiliser dans le futur.

En outre, assurez-vous que vous comprenez comment l’horodatage Isolé émet un compteur Linux ou Windows.

- Par défaut, un horodatage Isolé vide émet le compteur de timbres Windows. Par exemple, sans worker déployé. Il continue à émettre le compteur lorsque les workers Windows sont déployés sur l’horodatage.
- Si vous déployez un rôle de travail Linux, le compteur prend la valeur du compteur de tampon Linux.
- Lorsque les workers Linux et Windows sont déployés, l’horodatage émet le compteur Windows.

Le compteur d’horodatage peut donc changer entre Windows et Linux pendant toute la durée de vie de l’horodatage.

Achetez des réservations d’horodatage Windows si vous avez un ou plusieurs workers Windows sur l’horodatage. Le seul cas où vous devez acheter une réservation d’horodatage Linux est si vous envisagez d’avoir _uniquement_ des workers Linux dans l’horodatage.

## <a name="buy-isolated-stamp-reserved-capacity"></a>Achetez une capacité réservée d’horodatage Isolé

Vous pouvez acheter une capacité réservée d’horodatage Isolé dans le [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D). Payez la réservation [à l’avance ou par paiements mensuels](./prepare-buy-reservation.md). Pour acheter une capacité réservée, vous devez disposer du rôle Propriétaire sur au moins un abonnement Entreprise ou d’un abonnement individuel avec des tarifs de paiement à l’utilisation.

- Pour les abonnements Entreprise, l’option **Ajouter des instances réservées** doit être activée sur le [portail EA](https://ea.azure.com/). Ou, si le paramètre est désactivé, vous devez être un administrateur EA.
- Pour le programme Fournisseur de solutions cloud (CSP), seuls des agents d’administration ou des agents commerciaux peuvent acheter une capacité de réserve Azure Synapse Analytics.

**Pour acheter :**

1. Accédez au [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D).
1. Sélectionnez un abonnement. Utilisez la liste **Abonnement** pour choisir l’abonnement à utiliser pour payer la capacité réservée. Les coûts de la capacité réservée sont facturés conformément au mode de paiement défini pour l’abonnement. Le type d’abonnement doit être Accord Entreprise (numéros de l’offre : MS-AZR-0017P ou MS-AZR-0148P) ou Paiement à l’utilisation (numéros de l’offre : MS-AZR-0003P ou MS-AZR-0023P) ou un abonnement CSP.
    - Pour un abonnement d’entreprise, les frais sont déduits du solde de Paiement anticipé Azure (anciennement « Engagement financier ») de l’inscription ou facturés comme dépassement.
    - Pour un abonnement Paiement à l’utilisation, les frais sont facturés sur le mode de paiement par carte de crédit ou par facture défini sur l’abonnement.
1. Sélectionnez une **Étendue** pour choisir une étendue d’abonnement.
    - **Étendue de groupe de ressources unique** : applique la remise de réservation aux ressources correspondantes incluses dans le groupe de ressources sélectionné uniquement.
    - **Étendue d’abonnement unique** : applique la remise de réservation aux ressources correspondantes incluses dans l’abonnement sélectionné.
    - **Étendue partagée** : applique la remise de réservation aux ressources correspondantes dans les abonnements éligibles inclus dans le contexte de facturation. Pour les clients Contrat Entreprise, le contexte de facturation correspond à l’inscription. Pour les abonnements individuels utilisant les tarifs du paiement à l’utilisation, l’étendue de facturation correspond à tous les abonnements éligibles créés par l’administrateur de compte.
    - **Groupe d’administration** : applique la remise de réservation à la ressource correspondante dans la liste des abonnements qui font partie du groupe d’administration et de l’étendue de facturation.
1. Sélectionnez une **région** éligible pour la capacité réservée et ajoutez la réservation au panier.
1. Sélectionnez un type de plan Isolé, puis cliquez sur **Sélectionner**.  
    ![Exemple](./media/prepay-app-service/app-service-isolated-stamp-select.png)
1. Entrez le nombre d’horodatages App Service Isolé à réserver. Par exemple, une quantité de trois donne trois horodatages réservés dans une région. Cliquez sur **Suivant : Vérifier + acheter**.
1. Vérifiez, puis cliquez sur **Acheter maintenant**.

Après l’achat, accédez à [Réservations](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) pour afficher l’état de l’achat et le surveiller à tout moment.

## <a name="cancel-exchange-or-refund-reservations"></a>Annuler, échanger ou rembourser des réservations

Vous pouvez annuler, échanger ou rembourser des réservations avec certaines limitations. Pour plus d’informations, consultez [Échanges et remboursements en libre-service pour les réservations Azure](exchange-and-refund-azure-reservations.md).

## <a name="discount-application-shown-in-usage-data"></a>Application de remise dans les données d’utilisation

Vos données d’utilisation ont un prix effectif de zéro pour l’utilisation qui bénéficie d’une remise de réservation. Les données d’utilisation affichent la remise de réservation pour chaque instance d’horodatage dans chaque réservation.

Pour plus d’informations sur l’affichage de la remise de réservation dans les données d’utilisation, consultez [Obtenir les données d’utilisation et de coûts de la réservation pour les Contrats Entreprise](understand-reserved-instance-usage-ea.md) si vous êtes un client ayant conclu un Accord Entreprise (EA). Sinon, consultez [Comprendre l’utilisation d’une réservation Azure pour votre abonnement individuel avec tarifs de paiement à l’utilisation](understand-reserved-instance-usage.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les réservations Azure, consultez les articles suivants :
  - [Qu’est-ce qu’une réservation Azure ?](save-compute-costs-reservations.md)
  - [Comprendre comment une remise de réservation Azure App Service Isolé est appliquée](reservation-discount-app-service.md)
  - [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](understand-reserved-instance-usage-ea.md)
