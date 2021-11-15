---
title: Configurer la mise à l’échelle automatique pour les applications
description: Cet article décrit comment configurer les paramètres de mise à l’échelle automatique pour vos applications à l’aide du portail Microsoft Azure ou de l’interface Azure CLI.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/03/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 67b5976e8df650ac8961cdb428338ca8147878d4
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846631"
---
# <a name="set-up-autoscale-for-applications"></a>Configurer la mise à l’échelle automatique pour les applications

**Cet article s’applique à :** ✔️ Java ✔️ C#

Cet article décrit comment configurer les paramètres de mise à l’échelle automatique pour vos applications à l’aide du portail Microsoft Azure ou de l’interface Azure CLI.

La mise à l’échelle automatique est une fonctionnalité intégrée d’Azure Spring Cloud qui permet aux applications d’atteindre des performances maximales quand la demande évolue. Azure Spring Cloud prend en charge le scale-out et le scale-in, ce qui inclut la modification du nombre d’instances d’application et de l’équilibrage de charge.
 
## <a name="prerequisites"></a>Prérequis

Pour suivre ces procédures, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
* Une instance du service Azure Spring Cloud déployée. Pour bien démarrer, suivez notre [guide de démarrage rapide sur le déploiement d’une application par le biais d’Azure CLI](./quickstart.md).
* Au moins une application déjà créée dans votre instance de ce service.

## <a name="navigate-to-the-autoscale-page-in-the-azure-portal"></a>Accéder à la page Mise à l’échelle automatique sur le portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Accédez à la page **Vue d’ensemble** d’Azure Spring Cloud.
3. Sélectionnez le groupe de ressources qui contient votre service.
4. Sélectionnez l’onglet **Applications** sous **Paramètres** dans le menu du volet de navigation de gauche.
5. Sélectionnez l’application pour laquelle vous souhaitez configurer la mise à l’échelle automatique. Dans cet exemple, sélectionnez l’application **demo**. La page **Vue d’ensemble** de l’application doit alors s’afficher.
6. Accédez à l’onglet **Monter en charge** sous **Paramètres** dans le menu du volet de navigation de gauche.
7. Sélectionnez le déploiement pour lequel vous souhaitez configurer la mise à l’échelle automatique. Vous devez voir les options de mise à l’échelle automatique présentées dans la section suivante.

![Menu de mise à l’échelle automatique](./media/spring-cloud-autoscale/autoscale-menu.png)

## <a name="set-up-autoscale-settings-for-your-application-in-the-azure-portal"></a>Configurer les paramètres de mise à l’échelle automatique pour votre application dans le portail Azure

Deux options sont disponibles pour la gestion des demandes de mise à l’échelle automatique :

* Mise à l’échelle manuelle : conserve un nombre d’instances fixe. Avec le niveau Standard, vous pouvez effectuer un scale-out allant jusqu’à 500 instances. Cette valeur change le nombre d’instances distinctes de l’application en cours d’exécution.
* Mise à l’échelle automatique personnalisée : effectue la mise à l’échelle selon n’importe quelle planification en fonction de métriques.

Dans le portail Azure, choisissez la façon dont vous souhaitez effectuer la mise à l’échelle.  L’illustration suivante montre l’option **Mise à l’échelle automatique personnalisée** et les paramètres de mode.

![Mise à l’échelle automatique personnalisée](./media/spring-cloud-autoscale/custom-autoscale.png)

## <a name="set-up-autoscale-settings-for-your-application-in-azure-cli"></a>Configurer les paramètres de mise à l’échelle automatique pour votre application dans l’interface Azure CLI

Vous pouvez également définir les modes de mise à l’échelle automatique à l’aide de l’interface Azure CLI.  Les commandes suivantes créent un paramètre et une règle de mise à l’échelle automatique.

* Créer un paramètre de mise à l’échelle automatique :

   ```azurecli
   az monitor autoscale create -g demo-rg --resource /subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourcegroups/demo-rg/providers/Microsoft.AppPlatform/Spring/autoscale/apps/demo/deployments/default --name demo-setting --min-count 1 --max-count 5 --count 1
   ```

* Créer une règle de mise à l’échelle automatique :

   ```azurecli
   az monitor autoscale rule create -g demo-rg --autoscale-name demo-setting --scale out 1 --cooldown 1 --condition "tomcat.global.request.total.count > 100 avg 1m where AppName == demo and Deployment == default"
   ```

## <a name="upgrade-to-the-standard-tier"></a>Passer au niveau Standard

Si vous êtes au niveau De base et limité par une ou plusieurs de ces restrictions, vous pouvez passer au niveau Standard. Pour ce faire, accédez au menu **Niveau tarifaire** en sélectionnant d’abord la colonne **Niveau standard**, puis le bouton **Mettre à niveau**.

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble de la mise à l’échelle automatique dans Microsoft Azure](../azure-monitor/autoscale/autoscale-overview.md)
* [Supervision de la mise à l’échelle automatique avec l’interface Azure CLI](/cli/azure/monitor/autoscale)
