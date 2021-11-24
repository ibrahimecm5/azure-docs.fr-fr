---
title: Configurer Microsoft Defender pour le stockage
titleSuffix: Azure Storage
description: Configurez Microsoft Defender pour le stockage de façon à détecter les anomalies dans l’activité de votre compte et à être notifié en cas de tentatives d’accès potentiellement dangereuses à votre compte.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: ozgun
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 17cc82bd448910cc92eec42db889605c48b65fb5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297009"
---
# <a name="configure-microsoft-defender-for-storage"></a>Configurer Microsoft Defender pour le stockage

Microsoft Defender pour le stockage fournit une couche supplémentaire d’informations de sécurité qui détecte les tentatives inhabituelles et potentiellement dangereuses d’accès ou d’exploitation des comptes de stockage. Cette couche de protection vous permet de traiter les menaces sans pour autant être un expert en sécurité ni avoir besoin de gérer des systèmes de supervision de la sécurité.

Les alertes de sécurité sont déclenchées lorsque des anomalies se produisent dans l’activité. Ces alertes de sécurité sont intégrées avec [Microsoft Defender pour le cloud](https://azure.microsoft.com/services/security-center/) et sont également envoyées par e-mail aux administrateurs d’abonnement, avec les détails des activités suspectes, ainsi que des recommandations sur la façon d’examiner et de corriger les menaces.

Le service ingère les journaux de ressources des demandes de lecture, d’écriture et de suppression adressées au Stockage Blob et à Azure Files en vue de détecter les menaces. Pour examiner les alertes de Microsoft Defender pour le cloud, vous pouvez afficher l’activité de stockage associée à l’aide de la journalisation Storage Analytics. Pour plus d’informations, consultez **Configuration de la journalisation** dans [Surveillance d’un compte de stockage dans le portail Azure](./manage-storage-analytics-logs.md#configure-logging).

## <a name="availability"></a>Disponibilité

Microsoft Defender pour le stockage est actuellement disponible pour Stockage Blob, Azure Files et Azure Data Lake Storage Gen2. Les types de compte prenant en charge Microsoft Defender pour le cloud incluent les comptes à usage général v2, les objets blob de blocs et les comptes Stockage Blob. Microsoft Defender pour le stockage est disponible dans tous les clouds publics et US Government, mais pas dans d’autres régions de cloud souverain ou de cloud Azure Government.

Les comptes avec des espaces de noms hiérarchiques activés pour Data Lake Storage prennent en charge les transactions à l’aide des API Stockage Blob Azure et des API Data Lake Storage. Les partages de fichiers Azure prennent en charge les transactions via SMB.

Pour plus d’informations sur la tarification, y compris une version d’évaluation gratuite de 30 jours, consultez la [page de tarification de Microsoft Defender pour le cloud](https://azure.microsoft.com/pricing/details/security-center/).

La liste suivante récapitule la disponibilité de Microsoft Defender pour le stockage :

- État de sortie :
  - [Stockage Blob](https://azure.microsoft.com/services/storage/blobs/) (disponibilité générale)
  - [Azure Files](../files/storage-files-introduction.md) (disponibilité générale)
  - Azure Data Lake Storage Gen2 (disponibilité générale)
- Clouds : ✔ Clouds commerciaux<br>
    ✔ Azure Government<br>
    ✘ Azure China 21Vianet

## <a name="set-up-microsoft-defender-for-cloud"></a>Configurer Microsoft Defender pour le cloud

Vous pouvez configurer Microsoft Defender pour le stockage de plusieurs façons, qui sont décrites dans les sections suivantes.

### <a name="microsoft-defender-for-cloud"></a>[Microsoft Defender pour le cloud](#tab/azure-security-center)

Microsoft Defender pour le stockage est intégré à Microsoft Defender pour le cloud. Lorsque vous activez les fonctionnalités de sécurité renforcée de Microsoft Defender pour le cloud dans votre abonnement, Microsoft Defender pour le stockage est automatiquement activé pour tous vos comptes de stockage. Pour activer ou désactiver Defender pour le stockage avec des comptes de stockage individuels dans un abonnement spécifique :

1. Lancez **Microsoft Defender pour le cloud** dans le [portail Azure](https://portal.azure.com).
1. Dans le menu principal de Defender pour le cloud, sélectionnez **Paramètres de l’environnement**.
1. Sélectionnez l’abonnement pour lequel vous souhaitez activer ou désactiver Microsoft Defender pour le cloud.
1. Sélectionnez **Activer tous les abonnements Microsoft Defender** afin d’activer Microsoft Defender pour le cloud dans l’abonnement.
1. Sous **Sélectionner un plan Microsoft Defender par type de ressource**, recherchez la ligne **Stockage**, puis sélectionnez **Activé** dans la colonne **Plan**.
1. Enregistrez vos modifications.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-security-center.png" alt-text="Capture d’écran montrant comment activer Microsoft Defender pour le stockage.":::

Microsoft Defender pour le stockage est maintenant activé pour tous les comptes de stockage de cet abonnement.

### <a name="portal"></a>[Portail](#tab/azure-portal)

1. Lancez le [portail Azure](https://portal.azure.com/).
1. Accédez à votre compte de stockage. Sous **Paramètres**, sélectionnez **Sécurité avancée**.
1. Sélectionnez **Activer Microsoft Defender pour le stockage**.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-portal.png" alt-text="Capture d'écran montrant comment activer un compte pour Microsoft Defender pour le stockage.":::

Microsoft Defender pour le stockage est maintenant activé pour ce compte de stockage.

### <a name="template"></a>[Modèle](#tab/template)

Utilisez un modèle Azure Resource Manager pour déployer un compte Stockage Azure avec Microsoft Defender pour le cloud activé. Pour plus d’informations, consultez [Compte de stockage avec la protection avancée contre les menaces](https://azure.microsoft.com/resources/templates/storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Utilisez Azure Policy pour activer Microsoft Defender pour le cloud dans les comptes de stockage sous un abonnement ou un groupe de ressources spécifiques.

1. Lancez la page **Azure Policy - Définitions**.
1. Recherchez la stratégie **Déployer des comptes Microsoft Defender pour le stockage**.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy-definitions.png" alt-text="Appliquer la stratégie afin d’activer Microsoft Defender pour le cloud pour les comptes de stockage":::

1. Sélectionnez un groupe de ressources ou un abonnement Azure.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy2.png" alt-text="Sélectionner un abonnement ou un groupe de ressources pour l’étendue de la stratégie ":::

1. Attribuez la stratégie.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy1.png" alt-text="Attribuer la stratégie afin d’activer Microsoft Defender pour le stockage":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Afin d’activer Microsoft Defender pour le cloud pour un compte de stockage via PowerShell, commencez par vérifier que vous avez installé le module [Az.Security](https://www.powershellgallery.com/packages/Az.Security). Ensuite, appelez la commande [Enable-AzSecurityAdvancedThreatProtection](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection). N’oubliez pas de remplacer les valeurs entre crochets par vos propres valeurs :

```azurepowershell
Enable-AzSecurityAdvancedThreatProtection -ResourceId "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/"
```

Afin de vérifier le paramètre Microsoft Defender pour le stockage pour un compte de stockage avec PowerShell, appelez la commande [Get-AzSecurityAdvancedThreatProtection](/powershell/module/az.security/get-azsecurityadvancedthreatprotection). N’oubliez pas de remplacer les valeurs entre crochets par vos propres valeurs :

```azurepowershell
Get-AzSecurityAdvancedThreatProtection -ResourceId "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/"
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Afin d’activer Microsoft Defender pour le stockage pour un compte de stockage via Azure CLI, appelez la commande [az security atp storage update](/cli/azure/security/atp/storage#az_security_atp_storage_update). N’oubliez pas de remplacer les valeurs entre crochets par vos propres valeurs :

```azurecli
az security atp storage update \
    --resource-group <resource-group> \
    --storage-account <storage-account> \
    --is-enabled true
```

Afin de vérifier le paramètre Microsoft Defender pour le stockage pour un compte de stockage avec Azure CLI, appelez la commande [az security atp storage show](/cli/azure/security/atp/storage#az_security_atp_storage_show). N’oubliez pas de remplacer les valeurs entre crochets par vos propres valeurs :

```azurecli
az security atp storage show \
    --resource-group <resource-group> \
    --storage-account <storage-account>
```

---

## <a name="explore-security-anomalies"></a>Explorer les anomalies de sécurité

Quand des anomalies d’activité du stockage sont détectées, vous recevez une notification par e-mail qui contient des informations sur l’événement de sécurité suspect. Les détails de l’événement fournis sont les suivants :

- Nature de l’anomalie
- Nom du compte de stockage
- Heure de l’événement
- Type de stockage
- Causes potentielles
- Procédure d’investigation
- Procédure de correction

L’e-mail fournit également des détails sur les causes possibles et les actions recommandées pour examiner et atténuer la menace potentielle.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert-email.png" alt-text="E-mail d’alerte Microsoft Defender pour le stockage":::

Vous pouvez consulter et gérer vos alertes de sécurité actuelles à partir de la [mosaïque des alertes de sécurité](../../security-center/security-center-managing-and-responding-alerts.md) de Microsoft Defender pour le cloud. Sélectionnez une alerte pour afficher les détails et les actions recommandées pour examiner la menace actuelle et atténuer les menaces futures.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert.png" alt-text="Alerte Microsoft Defender pour le stockage":::

## <a name="security-alerts"></a>Alertes de sécurité

Les alertes sont générées en cas de détection de tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des comptes de stockage. Pour obtenir la liste des alertes pour Stockage Azure, consultez [Alertes pour Stockage Azure](../../security-center/alerts-reference.md#alerts-azurestorage).

## <a name="next-steps"></a>Étapes suivantes

- [Présentation de Microsoft Defender pour le stockage](../../security-center/defender-for-storage-introduction.md)
- [Microsoft Defender pour le cloud](../../security-center/security-center-introduction.md)
- [Journaux dans des comptes de stockage Azure](/rest/api/storageservices/About-Storage-Analytics-Logging)
