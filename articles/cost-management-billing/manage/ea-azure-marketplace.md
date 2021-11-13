---
title: Place de marché Azure
description: Décrit comment les clients EA peuvent utiliser la Place de marché Azure
author: bandersmsft
ms.reviewer: sapnakeshari
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: banders
ms.openlocfilehash: f22dd59be380a7535d8bdc724d48152eeec4041f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130244188"
---
# <a name="azure-marketplace"></a>Place de marché Azure

Cet article explique comment les clients et partenaires EA peuvent afficher les frais de la Place de marché et activer les achats sur la Place de marché Azure.

## <a name="azure-marketplace-for-ea-customers"></a>Place de marché Azure pour les clients EA

Pour les clients directs, les frais de la Place de marché Azure sont visibles dans le [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/BillingAccounts). Les achats et consommations de la Place de marché Azure sont facturés hors Paiement anticipé Azure, tous les trimestres ou tous les mois, et à terme échu. Consultez [Gérer la Place de marché Azure dans le portail Azure](direct-ea-administration.md#enable-azure-marketplace-purchases).

Les clients indirects peuvent consulter leurs abonnements Place de marché Azure sur la page **Gérer les abonnements** du portail Azure Enterprise, mais la tarification est masquée. Les clients doivent contacter leur fournisseur de services de concession de licences (LSP) pour obtenir des informations sur les frais de la Place de marché Azure.

Les nouveaux achats récurrents mensuels ou annuels de la Place de marché Azure sont facturés en totalité dans la période au cours de laquelle les articles de la Place de marché Azure ont été achetés. Ces articles sont renouvelés automatiquement dans la période suivante, le même jour que l’achat d’origine.

Les frais récurrents mensuels continuent d’être renouvelés le premier jour de chaque mois calendaire. Les frais annuels sont renouvelés à l’anniversaire de la date d’achat.

Certains services de revendeur tiers sur la Place de marché Azure utilisent maintenant votre solde de Paiement anticipé Azure Contrat Entreprise (EA). Ces services étaient précédemment facturés séparément, c’est-à-dire en dehors de Paiement anticipé Azure EA. Paiement anticipé Azure EA pour ces services de la Place de marché Azure permet de simplifier la gestion des achats et des paiements clients. Pour obtenir la liste complète des services qui consomment désormais Paiement anticipé Azure, consultez la [mise à jour du 6 mars 2018 sur le site web d’Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/).

### <a name="partners"></a>Partenaires

Les LSP peuvent télécharger une liste de prix spécifique à la Place de marché Azure en accédant à la grille tarifaire dans le portail Azure Enterprise. Sélectionnez le lien vers la **liste des prix de la Place de marché** dans le coin supérieur droit. La liste des prix de la Place de marché Azure répertorie tous les services disponibles et leurs prix.

Pour télécharger la liste de prix :

1. Dans le portail Azure Enterprise, sélectionnez **Rapports** > **Grille tarifaire**.
1. Dans le coin supérieur droit, recherchez le lien vers la liste des prix de la Place de marché Azure sous votre nom d’utilisateur.
1. Cliquez sur le lien avec le bouton droit et sélectionnez **Enregistrer la cible sous**.
1. Dans la fenêtre d’**enregistrement**, remplacez le titre du document par `AzureMarketplacePricelist.zip`, ce qui remplacera le fichier .xlsx par un fichier .zip.
1. Une fois le téléchargement terminé, vous disposez d’un fichier zip avec des listes de prix spécifiques des pays.
1. Les LSP doivent se référer au fichier de pays pour la tarification spécifique du pays. Les LSP peuvent utiliser l'onglet **Notifications** pour connaître les références (SKU) nouvelles ou retirées.
1. Les changements de prix sont peu fréquents. Les LSP sont informés par e-mail des augmentations de prix et des modifications des taux de change 30 jours à l'avance.
1. Les LSP reçoivent une facture par inscription, par ISV et par trimestre.

### <a name="enabling-azure-marketplace-purchases"></a>Activation des achats de la Place de marché Azure

Les administrateurs d’entreprise peuvent désactiver ou activer des achats de la Place de marché Azure pour tous les abonnements Azure dans le cadre de cette inscription. Si l’administrateur d’entreprise désactive les achats et que des abonnements Azure incluent déjà des abonnements de Place de marché Azure, ceux-ci ne sont pas annulés ni affectés.

Bien que les clients puissent convertir leurs abonnements Azure directs en Azure EA en les associant à leur inscription dans le portail Azure Enterprise, cette action ne convertit pas automatiquement les abonnements enfants.

Pour activer les achats sur la Place de marché Azure dans le portail Azure Enterprise :

1. Connectez-vous au portail Azure Enterprise en tant qu’administrateur d’entreprise.
1. Accédez à **Gérer**.
1. Sous **Détails de l’inscription**, sélectionnez l’icône du crayon en regard de la ligne **Place de marché Azure**.
1. Basculez sur **Activé/désactivé** ou sur **BYOL SKUs Only** (Références SKU BYOL uniquement) en fonction des besoins.
1. Sélectionnez **Enregistrer**.

Le client direct peut activer les achats sur la Place de marché Azure dans le portail Azure :

1. Connectez-vous au portail Azure.
1. Accédez à **Cost Management + facturation**.
1. Dans le menu de gauche, sélectionnez Étendues de facturation, puis sélectionnez une étendue de compte de facturation.
1. Dans le menu de gauche, sélectionnez **Stratégies**.
1. Sous Place de marché Azure, définissez la stratégie sur **Activer**.
1. Sélectionnez **Enregistrer**.

> [!NOTE]
> BYOL (apportez votre propre licence) et l’option gratuite uniquement limitent l’achat et l’acquisition de références SKU de la Place de marché Azure aux références SKU BYOL et gratuites uniquement.

### <a name="services-billed-hourly-for-azure-ea"></a>Services facturés à l’heure pour Azure EA

Les services suivants sont facturés à l'heure dans le cadre d'un Contrat Entreprise par rapport au tarif mensuel dans MOSP :

- Application Delivery Network (ADN)
- Pare-feu d’applications web

### <a name="azure-remoteapp"></a>Azure RemoteApp

Si vous disposez d’un Contrat Entreprise, vous payez Azure RemoteApp en fonction du niveau de prix de ce Contrat Entreprise. Il n'y a pas de frais supplémentaires. Le prix standard inclut une durée initiale de 40 heures. Le niveau de prix illimité couvre une durée initiale de 80 heures. L’application distante cesse d’émettre de l’utilisation au-delà des 80 heures.

## <a name="next-steps"></a>Étapes suivantes

- Obtenez davantage d’informations sur la [tarification](ea-pricing-overview.md).
- Consultez les [questions fréquentes (FAQ) sur Cost Management + Billing](../cost-management-billing-faq.yml) pour voir une liste de questions et réponses sur les services de la Place de Marché Azure et le paiement anticipé Azure EA.
