---
title: Planifier une offre de machine virtuelle – Place de marché commerciale de Microsoft
description: Cet article décrit les conditions requises pour publier une offre de machine virtuelle sur la Place de marché Azure.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/26/2021
ms.openlocfilehash: 2f8aa4612d386995f39fccceebdaee67e4afe9dd
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848341"
---
# <a name="plan-a-virtual-machine-offer"></a>Planifier une offre de machine virtuelle

Cet article explique les différentes options et conditions requises pour la publication d’une offre de machine virtuelle sur la Place de marché commerciale. Les offres de machines virtuelles sont des offres pouvant faire l’objet d’une transaction, déployées et facturées via la Place de marché Azure.

Avant de commencer, vous devez [créer un compte sur la Place de marché commerciale dans l’Espace partenaires](create-account.md), et vérifier que votre compte est inscrit dans le programme de la Place de marché commerciale.

> [!TIP]
> Pour voir l’achat du client sur la place de marché commerciale, consultez la page [Achat sur la Place de marché Azure](/marketplace/azure-purchasing-invoicing).

### <a name="technical-fundamentals"></a>Notions de base techniques

Le processus de conception, de création et de test des offres prend du temps et nécessite une expertise en ce qui concerne la plateforme Azure et les technologies utilisées pour créer votre offre. Votre équipe d’ingénierie doit disposer d’une connaissance pratique des [machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/), du service [Stockage Azure](https://azure.microsoft.com/services/?filter=storage#storage) et des services de [Mise en réseau Azure](https://azure.microsoft.com/services/?filter=networking#networking), ainsi que de compétences en matière de [conception et d’architecture d’applications Azure](https://azure.microsoft.com/solutions/architecture/). Consultez les ressources techniques supplémentaires suivantes :

- Tutoriels
  - [Machines virtuelles Linux](../virtual-machines/linux/tutorial-manage-vm.md)
  - [Machines virtuelles Windows](../virtual-machines/windows/tutorial-manage-vm.md)

- Exemples
  - [Exemples d’interface de ligne de commande Azure pour machines virtuelles Linux](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
  - [Azure PowerShell pour les machines virtuelles Linux](https://github.com/Azure/azure-docs-powershell-samples/tree/master/virtual-machine)
  - [Exemples d’interface de ligne de commande Azure pour machines virtuelles Windows](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
  - [Azure PowerShell pour machines virtuelles Windows](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

## <a name="technical-requirements"></a>Exigences techniques

Les exigences techniques des offres de machines virtuelles sont les suivantes :

- Vous devez préparer un disque dur virtuel (VHD) de système d’exploitation. Les disques durs virtuels de données sont facultatifs. Cette procédure est expliquée plus en détail ci-dessous.
- Le client peut annuler votre offre à tout moment.
- Vous devez créer au moins un plan pour votre offre. Votre plan est facturé en fonction de l’[option de licence](#licensing-options) que vous sélectionnez.
   > [!IMPORTANT]
   > Chaque image de machine virtuelle d’un plan doit avoir le même nombre de disques de données.

Une machine virtuelle contient deux composants :

- **Disque dur virtuel de système d’exploitation** : contient le système d’exploitation et la solution déployée avec votre offre. Le processus de préparation du disque dur virtuel est différent en fonction du type de machine virtuelle (Linux, Windows ou personnalisée).
- **Disques durs virtuels de données** (facultatifs) : stockage persistant dédié à une machine virtuelle. N’utilisez pas le disque dur virtuel du système d’exploitation (par exemple le lecteur C:) pour stocker des informations persistantes. 
    - Vous pouvez inclure jusqu’à 16 disques de données.
    - Utilisez un disque dur virtuel par disque de données, même si le disque est vide.

    > [!NOTE]
    > Quel que soit le système d’exploitation que vous utilisez, ajoutez uniquement le nombre minimal de disques de données requis par la solution. Les clients ne peuvent pas supprimer les disques qui font partie d’une image au moment du déploiement, mais ils peuvent toujours ajouter des disques pendant ou après le déploiement.

Pour obtenir des instructions détaillées sur la préparation de vos ressources techniques, consultez [Créer une machine virtuelle à l’aide d’une base approuvée](azure-vm-create-using-approved-base.md) ou [Créer une machine virtuelle à l’aide de votre propre image](azure-vm-create-using-own-image.md).

## <a name="preview-audience"></a>Public de préversion

[!INCLUDE [Test drives section](includes/preview-audience.md)]

## <a name="plans-and-pricing"></a>Plans et tarifs

Les offres de machines virtuelles nécessitent au moins un plan. Un plan définit l’étendue et les limites de la solution, ainsi que la tarification associée. Vous pouvez créer plusieurs plans pour votre offre, afin d’offrir à vos clients différentes options techniques et de licence, ainsi que des évaluations gratuites. Pour obtenir des conseils généraux sur les plans, notamment des modèles de tarification, des essais gratuits et des plans privés, consultez [Plans et tarification des offres de la Place de marché commerciale](plans-pricing.md). 

Les machines virtuelles sont entièrement commercialisables à l’aide des modèles de licence Paiement à l’utilisation ou BYOL (apportez votre propre licence). Microsoft héberge la transaction commerciale et facture votre client pour votre compte. Vous avez ainsi l’avantage utiliser la relation de paiement préférée entre votre client et Microsoft, et notamment un éventuel contrat Entreprise. Pour plus d’informations, consultez [Fonctionnalités de transaction de la Place de marché commerciale](./marketplace-commercial-transaction-capabilities-and-considerations.md).

> [!NOTE]
> Vous pouvez consacrer l’Acompte Azure (précédemment appelé engagement monétaire) associé à un Contrat Entreprise à la couverture de l’utilisation par Azure de votre machine virtuelle, mais pas à celle de vos frais de licence logicielle.

### <a name="licensing-options"></a>Options de licence

Lorsque vous vous préparez à publier une nouvelle offre, vous devez choisir une option de licence. Cela détermine les informations supplémentaires que vous devrez fournir lors de la création de l’offre dans Espace partenaires.

Voici les options de licence disponibles pour les offres de machines virtuelles :

| Option de licence | Processus de transaction |
| --- | --- |
| Essai gratuit | Offrez à vos clients une évaluation gratuite de 1, 3 ou 6 mois. |
| Version d'évaluation | Cette option permet à vos clients d’évaluer des machines virtuelles sans coût supplémentaire. Pour bénéficier de l’évaluation gratuite, un client ne doit pas nécessairement être un client Azure existant. Pour plus d’informations, consultez [Qu’est-ce qu’une version d’évaluation ?](./what-is-test-drive.md) |
| BYOL | L’option BYOL (avec apport de sa propre licence) permet à vos clients d’intégrer des licences logicielles existantes à Azure.\* |
| Basés sur l’utilisation | Également appelée paiement à l’utilisation, cette option permet à vos clients de payer par heure. |
| Démonstration interactive  | Proposez à vos clients une expérience guidée de votre solution à l’aide d’une démonstration interactive. L’avantage de celle-ci est qu’elle vous permet de proposer une expérience d’évaluation sans devoir effectuer une configuration compliquée de votre solution complexe. |
|

\* En tant qu’éditeur, vous prenez en charge tous les aspects de la transaction de licence logicielle, notamment la commande, l’exécution de celle-ci, sa mesure, sa comptabilisation et sa facturation, ainsi que son paiement et son encaissement.

L’exemple suivant montre une offre de machine virtuelle de la Place de marché Azure qui prévoit une tarification basée sur l’utilisation.

:::image type="content" source="media/vm/sample-offer-screen.png" alt-text="Exemple d’écran d’offre de machine virtuelle.":::

### <a name="private-plans"></a>Plans privés

Vous pouvez limiter la découverte et le déploiement de votre machine virtuelle à un ensemble spécifique de clients en publiant l’image et les tarifs sous forme de plan privé. Les plans privés vous permettent de créer des offres exclusives pour vos clients les plus fidèles, ainsi que de proposer des conditions et logiciels personnalisés. Les conditions personnalisées vous permettent de présenter différents scénarios, notamment des offres économiques avec des conditions et des tarifs adaptés ainsi qu’un accès en avant-première à des logiciels en version limitée. Les plans privés vous permettent d’offrir des tarifs ou produits spécifiques à un ensemble limité de clients.

Pour plus d’informations, consultez [Plans et tarification pour les offres du marketplace commercial](plans-pricing.md) et [Offres privées sur le marketplace commercial de Microsoft](private-offers.md).

## <a name="test-drive"></a>Test drive

Vous pouvez choisir d’activer une version d’évaluation pour votre machine virtuelle. Les versions d’évaluation permettent aux clients d’accéder à un environnement préconfiguré pendant un nombre d’heures fixe. Vous pouvez activer des versions d’évaluation pour n’importe quelle option de publication, mais cette fonctionnalité est assortie d’exigences supplémentaires. Pour en savoir plus sur les versions d’évaluation, consultez [Qu’est-ce qu’une version d’évaluation ?](what-is-test-drive.md). Pour plus d’informations sur la configuration de différents types de versions d’évaluation, consultez [Configuration technique de la version d’évaluation](test-drive-technical-configuration.md).

[!INCLUDE [Test drives section](includes/test-drives.md)]

## <a name="customer-leads"></a>Prospects

[!INCLUDE [Customer leads section](includes/customer-leads.md)]

## <a name="legal-contracts"></a>Contrats juridiques

[!INCLUDE [Legal contracts section](includes/legal-contracts-intro.md)]
[!INCLUDE [Legal section for Standard contracts](includes/legal-contracts-standard.md)]

## <a name="cloud-solution-providers"></a>Fournisseurs de solutions cloud

Lors de la création de votre offre dans l’Espace partenaires, vous verrez l’onglet **Revendre via des fournisseurs de solutions cloud**. Cette option permet aux partenaires qui font partie du programme Fournisseur de solutions cloud (CSP) de Microsoft de revendre votre machine virtuelle dans le cadre d’une offre groupée. Tous les plans BYOL (apportez votre propre licence) rejoignent automatiquement le programme. Vous pouvez également choisir de joindre vos plans non BYOL. Pour plus d’informations, consultez [Programme des fournisseurs de solution cloud](cloud-solution-providers.md). 

> [!NOTE]
> L’abonnement à un réseau de partenaires fournisseurs de solution cloud est maintenant disponible. Pour plus d’informations sur la commercialisation de votre offre via les canaux partenaires CSP de Microsoft, consultez [**Fournisseurs de solutions cloud**](./cloud-solution-providers.md).

## <a name="next-steps"></a>Étapes suivantes

- [Créer une offre de machine virtuelle sur la Place de marché Azure](azure-vm-create.md)
- [Crée une machine virtuelle à l’aide d’une base approuvée](azure-vm-create-using-approved-base.md) ou [Créer une machine virtuelle à l’aide de votre propre image](azure-vm-create-using-own-image.md).
- [Bonnes pratiques pour le référencement des offres](gtm-offer-listing-best-practices.md)
