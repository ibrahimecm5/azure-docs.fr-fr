---
title: Concepts relatifs aux SMS dans Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Découvrez les concepts relatifs aux SMS dans Communication Services.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: sms
ms.openlocfilehash: 455bad570c4b86c00933ffb1fd1f27f85871e6d9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426104"
---
# <a name="sms-concepts"></a>Concepts relatifs aux SMS

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Azure Communication Services vous permet d’envoyer et de recevoir des SMS à l’aide des kits SDK SMS Communication Services. Ces kits SDK peuvent être utilisés pour prendre en charge les scénarios de service client, les rappels de rendez-vous, l’authentification à 2 facteurs et d’autres besoins de communication en temps réel. Le service SMS de Communication Services vous permet d’envoyer des messages de manière fiable tout en affichant les métriques de remise et de réponse.

Les principales fonctionnalités des kits SDK SMS Azure Communication Services incluent :

-  Une expérience de configuration **simple** pour l’ajout de la fonctionnalité SMS dans vos applications.
- Une prise en charge des messages à **haute vitesse** via des numéros gratuits et des numéros courts (*bientôt disponible*) pour les cas d’usage A2P (application à personne) aux États-Unis.
- Une **Messagerie en bloc** prise en charge pour permettre l’envoi de messages à plusieurs destinataires à la fois.
- Des conversations **bidirectionnelles** pour prendre en charge des scénarios tels que le support client, les alertes et les rappels de rendez-vous.
- Une **remise fiable** avec des rapports de remise en temps réel pour les messages envoyés à partir de votre application.
- Une **Analytique** pour effectuer le suivi de vos modèles d’utilisation de SMS.
- Une prise en charge de la gestion des **désactivations** pour détecter et respecter automatiquement les désactivations pour les numéros gratuits. Les désinscriptions des numéros gratuits aux États-Unis sont régies et appliquées par les opérateurs nationaux.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [ l’envoi de SMS](../../quickstarts/telephony-sms/send.md)

Les documents suivants peuvent vous intéresser :

- Se familiariser avec les [kits SDK SMS](../telephony-sms/sdk-features.md)
- Obtenir un [numéro de téléphone](../../quickstarts/telephony-sms/get-phone-number.md) compatible SMS
- [Types de numéros de téléphone dans Azure Communication Services](../telephony-sms/plan-solution.md)
