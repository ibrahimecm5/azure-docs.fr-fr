---
title: Autorisations dans Microsoft Defender pour le cloud | Microsoft Docs
description: Cet article explique la façon dont Microsoft Defender pour le cloud utilise le contrôle d’accès en fonction du rôle pour attribuer des autorisations aux utilisateurs et identifier les actions autorisées pour chaque rôle.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 07/04/2021
ms.author: memildin
ms.openlocfilehash: 9f22f21f5358dd6cdf798e64727fee510abed105
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437787"
---
# <a name="permissions-in-microsoft-defender-for-cloud"></a>Autorisations dans Microsoft Defender pour le cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender pour le cloud utilise le [contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md), qui fournit des [rôles intégrés](../role-based-access-control/built-in-roles.md) susceptibles d’être affectés à des utilisateurs, des groupes et des services dans Azure.

Defender pour le cloud évalue la configuration de vos ressources pour identifier les vulnérabilités et les problèmes de sécurité. Dans Defender pour le cloud, vous ne voyez les informations relatives à une ressource que lorsque vous avez reçu le rôle Propriétaire, Contributeur ou Lecteur pour l’abonnement ou le groupe de ressources auquel appartient la ressource.

Outre ces rôles intégrés, il existe deux rôles propres à Defender pour le cloud :

* **Lecteur Sécurité** : L’utilisateur ayant ce rôle dispose de droits de consultation dans Defender pour le cloud. Il peut afficher les recommandations, les alertes, la stratégie de sécurité actuelle et les états de sécurité, mais ne peut pas apporter de modifications.
* **Administrateur de la sécurité** : l’utilisateur ayant ce rôle dispose des mêmes droits que le Lecteur Sécurité. Il peut en outre modifier la stratégie de sécurité et ignorer les alertes et les recommandations.

> [!NOTE]
> Les rôles de sécurité que sont Lecteur Sécurité et Administrateur de la sécurité ont uniquement accès à Defender pour le cloud. Les rôles de sécurité n’ont pas accès aux autres services d’Azure (par exemple, Stockage, Web et mobile ou Internet des objets).

## <a name="roles-and-allowed-actions"></a>Rôles et actions autorisées

Le tableau suivant présente les rôles et les actions autorisées dans Defender pour le cloud.

| **Action**                                                                                                                      | [Lecteur de sécurité ](../role-based-access-control/built-in-roles.md#security-reader) / <br> [Lecteur](../role-based-access-control/built-in-roles.md#reader) | [Administrateur de la sécurité](../role-based-access-control/built-in-roles.md#security-admin) | [Contributeur](../role-based-access-control/built-in-roles.md#contributor) / [Propriétaire](../role-based-access-control/built-in-roles.md#owner)| [Contributeur](../role-based-access-control/built-in-roles.md#contributor)| [Propriétaire](../role-based-access-control/built-in-roles.md#owner)|
|:----------------------------------------------------------------------------------------------------------------------------|:-----------------------------:|:--------------:|:------------------------------------------------------:|:------------------------:|:------------------:|
||||**(Au niveau du groupe de ressources)**|**(Au niveau de l’abonnement)**|**(Au niveau de l’abonnement)**|
| Ajouter/attribuer des initiatives (y compris) des standards de conformité réglementaire)                                                         | -                             | -              | -                                                      | -                        | ✔                 |
| Modifier une stratégie de sécurité                                                                                                        | -                             | ✔             | -                                                      | -                        | ✔                 |
| Activer/désactiver des plans Microsoft Defender                                                                                             | -                             | ✔             | -                                                      | -                        | ✔                 |
| Activer/désactiver le provisionnement automatique                                                                                          | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| Ignorer les alertes                                                                                                              | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| Appliquer des recommandations de sécurité à une ressource</br> (et utiliser [Corriger](implement-security-recommendations.md#fix-button)) | -                             | -              | ✔                                                     | ✔                        | ✔                 |
| Afficher les alertes et les recommandations                                                                                             | ✔                            | ✔              | ✔                                                     | ✔                        | ✔                 |
||||||

> [!NOTE]
> Nous vous recommandons d’attribuer le rôle le moins permissif permettant aux utilisateurs d’effectuer leurs tâches. Par exemple, affectez le rôle Lecteur aux utilisateurs qui n’ont besoin que de consulter des informations sur l’intégrité de la sécurité d’une ressource sans effectuer aucune action, telles que l’application des recommandations ou la modification des stratégies.

## <a name="next-steps"></a>Étapes suivantes
Cet article vous a expliqué de quelle manière Defender pour le cloud utilise RBAC Azure pour attribuer des autorisations aux utilisateurs et a identifié les actions autorisées pour chaque rôle. Maintenant que vous êtes familiarisé avec les affectations de rôles nécessaires pour surveiller l’état de sécurité de votre abonnement, modifier les stratégies de sécurité et appliquer les recommandations, découvrez comment :

- [Définir des stratégies de sécurité dans Defender pour le cloud](tutorial-security-policy.md)
- [Gérer les recommandations de sécurité dans Defender pour le cloud](review-security-recommendations.md)
- [Gérer les alertes de sécurité et y répondre dans Defender pour le cloud](managing-and-responding-alerts.md)
- [Surveiller les solutions de sécurité des partenaires](./partner-integration.md)