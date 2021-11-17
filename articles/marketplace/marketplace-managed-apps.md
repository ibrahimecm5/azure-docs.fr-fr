---
title: 'Applications Azure : Guide de publication d’une offre d’application managée - Place de marché Azure'
description: Cet article décrit les conditions requises pour publier une application managée dans Place de marché Azure.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 11/11/2021
ms.openlocfilehash: 3a0ebed6767666373095b9ec33d2f20228c9d373
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289658"
---
# <a name="publishing-guide-for-azure-managed-applications"></a>Guide de publication pour les applications managées Azure

Une offre d’*application managée* Azure est un moyen de publier une application Azure dans Place de marché Azure. Les applications managées sont des offres de transaction qui sont déployées et facturées via Place de marché Azure. L’option de référencement qu’un utilisateur voit est *Obtenir maintenant*.

Cet article explique la configuration requise pour le type d’offre d’application managée.

Utilisez le type d’offre d’application managée dans les conditions suivantes :

- Vous déployez une solution basée sur un abonnement pour votre client à l’aide d’une machine virtuelle (VM) ou d’une solution basée sur une infrastructure as a service (IaaS) complète.
- Vous ou votre client exigez que la solution soit managée par un partenaire.

>[!NOTE]
>Par exemple, un partenaire peut être un intégrateur système ou un fournisseur de services managés (MSP).  

## <a name="managed-application-offer-requirements"></a>Exigences relatives à une offre d’application managée

|Spécifications |Détails  |
|---------|---------|
|Abonnement Azure | Les applications gérées doivent être déployées dans l’abonnement d’un client, mais elles peuvent être gérées par un tiers. |
|Facturation et mesure    |  Les ressources sont fournies dans l’abonnement Azure d’un client. Les machines virtuelles qui utilisent le modèle de paiement à l’utilisation font l’objet de transactions avec le client par le biais de Microsoft et sont facturées dans le cadre de l’abonnement Azure du client. <br><br> Pour les ressources Azure BYOL (apportez votre propre licence), Microsoft facture tous les frais d’infrastructure engagés dans l’abonnement client, mais vous effectuez la transaction de vos frais de licence logicielle directement avec le client.        |
|Un package d’application managée Azure    |   Modèle Azure Resource Manager configuré et Créer une définition d’interface utilisateur qui seront utilisés pour déployer votre application dans l’abonnement du client.<br><br>Pour plus d’informations sur la création d’une application managée, consultez [Vue d’ensemble des applications managées](../azure-resource-manager/managed-applications/publish-service-catalog-app.md).|

---

> [!NOTE]
> Les applications managées doivent pouvoir être déployées via Place de marché. Si la communication client est un problème, contactez les clients intéressés une fois que vous avez activé le partage des prospects.  

> [!Note]
> Un abonnement à un réseau de partenaires fournisseurs de solution cloud est maintenant disponible. Pour plus d’informations sur le marketing de votre offre via les canaux partenaires fournisseurs de solutions cloud Microsoft, consultez [Fournisseurs de solutions cloud](./cloud-solution-providers.md).

## <a name="next-steps"></a>Étapes suivantes

Si vous ne l’avez pas déjà fait, découvrez comment [développer votre activité dans le cloud avec la Place de marché Azure](https://azuremarketplace.microsoft.com/sell).

Pour vous inscrire à l’Espace partenaires et commencer à travailler dessus :

- [Connectez-vous à l’Espace partenaires](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) pour créer ou terminer votre offre.
- Pour plus d’informations, consultez [Créer une offre d’application Azure](azure-app-offer-setup.md).
