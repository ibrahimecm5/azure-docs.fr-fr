---
title: Utiliser des images du client Windows dans Azure
description: Comment utiliser les avantages de l’abonnement Visual Studio pour déployer Windows 7, Windows 8 ou Windows 10 dans Azure pour des scénarios de développement et/ou de test
author: mimckitt
ms.subservice: imaging
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: mimckitt
ms.openlocfilehash: fdb0459b796704666fedfd013571f952b8382c8f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466729"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Utilisation d’un client Windows dans Azure pour les scénarios de développement et/ou test

**S’applique à :** :heavy_check_mark : Machines virtuelles Windows 

Vous pouvez utiliser Windows 7, Windows 8 ou Windows 10 Entreprise (x64) dans Azure pour des scénarios de développement / de test à condition de disposer d’un abonnement Visual Studio (anciennement MSDN) approprié. 

Pour exécuter Windows 10 dans un environnement de production, consultez [Comment déployer Windows 10 sur Azure avec des droits d’hébergement multi-locataire](windows-desktop-multitenant-hosting-deployment.md).


## <a name="subscription-eligibility"></a>Admissibilité à un abonnement
Les abonnés Visual Studio actifs (les personnes qui ont acquis une licence d’abonnement Visual Studio) peuvent utiliser des images de client Windows à des fins de développement et de tests. Les images de client Windows peuvent être utilisées sur votre propre matériel ou sur des machines virtuelles Azure.

Certaines images de client Windows sont disponibles sur la Place de marché Azure. Les abonnés Visual Studio avec n’importe quel type d’offre peuvent également [préparer et créer](prepare-for-upload-vhd-image.md) des images Windows 7, Windows 8 ou Windows 10 64 bits, puis les [charger dans Azure](upload-generalized-managed.md).

## <a name="eligible-offers-and-client-images"></a>Offres éligibles et images de client
Le tableau suivant décrit en détail les ID d’offres admissibles pour le déploiement d’images de client Windows via la Place de marché Azure. Les images de client Windows sont uniquement visibles par les offres suivantes. 

> [!NOTE]
> Les offres d’images sont sous **Client Windows** dans la Place de marché Azure. Utilisez **Client Windows** lors de la recherche d’images clientes disponibles pour les abonnés Visual Studio. Si vous avez besoin d’acheter un abonnement Visual Studio, consultez les différentes options proposées dans [Acheter Visual Studio](https://visualstudio.microsoft.com/vs/pricing/?tab=business)

| Nom de l’offre | Numéro de l’offre | Images de client disponibles | 
|:--- |:---:|:---:|
| [Dev/Test – Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P | Windows 10 Entreprise N (x64) <br> Windows 8.1 Entreprise N (x64) <br> Windows 7 Entreprise N avec SP1 (x64) |
| [Abonnés Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P | Windows 10 Entreprise N (x64) <br> Windows 8.1 Entreprise N (x64) <br> Windows 7 Entreprise N avec SP1 (x64) |
| [Abonnés Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P | Windows 10 Entreprise N (x64) <br> Windows 8.1 Entreprise N (x64) <br> Windows 7 Entreprise N avec SP1 (x64) |
| [Abonnés Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P | Windows 10 Entreprise N (x64) <br> Windows 8.1 Entreprise N (x64) <br> Windows 7 Entreprise N avec SP1 (x64) |
| [Abonnés Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P | Windows 10 Entreprise N (x64) <br> Windows 8.1 Entreprise N (x64) <br> Windows 7 Entreprise N avec SP1 (x64) |
| [Abonnés Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P | Windows 10 Entreprise N (x64) <br> Windows 8.1 Entreprise N (x64) <br> Windows 7 Entreprise N avec SP1 (x64) |
| [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P | Windows 10 Entreprise N (x64) <br> Windows 8.1 Entreprise N (x64) <br> Windows 7 Entreprise N avec SP1 (x64) |

Pour plus d’informations, consultez [Comprendre les types d’offres Microsoft](../../cost-management-billing/costs/understand-cost-mgt-data.md#supported-microsoft-azure-offers).

## <a name="check-your-azure-subscription"></a>Vérification de votre abonnement Azure
Si vous ne connaissez pas votre ID d'offre, vous pouvez l'obtenir via le portail Azure.  
- Sur la fenêtre *Abonnements* : ![Détails de l’ID de l’offre dans le portail Azure](./media/client-images/offer-id-azure-portal.png) 
- Vous pouvez également cliquer sur **Facturation**, puis sur votre ID d’abonnement. L’ID de l’offre apparaît sur la fenêtre *Facturation*. 
- Vous pouvez également voir l’ID d’offre dans [l’onglet « Abonnements »](https://account.windowsazure.com/Subscriptions) du portail de compte Azure : ![Détails de l’ID de l’offre dans le portail de compte Azure](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez désormais déployer vos machines virtuelles à l’aide de [PowerShell](quick-create-powershell.md), de [modèles Resource Manager](ps-template.md) ou de [Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
