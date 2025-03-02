---
title: Changement de notification de détection intelligente - Azure Application Insights
description: Passez aux destinataires des notifications par défaut depuis la détection intelligente. La détection intelligente vous permet de superviser les suivis d’application avec Azure Application Insights afin de déterminer si les données de télémétrie du suivi présentent des anomalies.
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 02/14/2021
ms.reviewer: mbullwin
ms.openlocfilehash: 0937683eaacefc9fe4bbee21802a0f1657918efd
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111536697"
---
# <a name="smart-detection-e-mail-notification-change"></a>Modification des notifications par e-mail de la détection intelligente

>[!NOTE]
>Vous pouvez migrer vos ressources Application Insights la détection intelligente des alertes (préversion). La migration crée des règles d’alerte pour les différents modules de détection intelligente. Vous pouvez alors gérer et configurer ces règles comme n’importe quelle autre règle d’alerte Azure Monitor. Vous pouvez également configurer des groupes d’actions pour ces règles, ce qui permet à plusieurs méthodes de prendre des mesures ou de déclencher des notifications sur de nouvelles détections.
>
> Pour plus d’informations sur le processus de migration et le comportement de la détection intelligente après la migration, consultez [Migration des alertes de détection intelligente](../alerts/alerts-smart-detections-migration.md).

Sur la base des commentaires des clients, nous modifions, le 1{1}er{2} avril 2019, les rôles par défaut qui reçoivent les notifications par e-mail de la détection intelligente.

## <a name="what-is-changing"></a>Qu’est-ce qui change ?

Actuellement, les notifications par e-mail de la détection intelligente sont envoyées par défaut aux rôles _Propriétaire de l’abonnement_, _Contributeur de l’abonnement_ et _Lecteur de l’abonnement_. Ces rôles englobent souvent des utilisateurs qui ne participent pas activement au monitoring, et qui reçoivent donc inutilement des notifications. Pour améliorer cette expérience, nous sommes en train d’apporter une modification : les notifications par e-mail ne seront par défaut envoyées qu’aux rôles [Lecteur de monitoring](../../role-based-access-control/built-in-roles.md#monitoring-reader) et [Contributeur de monitoring](../../role-based-access-control/built-in-roles.md#monitoring-contributor).

## <a name="scope-of-this-change"></a>Portée de ce changement

Cette modification touchera toutes les règles de détection intelligente, à l’exception des suivantes :

* les règles de détection intelligente marquées comme étant en préversion, qui à l’heure actuelle ne prennent pas en charge les notifications par e-mail ;

* la règle Anomalies de défaillance,

## <a name="how-to-prepare-for-this-change"></a>Préparation à cette modification

Pour que les notifications par e-mail de la détection intelligente soient envoyées aux utilisateurs concernés, ces derniers doivent être assignés aux rôles [Lecteur de supervision](../../role-based-access-control/built-in-roles.md#monitoring-reader) ou [Contributeur de supervision](../../role-based-access-control/built-in-roles.md#monitoring-contributor) de l’abonnement.

Pour attribuer le rôle Lecteur de monitoring ou Contributeur de monitoring à des utilisateurs sur le Portail Azure, suivez les étapes de l’article [Attribuer des rôles Azure](../../role-based-access-control/role-assignments-portal.md). Veillez à sélectionner le rôle _Lecteur de monitoring_ ou _Contributeur de monitoring_ pour l’attribuer aux utilisateurs.

> [!NOTE]
> Les destinataires spécifiques des notifications de la détection intelligente, configurés avec l’option _Autres destinataires des e-mails_ dans les paramètres des règles, ne seront pas concernés par cette modification. Ils continueront de recevoir les notifications par e-mail.

Si vous avez des questions ou des commentaires au sujet de cette modification, n’hésitez pas à [nous contacter](mailto:smart-alert-feedback@microsoft.com).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la détection intelligente :

- [Défaillances](./proactive-failure-diagnostics.md)
- [Fuites de mémoire](./proactive-potential-memory-leak.md)
- [Anomalies de performance](./proactive-performance-diagnostics.md)

