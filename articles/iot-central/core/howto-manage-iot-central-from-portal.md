---
title: Gérer et superviser IoT Central dans le portail Azure | Microsoft Docs
description: Cet article explique comment créer, gérer et superviser vos applications IoT Central et autoriser la gestion des identités à partir du portail Azure.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2021
ms.topic: how-to
ms.openlocfilehash: 2d8ab101b39412d9345fa470d3b6584036990581
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084964"
---
# <a name="manage-and-monitor-iot-central-from-the-azure-portal"></a>Gérer et superviser IoT Central à partir du portail Azure

Vous pouvez utiliser le [portail Azure](https://portal.azure.com) pour créer, gérer et superviser des applications IoT Central.

## <a name="create-iot-central-applications"></a>Créer des applications IoT Central

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Pour créer une application, accédez à la page [Application IoT Central](https://ms.portal.azure.com/#create/Microsoft.IoTCentral) dans le portail Azure :

![Créer un formulaire IoT Central](media/howto-manage-iot-central-from-portal/create-form.png)

* Le **Nom de la ressource** est un nom unique que vous pouvez choisir pour votre application IoT Central dans votre groupe de ressources Azure.

* L’**URL de l’application** est l’URL que vous pouvez utiliser pour accéder à votre application.

* **Modèle** est le type d’application IoT Central que vous souhaitez créer. Vous pouvez créer une application à partir de la liste des modèles pertinents pour le secteur pour démarrer rapidement ou partir de zéro à l’aide du modèle **Application personnalisée**.

* L’**Emplacement** correspond à la [zone géographique](https://azure.microsoft.com/global-infrastructure/geographies/) où vous souhaitez créer votre application. D'une façon générale, il est recommandé de choisir l'emplacement qui est physiquement le plus proche de vos appareils pour obtenir des performances optimales. Azure IoT Central est actuellement disponible aux localisations suivantes :
    
    * Australie
    * USA Centre-Est
    * USA Est
    * USA Est 2
    * Japon Est
    * Europe Nord
    * Asie Sud-Est
    * Sud du Royaume-Uni
    * Europe Ouest
    * USA Ouest

  Une fois que vous avez choisi un emplacement, vous ne pouvez pas par la suite déplacer votre application dans un autre emplacement.

Après avoir renseigné tous les champs, sélectionnez **Créer**. Pour plus d’informations, consultez [Créer une application IoT Central](howto-create-iot-central-application.md).

## <a name="manage-existing-iot-central-applications"></a>Gérer des applications IoT Central existantes

Si vous disposez déjà d’une application Azure IoT Central, vous pouvez la supprimer ou la déplacer vers un abonnement ou un groupe de ressources différent dans le portail Azure.

> [!NOTE]
> Les applications créées à l’aide du plan *gratuit* ne nécessitent pas d’abonnement Azure. Elles ne sont donc pas listées dans votre abonnement Azure sur le portail Azure. Vous pouvez voir et gérer les applications gratuites uniquement à partir du portail IoT Central.

Pour commencer, recherchez votre application dans la barre de recherche située en haut du portail Azure. Vous pouvez également voir toutes vos applications en recherchant _Applications IoT Central_, puis en sélectionnant le service :

![Capture d’écran qui montre les résultats de la recherche pour « Applications IoT Central » avec le premier service sélectionné.](media/howto-manage-iot-central-from-portal/search-iot-central.png)

Quand vous sélectionnez une application dans les résultats de la recherche, le portail Azure vous montre sa vue d’ensemble. Vous pouvez accéder à l’application en sélectionnant l’**URL de l’application IoT Central** :

![Capture d’écran montrant la page « Vue d’ensemble » avec l’option « URL de l’application IoT Central » mise en évidence.](media/howto-manage-iot-central-from-portal/highlight-application.png)

Pour déplacer l’application vers un autre groupe de ressources, sélectionnez le lien **Modifier** à côté du groupe de ressources. Dans la page **Déplacer des ressources**, sélectionnez le groupe de ressources vers lequel vous souhaitez déplacer cette application :

![Capture d’écran montrant la page « Vue d’ensemble » avec l’option « Groupe de ressources (modifier) » mise en surbrillance.](media/howto-manage-iot-central-from-portal/highlight-resource-group.png)

Pour déplacer l’application vers un autre abonnement, sélectionnez **Modifier** en regard de l’abonnement. Dans la page **Déplacer des ressources**, choisissez l’abonnement vers lequel vous souhaitez déplacer cette application :

![Portail de gestion : gestion des ressources](media/howto-manage-iot-central-from-portal/highlight-subscription.png)

## <a name="configure-a-managed-identity"></a>Configurer une identité managée

Quand vous configurez une exportation de données dans votre application IoT Central, vous pouvez choisir de configurer la connexion à la destination à l’aide d’une *chaîne de connexion* ou d’une [identité gérée](../../active-directory/managed-identities-azure-resources/overview.md). L’utilisation d’une identité gérée est plus sécurisée, car vous n’avez pas besoin de stocker les informations d’identification pour la destination dans votre application IoT Central. IoT Central utilise actuellement des [identités managées affectées par le système](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types). Pour créer l’identité managée de votre application, utilisez le Portail Azure ou l’API REST.

> [!NOTE]
> Vous pouvez uniquement ajouter une identité managée à une application IoT Central qui a été créée dans une région. Toutes les nouvelles applications sont créées dans une région. Pour en savoir plus, consultez [Mises à jour](https://azure.microsoft.com/updates/azure-iot-central-new-and-updated-features-august-2021/).

Quand vous configurez une identité managée, la configuration comprend une *étendue* et un *rôle* :

* L’étendue définit l’emplacement où vous pouvez utiliser l’identité managée. Par exemple, vous pouvez utiliser un groupe de ressources Azure comme étendue. Dans ce cas, l’application IoT Central et la destination doivent être dans le même groupe de ressources.
* Le rôle définit les autorisations qui sont accordées à l’application IoT Central dans le service de destination. Par exemple, pour qu’une application IoT Central envoie des données à un Event Hub, l’identité managée a besoin de l’attribution de rôle **Expéditeur de données Azure Event Hubs**.

[!INCLUDE [iot-central-managed-identity](../../../includes/iot-central-managed-identity.md)]

Vous pouvez configurer les attributions de rôles dans le Portail Azure ou utiliser Azure CLI :

* Pour en savoir plus sur la configuration des attributions de rôles dans le Portail Azure pour des destinations spécifiques, consultez [Exporter des données IoT vers des destinations Cloud à l’aide de l’exportation de données](howto-export-data.md).
* Pour en savoir plus sur la configuration des attributions de rôles à l’aide de l’interface Azure CLI, consultez [Gérer IoT Central à partir depuis Azure CLI ou PowerShell](howto-manage-iot-central-from-cli.md).

## <a name="monitor-application-health"></a>Superviser l’intégrité de l’application

> [!NOTE]
> Les métriques sont uniquement disponibles pour les applications de la version 3 d’IoT Central. Pour savoir comment vérifier la version de votre application, consultez [Comment obtenir des informations sur mon application ?](howto-faq.yml#how-do-i-get-information-about-my-application-).

Vous pouvez utiliser l’ensemble des métriques fournies par IoT Central pour évaluer l’intégrité des appareils connectés à votre application IoT Central et l’intégrité des exportations de données en cours d’exécution.

Les métriques sont activées par défaut pour votre application IoT Central et vous y accédez à partir du [portail Azure](https://portal.azure.com/). La [plateforme de données Azure Monitor expose ces métriques](../../azure-monitor/essentials/data-platform-metrics.md) et offre plusieurs moyens d’interagir avec elles. Par exemple, vous pouvez utiliser des graphiques dans le portail Azure, une API REST ou des requêtes dans PowerShell ou Azure CLI.

> [!TIP]
> Les applications qui utilisent le plan d’essai gratuit n’ont pas d’abonnement Azure associé et ne prennent donc pas en charge les métriques d’Azure Monitor. Vous pouvez [convertir une application en plan de tarification standard](./howto-faq.yml#how-do-i-move-from-a-free-to-a-standard-pricing-plan-) et accéder à ces métriques.

### <a name="view-metrics-in-the-azure-portal"></a>Afficher les métriques dans le portail Azure

Les étapes suivantes partent du principe que vous disposez d’une [application IoT Central](./howto-create-iot-central-application.md) avec quelques [appareils connectés](./tutorial-connect-device.md) ou une [exportation de données](howto-export-data.md) en cours d’exécution.

Pour afficher les métriques IoT Central dans le portail :

1. Accédez à votre ressource d’application IoT Central dans le portail. Par défaut, les ressources IoT Central se trouvent dans un groupe de ressources appelé **IOTC**.
1. Pour créer un graphique à partir des métriques de votre application, sélectionnez **Métriques** dans la section **Surveillance**.

![Métriques Azure](media/howto-manage-iot-central-from-portal/metrics.png)

### <a name="azure-portal-permissions"></a>Autorisations de Portail Azure

L’accès aux métriques dans le portail Azure est géré par le [contrôle d’accès en fonction du rôle Azure](../../role-based-access-control/overview.md). Utilisez le portail Azure pour ajouter des utilisateurs à l’application, au groupe de ressources ou à l’abonnement IoT Central pour leur accorder l’accès. Vous devez ajouter un utilisateur dans le portail, même s’il a déjà été ajouté à l’application IoT Central. Utilisez des [rôles intégrés Azure](../../role-based-access-control/built-in-roles.md) pour un contrôle d’accès plus précis.

### <a name="iot-central-metrics"></a>Métriques IoT Central

Pour obtenir la liste des métriques actuellement disponibles pour IoT Central, consultez [Métriques prises en charge avec Azure Monitor](../../azure-monitor/essentials/metrics-supported.md#microsoftiotcentraliotapps).

### <a name="metrics-and-invoices"></a>Métriques et factures

Les métriques peuvent être différer des valeurs figurant sur votre facture Azure IoT Central. Cette situation se produit pour plusieurs raisons :

* Les [plans de tarification standard](https://azure.microsoft.com/pricing/details/iot-central/) d’IoT Central incluent deux appareils et des quotas de message variables gratuitement. Si les éléments gratuits sont exclus de la facturation, ils sont toujours comptabilisés dans les métriques.

* IoT Central génère automatiquement une identité d’appareil de test pour chaque modèle d’appareil dans l’application. Cette identité d’appareil est visible sur la page **Gérer l’appareil de test** pour un modèle d’appareil. Vous pouvez choisir de valider vos modèles d’appareils avant de les publier en générant un code qui utilise ces identités d’appareil de test. Si ces appareils sont exclus de la facturation, ils sont toujours comptabilisés dans les métriques.

* Bien que les métriques puissent afficher un sous-ensemble de communications appareil-à-cloud, toutes les communications entre l’appareil et le cloud [comptent comme un message pour la facturation](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="monitor-connected-iot-edge-devices"></a>Superviser les appareils IoT Edge connectés

Pour savoir comment superviser à distance votre flotte IoT Edge à l’aide d’Azure Monitor et de l’intégration de métriques prédéfinies, consultez [Collecter et transporter des métriques](../../iot-edge/how-to-collect-and-transport-metrics.md).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à gérer et à superviser des applications Azure IoT Central dans le portail Azure, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Administrer votre application](howto-administer.md)