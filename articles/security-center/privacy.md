---
title: Gérer les données utilisateur dans Microsoft Defender pour le cloud | Microsoft Docs
description: Découvrez comment gérer les données utilisateur dans Microsoft Defender pour le cloud. La gestion des données utilisateur inclut la possibilité d’accéder à des données, d’en supprimer ou d’en exporter.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: memildin
ms.openlocfilehash: 69130a9d3804ffcf118dcd13aa2a40422fa01469
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428365"
---
# <a name="manage-user-data-in-microsoft-defender-for-cloud"></a>Gérer les données utilisateur dans Microsoft Defender pour le cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cet article fournit des informations sur la façon dont vous pouvez gérer les données utilisateur dans Microsoft Defender pour le cloud. La gestion des données utilisateur inclut la possibilité d’accéder à des données, d’en supprimer ou d’en exporter.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

Un utilisateur de Defender pour le cloud disposant du rôle Lecteur, Propriétaire, Contributeur ou Administrateur de compte peut accéder aux données client dans l’outil. Pour en savoir plus sur le rôle Administrateur de compte, consultez [Rôles intégrés pour le contrôle d’accès en fonction du rôle Azure](../role-based-access-control/built-in-roles.md) pour en savoir plus sur les rôles Lecteur, Propriétaire et Contributeur. Consultez [Administrateurs d’abonnements Azure](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="searching-for-and-identifying-personal-data"></a>Recherche et identification des données personnelles
Un utilisateur de Defender pour le cloud peut afficher ses données personnelles via le Portail Azure. Defender pour le cloud ne stocke que les informations de contact de sécurité telles que les adresses e-mail et les numéros de téléphone. Pour plus d’informations, consultez [Fournir les détails du contact de sécurité dans Microsoft Defender pour le cloud](configure-email-notifications.md).

Sur le portail Azure, un utilisateur peut consulter les configurations IP autorisées en utilisant la fonctionnalité d’accès juste-à-temps à la machine virtuelle de Defender pour le cloud. Pour plus d’informations, consultez [Gérer l’accès Juste à temps à la machine virtuelle](just-in-time-access-usage.md).

Dans le Portail Azure, un utilisateur peut afficher des alertes de sécurité fournies par Defender pour le cloud, notamment des informations sur l’attaquant et les adresses IP. Pour plus d’informations, consultez [Gestion et résolution des alertes de sécurité dans Microsoft Defender](managing-and-responding-alerts.md).

## <a name="classifying-personal-data"></a>Classification des données personnelles
Vous n’avez pas besoin de classer les données personnelles trouvées dans la caractéristique de contact de sécurité de Defender pour le cloud. Les données enregistrées sont une adresse e-mail (ou plusieurs) et un numéro de téléphone. Les [données de contact](configure-email-notifications.md) sont validées par Defender pour le cloud.

Vous n’avez pas besoin de classer les adresses IP et les numéros de port enregistrés par la caractéristique [juste-à-temps](just-in-time-access-usage.md) de Defender pour le cloud.

Seul un utilisateur possédant le rôle Administrateur peut classer les données personnelles par [alertes d’affichage](managing-and-responding-alerts.md) dans Defender pour le cloud.

## <a name="securing-and-controlling-access-to-personal-data"></a>Sécurisation et contrôle de l’accès aux données personnelles
Un utilisateur de Defender pour le cloud disposant du rôle Lecteur, Propriétaire, Contributeur ou Administrateur de compte peut accéder aux [données de contact de sécurité](configure-email-notifications.md).

Un utilisateur de Defender pour le cloud disposant du rôle Lecteur, Propriétaire, Contributeur ou Administrateur de compte peut accéder à ses stratégies [juste-à-temps](just-in-time-access-usage.md).

Un utilisateur de Defender pour le cloud disposant du rôle Lecteur, Propriétaire, Contributeur ou Administrateur de compte peut afficher ses [alertes](managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>Mise à jour des données personnelles
Un utilisateur de Defender pour le cloud disposant du rôle Propriétaire, Contributeur ou Administrateur de compte peut mettre à jour des [données de contact de sécurité](configure-email-notifications.md) via le Portail Azure.

Un utilisateur de Defender pour le cloud disposant du rôle Propriétaire, Contributeur ou Administrateur de compte peut mettre à jour ses [stratégies juste-à-temps](just-in-time-access-usage.md).

Un administrateur de compte ne peut pas modifier des incidents d’alerte. Un [incident d’alerte](managing-and-responding-alerts.md) fait partie des données de sécurité et est en lecture seule.

## <a name="deleting-personal-data"></a>Suppression des données personnelles
Un utilisateur de Defender pour le cloud disposant du rôle Propriétaire, Contributeur ou Administrateur de compte peut supprimer des [données de contact de sécurité](configure-email-notifications.md) via le Portail Azure.

Un utilisateur de Defender pour le cloud disposant du rôle Propriétaire, Contributeur ou Administrateur de compte peut supprimer les [stratégies juste-à-temps](just-in-time-access-usage.md) via le portail Azure.

Un utilisateur de Defender pour le cloud ne peut pas supprimer d’incidents d’alerte. Pour des raisons de sécurité, un [incident d’alerte](managing-and-responding-alerts.md) fait partie des données en lecture seule.

## <a name="exporting-personal-data"></a>Exportation des données personnelles
Un utilisateur de Defender pour le cloud disposant du rôle Lecteur, Propriétaire, Contributeur ou Administrateur de compte peut exporter des [données de contact de sécurité](configure-email-notifications.md) en procédant comme suit :

- Copie à partir du Portail Azure
- En exécutant l’appel d’API REST Azure, GET HTTP :
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

Un utilisateur de Defender pour le cloud disposant du rôle Administrateur de compte peut exporter les [stratégies juste-à-temps](just-in-time-access-usage.md) contenant les adresses IP via les méthodes suivantes :

- Copie à partir du Portail Azure
- En exécutant l’appel d’API REST Azure, GET HTTP :
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

Un administrateur de compte peut exporter les détails de l’alerte via les méthodes suivantes :

- Copie à partir du Portail Azure
- En exécutant l’appel d’API REST Azure, GET HTTP :
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

Pour plus d’informations, consultez [Get Security Alerts (GET Collection)](/previous-versions/azure/reference/mt704050(v=azure.100)) (Obtenir des alertes de sécurité (Collection GET)).

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Restriction de l’utilisation des données personnelles pour le profilage ou le marketing sans consentement
Un utilisateur de Defender pour le cloud peut choisir de refuser en supprimant ses [données de contact de sécurité](configure-email-notifications.md).

Les [données juste-à-temps](just-in-time-access-usage.md) sont considérées comme des données non identifiables et sont conservées pendant 30 jours.

Les [données d’alerte](managing-and-responding-alerts.md) sont considérées comme des données de sécurité et sont conservées pendant deux ans.

## <a name="auditing-and-reporting"></a>Audit et création de rapports
Les journaux d’audit des contacts de sécurité, des données juste-à-temps et des mises à jour d’alertes sont conservés dans les [journaux d’activité Azure](../azure-monitor/essentials/platform-logs-overview.md).
