---
title: Notification de mise à jour automatique et d’expiration du runtime d’intégration auto-hébergé
description: En savoir plus sur la notification de mise à jour automatique et d’expiration du runtime d’intégration auto-hébergé
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 06/16/2021
ms.openlocfilehash: 5a1cf4366ffd86491cc3e3e09358e364d878cb87
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989334"
---
# <a name="self-hosted-integration-runtime-auto-update-and-expire-notification"></a>Notification de mise à jour automatique et d’expiration du runtime d’intégration auto-hébergé

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article décrit comment autoriser la mise à jour automatique du runtime d’intégration auto-hébergé vers la dernière version et comment ADF gère les versions du runtime d’intégration auto-hébergé.

## <a name="self-hosted-integration-runtime-auto-update"></a>Mise à jour automatique du runtime d'intégration auto-hébergé
En règle générale, lorsque vous installez un runtime d’intégration auto-hébergé sur votre machine locale ou sur une machine virtuelle Azure, vous disposez de deux options pour gérer la version du runtime d’intégration auto-hébergé : mise à jour automatique ou maintenance manuelle. En général, ADF publie deux nouvelles versions du runtime d’intégration auto-hébergé chaque mois, qui incluent une nouvelle fonctionnalité, un correctif de bogue ou une amélioration. Nous recommandons donc aux utilisateurs de mettre à jour vers la dernière version pour bénéficier des fonctionnalités et améliorations les plus récentes.

La méthode la plus pratique consiste à activer la mise à jour automatique lorsque vous créez ou modifiez le runtime d’intégration auto-hébergé. Le runtime d’intégration auto-hébergé sera automatiquement mis à jour vers la version la plus récente. Vous pouvez également planifier la mise à jour sur la plage horaire qui vous convient le mieux.

:::image type="content" source="media/create-self-hosted-integration-runtime/shir-auto-update.png" alt-text="Activer la mise à jour automatique":::

Vous pouvez vérifier la date et l’heure de la dernière mise à jour dans votre client du runtime d’intégration auto-hébergé.

:::image type="content" source="media/create-self-hosted-integration-runtime/shir-auto-update-2.png" alt-text="Capture d’écran de la vérification de l’heure de mise à jour":::

Vous pouvez utiliser cette [commande PowerShell](/powershell/module/az.datafactory/get-azdatafactoryv2integrationruntime?view=azps-6.1.0&preserve-view=true#example-5--get-self-hosted-integration-runtime-with-detail-status) pour récupérer la version mise à jour automatiquement. 

> [!NOTE]
> Si vous avez plusieurs nœuds de runtime d’intégration auto-hébergés, aucun temps d’arrêt n’est à déplorer lors de la mise à jour automatique. La mise à jour automatique intervient d’abord dans un nœud, tandis que les autres travaillent sur les tâches. Lorsque le premier nœud termine la mise à jour, il prend en charge les tâches restantes pour permettre la mise à jour des autres nœuds. Si vous n’avez qu’un seul runtime d’intégration auto-hébergé, cela entraîne un temps d’arrêt lors de la mise à jour automatique.

## <a name="auto-update-version-vs-latest-version"></a>Version de mise à jour automatique et version la plus récente
Pour garantir la stabilité du runtime d’intégration auto-hébergé, même si nous publions deux versions, nous n’envoyons (push) qu’une version par mois. Par conséquent, vous constaterez parfois que la version mise à jour automatiquement correspond à la version précédente de la version la plus récente. Si vous souhaitez obtenir la version la plus récente, vous pouvez accéder au [centre de téléchargement](https://www.microsoft.com/download/details.aspx?id=39717). En outre, la mise à jour automatique vers une version est managée. Vous ne pouvez pas la modifier. Si vous souhaitez effectuer une mise à niveau vers la dernière version, vous devez le faire manuellement. 

Dans le portail ADF, la page **Mise à jour automatique** du runtime d’intégration auto-hébergé affiche la version la plus récente si la version actuelle est ancienne. Lorsque votre runtime d’intégration auto-hébergé est en ligne, cette version est une version de mise à jour automatique et met automatiquement à jour votre runtime d’intégration auto-hébergé à l’heure planifiée. Toutefois, si votre runtime d’intégration auto-hébergé est hors connexion, la page affiche uniquement la version la plus récente.

Si vous avez plusieurs nœuds et si, pour diverses raisons, certains ne sont pas mis à jour automatiquement. Ensuite, ces nœuds restaurent la version qui était la même sur tous les nœuds avant la mise à jour automatique. 

## <a name="self-hosted-integration-runtime-expire-notification"></a>Notification d’expiration du runtime d’intégration auto-hébergé
Si vous souhaitez contrôler manuellement la version du runtime d’intégration auto-hébergé, vous pouvez désactiver le paramètre de mise à jour automatique et l’installer manuellement. Chaque version du runtime d’intégration auto-hébergé expire dans un délai d’un an. Le message d’expiration s’affiche dans le portail ADF et le client du runtime d’intégration auto-hébergé **90 jours** avant l’expiration.

## <a name="next-steps"></a>Étapes suivantes

- Étudiez les [concepts de runtime d’intégration dans Azure Data Factory](./concepts-integration-runtime.md).

- Apprenez à [créer un runtime d’intégration auto-hébergé sur le portail Azure](./create-self-hosted-integration-runtime.md).
