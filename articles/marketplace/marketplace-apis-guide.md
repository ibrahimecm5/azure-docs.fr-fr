---
title: Aligner votre activité avec notre plateforme d’e-commerce et Place de marché Azure
description: Alignez votre activité avec notre plateforme d’e-commerce (Place de marché Azure).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: MarlEva
ms.author: maevan
ms.date: 05/13/2021
ms.openlocfilehash: ee5613a316d1a45e296ca25af759590192342c3e
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057401"
---
# <a name="align-your-business-with-our-e-commerce-platform"></a>Aligner votre activité avec notre plateforme d’e-commerce

Cet article décrit comment l’interface utilisateur (IU) du marketplace commercial et les interfaces de programmation d’applications (API) programmatiques se combinent pour prendre en charge vos processus métier. Les liens sous l’API pointent vers les interfaces spécifiques que les développeurs peuvent utiliser pour intégrer leur système CRM au marketplace commercial.

## <a name="overview-of-activities"></a>Vue d’ensemble des activités

Les activités ci-dessous ne sont pas séquentielles. L’activité que vous utilisez dépend de vos besoins métier et de vos processus de vente. Ce guide montre comment intégrer différentes API pour automatiser chaque activité.

| <center>Activité | Activités de vente des fournisseurs de logiciels | API de marketplace correspondante | IU de marketplace correspondante |
| --- | --- | --- | --- |
| <center>**1. Marketing produit**<br><img src="media/api-guide/icon-product-marketing.png" alt="The icon for product marketing"> | Créer des messages sur les produits, leur positionnement, leur promotion et leur tarification | Créer des messages sur les produits, leur positionnement, leur promotion et leur tarification<br>[API d’ingestion de partenaire](https://apidocs.microsoft.com/services/partneringestion/)<br>[API d’intégration des applications Azure](azure-app-apis.md)</ul> | Créer des messages sur les produits, leur positionnement, leur promotion et leur tarification<br>Espace partenaires (PC) → Création d’offre |
| <center>**2. Génération de la demande**<br><img src="media/api-guide/icon-demand-generation.png" alt="The icon for demand generation"> | Promotion de produit<br>Renforcement des liens avec les prospects<br>Éval., essai et POC | Promotion de produit<br>Renforcement des liens avec les prospects<br>Éval., essai et POC<br>[Connecteur CRM pour les prospects pour D365, SFDC et Marketo](partner-center-portal/commercial-marketplace-get-customer-leads.md)<br>[Connecteur de co-vente pour le CRM de Salesforce](/partner-center/connector-salesforce)<br>[Connecteur de co-vente pour le CRM de Dynamics 365](/partner-center/connector-dynamics) | Promotion de produit<br>Renforcement des liens avec les prospects<br>Éval., essai et POC<br>Place de marché Azure et AppSource<br>Insights sur le marketplace PC<br>Opportunités de co-vente PC |
| <center>**3. Négociation et création de devis**<br><img src="media/api-guide/icon-negotiation-quote-creation.png" alt="The icon for negotiation and quote creation"> | T&C<br>Tarifs<br>Approbations de remise<br>Devis final | T&C<br>Tarifs<br>Approbations de remise<br>Devis final<br>[API CPP pour machines virtuelles](cloud-partner-portal-api-overview.md)<br>[Microsoft Graph pour l’API AAD](../active-directory/reports-monitoring/concept-reporting-api.md)<br>[Famille de sept API Espace partenaires](https://apidocs.microsoft.com/services/partnercenter) | T&C<br>Tarifs<br>Approbations de remise<br>Devis final<br>PC → Plans (public ou privé) |
| <center>**4. Vente**<br><img src="media/api-guide/icon-sale.png" alt="The icon for sale"> | Signature de contrat<br>Constatation du chiffre d’affaires<br>Facturation<br>Facturation | Signature de contrat<br>Constatation du chiffre d’affaires<br>Facturation<br>Facturation<br>[API de traitement SaaS v.2](partner-center-portal/pc-saas-fulfillment-apis.md)<br>[API de création de rapports](https://partneranalytics-api.azureedge.net/partneranalytics-api/Programmatic%20Access%20to%20Commercial%20Marketplace%20Analytics%20Data_v1.pdf) | Signature de contrat<br>Constatation du chiffre d’affaires<br>Facturation<br>Facturation<br>Portail Azure/Centre d’administration<br>Récompenses du marketplace PC<br>Rapports de paiements PC<br>Analyse du marketplace PC<br>Clôture de la co-vente PC |
| <center>**5. Maintenance**<br><img src="media/api-guide/icon-maintenance.png" alt="The icon for maintenance"> | Facturation récurrente<br>Dépassements<br>Prise en charge du produit | Facturation récurrente<br>Dépassements<br>Prise en charge du produit<br>[SaaS/AMA : API Facturation](https://partneranalytics-api.azureedge.net/partneranalytics-api/Programmatic%20Access%20to%20Commercial%20Marketplace%20Analytics%20Data_v1.pdf)<br>[API de traitement SaaS v.2](partner-center-portal/pc-saas-fulfillment-apis.md)<br>[API de paiement des partenaires](https://apidocs.microsoft.com/services/partnerpayouts) <br>[github](https://github.com/microsoft/Partner-Center-Payout-APIs)<br>[API de facturation à l’usage](marketplace-metering-service-apis.md)<br>[(Client EA) API de consommation Azure](/rest/api/consumption/)<br>[(Client EA) API de liste des frais Azure](/rest/api/consumption/charges/list) | Facturation récurrente<br>Dépassements<br>Prise en charge du produit<br>Rapports de paiements PC<br>Analyse du marketplace PC |
| <center>**6. Fin de contrat**<br><img src="media/api-guide/icon-contract-end.png" alt="The icon for contract end"> | Renouveler ou<br>Terminate |Renouveler ou<br>Terminate <br>[API de traitement SaaS v.2](./partner-center-portal/pc-saas-fulfillment-apis.md)<br>AMA/machines virtuelles : renouvellement automatique | Renouveler ou<br>Terminate<br>Analyse du marketplace PC |
|

## <a name="next-steps"></a>Étapes suivantes

- Suivez les liens ci-dessus pour chaque API en fonction des besoins.