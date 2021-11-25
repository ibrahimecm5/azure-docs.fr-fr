---
title: Configurer des clés gérées par le client dans Microsoft Sentinel | Microsoft Docs
description: Découvrez comment configurer des clés gérées par le client (CMK) dans Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: d0b172d2af5393c71acb9ea25e1b2d3df6091276
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522913"
---
# <a name="set-up-microsoft-sentinel-customer-managed-key"></a>Configurer la clé gérée par le client Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cet article fournit des informations générales et explique comment procéder pour configurer une [clé gérée par le client (CMK)](../azure-monitor/logs/customer-managed-keys.md) pour Microsoft Sentinel. Les clés CMK vous permettent de fournir une couche supplémentaire de protection à toutes les données stockées dans Microsoft Sentinel (qui sont déjà chiffrées par Microsoft dans toutes les ressources de stockage pertinentes), grâce à une clé de chiffrement créée et détenue par vous et stockée dans votre coffre [Azure Key Vault](../key-vault/general/overview.md).

## <a name="prerequisites"></a>Configuration requise

- La fonction CMK nécessite un cluster dédié à Log Analytics avec un niveau d'engagement d'au moins 500 Go/jour. Plusieurs espaces de travail peuvent être liés au même cluster dédié et ils partageront la même clé gérée par le client.

- Une fois que vous avez procédé comme indiqué dans ce guide, avant d’utiliser l’espace de travail, contactez le [Groupe produits Microsoft Sentinel](mailto:azuresentinelCMK@microsoft.com) pour la confirmation de l’intégration.

- En savoir plus sur la [Tarification des clusters dédiés Log Analytics](../azure-monitor/logs/logs-dedicated-clusters.md#cluster-pricing-model).

## <a name="considerations"></a>Considérations

- L’intégration d’un espace de travail CMK à Sentinel est prise en charge uniquement via l’API REST et non via le Portail Azure. Les modèles Azure Resource Manager (modèles ARM) ne sont actuellement pas pris en charge pour l’intégration CMK.

- La capacité CMK de Microsoft Sentinel est fournie uniquement aux *espaces de travail dans les clusters dédiés Log Analytics* qui *n’ont pas déjà été intégrés à Microsoft Sentinel*.

- Les modifications suivantes concernant les clés CMK ne *sont pas prises en charge*, car elles sont inefficaces (les données Microsoft Sentinel continuent d’être chiffrées uniquement par la clé gérée par Microsoft, et non par la clé CMK) :

  - Activation de CMK sur un espace de travail *déjà intégré* à Microsoft Sentinel.
  - Activation de CMK sur un cluster qui contient des espaces de travail intégrés à Sentinel.
  - Liaison d’un espace de travail non CMK intégré à Sentinel à un cluster compatible CMK.

- Les modifications suivantes liées à CMK *ne sont pas prises en charge*, car elles peuvent provoquer un comportement non défini ou problématique :

  - Désactivation de CMK sur un espace de travail déjà intégré à Microsoft Sentinel.
  - Définition d’un espace de travail CMK et intégré à Sentinel en tant qu’espace de travail non-CMK en désassociant de son cluster dédié CMK.
  - Désactivation de CMK sur un cluster Log Analytics CMK dédié.

- Microsoft Sentinel prend en charge les identités affectées par le système dans la configuration CMK. Donc, l’identité du cluster dédié Log Analytics doit être de type **Attribué par le système**. Nous vous recommandons d’utiliser l’identité qui est automatiquement attribuée au cluster Log Analytics lors de sa création.

- La modification de la clé gérée par le client en une autre clé (avec un autre URI) *n’est actuellement pas prise en charge*. Vous devez changer de clé par [rotation](../azure-monitor/logs/customer-managed-keys.md#key-rotation).

- Avant d’apporter des modifications CMK à un espace de travail de production ou à un cluster Log Analytics, contactez le [groupe de produits Microsoft Sentinel](mailto:azuresentinelCMK@microsoft.com).

## <a name="how-cmk-works"></a>Fonctionnement de CMK 

La solution Microsoft Sentinel utilise plusieurs ressources de stockage pour la collecte de journaux et les fonctionnalités, notamment un cluster dédié Log Analytics. Dans le cadre de la configuration de la fonctionnalité CMK de Microsoft Sentinel, vous devez également configurer les paramètres CMK sur le cluster dédié Log Analytics. Les données enregistrées par Microsoft Sentinel dans les ressources de stockage autres que Log Analytics sont également chiffrées à l’aide de la clé gérée par le client configurée pour le cluster dédié Log Analytics.

Consultez la documentation supplémentaire suivante :
- [Clés gérées par le client dans Azure Monitor (CMK)](../azure-monitor/logs/customer-managed-keys.md)
- [Azure Key Vault](../key-vault/general/overview.md).
- [Clusters dédiés Log Analytics](../azure-monitor/logs/logs-dedicated-clusters.md).

> [!NOTE]
> Si vous activez la fonctionnalité CMK sur Microsoft Sentinel, toute fonctionnalité d’évaluation en préversion publique qui ne prend pas en charge CMK n’est pas activée.

## <a name="enable-cmk"></a>Activer CMK 

Pour provisionner CMK, effectuez les étapes suivantes : 

1.  Créez un Azure Key Vault et générez ou importez une clé.

2.  Activez CMK sur votre espace de travail Log Analytics.

3.  Inscrivez-vous auprès du fournisseur de ressources Cosmos DB.

4.  Ajoutez une stratégie d’accès à votre instance Azure Key Vault.

5.  Intégrez l’espace de travail à Microsoft Sentinel via [l’API d’intégration](https://github.com/Azure/Azure-Sentinel/raw/master/docs/Azure%20Sentinel%20management.docx).

### <a name="step-1-create-an-azure-key-vault-and-generate-or-import-a-key"></a>ÉTAPE 1 : créer un Azure Key Vault et générer ou importer une clé

1. [Créez une ressource Azure Key Vault](/azure-stack/user/azure-stack-key-vault-manage-portal), puis générez ou importez une clé à utiliser pour le chiffrement des données.

    > [!NOTE]
    >  Azure Key Vault doit être configuré comme récupérable pour protéger votre clé et l’accès.

1.  [Activez les options de récupération :](../key-vault/general/key-vault-recovery.md)

    -   Vérifiez que la [suppression réversible](../key-vault/general/soft-delete-overview.md) est activée.

    -   Activez la [protection contre le vidage](../key-vault/general/soft-delete-overview.md#purge-protection) pour bénéficier d’une protection contre la suppression forcée du secret ou du coffre, même après activation de la suppression réversible.

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>ÉTAPE 2 : Activer CMK sur votre espace de travail Log Analytics

Suivez les instructions dans [Configuration de clés gérées par le client dans Azure Monitor](../azure-monitor/logs/customer-managed-keys.md) afin de créer un espace de travail CMK que vous allez utiliser comme espace de travail Microsoft Sentinel dans les étapes suivantes.

### <a name="step-3-register-to-the-cosmos-db-resource-provider"></a>ÉTAPE 3 : inscrivez-vous auprès du fournisseur de ressources Cosmos DB.

Microsoft Sentinel fonctionne avec Cosmos DB en tant que ressource de stockage supplémentaire. Vérifiez que vous êtes bien inscrit auprès du fournisseur de ressources Cosmos DB.

Suivez les instructions de Cosmos DB indiquant comment [inscrire le fournisseur de ressources Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) dans l’abonnement Azure.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>ÉTAPE 4 : Ajouter une stratégie d’accès à votre instance Azure Key Vault

Veillez à ajouter l’accès à partir de Cosmos DB à votre instance Azure Key Vault. Suivez les instructions Cosmos DB indiquant comment [ajouter une stratégie d’accès à votre instance Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#add-access-policy) avec le principal Azure Cosmos DB.

### <a name="step-5-onboard-the-workspace-to-microsoft-sentinel-via-the-onboarding-api"></a>ÉTAPE 5 : Intégrer l’espace de travail à Microsoft Sentinel via l’API d’intégration

Intégrez l’espace de travail à Microsoft Sentinel via [l’API d’intégration](https://github.com/Azure/Azure-Sentinel/raw/master/docs/Azure%20Sentinel%20management.docx).

## <a name="key-encryption-key-revocation-or-deletion"></a>Révocation ou suppression d’une clé de chiffrement principale

Si un utilisateur révoque la clé de chiffrement principale (la clé CMK), soit en la supprimant, soit en supprimant l’accès au cluster dédié et au fournisseur de ressources Cosmos DB, Microsoft Sentinel prend en compte cette modification et se comporte comme si les données n’étaient plus disponibles dans un délai d’une heure. À ce stade, toute opération qui utilise des ressources de stockage persistantes, telle que l’ingestion de données, la modification de configuration persistante et la création d’incident, est empêchée. Les données déjà stockées ne sont pas supprimées, mais restent inaccessibles. Les données inaccessibles sont régies par la stratégie de conservation des données et sont vidées conformément à cette stratégie.

La seule opération possible une fois que la clé de chiffrement est révoquée ou supprimée est la suppression du compte.

Si l’accès est restauré après la révocation, Microsoft Sentinel restaure l’accès aux données dans un délai d’une heure.

L’accès aux données peut être révoqué en désactivant la clé gérée par le client dans le coffre de clés ou en supprimant la stratégie d’accès à la clé pour le cluster Log Analytics dédié et Cosmos DB. La révocation de l’accès en supprimant la clé du cluster Log Analytics dédié, ou en supprimant l’identité associée au cluster Log Analytics dédié, n’est pas prise en charge.

Pour en savoir plus sur la façon dont cela fonctionne dans Azure Monitor, consultez [Révocation de clé CMK dans Azure Monitor](../azure-monitor/logs/customer-managed-keys.md#key-revocation).

## <a name="customer-managed-key-rotation"></a>Rotation des clés gérées par le client

Microsoft Sentinel et Log Analytics prennent en charge la rotation des clés. Quand un utilisateur effectue une rotation des clés dans Key Vault, Microsoft Sentinel prend en charge la nouvelle clé dans un délai d’une heure.

Dans Key Vault, vous pouvez effectuer une rotation de clé en créant une version de la clé :

![rotation de clé](./media/customer-managed-keys/key-rotation.png)

Vous pouvez désactiver la version précédente de la clé après 24 heures, ou dès que les journaux d’audit Azure Key Vault ne font état d’aucune activité utilisant cette version.

Après avoir effectué la rotation de la clé, vous devez mettre à jour explicitement la ressource de cluster Log Analytics dédiée dans Log Analytics avec la nouvelle version de la clé Azure Key Vault. Pour plus d’informations, consultez [Rotation de clé CMK dans Azure Monitor](../azure-monitor/logs/customer-managed-keys.md#key-rotation).

## <a name="replacing-a-customer-managed-key"></a>Remplacement d’une clé gérée par le client

Microsoft Sentinel ne prend pas en charge le remplacement d’une clé gérée par le client. Utilisez plutôt la [capacité de rotation des clés](#customer-managed-key-rotation).

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez découvert comment configurer une clé gérée par le client dans Microsoft Sentinel. Pour en savoir plus sur Microsoft Sentinel, consultez les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Microsoft Sentinel](./detect-threats-built-in.md).
- [Utilisez des classeurs](monitor-your-data.md) pour superviser vos données.
