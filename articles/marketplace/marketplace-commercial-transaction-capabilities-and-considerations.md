---
title: Fonctionnalités de transaction de la place de marché commerciale de Microsoft
description: Cet article décrit les considérations sur les prix, la facturation et le paiement pour l’option de transaction de la place de marché commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/26/2021
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 0e7166399dac6d28a5b7e0ed4cf5f4e5cd20e97c
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059031"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Fonctionnalités de transaction de la place de marché commerciale

Cet article décrit les considérations sur les prix, la facturation et le paiement pour les offres de *transaction* vendues sur la place de marché commerciale. Pour plus d’informations sur la publication d’offres ne pouvant faire l’objet de transactions (gratuites ou BYOL), consultez[Présentation des options de référencement](determine-your-listing-type.md).

> [!TIP]
> Pour voir l’achat du client sur la place de marché commerciale, consultez la page [Achat sur la Place de marché Azure](/marketplace/azure-purchasing-invoicing). Pour les offres SaaS, consultez [Acheter des applications SaaS sur Microsoft AppSource](/marketplace/purchase-software-appsource).

## <a name="transactions-by-listing-option"></a>Transactions par option de liste

Il incombe à l’éditeur ou à Microsoft de gérer les transactions de licences logicielles pour les offres de la place de marché commerciale. L’option de liste que vous choisissez pour votre offre détermine qui gère la transaction. Pour obtenir la disponibilité de chaque option de publication et les explications correspondantes, consultez [Présentation des options de référencement](determine-your-listing-type.md).

### <a name="transact-publishing-option"></a>Option de publication Transaction

Le choix de vendre via le réseau Microsoft tire parti des fonctionnalités de transactions commerciales de Microsoft et fournit une expérience de bout en bout, de la découverte et l’évaluation à l’achat et l’implémentation. Une offre *pouvant faire l’objet d’une transaction* est une offre en lien avec laquelle Microsoft facilite l’échange d’argent contre une licence logicielle au nom de l’éditeur. Les offres de transaction sont facturées en fonction d’un abonnement Microsoft existant ou sur une carte de crédit, ce qui permet à Microsoft d’héberger des transactions de la place de marché dans le cloud pour le compte de l’éditeur.

Vous choisissez l’option de transaction au moment de la création d’une offre dans l’Espace partenaires. Cette option s’affiche seulement si la transaction est disponible pour votre type d’offre.

## <a name="transact-overview"></a>Présentation de l’option Transaction

Lorsque vous choisissez l’option Transaction, Microsoft permet la vente de logiciels tiers et le déploiement de certains types d’offres dans l’abonnement Azure du client. Pour choisir le modèle de tarification d’une offre, l’éditeur doit prendre en compte la facturation des frais d’infrastructure et ses propres frais de licence logicielle.

L’option de publication Transaction est actuellement disponible pour ces types d’offres :

| Type d’offre | Cadence de facturation | Facturation à l’usage | Modèle de tarification |
| ------------ | ------------- | ------------- | ------------- |
| Azure Application <br>(Application managée) | Mensuelle | Oui | Basés sur l’utilisation |
| Machine virtuelle Azure | Mensuellement* | Non | Basé sur l’utilisation, BYOL |
| SaaS (software as a service) | Mensuelle et annuelle | Oui | Tarification fixe, par utilisateur, basée sur l’utilisation. |
|||||

\* La machine virtuelle Azure offre une prise en charge des plans de facturation basés sur l’utilisation. Ces plans sont facturés mensuellement pour l’utilisation horaire de l’abonnement en fonction de l’utilisation par cœur, par taille de cœur, ou par marché et par taille de cœur.

### <a name="metered-billing"></a>Facturation à l’usage

Le *service de mesure de la Place de marché* vous permet de spécifier des frais de paiement à l’utilisation (basés sur la consommation) en plus des frais mensuels ou annuels inclus dans le contrat (droit d’utilisation). Vous pouvez facturer les coûts d’utilisation selon les dimensions du service de mesure de la Place de marché que vous spécifiez, telles que la bande passante, les tickets ou les e-mails traités. Pour plus d’informations sur la facturation à l’usage pour les offres SaaS, consultez [Facturation à l’usage pour SaaS à l’aide du service de mesure de la consommation de la Place de marché commerciale](./partner-center-portal/saas-metered-billing.md). Pour plus d’informations sur la facturation à l’usage pour les offres Azure Application, consultez la page [Facturation des applications managées basée sur des mesures](marketplace-metering-service-apis.md).

### <a name="billing-infrastructure-costs"></a>Facturation des coûts d’infrastructure

Pour les **machines virtuelles** et les **applications Azure**, des frais d’utilisation de l’infrastructure Azure sont facturés sur l’abonnement Azure du client. Ils sont détaillés et présentés séparément des frais de licence logicielle du fournisseur sur la facture du client.

Pour les offres **Applications SaaS**, l’éditeur doit regrouper les frais d’utilisation de l’infrastructure Azure et les frais de licence logicielle dans le même élément de coût. Ils font facturés au client selon un tarif fixe. L’utilisation de l’infrastructure Azure est présentée et facturée directement à l’éditeur. Les frais réels d’utilisation de l’infrastructure ne sont pas visibles par le client. Les éditeurs choisissent généralement d’inclure les frais d’utilisation de l’infrastructure Azure dans leurs tarifs de licence logicielle. Les frais de licence des logiciels ne sont pas mesurés ni basés sur la consommation des utilisateurs.

## <a name="pricing-models"></a>Modèles tarifaires

Selon l’option Transaction choisie, les frais d’abonnement se présentent ainsi :

- **Tarification par abonnement** : les frais de licence logicielle sont facturés mensuellement ou annuellement dans le cadre d’un abonnement selon un tarif fixe ou par poste. Les frais d’abonnement récurrents ne sont pas calculés au prorata en cas d’annulation en cours de trimestre ou en cas de services non utilisés. Les frais d’abonnement réactualisés peuvent être facturés au prorata si le client met à niveau ou rétrograde son abonnement au milieu de la durée de l’abonnement.
- **Tarification basée sur l’utilisation** : pour les offres de machines virtuelles Azure, les clients sont facturés en fonction de l’étendue de leur utilisation de l’offre. Pour les images Machines Virtuelles, les frais Place de marché Azure facturés aux clients sont basés sur l’utilisation réelle des machines virtuelles déployées à partir de ces images de machine virtuelle, selon un tarif horaire convenu par les éditeurs. Le tarif horaire peut être fixe ou variable en fonction de la taille des machines virtuelles. Les heures non terminées sont facturées à la minute. Les plans sont facturés tous les mois.
- **Tarification limitée** : pour les offres Azure Application et les offres SaaS, les éditeurs peuvent utiliser le [service de mesure de la place de marché Microsoft Azure](marketplace-metering-service-apis.md) pour facturer la consommation en fonction des dimensions de mesure personnalisées qu’ils configurent. Ces modifications s’ajoutent aux frais mensuels ou annuels inclus dans le contrat (droit d’utilisation). Exemples de dimensions de mesure personnalisée : bande passante, tickets ou e-mails. Les éditeurs peuvent définir une ou plusieurs dimensions mesurables pour chaque plan, avec un maximum de 30 par offre. Les éditeurs sont chargés d’effectuer le suivi personnel de l’utilisation pour chaque mesure définie dans l’offre. Les événements doivent être signalés à Microsoft dans l’heure qui suit leur survenance. Celui-ci facture ensuite les clients en fonction des informations collectées par les éditeurs pour la période de facturation applicable.

> [!NOTE]
> Les offres qui sont facturées en fonction de la consommation après l’utilisation d’une solution ne sont pas éligibles à un remboursement.

Les éditeurs qui souhaitent modifier les frais d’utilisation associés à une offre doivent tout d’abord supprimer l’offre (ou le plan spécifique de l’offre) de la place de marché commerciale. Cette suppression doit se faire conformément aux dispositions du [Contrat d’Éditeur Microsoft](/legal/marketplace/msft-publisher-agreement). L’éditeur peut ensuite publier une nouvelle offre (ou un plan intégré à une offre) avec les nouveaux frais d’utilisation. Pour plus d’informations sur la suppression d’une offre ou d’un plan, consultez [Arrêter la distribution d’une offre ou d’un plan](./update-existing-offer.md#stop-distribution-of-an-offer-or-plan).

### <a name="determine-offer-type-and-pricing-plan"></a>Déterminer le type d’offre et le plan de tarification

Utilisez cet organigramme afin de déterminer le type d’offre de *transaction* et le plan tarifaire pour vendre vos logiciels sur la place de marché commerciale. Ce graphique part du principe que vous, l’éditeur, avez une compréhension claire de vos modèles de distribution et de gestion des licences.

En règle générale, les offres SaaS conviennent parfaitement si vos clients veulent simplement s’abonner à votre service et l’utiliser en ligne. Avec SaaS, vous (l’éditeur) hébergez le logiciel dans votre infrastructure cloud et gérez l’infrastructure technique, les logiciels d’application et les responsabilités de gestion des données.

Les offres de machines virtuelles et Azure Application sont adaptées si vous souhaitez que les clients déploient, gèrent et exécutent votre application ou service empaqueté (comme une image de machine virtuelle et/ou d’autres services Azure dans le modèle ARM) dans leur propre infrastructure cloud.

[![Affiche un organigramme pour déterminer le type d’offre et le plan tarifaire.](media/commercial-marketplace-plans/offer-type-and-pricing-plan-flowchart.png)](media/commercial-marketplace-plans/offer-type-and-pricing-plan-flowchart.png#lightbox)
&nbsp;&nbsp;&nbsp;<sup>(1)</sup> Contactez [Microsoft Office](https://microsoftcloudpartner.eventbuilder.com/MarketplaceDeveloperOfficeHours) ou le [support](./support.md).<br>
&nbsp;&nbsp;&nbsp;<sup>(2)</sup> Les images des offres de machines virtuelles peuvent être incluses dans l’offre Azure App pour plus de flexibilité tarifaire.<br>
&nbsp;&nbsp;&nbsp;<sup>(3)</sup> Les clients s’acquittent des coûts liés à l’infrastructure, car les services Azure sont déployés sur le locataire client pour les offres de machines virtuelles et Azure App.

### <a name="usage-based-and-subscription-pricing"></a>Tarification basée sur l’utilisation et les abonnements

Quand vous publiez une offre basée sur l’utilisateur ou une transaction d’abonnement, Microsoft fournit la technologie et les services nécessaires pour traiter les achats, retours et rétrofacturations des licences logicielles. Dans ce scénario, l’éditeur autorise Microsoft à agir en tant qu’agent. L’éditeur permet à Microsoft de faciliter la transaction de gestion des licences logicielles. L’éditeur conserve toutefois la désignation en tant que vendeur, fournisseur, distributeur et concédant de licence.

Microsoft permet aux clients de commander, d’acquérir sous licence et d’utiliser votre logiciel selon les conditions générales de la place de marché commerciale Microsoft et de votre contrat de licence utilisateur final. Vous êtes tenu de fournir votre contrat de licence utilisateur final ou de sélectionner le [contrat Standard](./standard-contract.md) lors de la création de l’offre.

### <a name="free-software-trials"></a>Essai logiciel gratuit

Dans les scénarios de publication d’offres Transaction, vous pouvez proposer gratuitement une licence logicielle pendant une période de 30 ou 120 jours selon l’abonnement. Les clients seront facturés pour l’utilisation de l’infrastructure Azure applicable.

### <a name="examples-of-pricing-and-store-fees"></a>Exemples de tarifs et de frais de stockage

**Basés sur l’utilisation**

La tarification basée sur l’utilisation présente la structure de coûts suivante :

| **Coût de votre licence** | **1 $/heure** |
|---------|---------|
| Coût d’utilisation Azure (D1/1 cœur) | 0,14 $/heure |
| *Microsoft facture au client le montant suivant :* | *1,14 $/heure* |
||

Dans ce scénario, Microsoft facture 1,14 $ l’heure d’utilisation de votre image de machine virtuelle publiée.

| **Microsoft facture** | **1,14 $/heure**  |
|---------|---------|
| Microsoft vous verse 97 % des revenus générés par les licences | 0,97 $/heure |
| Microsoft conserve 3 % des revenus générés par les licences  |  0,03 $/heure |
| Microsoft conserve 100 % des revenus générés par l’utilisation d’Azure | 0,14 $/heure |
||

**BYOL (apportez votre propre licence)**

L’option BYOL a la structure de coûts suivante :

| **Coût de votre licence** | **Frais de licence négociés et facturés par vos soins** |
|---------|---------|
|Coût d’utilisation Azure (D1/1 cœur)    |   0,14 $/heure     |
| *Microsoft facture au client le montant suivant :* | *0,14 $/heure* |
||

Dans ce scénario, Microsoft facture 0,14 $ l’heure d’utilisation de votre image de machine virtuelle publiée.

| **Microsoft facture** | **0,14 $/heure** |
|---------|---------|
| Microsoft conserve les revenus générés par l’utilisation Azure | 0,14 $/heure |
| Microsoft conserve 0 % des revenus générés par les licences | 0 $/heure |
||

**Abonnement Application SaaS**

Les abonnements SaaS peuvent être tarifés selon une tarification fixe ou par utilisateur sur une base mensuelle ou annuelle. Si vous activez l’option **Vendre via Microsoft** pour une offre SaaS, vous obtenez la structure de coûts suivante :

| **Coût de votre licence** | **100 $/mois** |
|--------------|---------|
| Coût d’utilisation Azure (D1/1 cœur) | Facturé directement à l’éditeur au lieu du client |
| *Microsoft facture au client le montant suivant :* | *100 $/mois (l’éditeur doit inclure les coûts d’infrastructure encourus ou transmis dans les frais de licence)* |
||

Dans ce scénario, Microsoft facture 100 $ pour votre licence logicielle et verse 97 $.

| **Microsoft facture** | **100 $/mois** |
|---------|---------|
| Microsoft vous verse 97 % des revenus générés par les licences  | 97 $/mois |
| Microsoft conserve 3 % des revenus générés par les licences | 3 $/mois |

### <a name="commercial-marketplace-service-fees"></a>Frais de service de la Place de marché commerciale

Nous facturons un tarif de 3 % pour le service de magasin standard quand les clients achètent votre offre à partir de la Place de marché commerciale.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Tarification, paiement, facturation et collecte côté client

**Facturation et paiement** : vous pouvez appliquer la méthode de facturation par défaut du client pour présenter les frais de licence logicielle avec abonnement ou [paiement à l’utilisation](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

**Contrat Entreprise** : si la méthode de facturation par défaut du client est un Contrat Entreprise Microsoft, les frais de licence logicielle sont facturés selon cette méthode et présentés de manière détaillée et séparée des coûts propres à l’utilisation d’Azure.

**Cartes de crédit et facture mensuelle** : les clients peuvent payer à l’aide d’une carte de crédit et d’une facture mensuelle. Dans ce cas, vos frais de licence logicielle sont facturés comme dans le scénario du Contrat Entreprise, c’est-à-dire de manière détaillée et séparée des coûts de l’utilisation d’Azure.

**Crédits gratuits et engagement financier** : certains clients choisissent de prépayer l’utilisation d’Azure avec un engagement financier dans le Contrat Entreprise, ou ont obtenu des crédits gratuits à utiliser avec Azure. Ces crédits peuvent servir à payer l’utilisation d’Azure, mais pas les frais de licence logicielle de l’éditeur.

**Facturation et collections** : les licences logicielles des éditeurs sont facturées avec la méthode de facturation choisie par le client et selon le calendrier de facturation établi. Les clients sans Contrat Entreprise sont facturés au mois pour les licences logicielles de la Place de marché. Les clients ayant un Contrat Entreprise sont facturés au mois, mais reçoivent une seule facture par trimestre.

Quand les modèles tarifaires Abonnement ou Paiement à l'utilisation sont sélectionnés, Microsoft agit en tant qu’agent de l’éditeur et est responsable de tous les aspects de la facturation, du paiement et de la collecte.

### <a name="publisher-payout-and-reporting"></a>Paiement de l’éditeur et rapports

Les frais de licence logicielle collectés par Microsoft en tant qu’agent sont soumis à des frais de service de 3 %, sauf indication contraire, et sont déduits au [moment du paiement à l’éditeur](/partner-center/payout-policy-details).

Les clients achètent généralement avec un Contrat Entreprise, ou par carte de crédit dans le cadre d’un contrat de paiement à l’utilisation. Le type de contrat détermine la tarification, la facturation, la collecte et le calendrier de paiement.

>[!NOTE]
>Tous les rapports et insights associés à l’option de publication Transaction sont disponibles dans la section Analytiques de l’Espace partenaires.

#### <a name="billing-questions-and-support"></a>Questions et support sur la facturation

Pour obtenir plus d’informations et connaître les règles juridiques, consultez le [Contrat d’éditeur Microsoft](/legal/marketplace/msft-publisher-agreement). Pour obtenir de l’aide sur les questions de facturation, contactez le [support de l’éditeur de la place de marché commerciale](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="transact-requirements"></a>Exigences relatives aux offres Transaction

Cette section décrit la configuration requise pour les différents types d’offres.

### <a name="requirements-for-all-offer-types"></a>Exigences applicables à tous les types d’offres

- Un compte Microsoft et des informations financières sont requis pour l’offre de publication Transaction, quel que soit son modèle de tarification.
- Les informations financières obligatoires comprennent un compte de paiement et un profil fiscal.

Pour plus d’informations sur la configuration de ces comptes, consultez [Gestion de votre compte de la place de marché commerciale dans l’Espace partenaires](manage-account.md).

### <a name="requirements-for-specific-offer-types"></a>Exigences applicables à certains types d’offres

La possibilité d’effectuer des transactions par le biais de Microsoft est disponible uniquement pour les types d’offres de la place de marché commerciale. Cette liste indique les conditions requises pour que ces types d’offre puissent faire l’objet d’une transaction dans la place de marché commerciale.

- **L’application Azure (modèle de solution et plans d’application managée)**  : dans certains cas, les frais d’utilisation de l’infrastructure Azure sont dissociés des frais de licence logicielle, mais ils sont présentés au client sur la même facture. Toutefois, si vous configurez un plan d’application managée pour les frais d’infrastructure ISV, les ressources Azure sont facturées à l’éditeur et le client se voit présenter un tarif fixe incluant le coût de l’infrastructure, des licences logicielles et des services de gestion.

- **Machine virtuelle Azure** : sélectionner entre les modèles de facturation gratuits, BYOL ou basés sur l’utilisation. Sur la facture Azure du client, Microsoft présente les frais de licence logicielle de l’éditeur séparément des frais d’infrastructure Azure sous-jacents. Les frais d’utilisation de l’infrastructure Azure sont occasionnés par l’utilisation du logiciel de l’éditeur.

- **Application SaaS** : il doit s’agir d’une solution multilocataire, qui utilise [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) pour l’authentification et s’intègre aux [API d’approvisionnement SaaS](partner-center-portal/pc-saas-fulfillment-apis.md). L’utilisation de l’infrastructure Azure est gérée et vous est facturée directement (en tant qu’éditeur). Vous devez donc considérer ces frais et les frais de licence logicielle comme un seul poste de coût. Pour plus d’informations, consultez [Comment planifier une offre SaaS pour la place de marché commerciale](plan-saas-offer.md#plans).

## <a name="private-plans"></a>Plans privés

Vous pouvez créer un plan privé pour une offre, compléter avec une tarification négociée et spécifique à l’offre ou des configurations personnalisées.

Les plans privés vous permettent de fournir des tarifs supérieurs ou inférieurs à des clients spécifiques par rapport au plan publiquement disponible. Les plans privés permettent d’appliquer une remise ou d’ajouter un supplément à une offre. Ils peuvent être proposés à un ou plusieurs clients en mentionnant leur abonnement Azure au niveau de l’offre.

## <a name="next-steps"></a>Étapes suivantes

- Pour connaître les options d’offre et de tarification par magasin en ligne, consultez [Présentation des options de référencement](determine-your-listing-type.md#listing-and-pricing-options-by-online-store).
- [Guide de publication par type d’offre](publisher-guide-by-offer-type.md).
- [Plans et tarification pour les offres de la place de marché commerciale](./plans-pricing.md)