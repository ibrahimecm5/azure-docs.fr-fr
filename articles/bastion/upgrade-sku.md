---
title: Mettre à niveau un SKU
titleSuffix: Azure Bastion
description: Découvrez comment passer des niveaux de base à la référence SKU standard.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 08/30/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: f88ab798618608b8dbb0e27fb772a82a5199a889
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131080856"
---
# <a name="upgrade-a-sku"></a>Mettre à niveau un SKU

Cet article vous aide à effectuer la mise à niveau du niveau de base (SKU) vers le niveau standard. Une fois la mise à niveau effectuée, vous ne pouvez plus revenir à la référence SKU de base sans supprimer et reconfigurer Bastion. Actuellement, ce paramètre ne peut être configuré que dans le portail Azure. Pour plus d’informations sur la mise à l’échelle des hôtes, consultez [Paramètres de configuration – Références SKU](configuration-settings.md#skus). 

## <a name="configuration-steps"></a>Étapes de configuration

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com).
1. Accédez à votre hôte Bastion dans le portail Azure.
1. Sur la page **Configuration**, pour **Niveau**, sélectionnez **Standard** dans le menu déroulant.

   :::image type="content" source="./media/upgrade-sku/select-sku.png" alt-text="Capture d’écran de la liste déroulante Sélectionner le niveau avec l’option standard sélectionnée." lightbox="./media/upgrade-sku/select-sku-expand.png":::

1. Cliquez sur **Appliquer** pour appliquer les modifications.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la configuration, consultez [Paramètres de configuration](configuration-settings.md).
* Lisez les [questions fréquentes sur Bastion](bastion-faq.md).
